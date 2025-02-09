---
layout: single
title: "SpringBatch의 SimpleJob preventRestart()"
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
date: 2025-02-09
last_modified_at: 2025-02-09
---

## 목표

```
스프링 배치의 SimpleJob의 preventRestart()에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 SimpleJob의 preventRestart()에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* job의 재시작 가능 여부를 설정한다.
* 기본값은 true이며 preventRestart() 설정시 false가 된다.
* job이 실패하면 재시작이 되어야 하지만 이것을 설정하면 JobRestartException이 발생한다.
* 처음 job구동하는 것과는 관련이 없다.

### 2.preventRestart()

<span style="font-size:13pt">
기본적으로 true로 되어 있어 재시작이 가능하게 되어있다.<br>
AbstractJob 클래스와 CommonJobProperties클래스에 가서 보면 restartable이 true로 되어있는걸 알 수있다.<br>
preventRestart()를 선언하게 되면 내부에서 false로 설정하는 간단한 기능이다.<br>
</span>

```java
@Configuration
public class PreventRestartConfiguration {

    @Bean
    public Job preventRestartJob(JobRepository jobRepository, Step step1, Step step2, Step step3) {
        return new JobBuilder("preventRestartJob", jobRepository)
                .start(step1)
                .next(step2)
                .next(step3)
                .preventRestart() // job이 실패하면 재시작이 되어야 하지만 재시작을 못한다.
                .build();
    }

    @Bean
    public Step preventRestartStep1(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("preventRestartStep1", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step preventRestartStep2(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("preventRestartStep2", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step preventRestartStep3(JobRepository jobRepository, Tasklet failTasklet, PlatformTransactionManager transactionManager) {
        return new StepBuilder("preventRestartStep3", jobRepository)
                .tasklet(failTasklet, transactionManager)
                .build();
    }

    @Bean
    public Tasklet preventRestartTestTasklet() {
        return ((contribution, chunkContext) -> {
            String stepName = chunkContext.getStepContext().getStepName();
            System.out.println(stepName + " is execute");
            return RepeatStatus.FINISHED;
        });
    }

    @Bean
    public Tasklet failTasklet() {
        return ((contribution, chunkContext) -> {
            throw new RuntimeException("step3 was failed");
        });
    }
}
```

