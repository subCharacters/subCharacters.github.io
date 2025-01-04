---
layout: single
title: "SpringBoot 메소드 실행 시간 기록하기"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - SpringBoot
tags:
  - SpringBoot
  - StopWatch
toc: true
toc_sticky: true
date: 2025-01-04
last_modified_at: 2025-01-04
---

## 개요

---

<span style="font-size:13pt">
SpringBoot에서 메소드의 실행 시간을 기록하는 방법에 대해 알아본다.<br>
간단하게 top 페이지가 존재하고 login 유무의 따라 top 페이지가 달리 보이는 간단한 웹 페이지이다.<br>
우선 코드는 이러하다.<br>
로그인을 하면 login controller에서 session을 설정하여 login_flag에 값을 설정해주는 간단한 로직이다.
</span>

```java
@Controller
@RequestMapping("/")
public class TopController {
    @GetMapping(value = {"", "top"})
    public String top(HttpServletRequest request, Model model, @ModelAttribute String login_flag,
                      @SessionAttribute(name = "login_flag", required = false) String lf) {
        HttpSession session = request.getSession();
        boolean flag = session.getAttribute("login_flag") != null ? (boolean) session.getAttribute("login_flag") : false;
        System.out.println(lf);
        model.addAttribute("login_flag", flag);
        return "top";
    }
}

@Controller
@RequestMapping("/")
public class LoginController {
    @PostMapping("login")
    public String login(HttpServletRequest request) {
        HttpSession session = request.getSession();
        session.setAttribute("login_flag", true);
        return "redirect:top";
    }

    @PostMapping("logout")
    public String  logout(HttpServletRequest request) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate();
        }
        return "redirect:top";
    }
}
```

## StopWatch

<span style="font-size:13pt">
고전적인 방법으로는 <span style="font-weight:bold">System.currentTimeMillis();</span>와 <span style="font-weight:bold">System.nanoTime();</span>와 같은 것을 이용하여 시간을 측정하는 방법이다.
</span>

```java
long startTime = System.currentTimeMillis();
/**
* 메소드 내용
*/
long endTime = System.currentTimeMillis();
System.out.println(endTime - startTime + " ms");
```

<span style="font-size:13pt">
하지만 해당 방법은 슬프게도 이쁘게 출력이 안된다는 단점이 있다.<br>
그리고 일일이 변수를 지정해줘야 해서 사용하기 안좋다는 부분도 존재한다.<br>
대안이 없다면 직접 시간 체크 부분을 구현해야 하겠지만 SpringBoot에서는 StopWatch라는 시간 체크 기능을 서포트한다.<br>
아래는 기존 코드에 StopWatch를 설정한 코드이다.
</span>

```java
@Controller
@RequestMapping("/")
public class TopController {
    private static final Logger logger = LoggerFactory.getLogger(TopController.class); // 추가
    @GetMapping(value = {"", "top"})
    public String top(HttpServletRequest request, Model model, @ModelAttribute String login_flag,
                      @SessionAttribute(name = "login_flag", required = false) String lf) {
        StopWatch stopWatch = new StopWatch(); // 추가
        stopWatch.start("TopController"); // 추가
        HttpSession session = request.getSession();
        boolean flag = session.getAttribute("login_flag") != null ? (boolean) session.getAttribute("login_flag") : false;
        System.out.println(lf);
        model.addAttribute("login_flag", flag);
        stopWatch.stop(); // 추가
        logger.info(stopWatch.prettyPrint()); // 추가
        return "top";
    }
}
```

<span style="font-size:13pt">
logger부분은 System.out.print보다 보기 좋게 하려고 넣은 것 뿐이다. <br>
사용법은 딱히 어려운 것이 없으니 따로 적진 않겠다.<br>
출력을 하면 아래와 같이 나온다.
</span>

```java
2025-01-04T21:38:20.921+09:00  INFO 22088 --- [nio-8080-exec-1] c.e.s.controller.TopController           : StopWatch '': 0.0004376 seconds
----------------------------------------
Seconds       %       Task name
----------------------------------------
0.0004376     100%    TopController
```

<span style="font-size:13pt">
하지만 문제점이 있는 데 여러 메소드의 실행시간을 알고 싶을때에 각각 메소드에 다 적어야 해서 매우 비효율적인 보일러플레이트 코드가 발생한다.<br>
물론 유지보수도 힘들다는 단점은 덤이다. <br>
스프링을 이용하므로 AOP개념을 넣으면 더 깔끔할 것이다.
</span>

### with AOP
<span style="font-size:13pt">
TopController 추가 했던 StopWatch를 지워버리고 AOP용 클래스를 작성한다.
</span>

```java
@Component
@Aspect
public class TimeTrace {
    private static final Logger logger = LoggerFactory.getLogger(TimeTrace.class);
    @Around("execution(* com.example.springboot_login.controller..*(..))")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        String methodName = joinPoint.getSignature().toShortString();
        stopWatch.start(methodName);

        try {
            return joinPoint.proceed();
        } finally {
            stopWatch.stop();
            logger.info(stopWatch.prettyPrint());
        }
    }
}
```

<span style="font-size:13pt">
그러면 Around 애노테이션에 적용된 루트의 컨트롤러 클래스에 모두 적용이 된다.
</span>

```java
2025-01-04T23:16:15.109+09:00  INFO 18016 --- [nio-8080-exec-1] c.e.springboot_login.aop.TimeTrace       : StopWatch '': 0.0005474 seconds
----------------------------------------
Seconds       %       Task name
----------------------------------------
0.0005474     100%    TopController.top(..)

2025-01-04T23:16:18.668+09:00  INFO 18016 --- [nio-8080-exec-4] c.e.springboot_login.aop.TimeTrace       : StopWatch '': 0.0004356 seconds
----------------------------------------
Seconds       %       Task name
----------------------------------------
0.0004356     100%    LoginController.login(..)

true
2025-01-04T23:16:18.680+09:00  INFO 18016 --- [nio-8080-exec-5] c.e.springboot_login.aop.TimeTrace       : StopWatch '': 0.00004 seconds
----------------------------------------
Seconds       %       Task name
----------------------------------------
0.00004       100%    TopController.top(..)
```

## 마무리
<span style="font-size:13pt">
추가적으로 디버깅 모드에서만 시간을 낸다거나 하는 추가적인 설정은 필요할 것으로 보이나 그것은 다른 문제이므로 고려하지 않았다.<br>
마지막으로 해당 클래스에 대한 공식 문서 링크를 남기고 마친다.<br>
</span>
[StopWatch Document](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/StopWatch.html)
