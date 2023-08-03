---
layout: single
title: "SpringBoot로그 설정"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - LogBack
toc: true
toc_sticky: true
date: 2023-08-02
last_modified_at: 2023-08-02
---

### 개요

---

<span style="font-size:13pt">
Logback을 이용하여 Log를 출력하는 방법이다.<br>
</span>

### 코드

<span style="font-size:13pt">
logback-spring.xml에 아래와 같이 설정.<br>
CONSOLE_LOG_PATTERN이나 CONSOLE_LOG_PATTERN은 include한 defaults.xml등에서 일정 패턴을 제공해주고 있으므로 그대로 사용해도 무방하다.<br>
LOG_FILE의 경우는 yml에 logging.file.name으로 디렉토리와 같이 지정을 해줘야한다. <br>
혹은 -Dlogging.file.name=logs/spring.log 와 같이 vm옵션으로 지정하여 설정을 해야한다. <br> 
</span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    <include resource="org/springframework/boot/logging/logback/console-appender.xml"/>
    <springProfile name="log_console">
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
            <layout class="ch.qos.logback.classic.PatternLayout">
                <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            </layout>
        </appender>
    </springProfile>
    <springProfile name="log_file_debug, log_file_info">
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
            <layout class="ch.qos.logback.classic.PatternLayout">
                <Pattern>${CONSOLE_LOG_PATTERN}</Pattern>
            </layout>
        </appender>
        <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <encoder>
                <pattern>${FILE_LOG_PATTERN}</pattern>
            </encoder>
            <file>${LOG_FILE}</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>${LOG_FILE}.%d{yyyyMMdd}</fileNamePattern>
                <maxHistory>7</maxHistory>
            </rollingPolicy>
        </appender>
    </springProfile>

    <springProfile name="log_console">
        <root level="DEBUG">
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>
    <springProfile name="log_file_debug">
        <root level="DEBUG">
            <appender-ref ref="FILE"/>
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>
    <springProfile name="log_file_info">
        <root level="INFO">
            <appender-ref ref="FILE"/>
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>
</configuration>

```

<span style="font-size:13pt">
yml파일에 xml에서 정의한 springProfile을 설정하면 해당하는 로그 레벨과 appender 설정에 맞춰서 로그가 설정된다.<br>
이를 이용해서 테스트와 프로덕션 서버의 로그 레벨과 출력 설정, 콘솔에 출력 유무등 다양하게 설정이 가능하다. 
</span>

```yml
spring:
  profiles:
    include: log_file_debug

logging:
  level:
    root: DEBUG
```
