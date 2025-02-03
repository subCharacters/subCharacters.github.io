---
layout: single
title: "SpringBatch의 SimpleJob start()와 next()"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
  - simplejob
toc: true
toc_sticky: true
date: 2025-02-03
last_modified_at: 2025-02-03
---

## 목표

```
스프링 배치의 SimpleJob의 start()와 next()에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 SimpleJob의 start()와 next()에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* start()는 처음 실행 할 Step을 설정한다.
* 최초 한번 설정하고 스프링 배치5부터는 Factory를 사용안하고 new JobBuilder로 생성한다.
* next() 는 다음에 실행 할 Step들을 순차적으로 연결하도록 설정 가능하다.
* 여러개 설정 가능하며 모든 next()가 종료되어야 job이 종료된다.

### 2.start(), next()

<span style="font-size:13pt">
딱히 설명할 건 없는 듯 하다.<br>
</span>

```java
@Configuration
public class StartNextConfiguration {

    @Bean
    public Job startNextJob(JobRepository jobRepository, Step startNextstep1, Step step2, Step sampleStep) {
        return new JobBuilder("startNextJob", jobRepository) // Job을 생성 batch4에서의 get이 사라졌다. 정확히는 factory가 사라짐
                .start(startNextstep1) // 처음 실행 할 Step 설정, 최초 한번 설정, 이 메서드를 실행하면 SimpleJobBuilder 반환
                .next(step2) //  다음에 실행 할 Step 설정, 횟수는 제한이 없으며 모든 next() 의 Step 이 종료가 되면 Job 이 종료된다
                .next(sampleStep)
                .build(); // SimpleJob 생성
    }

    @Bean
    public Step startNextstep1(JobRepository jobRepository, Tasklet testTasklet, PlatformTransactionManager transactionManager) {
        return new StepBuilder("startNextstep1", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step startNextstep(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("startNextstep2", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Tasklet startNextTestTasklet() {
        return ((contribution, chunkContext) -> {
            String stepName = chunkContext.getStepContext().getStepName();
            System.out.println(stepName + " is execute");
            return RepeatStatus.FINISHED;
        });
    }
}
```

### 3.번외 step 다른 클래스로 빼기

```java
@Component
public class CustomStepConfiguration {

    private final JobRepository jobRepository;
    public CustomStepConfiguration(JobRepository jobRepository) {
        this.jobRepository = jobRepository;
    }

    @Bean
    public Step sampleStep(Tasklet startNextTestTasklet, PlatformTransactionManager transactionManager) {
        return new StepBuilder("sampleStep", jobRepository)
                .tasklet(startNextTestTasklet, transactionManager)
                .build();
    }
}
```

### 4.번외 tasklet 다른 클래스로 빼기

```java
@Slf4j
@Component
public class FirstTasklet implements Tasklet {
    private String msg;

    @Override
    public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        log.info(msg);
        System.out.println(msg);
        return RepeatStatus.FINISHED;
    }

    public void setMsg(String paramMsg) {
        this.msg = paramMsg;
    }
}

```