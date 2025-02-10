---
layout: single
title: "SpringBatch의 Step JobStep"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - SpringBatch
tags:
  - SpringBoot
  - SpringBatch
  - step
  - JobStep
toc: true
toc_sticky: true
date: 2025-02-11
last_modified_at: 2025-02-11
---

## 목표

```
스프링 배치의 Step중의 한 종류인 JobStep 대해 안다.
```

## 개요

<span style="font-size:13pt">
SpringBatch의 JobStep에 대해 간단히 정리한다.<br>
개념적으론 batch4버전과 5버전 동일하다.<br>
</span>

### 1.기본개념

* Job에 속하는 Step중 외부의 Job을 포함(실행)하는 Step
* 외부의 Job이 실패하면 Step이 실패하고 최종적으로 원래 Job도 실패한다.
  * Job이라도 step안에 포함되어 있으니 외부 job이 실패할 경우 당연히 step이 실패하고 원래 job도 당연히 실패하는 것이다.
* 모든 메타데이터는 따로 원래 Job, 외부 Job 각각 저장된다.
* 커다란 시스템을 작은 모듈로 쪼개고 Job의 흐름을 관리하고자 할 때 사용 할 수 있다.

### 2.API소개

<span style="font-size:13pt">
job, launcher, parametersExtractor 이 3가지가 주요 api이다.<br>
리스너를 설정하여 파라미터에 값을 넣어 하위 job에 전달되게 하는 로직이다.<br>
</span>

```java
    @Bean
    public Step jobStep1(JobRepository jobRepository, Job childJob, JobLauncher jobLauncher) {
        return new StepBuilder("jobStep1", jobRepository)
                .job(childJob) // JobStep 내 에서 실행 될 Job 설정, JobStepBuilder 반환
                .launcher(jobLauncher) // Job 을 실행할 JobLauncher설정
                .parametersExtractor(parametersExtractor()) // Step의 ExecutionContext를 Job이 실행되는 데 필요한 JobParameters로 변환
                .listener(new StepExecutionListener() {
                    @Override
                    public void beforeStep(StepExecution stepExecution) {
                        StepExecutionListener.super.beforeStep(stepExecution);
                        stepExecution.getExecutionContext().put("user", "users1");
                    }

                    @Override
                    public ExitStatus afterStep(StepExecution stepExecution) {
                        return StepExecutionListener.super.afterStep(stepExecution);
                    }
                })
                .build();
    }
```

### 3.예제코드

```java
@Configuration
public class JobStep {

    @Bean
    public Job batchJopStep(JobRepository jobRepository, Step jobStep1, Step parentStep) {
        return new JobBuilder("batchJopStep", jobRepository)
                // 하위 job이 성공하고 재시작 하더라도 JobInstance already exists 예외는 발생하지 않는다.
                // 실행을 건너뛰고 바로 다음 step으로 간다.
                // allowStartIfComplete()설정을 하위 잡에 주면 재실행을 한다.
                // incrementer는 상위잡과 하위잡 모두 공유한다.
                // incrementer를 안한 상태에서 중간에 allowStartIfComplete를 추가하여 재실행하면 JobInstance already exists 예외가 발생한다.
                .start(jobStep1)
                .next(parentStep)
                .incrementer(new RunIdIncrementer())
                .build();
    }

    @Bean
    public Step jobStep1(JobRepository jobRepository, Job childJob, JobLauncher jobLauncher) {
        return new StepBuilder("jobStep1", jobRepository)
                .job(childJob) // JobStep 내 에서 실행 될 Job 설정, JobStepBuilder 반환
                .launcher(jobLauncher) // Job 을 실행할 JobLauncher설정
                .parametersExtractor(parametersExtractor()) // Step의 ExecutionContext를 Job이 실행되는 데 필요한 JobParameters로 변환
                .allowStartIfComplete(true)
                .listener(new StepExecutionListener() {
                    @Override
                    public void beforeStep(StepExecution stepExecution) {
                        StepExecutionListener.super.beforeStep(stepExecution);
                        stepExecution.getExecutionContext().put("user", "users1");
                    }

                    @Override
                    public ExitStatus afterStep(StepExecution stepExecution) {
                        return StepExecutionListener.super.afterStep(stepExecution);
                    }
                })
                .build();
    }

    // DefaultJobParametersExtractor는 스프링 배치에서 제공하는 기본 클래스
    // execute context안에 정의된 키 정보를 가지고 온다.
    private DefaultJobParametersExtractor parametersExtractor() {
        DefaultJobParametersExtractor defaultJobParametersExtractor = new DefaultJobParametersExtractor();
        defaultJobParametersExtractor.setKeys(new String[]{"user"});
        return defaultJobParametersExtractor;
    }

    @Bean
    public Job childJob(JobRepository jobRepository, Step childStep) {
        return new JobBuilder("childJob", jobRepository)
                .start(childStep)
                .incrementer(new RunIdIncrementer())
                .build();
    }

    @Bean
    public Step childStep(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("childStep", jobRepository)
                .tasklet(tasklet1(), transactionManager)
                .build();
    }

    @Bean
    public Step parentStep(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new StepBuilder("parentStep", jobRepository)
                .tasklet(tasklet2(), transactionManager)
                .build();
    }


    public Tasklet tasklet1() {
        return ((contribution, chunkContext) -> {
            String stepName = chunkContext.getStepContext().getStepExecution().getStepName();
            System.out.println(stepName + " tasklet");
            return RepeatStatus.FINISHED;
        });
    }

    public Tasklet tasklet2() {
        return ((contribution, chunkContext) -> {
            String stepName = chunkContext.getStepContext().getStepExecution().getStepName();
            throw new RuntimeException("failed");
            // return RepeatStatus.FINISHED;
        });
    }
}
```
