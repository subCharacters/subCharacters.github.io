---
layout: single
title: "SpringBatch의 FlowJob의 JobExecutionDecider"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
  - JobExecutionDecider
toc: true
toc_sticky: true
date: 2025-03-03
last_modified_at: 2025-03-03
---

## 목표

```
스프링 배치의 FlowJob의 JobExecutionDecider 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 FlowJob의 JobExecutionDecider에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* ExitStatus 를 조작하거나 StepExecutionListener 를 등록할 필요 없이 Transition 처리를 위한 전용 클래스
* Step 과 Transiton 역할을 명확히 분리해서 설정 할 수 있음
* Step 의 ExitStatus 가 아닌 JobExecutionDecider 의 FlowExecutionStatus 상태값을 새롭게 설정해서 반환함

### 2.예제 코드

<span style="font-size:13pt">
flow job의 실행 결과에 따라서 다른 job이나 step을 실행시킬때에 사용한다.<br>
이전에 작성한 사용자 정의 transition로 정의하여 하나씩 정의하여 사용하는 것보다 가독성이 좋은 듯하다.<br>
하나의 decider를 정의하여 그 안에서 분기를 제어할 수 있다.<br>
또한 FlowExecutionStatus는 spring batch의 DB도메인에 남지 않는다.<br>
</span>

```java
@Configuration
public class FlowJobDeciderJob {

    @Bean
    public Job batchFlowJobDeciderJob(JobRepository jobRepository
            , Step flowJobDeciderStep1
            , Step flowJobDeciderStep2
            , Step flowJobDeciderStep3
            , Step flowJobDeciderStep4
            , Step flowJobDeciderStep5
            , Flow flowJobDeciderFlow) {
        return new JobBuilder("batchFlowJobDeciderJob", jobRepository)
                .incrementer(new RunIdIncrementer())
                // flowJobDeciderFlow에 주석 설명 추가.
                // .start(flowJobDeciderFlow) // 별도의 Flow를 만들어서 흐름을 분리해도 ok
                .start(flowJobDeciderStep1)
                .next(flowJobDeciderDecider())
                    /*.from(flowJobDeciderDecider())*/.on("PASS").to(flowJobDeciderStep2)
                    /*.from(flowJobDeciderDecider())*/.on("FAIL").to(flowJobDeciderStep3)
                .from(flowJobDeciderStep2) // 다음 flow로 가려면 step 다음에 와야하기때문에 다시 step을 불러준다.
                    .on("*").to(flowJobDeciderDecider2())
                .from(flowJobDeciderStep3)
                    .on("*").to(flowJobDeciderDecider2())
                .from(flowJobDeciderDecider2())
                    .on("PASS").to(flowJobDeciderStep4)
                    .on("FAIL").to(flowJobDeciderStep5)
                .end()
                .build();
    }

    /*
    * simple flow로 제어
    */
    @Bean
    public Flow flowJobDeciderFlow(JobRepository jobRepository
            , Step flowJobDeciderStep1
            , Step flowJobDeciderStep2
            , Step flowJobDeciderStep3
            , Step flowJobDeciderStep4
            , Step flowJobDeciderStep5) {
        return new FlowBuilder<Flow>("flowJobDeciderFlow")
                .start(flowJobDeciderStep1)
                .next(flowJobDeciderDecider()) // Decider로 분기를 설정.
                    .on("PASS").to(flowJobDeciderStep2) // Decider 결과가 PASS일 경우
                    .on("FAIL").to(flowJobDeciderStep3) // Decider 결과가 FAIL 경우
                .from(flowJobDeciderStep2) // step2부터의 경로 정의
                    .on("*").to(flowJobDeciderDecider2()) // 모든 경우 Decider2로 이동
                .from(flowJobDeciderStep3) // step3부터의 경로 정의
                    .on("*").to(flowJobDeciderDecider2()) // 모든 경우 Decider2로 이동
                .next(flowJobDeciderDecider2()) // Decider2로 분기를 설정.
                    .on("PASS").to(flowJobDeciderStep4) // Decider2 결과가 PASS일 경우
                    .on("FAIL").to(flowJobDeciderStep5) // Decider2 결과가 FAIL일 경우
                .end();
    }

    @Bean
    public JobExecutionDecider flowJobDeciderDecider() {
        return (jobExecution, stepExecution) -> {
            // 직전 step의 실행 결과를 취득.
            String decisionValue = jobExecution.getExecutionContext().getString("decisionValue");

            if ("NEXT".equals(decisionValue)) {
                return new FlowExecutionStatus("PASS");
            } else {
                return new FlowExecutionStatus("FAIL");
            }
        };
    }

    @Bean
    public JobExecutionDecider flowJobDeciderDecider2() {
        return new CustomDecider();
    }

    @Bean
    public Step flowJobDeciderStep1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobDeciderStep1", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 1 executed");
                    // step의 실행 결과를 context에 저장.
                    contribution.getStepExecution()
                            .getJobExecution().getExecutionContext().put("decisionValue", "NEXT");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step flowJobDeciderStep2(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobDeciderStep2", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 2 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step flowJobDeciderStep3(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobDeciderStep3", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 3 executed");
                    // step의 실행 결과를 context에 저장.
                    contribution.getStepExecution()
                            .getJobExecution().getExecutionContext().put("decisionValue", "NEXT");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step flowJobDeciderStep4(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobDeciderStep4", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 4 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step flowJobDeciderStep5(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobDeciderStep5", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 5 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    // 커스텀 클래스로 할 경우.
    private class CustomDecider implements JobExecutionDecider {
        @Override
        public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {
            String decisionValue = jobExecution.getExecutionContext().getString("decisionValue");

            if ("NEXT".equals(decisionValue)) {
                return new FlowExecutionStatus("PASS");
            } else {
                return new FlowExecutionStatus("FAIL");
            }
        }
    }
}
```