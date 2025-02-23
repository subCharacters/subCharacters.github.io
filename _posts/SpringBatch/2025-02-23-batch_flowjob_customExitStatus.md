---
layout: single
title: "SpringBatch의 FlowJob의 사용자 정의 ExitStatus"
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
date: 2025-02-23
last_modified_at: 2025-02-23
---

## 목표

```
스프링 배치의 FlowJob의 사용자 정의 ExitStatus 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 FlowJob의 사용자 정의 ExitStatus에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* ExitStatus에 존재하지 않는 새로운 exitCode를 정의 할 수 있다.
* StepExecutionListener 의 afterStep() 메서드에서 Custom exitCode 생성 후 새로운 ExitStatus 반환
* Step 실행 후 완료 시점에서 현재 exitCode 를 사용자 정의 exitCode 로 수정할 수 있음

### 2.배치 상태 유형

<span style="font-size:13pt">
Step의 흐름을 조절하는 6가지의 Exit Status 이외의 사용자가 따로 정의 할 수 있다.<br>
응용을 해본다면 스탭의 실행 성공 유무의 결과가 아닌, 성공 후에 분기로 스탭을 따로 둬야 할때 쓰일 수 있을 거 같다.<br>
예를 들면 카드 이용 청구서 발송에 관한 배치라고 한다.<br>
발송은 이메일, 지로 청구서가 존재하고 이메일 명세는 기본고 지로 청구서는 옵션이라고 가정한다.<br>
현재 처리하고 있는 유저의 이용 명세 발송 설정이 치로 정구서 옵션이 있다고 한다면 선행 step으로 이메일 처리를 하고 이어서 후속으로 지로 청구서가 되어있다면<br>
ExitStatus를 임의의 값으로 설정해서 분기를 둘 수 있을거 같다.
</span>

```java
// FlowJobCustomExitStatusConfiguration.java
@Configuration
public class FlowJobCustomExitStatusConfiguration {

    @Bean
    public Job batchFlowJobCustomExitStatusJob(JobRepository jobRepository
            , Step flowJobCustomExitStatusJobStep1
            , Step flowJobCustomExitStatusJobStep2) {
        return new JobBuilder("batchFlowJobCustomExitStatusJob", jobRepository)
                .start(flowJobCustomExitStatusJobStep1)
                    .on("FAILED")
                    .to(flowJobCustomExitStatusJobStep2)
                    .on("PASS")
                    .stop()
                .end()
                .build();
    }

    @Bean
    public Step flowJobCustomExitStatusJobStep1(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobCustomExitStatusJobStep1", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 1 executed");
                    contribution.setExitStatus(ExitStatus.FAILED); // exit status를 정의
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .build();
    }

    @Bean
    public Step flowJobCustomExitStatusJobStep2(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("flowJobCustomExitStatusJobStep2", jobRepository)
                .tasklet(((contribution, chunkContext) -> {
                    System.out.println("Step 2 executed");
                    return RepeatStatus.FINISHED;
                }), transactionManager)
                .listener(new CustomExitStatus()) // 리스너를 통해 새로 exit status code를 정의
                .build();
    }
}

// CustomExitStatus.java
public class CustomExitStatus implements StepExecutionListener {

    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {
        String exitCode = stepExecution.getExitStatus().getExitCode();
        if (ExitStatus.COMPLETED.getExitCode().equals(exitCode)) {
            return new ExitStatus("PASS");
        } else {
            return null;
        }
    }
}

```