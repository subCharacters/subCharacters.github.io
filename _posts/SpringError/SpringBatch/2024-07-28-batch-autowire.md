---
layout: single
title: "SpringBatch Could not autowire에러"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringError
tags:
  - SpringBoot
  - SpringBatch
  - IntelliJ
toc: true
toc_sticky: true
date: 2024-07-28
last_modified_at: 2024-07-28
---

## 목표

```
 Could not autowire. No beans of 'JobBuilderFactory' type found. 
 Could not autowire. No beans of 'StepBuilderFactory' type found.
 에러를 해결
```

## 개발환경

| 이름 | 버전 | 비고 |
|---|---|---|
| SpringBoot | 2.7.6 | - |
| SpringBatch | 4.3.7 | - |
| Java | 11 | - |
| MySql | 8.0 | - |
| IntelliJ | 2024.1.4 (Ultimate Edition) | - |

## 개요

job 관련 Configuration 자바 파일 작성시 의존할 bean을 못찾는 에러 문구가 발생했다.

## 코드

```java
@Slf4j
@RequiredArgsConstructor
@Configuration
@EnableBatchProcessing
public class TaskletJob {

    @Autowired
    private final JobBuilderFactory jobBuilderFactory; // 에러 발생
    @Autowired
    private final StepBuilderFactory stepBuilderFactory;  // 에러 발생
}
```

## 원인
해당 빈을 찾지 못해 생기는 IntelliJ의 버그이다.  
표시만 에러로 나올 뿐 실제 동작은 한다.

## 해결
인텔리제이 설정에서 해당 에러를 표시 안되도록 하거나 그냥 무시한다.  

### 1.ComponentScan어노테이션추가

```java
@Slf4j
@RequiredArgsConstructor
@Configuration
@EnableBatchProcessing
@ComponentScan(basePackages={"org.springframework.batch.core.configuration.annotation"}) // 추가한 어노테이션
public class TaskletJob {

    @Autowired
    private final JobBuilderFactory jobBuilderFactory;
    @Autowired
    private final StepBuilderFactory stepBuilderFactory;
}
```

### 2.idea.properties수정

Help -> Edit Custom Properties

```properties
idea.spring.boot.filter.autoconfig=false
```

### 3.Inspections설정 변경

File -> Settings -> Inspections  
Spring Core -> Code -> Incorrect autowiring in Spring bean components의 설정을 변경  

![Image](../../../images/springerror/springbatch/springerror_batch_00.PNG)


## 참고자료

-