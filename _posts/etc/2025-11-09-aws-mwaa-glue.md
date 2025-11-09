---
layout: single
title: "MWAA → Glue → S3 → (Airflow 로그 출력) 작업 정리"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - other
tags:
  - AWS
  - MWAA
  - Airflow
  - S3
  - Glue
toc: true
toc_sticky: true
date: 2025-11-09
last_modified_at: 2025-11-09
---

## 1) 개요

- **목표**: MWAA(Airflow)에서 **Glue PySpark Job**을 트리거하고, Job이 **S3에 결과 파일**을 생성하면, Airflow의 후속 태스크가 해당 파일을 **읽어 로그로 출력**.
- **리전**: `us-east-1` (실습 기준)
- **버킷**: `mwaa-glue-demo-bucket`
- **Glue Job**: `random_value_to_s3` — 랜덤 값을 생성해 S3에 저장

### 최종 플로우
1. Airflow DAG 수동 실행
2. `GlueJobOperator`가 Glue Job 실행 및 완료 대기
3. PythonOperator가 S3 출력물 중 **가장 최근 객체**를 읽어 Airflow 로그에 출력

---

## 2) S3 구조 & IAM 정책

### S3 기본 구조 (실습 시 사용)
```
s3://mwaa-glue-demo-bucket/
 └─ dags/                         # Airflow DAG 파일
 └─ plugins/
     └─ glue/
        ├─ scripts/               # Glue 스크립트
        └─ output/                # Glue 결과물 (랜덤 값 JSON 등)
```

