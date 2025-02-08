---
layout: single
title: "SpringBatch의 SimpleJob validator()"
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
date: 2025-02-08
last_modified_at: 2025-02-08
---

## 목표

```
스프링 배치의 SimpleJob의 validator()에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 SimpleJob의 validator()에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* job 실행에 필요한 파라미터를 검증한다.
* DefaultJobParametersValidator 구현체를 지원하며 좀 더 복잡한 제약 조건이 있다면 직접 구현이 가능하다.

### 2.validator()

<span style="font-size:13pt">
job이 실행될때 넘어오는 파라미터를 검증한다.<br>
스프링 배치에서 지원하는 DefaultJobParametersValidator 구현체의 경우 파라미터로 requiredKeys와 optionalKeys두가지를 String[]형태로 받는다.<br>
optionalKeys는 값이 없어도 에러가 나지 않으나 requiredKeys는 필수로 값이 있어야한다.<br>
JobParametersValidator 인터페이스를 구현하여 커스텀 클래스를 작성할 수 있다.<br>
</span>

```java
@Configuration
public class ValidatorConfiguration {

    @Bean
    public Job validatorJob(JobRepository jobRepository, Step step1, Step step2, Step step3) {
        return new JobBuilder("validatorJob", jobRepository)
                .start(step1)
                .next(step2)
                .next(step3)
                .validator(new CustomJobParametersValidator()) // 커스텀 할 경우.
                .validator(new DefaultJobParametersValidator(new String[]{"name", "date"}, new String[]{"count"})) // 스프링 배치에서 제공하는 클래스에 구현 할 경우.
                .build();
    }

    @Bean
    public Step validatorStep1(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("validatorStep1", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step validatorStep2(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("validatorStep2", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Step validatorStep3(JobRepository jobRepository, Tasklet testTasklet,PlatformTransactionManager transactionManager) {
        return new StepBuilder("validatorStep3", jobRepository)
                .tasklet(testTasklet, transactionManager)
                .build();
    }

    @Bean
    public Tasklet validatorTestTasklet() {
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
public class CustomJobParametersValidator implements JobParametersValidator {

    @Override
    public void validate(JobParameters jobParameters) throws JobParametersInvalidException {
        if (jobParameters.getString("name") == null) {
            throw new JobParametersInvalidException("name is required");
        }
    }
}
```

