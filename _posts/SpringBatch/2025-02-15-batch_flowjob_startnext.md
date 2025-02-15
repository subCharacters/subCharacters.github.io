---
layout: single
title: "SpringBatch의 FlowJob의 start()와 next()"
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
date: 2025-02-15
last_modified_at: 2025-02-15
---

## 목표

```
스프링 배치의 FlowJob의 start()와 next()에 대해   안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 FlowJob의 start()와 next()에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* Step인자가 아닌 Flow인자를 주어서 Flow가 실행되도록 한다.
* Step과 사용법이 같으나 Flow가 들어온 다는 것이 다르다.

### 2.start(), next()

<span style="font-size:13pt">
Flow는 스탭의 흐름을 담고 있는 객체이다.<br>
따라서 Flow가 오면 하나의 흐름의 시작, 다음 흐름을 정의한다 생각하면 이해하기 편할듯.<br>
</span>

```java
@Configuration
public class FlowJobStartNextConfiguration {

    @Bean
    public Job batchFlowJobStartNextJob(JobRepository jobRepository, Flow flowJobStartNextFlow1
            , Flow flowJobStartNextFlow2 ,Step flowJobStartNextStep3, Step flowJobStartNextStep6) {
        return new JobBuilder("batchFlowJobStartNextJob", jobRepository)
                .start(flowJobStartNextFlow1)
                .next(flowJobStartNextStep3) // Step or Flow or JobExecutionDecider가 들어올 수 있다.
                .next(flowJobStartNextFlow2)
                .next(flowJobStartNextStep6)
                .end()
                .build();
    }

    @Bean
    public Flow flowJobStartNextFlow1(Step flowJobStartNextStep1, Step flowJobStartNextStep2) {
        return new FlowBuilder<Flow>("flowJobStartNextFlow1")
                .start(flowJobStartNextStep1)
                .next(flowJobStartNextStep2)
                .end();
    }

    @Bean
    public Flow flowJobStartNextFlow2(Step flowJobStartNextStep4, Step flowJobStartNextStep5) {
        return new FlowBuilder<Flow>("flowJobStartNextFlow1")
                .start(flowJobStartNextStep4)
                .next(flowJobStartNextStep5)
                .end();
    }

    @Bean
    public Step flowJobStartNextStep1(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep1", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobStartNextStep2(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep2", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobStartNextStep3(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep3", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobStartNextStep4(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep4", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobStartNextStep5(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep5", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    @Bean
    public Step flowJobStartNextStep6(JobRepository jobRepository, PlatformTransactionManager platformTransactionManager) {
        return new StepBuilder("flowJobStartNextStep6", jobRepository)
                .tasklet(flowJobStartNextTasklet1(), platformTransactionManager)
                .build();
    }

    public Tasklet flowJobStartNextTasklet1() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                String stepName = contribution.getStepExecution().getStepName();
                System.out.println(stepName + " has executed");
                // throw new RuntimeException("fail");
                return RepeatStatus.FINISHED;
            }
        };
    }
}
```

