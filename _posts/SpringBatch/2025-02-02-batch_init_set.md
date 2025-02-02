---
layout: single
title: "SpringBatch의 초기화 설정"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
toc: true
toc_sticky: true
date: 2025-02-02
last_modified_at: 2025-02-02
---

## 목표

```
스프링 배치의 초기화 설정에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch가 실행될때 초기화 설정에 대해서 정리한다.<br>
batch4버전과 5버전 동일한 흐름이다.
</span>

### 1.JobLauncherApplicationRunner

<span style="font-size:13pt">
배치를 실행하는 주체이다.<br>
해당 클래스는 BatchAutoConfiguration클래스에서 생성된다.<br>
yml과 같은 설정 파일에서 등록한 정보를 BatchProperties클래스에서 가져와 실행 시킬 job을 등록하고 실행하는데<br>
등록된 job이 없으면 job을 모두 실행 시킨다.<br>
</span>

### 2.BatchProperties

<span style="font-size:13pt">
Spring Batch의 환경을 설정하는 클래스이다.<br>
job이름, scheme 초기화 설정, 테이블 prefix등을 설정할 수 있다.<br>
properties파일이나 yml파일에 설정 가능하다.<br>
</span>

```java
@ConfigurationProperties(
    prefix = "spring.batch"
)
public class BatchProperties {
    private final Job job = new Job();
    private final Jdbc jdbc = new Jdbc();

    public BatchProperties() {
    }

    public Job getJob() {
        return this.job;
    }

    public Jdbc getJdbc() {
        return this.jdbc;
    }
... 생략
```

<span style="font-size:13pt">
클래스를 보면 알 수 있듯이 prefix로 spring.batch를 가지고 있다.<br>
따라서 yml파일등에 spring.batch 하위에 job이나 jdbc의 값을 설정하면 여기에 저장된다.<br>
아래와 같이 설정 하면 된다.<br>
</span>

```yml
spring:
  batch:
    job:
      # program arguments로 --job.name=batchJob 과 같이 설정하여 특정 job을 지정할 수 있다.
      # 값이 없으면 임의의 문자열인 NONE이 설정된다.
      name: ${job.name:NONE}
      enabled: true # 기본 값은 true이며  spring boot실행시 자동 실행하지 말지의 플래그이다.
    jdbc:
        initialize-schema: embedded  # 기본값 (임베디드 DB에서만 배치 테이블 생성)
        # always: 항상 배치 테이블을 생성
        # embedded (기본값): H2, HSQLDB 같은 임베디드 DB에서만 자동 생성
        # never: 배치 테이블을 직접 생성해야 함 (운영 환경에서는 never 권장)

        isolation-level-for-create: default # 배치 테이블 생성 시 사용할 트랜잭션 격리 수준. 솔직히 잘 모르겠다.
        schema: classpath:/batch-schema.sql # 커스텀 배치 테이블 생성 SQL 경로
        table-prefix: SYSTEM_  # 기본값 (배치 테이블 접두사 변경 가능) 기본은 BATCH_일 것이다.
```

<span style="font-size:13pt">
배치4까지는 ,구분으로 여러 job실행이 가능했지만 스프링배치5부터 멀티 job실행이 불가능해졌다.<br>
자동 실행을 막고 스케쥴러나 api로 여러개 실행 하는 수 밖에 없다고 한다.<br>
또한 파라미터를 넘기는 것도 단일 실행이라면 배치4와 동일하게 name=user1과 같이 어규먼츠로 설정이 가능하지만 <br>
멀티실행은 소스 상에서 설정해야 한다는거 같다.<br>
job에서 job을 실행시키거나 하는 꼼수도 있을거 같다.<br>
</span>