### (예시) IAM 정책 조각
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowListBucketForGlueScripts",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::mwaa-glue-demo-bucket",
      "Condition": {
        "StringLike": {
          "s3:prefix": ["plugins/glue/scripts/*"]
        }
      }
    },
    {
      "Sid": "AllowReadGlueScript",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::mwaa-glue-demo-bucket/plugins/glue/scripts/*"
    },
    {
      "Sid": "WriteObjects",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:AbortMultipartUpload",
        "s3:ListMultipartUploadParts",
        "s3:ListBucketMultipartUploads"
      ],
      "Resource": "arn:aws:s3:::mwaa-glue-demo-bucket/plugins/glue/output/*"
    }
  ]
}
```

> 추가로, Glue가 사용하는 **TempDir**(`aws-glue-assets-<account>-<region>/temporary/`)과 Spark History Logs 경로를 사용하면 해당 버킷에 대한 접근 권한도 필요할 수 있음.

---

## 3) Glue Job

- 이름: `random_value_to_s3`
- 동작: 랜덤 값 생성 후 `s3://mwaa-glue-demo-bucket/plugins/glue/output/` 하위에 결과 파일 기록
- 단일 실행 테스트로 **정상 출력 확인**

```python
import json, random, datetime
from pyspark.sql import SparkSession

spark = SparkSession.builder.getOrCreate()

# 랜덤 값 생성
payload = {
    "ts": datetime.datetime.utcnow().isoformat() + "Z",
    "value": random.randint(1, 1000000)
}

# 단일 JSON 라인으로 저장할 문자열 RDD/DataFrame 생성
json_str = json.dumps(payload)
df = spark.createDataFrame([(json_str,)], ["line"])

# S3 경로: 결과 확인이 쉽도록 파일명을 time-based로 둡니다.
# (경로에 s3:// 접두어 필수)  :contentReference[oaicite:7]{index=7}
out_prefix = "s3://mwaa-glue-demo-bucket/glue/output/"
out_path = out_prefix + datetime.datetime.utcnow().strftime("%Y%m%dT%H%M%S") + ".json"

# 한 줄 JSON 파일로 저장
(df
 .coalesce(1)              # 작은 파일 1개
 .write.mode("overwrite")
 .text(out_path))          # .text()로 라인 단위 출력

print(f"Wrote random JSON to: {out_path}")
```

### 로그 관련 특이사항
- 종료 훅(`LogPusher`)에서 **존재하지 않는 버킷** 경고가 한 번 발생했으나, 데이터 출력에는 영향 없음.
- 이후 Airflow에서 Glue 로그를 읽으려다 `logs:FilterLogEvents` 권한 부족으로 실패 →
  - **해결 A**: MWAA 실행 역할에 CloudWatch Logs 권한(`logs:FilterLogEvents`) 부여 (리소스: `/aws-glue/jobs/output`, `/aws-glue/jobs/error` 등)
  - **해결 B**: Airflow에서 Glue 로그 스트리밍을 사용하지 않도록 설정(버전/오퍼레이터 옵션에 따라 조정)

---

## 4) MWAA(Airflow) DAG

### 발생 이슈 & 해결
- `ImportError: AwsGlueJobOperator` → **정식 클래스명은 `GlueJobOperator`**를 사용
- `AccessDenied: glue:GetJob` → MWAA 실행 Role에 **`glue:GetJob`, `StartJobRun`, `GetJobRun`, `GetJobRuns`** 권한 추가
- `ConcurrentRunsExceededException` → DAG 기본 재시도 설정으로 **동시 실행**이 겹침  
  - **해결**: `retries=0`로 조정(또는 Glue Job의 동시 실행 제한 고려), 작업이 끝난 뒤 후속 태스크로 진행
- `logs:FilterLogEvents` 권한 부족 → 위 **로그 권한 추가** 또는 **로그 스트리밍 비활성화**로 해결

### 최종 DAG
```python
from datetime import datetime, timedelta
import json
import logging
import boto3

from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.providers.amazon.aws.operators.glue import GlueJobOperator

# ===== 사용자 환경에 맞게 변수만 수정 =====
AWS_REGION = "us-east-1"
GLUE_JOB_NAME = "random_value_to_s3"
OUTPUT_BUCKET = "mwaa-glue-demo-bucket"
OUTPUT_PREFIX = "plugins/glue/output/"   # 예: glue/output/ or glue/output/dt=...

default_args = {
    "owner": "mwaa",
    "depends_on_past": False,
    "retries": 0,
    # "retry_delay": timedelta(minutes=2),
}

def _read_latest_s3_and_log(**context):
    s3 = boto3.client("s3", region_name=AWS_REGION)
    # 가장 최근(LastModified 최대) 객체 1개 선택
    paginator = s3.get_paginator("list_objects_v2")
    latest = None
    for page in paginator.paginate(Bucket=OUTPUT_BUCKET, Prefix=OUTPUT_PREFIX):
        for obj in page.get("Contents", []):
            if latest is None or obj["LastModified"] > latest["LastModified"]:
                latest = obj

    if not latest:
        logging.warning("S3에 출력 파일이 없습니다. prefix=%s", OUTPUT_PREFIX)
        return

    key = latest["Key"]
    head = s3.head_object(Bucket=OUTPUT_BUCKET, Key=key)
    size = head["ContentLength"]

    body = s3.get_object(Bucket=OUTPUT_BUCKET, Key=key)["Body"].read()
    # 파일이 한 줄 JSON이라 가정. (아니면 decode만 하고 앞 200자만 로깅)
    try:
        text = body.decode("utf-8", errors="replace").strip()
        # JSON 파싱 시도 (실패해도 원문 일부만 로깅)
        try:
            parsed = json.loads(text)
            logging.info("[RESULT] latest_key=%s size=%d bytes json=%s", key, size, parsed)
        except Exception:
            logging.info("[RESULT] latest_key=%s size=%d bytes text=%s", key, size, text[:500])
    except Exception as e:
        logging.exception("S3 파일 디코딩/로깅 중 오류: %s", e)
        raise

with DAG(
    dag_id="mwaa_glue_random_to_log",
    start_date=datetime(2025, 11, 1),
    schedule_interval=None,  # 수동 트리거
    catchup=False,
    max_active_runs=1,  
    default_args=default_args,
    tags=["glue", "mwaa", "s3"],
) as dag:

    run_glue = GlueJobOperator(
        task_id="run_glue_random_to_s3",
        job_name=GLUE_JOB_NAME,
        region_name=AWS_REGION,
        aws_conn_id="aws_default",
        wait_for_completion=True,   # Glue 완료까지 대기
        retries=0,
        # 만약 파라미터 쓰려면 아래 주석 해제
        # script_args={"--job-bookmark-option": "job-bookmark-disable"},
        verbose=True,
    )

    read_and_log = PythonOperator(
        task_id="read_s3_output_and_log",
        python_callable=_read_latest_s3_and_log,
        provide_context=True,
    )

    run_glue >> read_and_log

```

---

## 5) 실행 결과

- Glue Job **정상 실행** → S3에 결과 파일 생성 확인
- 이후 PythonOperator가 S3에서 **가장 최근 결과 파일**을 읽어 Airflow 로그에 **정상 출력**
- 최종적으로 **목표 동작 달성**

---

## 6) 비용 최소화를 위한 마무리

1. **MWAA 환경**: 당분간 사용 계획 없으면 **삭제** 권장 (환경 자체가 시간당 과금)
2. **NAT Gateway**: 실습용으로 만들었으면 **삭제** (시간당 과금 큼)
3. **CloudWatch Logs**: MWAA/Glue 로그 그룹 **삭제** 또는 **보존기간 3일** 설정
4. **S3 정리**: `plugins/glue/output/`·`dags/`·`plugins/`·`aws-glue-assets-*/temporary/` 등 실습 산출물 정리 또는 Lifecycle Rule로 자동 만료
5. **Glue Job**: 실행하지 않으면 과금 없음(필요시 Job/Role 삭제로 정리)

---

## 7) 트러블슈팅 타임라인(요약)

- `AwsGlueJobOperator` ImportError → **`GlueJobOperator`** 로 수정
- Glue 권한 부족(`glue:GetJob`) → MWAA Role에 **권한 추가**
- 동시 실행 초과 → Airflow **재시도 0**으로 조정(또는 트리거 중복 방지)
- CloudWatch Logs 권한 부족 → **권한 추가**(또는 로그 스트리밍 비활성화)
- 최종적으로 **Glue 정상 실행**, S3 결과 **정상 확인**, Airflow 로그에 **정상 출력**

---

## 8) 다음 액션(선택)

- DAG에 **XCom**으로 파일 키 전달/저장
- 결과 파일을 **파케/파케+파티션** 저장으로 변경
- MWAA 대신 **EventBridge → Glue → (Lambda로 후처리)** 로 간소화하는 비용 최적화 플로우 PoC
- IaC(Terraform/CloudFormation)로 **재현 가능한 템플릿**화