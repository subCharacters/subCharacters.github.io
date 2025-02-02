---
layout: single
title: "SpringBatch의 SimpleJob 기본개념과 API"
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
date: 2025-02-02
last_modified_at: 2025-02-02
---

## 목표

```
스프링 배치의 SimpleJob의 기본개념과 API에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 SimpleJob의 기본개념과 API에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* SimpleJob은 Step을 실행시키는 Job의 구현체로 SimpleJobBuilder에 의해 생성된다.
* 여러 단계의 Step으로 구성할 수 있으며 Step을 순차적으로 실행시킨다.
* 모든 스텝의 실행이 성공적으로 완료 되어야 Job이 성공적으로 완료된다.
* 맨 마지막에 실행한 Step의 Batch Status가 Job의 최종 BatchStatus가 된다.

### 2.API

<span style="font-size:13pt">
Simple Job에서 자주 사용하는 API들이다.<br>
</span>

```java
    @Bean
    public Job batchSimpleJob(JobRepository jobRepository, Step step1, Step step2) {
        return new JobBuilder("batchSimpleJob", jobRepository) // Job을 생성
                .start(step1) // 처음 실행 할 Step 설정, 최초 한번 설정, 이 메서드를 실행하면 SimpleJobBuilder 반환
                .next(step2) //  다음에 실행 할 Step 설정, 횟수는 제한이 없으며 모든 next() 의 Step 이 종료가 되면 Job 이 종료된다
                .incrementer(new RunIdIncrementer()) // JobParameter 의 값을 자동을 증가해 주는 JobParametersIncrementer 설정
                .validator(new JobParametersValidator() { // JobParameter 를 실행하기 전에 올바른 구성이 되었는지 검증하는 JobParametersValidator 설정
                    @Override
                    public void validate(JobParameters parameters) throws JobParametersInvalidException {

                    }
                })
                .preventRestart() // job 재시작 가능 여부를 설정한다. 기본은 true이며 설정하면 false로 된다.
                .listener(new JobExecutionListener() { // Job 라이프 사이클의 특정 시점에 콜백 제공받도록 JobExecutionListener 설정
                    @Override
                    public void beforeJob(JobExecution jobExecution) {
                        JobExecutionListener.super.beforeJob(jobExecution);
                    }

                    @Override
                    public void afterJob(JobExecution jobExecution) {
                        JobExecutionListener.super.afterJob(jobExecution);
                    }
                })
                .build(); // SimpleJob 생성
    }

```
