---
layout: single
title: "SpringBatch의 Step 기본개념과 API"
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
스프링 배치의 Step의 기본개념과 API에 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 Step의 기본개념과 API에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.
</span>

### 1.기본개념

* 스프링 배치에서 제고하는 Step의 구현체로서 Tasklet을 실행 시키는 객체이다.
* RepeatTemplate를 사용해서 Tasklet의 구문을 트랜잭션 경계 내에서 반복해서 실햄한다.
* Task기반과 Chunk기반으로 나누어서 Tasklet을 실행한다.

### 2.Task기반 vs Chunk기반

* 스프링 배치에서 Step의 실행 단위는 크게 2가지가 있다.
  * chunk기반
    * 하나의 큰 덩어리를 chunk size만큼 나눠서 실행한다는 의미로 대량 데이터 처리에 효과적이다.
    * ItemReader, ItemProcessor, ItemWriter를 사용하며 chunk기반 전용 Tasklet인 ChunkOrientedTasklet 구현체가 제공된다.
  * Task기반
    * ItemReader와 ItemWriter와 같은 chunk기반의 작업 보다 단일 작업 기반으로 처리 되는것이 효율 적인 경우에 사용한다.
    * 주로 Tasklet 구현체를 만들어 사용한다.
    * 대량 처리를 하는 경우 chunk기반에 비해 더 복잡한 구현이 필요하다.

### 3.API

```java

public Step batchStep(JobRepository jobRepository, , PlatformTransactionManager transactionManager) {
    return new StepBuilder("step1", JobRepository jobRepository)
            // Task기반
            .tasklet(tasklet(), transactionManager) // Tasklet클래스를 설정. 
            // chunk기반
            .<String, String>chunk(100) // chunk사이즈를 선언
            .reader().porocess().writer() // 청크기반의 클래스를 설정
            // api들
            .startLimit(10) // step의 실행 횟수를 설정. 설정한 만큼 실행되고 초과시 오류가난다.
            .allowStartIfComplete(true) // 성공, 실패 유무와 관계없이 항상 실행을 설정
            .listener(StepExecutionListner) // 리스너 콜백을 제공받도록 설정.
            .build();
}
```

