---
layout: single
title: "SpringBatch의 FlowJob의 기본개념과 API"
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
date: 2025-02-12
last_modified_at: 2025-02-12
---

## 목표

```
스프링 배치의 FlowJob의 기본개념과 API에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 FlowJob의 기본개념과 API에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* Step 을 순차적으로만 구성하는 것이 아닌 특정한 상태에 따라 흐름을 전환하도록 구성할 수 있으며 FlowJobBuilder 에 의해 생성된다
  * Step 이 실패 하더라도 Job 은 실패로 끝나지 않도록 해야 하는 경우
  * Step 이 성공 했을 때 다음에 실행해야 할 Step 을 구분해서 실행 해야 하는경우
  * 특정 Step은 전혀 실행되지 않게 구성 해야 하는 경우
* Flow 와 Job 의 흐름을 구성하는데만 관여하고 실제 비즈니스 로직은 Step 에서 이루어진다
* 내부적으로 SimpleFlow 객체를 포함하고 있으며 Job 실행 시 호출한다

### 2.API소개

<span style="font-size:13pt">
기본적으로 start, next, from과 같은 api는 job과 동일하다.<br>
여기서 눈여겨 봐야할 것은 on인데 바로 transition이다.<br>
이전의 종료상태(exit status)의 값으로 그 다음 실행 할 step과 flow를 정할 수 있다.<br>
</span>

```java
@Bean
public Job batchJob(JobRepository jobRepository) {
    return new JobBuilder("batchJob", jobRepository)
        .start(step1(jobRepository, null)) // Flow 시작하는 Step 설정
        .on("COMPLETED").to(step2(jobRepository, null)) // Step1 실행 결과를 기준으로 다음 Step 결정
        .on("FAILED").to(stepFail(jobRepository, null)) // Step1 실패 시 StepFail 실행
        .stop() // Flow 중지
        .fail() // Flow 실패 처리
        .end() // Flow 종료
        .stopAndRestart(step3(jobRepository, null)) // 실패 후 특정 Step 재시작
        .from(step2(jobRepository, null)) // 이전 Step에서 추가 Flow 정의
        .next(step3(jobRepository, null)) // 다음 Step으로 이동
        .end()
        .build();
}
```

### 3.예제코드

```java
@Configuration
public class FlowJobApi {

    @Bean
    public Job batchFlowJobApi(JobRepository jobRepository, Step flowJobApiStep1, Step flowJobApiStep2 ,Step flowJobApiStep3) {
        return new JobBuilder("flowJobApi", jobRepository)
                .start(flowJobApiStep1)
                    .on("COMPLETED").to(flowJobApiStep3) // flowJobApiStep1이 성공하면 flowJobApiStep3로 가고
                .from(flowJobApiStep1).on("FAILED").to(flowJobApiStep2) // flowJobApiStep1이 실패하면 flowJobApiStep2로 가라
                .end()
                .build();
    }

    @Bean
    public Step flowJobApiStep1(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobApiStep1", jobRepository)
                .tasklet(flowJobTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobApiStep2(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobApiStep2", jobRepository)
                .tasklet(flowJobTasklet2(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobApiStep3(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobApiStep3", jobRepository)
                .tasklet(flowJobTasklet3(), platformTransactionManager)
                .build();
    }

    public Tasklet flowJobTasklet1() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("flowJobTasklet1 has executed");
                // throw new RuntimeException("fail");
                return RepeatStatus.FINISHED;
            }
        };
    }

    public Tasklet flowJobTasklet2() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("flowJobTasklet2 has executed");
                return RepeatStatus.FINISHED;
            }
        };
    }

    public Tasklet flowJobTasklet3() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("flowJobTasklet3 has executed");
                return RepeatStatus.FINISHED;
            }
        };
    }
}
```
