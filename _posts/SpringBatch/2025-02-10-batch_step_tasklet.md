---
layout: single
title: "SpringBatch의 Step Tasklet"
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
스프링 배치의 Step의 Tasklet에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 Step의 Tasklet에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하나 Tasklet을 선언할때 Step에서 바로 익명클래스나 람다식 등으로만 선언하는 것이 Deprecated로 바뀌었다.<br>
Tasklet과 함께 PlatformTransactionManager도 같이 넘겨줘야한다.<br>
메소드에서 따로 만들면 익명클래스나 람다로 생성하는건 것도 가능하다.
</span>

### 1.기본개념

* Step내에서 구성되고 실행되는 도메인 객체로서 주로 단일 태스크를 수행하기 위한 것이다.
* TaskletStep(RepeatTemplate)에 의해 반복적으로 수행되며 반환값에 따라 계속 수행 혹은 종료한다.
  * RepeatStatus로 반복 여부가 결졍되며 null일 경우 RepeatStatus.FINISHED로 판단한다.
  * RepeatStatus.CONTINUABLE을 리턴하게 되면 반복하게 된다.
  * RepeatStatus.FINISHED가 리턴되거나 예외가 발생하기 전까지 while문 안에서 반복실행 되기 때문에 무한루프에 주의해야한다.
* 메소드나 구현클래스를 따로 만들어야한다.
* Step에는 하나에 Tasklet만 설정 가능하며 여러개가 설정되었을 경우 마지막에 설정한 객체가 실행된다.

### 2.Tasklet구현 방법

```java
@Configuration
public class TaskletStep {

    @Bean
    public Job taskletStepJob(JobRepository jobRepository, Step taskletStepStep) {
        return new JobBuilder("taskletStepJob", jobRepository)
                .start(taskletStepStep)
                .build();
    }

    @Bean
    public Step taskletStepStep(JobRepository jobRepository, PlatformTransactionManager transactionManager,
                                Tasklet taskletStepTaskletLambda) {
        return new StepBuilder("taskletStepStep", jobRepository)
                .tasklet(taskletStepTasklet(), transactionManager)
                .tasklet(taskletStepTaskletLambda, transactionManager)
                .tasklet(new CustomTasklet(), transactionManager)
                // batch4와 같이 바로 선언도 가능한다. 단, transactionManager가 필요하다.
                /*.tasklet(((contribution, chunkContext) -> {
                    contribution.setExitStatus(ExitStatus.FAILED);
                    return RepeatStatus.FINISHED;
                }), transactionManager)*/ 
                .build();
    }

    public Tasklet taskletStepTasklet() {
        return new Tasklet() {
            @Override
            public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
                System.out.println("taskletStepTasklet");
                return RepeatStatus.FINISHED;
            }
        };
    }

    // Step의 인수로 전달해서 사용할 경우 빈 등록이 필요하다.
    @Bean
    public Tasklet taskletStepTaskletLambda() {
        return ((contribution, chunkContext) -> {
            System.out.println("taskletStepTaskletLambda");
            return RepeatStatus.FINISHED;
        });
    }

    public class CustomTasklet implements Tasklet {

        @Override
        public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
            System.out.println("CustomTasklet");
            return RepeatStatus.FINISHED;
        }
    }

}
```

