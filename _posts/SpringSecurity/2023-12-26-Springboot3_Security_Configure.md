---
layout: single
title: "SpringBoot3 Security2"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringSecurity
tags:
  - SpringBoot
  - SpringSecurity
  - SecurityConfiguration
toc: true
toc_sticky: true
date: 2024-01-17
last_modified_at: 2024-01-18
---

### 개요

---

<span style="font-size:13pt">
SpringSecurity를 적용할때 작성하는 Config파일에 대해서 작성한다.
<br>

</span>

### 본문

<span style="font-size:13pt">
Configuration에서 WebSecurity와 HttpSecurity를 설정하여 SecurityFilterChain에 인증 처리를 등록하거나 제외하는 처리를 한다.
<br>
CSRF나 세션 고정과 같은 취약 부분 보호나 URL접근 관리, 인증, 인가 설정도 담당한다.
<br>
</span>

#### WebSecurity
<span style="font-size:13pt">
WebSecurity는 SpringSecurity의 앞단 설정들을 한다.
<br>
Http 뿐 아니라 웹과 관련된 전반적인 설정을 위해 존재한다. 
<br>
HttpSecurity에 의해 생성된 필터들을 연결하고 제어하는 역할을 한다.
</span>

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
        httpSecurity
                .httpBasic(AbstractHttpConfigurer::disable)
                .csrf(AbstractHttpConfigurer::disable)
                .cors(AbstractHttpConfigurer::disable)
                // .requestMatchers("/api/v1/users/join", "/api/v1/users/login").permitAll()
                // .requestMatchers(HttpMethod.POST, "/api/v1/**").authenticated()
                .authorizeHttpRequests(
                        r -> r.requestMatchers("/api/v1/users/join", "/api/v1/users/login").permitAll() // 특정 url 체크 제외
                                .requestMatchers(HttpMethod.POST, "/api/v1/**").authenticated() // 그 Post형식 및  url은 인증이 되어야함
                )
                // .and()
                // .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .sessionManagement(c -> c.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
                // .and();
                // TODO
                //.addFilterBefore()
                ;
        return httpSecurity.build();
    }
}
```

#### HttpSecurity
<span style="font-size:13pt">
SpringSecurity의 각종 설정은 HttpSecurity로 대부분 하게 된다.
<br>
인증이나 인가, CSRF, XSS등 구체적인 실행 항목들이 포함되어 있고 SpringSecurity에 대한 설정은 여기서 한다.
<br>
</span>

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) throws Exception {
        httpSecurity
                .httpBasic(AbstractHttpConfigurer::disable)
                .csrf(AbstractHttpConfigurer::disable)
                .cors(AbstractHttpConfigurer::disable)
                // .requestMatchers("/api/v1/users/join", "/api/v1/users/login").permitAll()
                // .requestMatchers(HttpMethod.POST, "/api/v1/**").authenticated()
                .authorizeHttpRequests(
                        r -> r.requestMatchers("/api/v1/users/join", "/api/v1/users/login").permitAll() // 특정 url 체크 제외
                                .requestMatchers(HttpMethod.POST, "/api/v1/**").authenticated() // 그 Post형식 및  url은 인증이 되어야함
                )
                // .and()
                // .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .sessionManagement(c -> c.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
                // .and();
                // TODO
                //.addFilterBefore()
                ;
        return httpSecurity.build();
    }
}
```

### 여담

<span style="font-size:13pt">
SpringSecurity5.7까지을 있는 SpringSecurity6을 기준으로 작성하려고 한다. 
<br>

</span>

```java

```

