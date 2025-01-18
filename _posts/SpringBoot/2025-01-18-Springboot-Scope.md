---
layout: single
title: "SpringBoot Bean Scope에 대해 - singleton, prototype, request"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - Scope
toc: true
toc_sticky: true
date: 2025-01-18
last_modified_at: 2025-01-18
---

## 개요

---

<span style="font-size:13pt">
SpringBoot에서 Bean에 Scope를 붙일 수 있다.<br>
기본적으로 Spring Boot에서 성능을 위해 Bean은 싱글톤으로 관리하고 있다.<br>
하지만 경우에 따라서 그때 그때 생성해야 할 경우가 있을때 Scope를 사용하여 빈을 관리 할 수 있다.<br>
Request Scope는 웹이 아니면 작동을 안하기 때문에 SpringBoot웹의 서비스 빈을 사용하여 알아본다.
</span>

## Code

---

<span style="font-size:13pt">
아래와 같은 코드가 있다. <br>
</span>

### Controller

```java
@RestController
public class NoneScopeController1 {

    private final NoneScopeBeanService noneScopeBeanService;

    public NoneScopeController1(NoneScopeBeanService noneScopeBeanService) {
        this.noneScopeBeanService = noneScopeBeanService;
    }

    @GetMapping("/none1")
    public String getMain(@RequestParam String name) {
        System.out.println(noneScopeBeanService.getName());
        noneScopeBeanService.setName(name);
        System.out.println(noneScopeBeanService.getName());
        System.out.println("beanService -> " + noneScopeBeanService);
        return name;
    }
}
```

```java
@RestController
public class NoneScopeController2 {
    private NoneScopeBeanService noneScopeBeanService;

    @Autowired
    public NoneScopeController2(NoneScopeBeanService noneScopeBeanService) {
        this.noneScopeBeanService = noneScopeBeanService;
    }

    @GetMapping("/none2")
    public String getMain(@RequestParam String name) {
        System.out.println(noneScopeBeanService.getName());
        noneScopeBeanService.setName(name);
        System.out.println(noneScopeBeanService.getName());
        System.out.println("beanService -> " + noneScopeBeanService);
        return name;
    }
}
```

```java
@RestController
public class PrototypeScopeController1 {
    private PrototypeScopeBeanService prototypeScopeBeanService;

    @Autowired
    public PrototypeScopeController1(PrototypeScopeBeanService prototypeScopeBeanService) {
        this.prototypeScopeBeanService = prototypeScopeBeanService;
    }

    @GetMapping("/proto1")
    public String getMain(@RequestParam String name) {
        System.out.println(prototypeScopeBeanService.getName());
        prototypeScopeBeanService.setName(name);
        System.out.println(prototypeScopeBeanService.getName());
        System.out.println("beanService -> " + prototypeScopeBeanService);
        return name;
    }
}
```

```java
@RestController
public class PrototypeScopeController2 {
    private PrototypeScopeBeanService prototypeScopeBeanService;

    @Autowired
    public PrototypeScopeController2(PrototypeScopeBeanService prototypeScopeBeanService) {
        this.prototypeScopeBeanService = prototypeScopeBeanService;
    }

    @GetMapping("/proto2")
    public String getMain(@RequestParam String name) {
        System.out.println(prototypeScopeBeanService.getName());
        prototypeScopeBeanService.setName(name);
        System.out.println(prototypeScopeBeanService.getName());
        System.out.println("beanService -> " + prototypeScopeBeanService);
        return name;
    }
}
```

```java
@RestController
public class RequestScopeController {
    private RequestScopeBeanService requestScopeBeanService;

    @Autowired
    public RequestScopeController(RequestScopeBeanService beanService) {
        this.requestScopeBeanService = beanService;
    }

    @GetMapping("/request")
    public String request(@RequestParam String name) {
        System.out.println(requestScopeBeanService.getName());
        requestScopeBeanService.setName(name);
        System.out.println(requestScopeBeanService.getName());
        System.out.println("beanService -> " + requestScopeBeanService);
        return name;
    }
}
```

