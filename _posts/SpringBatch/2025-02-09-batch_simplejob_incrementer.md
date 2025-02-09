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
스프링 배치의 SimpleJob의 incrementer()에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 SimpleJob의 incrementer()에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* JobParameters 에서 필요한 값을 증가시켜 다음에 사용될 JobParameters 오브젝트를 리턴한다.
* 기존의 JobParameter 변경없이 Job을 여러 번 시작하고자 할때 사용된다.
* RunIdIncrementer 구현체를 지원하며 인터페이스를 직접 구현할 수 있다.

### 2.incrementer()

<span style="font-size:13pt">
임의의 파라미터 값을 추가하여 job이 구동할때마다 값을 바꿔주는게 핵심이다.<br>
스프링 배치에서 제공하는 기본값은 run.id를 키값으로 하여 long타입의 값을 1씩 증가시키는 로직이다.<br>
JobParametersIncrementer 인터페이스를 구현하여 임의로 커스텀이 가능하다.
</span>

```java
@Configuration
public class IncrementerConfiguration {

    @Bean
    public Job incrementerJob(JobRepository jobRepository, Step incrementerStep1, Step incrementerStep2, Step incrementerStep3) {
        return new JobBuilder("incrementerJob", jobRepository)
                .start(incrementerStep1)
                .next(incrementerStep2)
                .next(incrementerStep3)
                .incrementer(new RunIdIncrementer()) // 스프링 배치에서 제공해주는 클래스를 이용할 경우
                .incrementer(new CustomJobParametersIncrementer()) // 직접 만들경우.
                .build();
    }

    @Bean
    public Step incrementerStep1(JobRepository jobRepository, Tasklet incrementerTestTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("incrementerStep1", jobRepository)
                .tasklet(incrementerTestTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step incrementerStep2(JobRepository jobRepository, Tasklet incrementerTestTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("incrementerStep2", jobRepository)
                .tasklet(incrementerTestTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step incrementerStep3(JobRepository jobRepository, Tasklet incrementerTestTasklet, PlatformTransactionManager transactionManager) {
        return new StepBuilder("incrementerStep3", jobRepository)
                .tasklet(incrementerTestTasklet, transactionManager)
                .build();
    }

    @Bean
    public Tasklet incrementerTestTasklet() {
        return ((contribution, chunkContext) -> {
            String stepName = chunkContext.getStepContext().getStepName();
            System.out.println(stepName + " is execute");
            return RepeatStatus.FINISHED;
        });
    }
}

/**
 * 커스텀으로 구현한 클래스
 */
public class CustomJobParametersIncrementer implements JobParametersIncrementer {

    static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Override
    public JobParameters getNext(JobParameters parameters) {

        String id = dateFormat.format(new Date());

        return new JobParametersBuilder().addString("run.id", id).toJobParameters();
    }
}

```

