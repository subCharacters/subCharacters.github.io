---
layout: single
title: "SpringBatch의 Step startLimit/allowStartIfComplete"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
  - step
toc: true
toc_sticky: true
date: 2025-02-10
last_modified_at: 2025-02-10
---

## 목표

```
스프링 배치의 Step의 startLimit() 와 allowStartIfComplete() 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 Step의 startLimit() 와 allowStartIfComplete() 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다
</span>

### 1.startLimit의 기본개념

* Step의 실행 횟수를 조정할 수 있다.
* Step 마다 설정할 수 있다
* 설정 값을 초과해서 다시 실행하려고 하면 StartLimitExceededException이 발생한다.
* 디폴트 값은 Integer.MAX_VALUE이다.
* 같은 job일때만 카운트한다. increment로 job의 파라미터를 다르게 할 경우 카운트하지 않는다.
  * 예외가 발생해서 job이 실패 할 경우 몇 번까지 재실행을 할지의 설정으로 유용할 듯 하다.

### 2.allowStartIfComplete의 기본개념

* 재시작 가능한 job에서 step의 이전 성공 여부와 관계없이 항상 step을 실행하는 설정이다.
* 실행 마다 유효성을 검증하는 step이나 사전 작업이 꼭 필요한 step등에서 설정.
* 기본적으로 COMPLETE 상태를 가진 step은 job 재시작 시 스킵된다.
* true로 설정할 경우 항상 실행한다.

### 3.예제코드  

```java
@Configuration
public class StartlimitAllowStartIfCompleteStep {

    @Bean
    public Job startlimitAllowStartIfCompleteStepJob(JobRepository jobRepository, Step allowStartIfCompleteStepStep, StepstartlimitStepStep) {
        return new JobBuilder("StartlimitAllowStartIfCompleteStepJob", jobRepository)
                .start(allowStartIfCompleteStepStep)
                .start(startlimitStepStep)
                .build();
    }

    @Bean
    public Step allowStartIfCompleteStepStep(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("allowStartIfCompleteStepStep", jobRepository)
                .tasklet(allowStartIfCompleteStepTasklet(), transactionManager)
                .allowStartIfComplete(true) // 항상 step실행이 된다.
                .build();
    }

    @Bean
    public Step startlimitStepStep(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("startlimitStepStep", jobRepository)
                .tasklet(startlimitStepTasklet(), transactionManager)
                .startLimit(3) // 4번째 시작시 limit 예외가 발생한다.
                .build();
    }

    public Tasklet allowStartIfCompleteStepTasklet() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("allowStartIfCompleteStepTasklet");
                return RepeatStatus.FINISHED;
            }
        };
    }

    public Tasklet startlimitStepTasklet() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("startlimitStepTasklet");
                throw new RuntimeException("");
                //return RepeatStatus.FINISHED;
            }
        };
    }
}
```