### Service

```java
@Service
public class NoneScopeBeanService {
    private String name = "None";

    public NoneScopeBeanService() {
        System.out.println("NoneScopeBeanService created");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```java
@Service
@Scope("prototype")
public class PrototypeScopeBeanService {
    private String name = "Prototype";;

    public PrototypeScopeBeanService() {
        System.out.println("PrototypeScopeBeanService created");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```java
@Service
@RequestScope
public class RequestScopeBeanService {
    private String name = "Request";;

    public RequestScopeBeanService() {
        System.out.println("RequestScopeBeanService created");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

<span style="font-size:13pt">
NoneScopeController는 싱글톤 <br>
PrototypeScopeController는 프로토타입 <br>
RequestScopeController는 리퀘스트이다.<br>
리퀘스트는 @RequestScope가 따로 존재하기에 이용했다.<br>
각각 실행한 결과는 다음과 같다.<br>
</span>

## 결과
<span style="font-size:13pt">
처음 구동시 아래와 같은 빈 생성 로그가 생성된다.<br>
</span>

```java
NoneScopeBeanService created
PrototypeScopeBeanService created
PrototypeScopeBeanService created
```

<span style="font-size:13pt">
두번째로 url을 none1과 파라미터 1을 입력하였을때 <br>
</span>

```java
None
1
beanService -> com.practice.springboot_practice_for_java.service.service.NoneScopeBeanService@3bf99cd7
```

<span style="font-size:13pt">
url을 none2와 파라미터 1을 입력하였을때 아래와 같이 나오고 빈 주소가 동일하고 name이 이전 반영사항을 가지고 있다.<br>
</span>

```java
1
1
beanService -> com.practice.springboot_practice_for_java.service.service.NoneScopeBeanService@3bf99cd7
```

<span style="font-size:13pt">
세번째로 url을 proto1과 파라미터 1을 입력하였을때 <br>
</span>

```java
Prototype
1
beanService -> com.practice.springboot_practice_for_java.service.service.PrototypeScopeBeanService@70ae95f2
```

<span style="font-size:13pt">
url을 proto2와 파라미터 1을 입력하였을때 아래와 같이 나오고 빈 주소가 다르고 name이 이전 반영사항을 가지고 있지 않다.<br>
처음 빈 생성 로그로 돌아가보면 빈 인스턴스가 두개 생성된것을 볼 수 있다.<br>
</span>

```java
Prototype
1
beanService -> com.practice.springboot_practice_for_java.service.service.PrototypeScopeBeanService@7215eef8
```

<span style="font-size:13pt">
마지막으로 request에 파라미터를 넣어서 여러번 보내보면 <br>
</span>

```java
RequestScopeBeanService created
Request
1
beanService -> com.practice.springboot_practice_for_java.service.service.RequestScopeBeanService@355d2381
RequestScopeBeanService created
Request
1
beanService -> com.practice.springboot_practice_for_java.service.service.RequestScopeBeanService@3436269d
RequestScopeBeanService created
Request
1
beanService -> com.practice.springboot_practice_for_java.service.service.RequestScopeBeanService@37852b62
RequestScopeBeanService created
Request
1
beanService -> com.practice.springboot_practice_for_java.service.service.RequestScopeBeanService@1cdfd04e
```

<span style="font-size:13pt">
그때 그때 리퀘스트마다 생성된 것을 볼 수 있다.<br>
</span>

## 마무리
<span style="font-size:13pt">
싱글톤으로 관리 되기 때문에 전역 변수를 함부로 수정하는 것은 매우 리스크가 있기에 설계 단계에서 잘 생각 해야 한다.<br>
그렇다고 무작정 많이 만들면 메모리, 성능 이슈로 이어질 수 있기 때문에 이것도 고려해야한다.<br>
마지막으로 프로토타입은 웹에서는 인스턴스 생성 타이밍이 기동 시 이기때문에 생성 타이밍도 고려해야한다.<br>
</span>

