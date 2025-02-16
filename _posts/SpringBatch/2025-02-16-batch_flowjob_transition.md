---
layout: single
title: "SpringBatch의 FlowJob의 transition"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
  - flowjob
toc: true
toc_sticky: true
date: 2025-02-16
last_modified_at: 2025-02-16
---

## 목표

```
스프링 배치의 FlowJob의 transition에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 FlowJob의 transition에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* transition은 전이라는 뜻으로 Flow 내 step의 조건부 전환을 정의한다.
* Job의 Api설정에서 on() 메소드를 호출하면 TransitionBuilder가 반환되어 Transition Flow를 구성할 수 있다.
* Step의 종료상태(ExitStatus)가 어떤 pattern과도 매칭되지 않으면 배치에서 예외를 발생시켜 Job은 실패하게 된다.
* transition은 구체적인 것부터 순차적으로 적용된다.
  * 예를 들어 패턴이 "FAILD"와 "*"가 있다고 하면 FAILD부터 매칭되는지 검사한다.

### 2.배치 상태 유형

<span style="font-size:13pt">
배치 상태를 나타내는 유형에는 3가지가 있다.<br>
BatchStats, ExitStatus, FlowExecutionStatus<br>
</span>

#### BatchStatus (배치 실행 상태)
---
- JobExecution, StepExecution의 전반적인 실행 상태를 나타내는 Enum 값
- Spring Batch가 자동 관리하며, 사용자가 직접 변경할 수 없음
- `BATCH_JOB_EXECUTION`, `BATCH_STEP_EXECUTION` 테이블에 저장됨

| 상태 값 | 설명 | 변경 가능 여부 |
|---------|-------------------------------|--------------|
| STARTING | 잡이 시작되기 전 대기 상태 | ❌ |
| STARTED | 잡이 실행 중 | ❌ |
| STOPPING | `JobOperator.stop()` 호출로 중단 요청됨 | ❌ |
| STOPPED | `JobOperator.stop()`에 의해 정상 중지됨 | ❌ |
| FAILED | 잡이 실패함 (`Exception` 발생) | ❌ |
| COMPLETED | 잡이 정상적으로 완료됨 | ❌ |
| ABANDONED | 실패 후 재시도되지 않고 버려짐 | ❌ |
| UNKNOWN | 알 수 없는 상태 | ❌ |

#### ExitStatus (실행 종료 상태)
---
- JobExecution 및 StepExecution이 종료될 때 사용되는 상태 값
- Spring Batch가 기본적으로 관리하지만, 사용자가 직접 변경 가능
- Step 간의 흐름을 제어하는 용도로 활용됨
- `BATCH_STEP_EXECUTION.exit_code` 컬럼에 저장됨

| 상태 값 | 설명 | 변경 가능 여부 |
|---------|-------------------------------|--------------|
| EXECUTING | 실행 중 | ❌ |
| COMPLETED | 정상 종료됨 | ✅ (커스텀 가능) |
| NOOP | 실행되지 않음 (건너뜀) | ✅ (커스텀 가능) |
| FAILED | 실행 실패 | ✅ (커스텀 가능) |
| STOPPED | 중지됨 | ✅ (커스텀 가능) |
| UNKNOWN | 알 수 없는 상태 | ✅ (커스텀 가능) |

#### FlowExecutionStatus (Flow 실행 상태)
---
- Flow (Step의 묶음) 실행 상태를 나타내는 값
- Spring Batch 내부에서 Flow의 흐름을 제어하기 위해 사용됨
- `BatchStatus`, `ExitStatus`와 다르게 DB에 저장되지 않음
- 사용자가 직접 새로운 상태 값을 정의하여 활용 가능

| 상태 값 | 설명 | 변경 가능 여부 |
|---------|-------------------------------|--------------|
| COMPLETED | 정상 완료됨 | ✅ (커스텀 가능) |
| FAILED | 실패함 | ✅ (커스텀 가능) |
| STOPPED | 중지됨 | ✅ (커스텀 가능) |
| UNKNOWN | 알 수 없는 상태 | ✅ (커스텀 가능) |

#### 정리 및 비교

| 구분 | 역할 | DB 저장 여부 | 변경 가능 여부 | 주요 사용처 |
|------|------|------------|--------------|------------|
| **BatchStatus** | Job/Step 실행 상태 | ✅ 저장됨 (`BATCH_JOB_EXECUTION`, `BATCH_STEP_EXECUTION`) | ❌ (Spring Batch가 자동 관리) | Job, Step 실행 상태 확인 |
| **ExitStatus** | 실행 종료 상태 (Step 간 흐름 제어) | ✅ 저장됨 (`BATCH_STEP_EXECUTION.exit_code`) | ✅ (사용자가 변경 가능) | Step의 종료 상태를 기반으로 Flow 제어 |
| **FlowExecutionStatus** | Flow 실행 상태 (Flow 간 이동 결정) | ❌ 저장되지 않음 | ✅ (사용자가 변경 가능) | Flow 내에서 조건에 따른 이동 |


- `BatchStatus`는 **DB에 저장되는 실행 상태**이며, Spring Batch가 자동 관리 → 사용자가 변경할 수 없음 ❌
- `ExitStatus`는 **Step이 끝날 때 사용자가 변경할 수 있는 상태 값** → Step 간 조건 제어에 활용 가능 ✅
- `FlowExecutionStatus`는 **Flow 실행 흐름을 제어하는 용도**로 사용됨 → 사용자 정의 가능 ✅


### 3.Transition의 API

<span style="font-size:13pt">
Flow 가 실행되면 FlowExecutionStatus 에 상태값이 저장되고 최종적으로 Job 의 BatchStatus 와 ExitStatus 에 반영된다.<br>
Step 의 BatchStatus 및 ExitStatus 에는 아무런 영향을 주지 않고 Job 의 상태만을 변경한다.<br>
</span>

* on(String pattern)
* Step의 실행 결과로 돌려받는 종료상태(ExitStatus)와 매칭하는 패턴 스키마, BatchStatus 와 매칭하는 것이 아님
* pattern 과 ExitStatus 와 매칭이 되면 다음으로 실행할 Step 을 지정할 수 있다.
* 특수문자는 두 가지만 허용
  * “*” : 0개 이상의 문자와 매칭, 모든 ExitStatus 와 매칭된다
  * ”?” : 정확히 1개의 문자와 매칭
    * ex) “c*t”는 “cat”과 “count”에 매칭되고, “c?t”는 “cat”에는 매칭되지만 “count” 에는 매칭 되지 않는다.
* to()
  * 다음으로 실행할 단계를 지정
* from()
  * 이전 단계에서 정의한 Transition 을 새롭게 추가 정의함
* stop()
  * FlowExecutionStatus 가 STOPPED 상태로 종료되는 transition
  * Job 의 BatchStatus 와 ExitStatus 가 STOPPED 으로 종료됨
* fail()
  * FlowExecutionStatus 가 FAILED 상태로 종료되는 transition
  * Job 의 BatchStatus 와 ExitStatus 가 FAILED 으로 종료됨
* end()
  * FlowExecutionStatus 가 COMPLETED 상태로 종료 되는 transition
  * Job 의 BatchStatus 와 ExitStatus 가 COMPLETED 으로 종료됨
  * Step 의 ExitStatus 가 FAILED 이더라도 Job 의 BatchStatus 가 COMPLETED 로 종료하도록 가능하며 이 때 Job의 재시작은 불가능함
* stopAndRestart(Step or Flow or JobExecutionDecider)
  * stop() transition 과 기본 흐름은 동일
  * 특정 step에서 작업을 중단하도록 설정하면 중단 이전의 Step 만 COMPLETED 저장되고 이후의 step 은 실행되지 않고 STOPPED 상태로 Job 종료
  * Job 이 다시 실행됐을 때 실행해야 할 step 을 restart 인자로 넘기면 이전에 COMPLETED 로 저장된 step 은 건너뛰고 중단 이후 step 부터 시작한다


```java
@Configuration
public class TransitionJob2 {

