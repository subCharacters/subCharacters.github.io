---
layout: single
title: "SpringBoot3 Security 구조"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringSecurity
tags:
  - SpringBoot
  - SpringSecurity
toc: true
toc_sticky: true
date: 2024-01-12
last_modified_at: 2024-01-17
---

### 개요

---

<span style="font-size:13pt">
SpringSecurity구조에 대해서 알아보자.
<br>
</span>

### 본문

<span style="font-size:13pt">
Spring Security는 인증, 인가, 권한을 쉽게 제어하기 위해 나온 <span style="background-color:rgba(255, 245, 177, 0.3);">하위 프레임워크</span>이다.
<br>
Spring Security는 ServletFilter를 기반으로 하며 서블릿 어플리케이션에서 실행 되므로 꼭 Spring FW가 아니더라도 서블릿 기반 어플리케이션이라면 사용이 가능하다.
<br>
<span style="background-color:rgba(255, 245, 177, 0.3);">Filter를 기반</span>으로 돌아가므로 Servlet의 FilterChain안에 SecurityFilterChain가 등록되어 있다. 이것은 Spring Security에서 제공하는 특수필터이다.
<br>
SecurityFilterChain은 DelegatingFilterProxy에 FilterChainProxy를 통해 호출된다.
<br>
다음은 아키텍처들이다.
</span>

#### Security Filter Chain
<div style="background-color: white">
    <img src="..\..\images\springsecurity\securityfilterchain.png">
</div>

<br>

#### Security Filter Chain List

![image](..\..\images\springsecurity\securityfilterchainlist.png)

#### Security Filter Architecture Container Base

![image](..\..\images\springsecurity\securitycontainer.png)
*<cite style="font-size:9pt">DelegatingFilterProxy의 존재가 SpringBean 등록을 가능하게 해줘 SpringContainer에 걸쳐 Filter 사용이 가능하다.</cite>*
{: .small}

#### Spring Security Authentication Architecture
<div style="background-color: white">
    <img src="..\..\images\springsecurity\securityauthentication.png">
</div>

---