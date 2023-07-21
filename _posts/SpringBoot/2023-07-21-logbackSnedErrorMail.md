---
layout: single
title: "Spring boot에서 logback 이용하여 에러 로그 발생 시 메일 통지"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - Logback
toc: true
toc_sticky: true
date: 2023-07-21
last_modified_at: 2023-07-21
---

### 개요

---

<span style="font-size:13pt">
에러 발생시 logback에서 자동으로 메일로 송신해주는 방법이다.<br/>
SpringBoot는 2.x버전<br/>
</span>

### 스크립트

---

<span style="font-size:13pt">
라이브러리 추가<br/>
</span>

```groovy
implementation("javax.activation:activation:1.1.1")
implementation("javax.mail:mail:1.4")
```

<span style="font-size:13pt">
logback-spring.xml 에 메일 통지 코드 작성<br/>
</span>

```xml
<appender name="EMAIL" class="ch.qos.logback.classic.net.SMTPAppender">
        <smtpHost>메일 호스트(email-smtp .... amazonaws.com등)</smtpHost>
        <smtpPort>smtp의 호스트</smtpPort>
        <!-- 아마존의 경우 로그인 인증이 필요 -->
        <username></username>
        <password></password>
        <to>exemple@exemple.com </to>
        <from>error_log@exemple.com</from>
        <subject>TESTING: %logger{20} - %m</subject>
        <STARTTLS>false</STARTTLS>
        <!-- HTML레이아웃 대신 포맷을 이용하는 방법도 있음 -->
        <layout class="ch.qos.logback.classic.html.HTMLLayout"/>

         <!--for testing , comment in production, default 256-->
        <cyclicBufferTracker class="ch.qos.logback.core.spi.CyclicBufferTracker">
             <!--Send just one log entry per email, ready for a lot of emails if you put one.-->
            <bufferSize>5</bufferSize>
        </cyclicBufferTracker>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>ERROR</level>
        </filter>
         <!--for testing , comment in production, default asynchronousSending = true-->
        <asynchronousSending>true</asynchronousSending>
    </appender>


 <!-- springProfile은 없어도 됨 -->
 <springProfile name="log_file_info">
    <root level="INFO">
        <appender-ref ref="EMAIL"/>
    </root>
 </springProfile>
```

<span style="font-size:13pt">
이렇게 하면 메일은 통지 되지만 에러로그가 발생할때마다 통지가 가버린다.<br/>
재수없게 분당 10만개 에러 나거나 그러면 요금 폭탄 혹은 메일 서버가 죽어버릴 것...<br/>
텀을 둬서 보내게끔 하거나 연속해서 몇번의 에러로그가 발생하면 에러 통지가 가게끔 필터등을 작성할 필요가 있을듯.
</span>