    @Bean
    public Job batchTransitionJob2(JobRepository jobRepository
            , Step transitionJob2Step1
            , Step transitionJob2Step2
            , Step transitionJob2Step3
            , Step transitionJob2Step4
            , Step transitionJob2Step5) {
        return new JobBuilder("batchTransitionJob2", jobRepository)
                .start(transitionJob2Step1)
                    .on("FAILED") //step1의 exit status가 failed일떄
                    .to(transitionJob2Step2) // step2를 실행
                    .on("FAILED") // step2가 실패할 경우
                    .stop() // job을 stop
                .from(transitionJob2Step1) // step1의 새로운 플로우 정의
                    .on("COMPLETED") // 위에 설정한 failed 이외이며 completed 일 경우
                    .to(transitionJob2Step3) // step3을 실행
                    .next(transitionJob2Step4) // step4를 실행
                .from(transitionJob2Step1) // step1의 새로운 플로우 정의
                    .on("*") // 위에 설정한 failed 와 complete 이외의 경우
                    .to(transitionJob2Step4) // step4를 실행
                .from(transitionJob2Step2) // step2의 새로운 플로우 정의
                    .on("*") // faile 이외의 경우
                    .to(transitionJob2Step5) // step5를 실행
                .end()
                .build();
    }

    @Bean
    public Step transitionJob2Step1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("transitionJob2Step1", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 1 executed");
                    contribution.setExitStatus(ExitStatus.FAILED); // exit status를 정의
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step transitionJob2Step2(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("transitionJob2Step2", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 2 executed");
                    contribution.setExitStatus(ExitStatus.COMPLETED);
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step transitionJob2Step3(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("transitionJob2Step3", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 3 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step transitionJob2Step4(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("transitionJob2Step4", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 4 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step transitionJob2Step5(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("transitionJob2Step5", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 5 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }
}
```