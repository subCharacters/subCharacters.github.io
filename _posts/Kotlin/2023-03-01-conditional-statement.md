---
title: "조건문"  
sidebar:
  nav: "docs"
categories: 
  - Kotlin
tags:
  - 조건문
toc: true
toc_sticky: true
date: 2023-03-01
last_modified_at: 2023-03-01
---

### 조건문 if
---

<span style="font-size:13pt">
기본적으로 부등호, 등호를 사용하여 조건을 검사한다. <br/>
if(참or거짓) {} 로 작성하고 if 안에 들어가는 구문이 한줄이면 {} 는 생략해도 된다. <br/>
if() {} else {} 형식으로 if 조건이 아닐때 동작하도록 기능을 구현하거나<br/>
if() {} else if {} 형식으로 또 다른 조건을 추가 할 수도 있다.
</span>

|기호|뜻|
|:---:|:---:|
|==|서로 같으면|
|!=|서로 다르면|
|<|왼쪽이 더 작으면|
|<=|왼쪽이 더 작거나 같으면|
|>|왼쪽이 더 크면|
|>=|왼쪽이 더 크거나 같으면|


<span style="font-size:13pt">
혹은 true, false의 boolean 값으로도 조건문 설정이 가능하다. <br/>
</span>

```kotlin

fun main() {
    if (1==1)
 		println("if")
    
    if (1!=1) {
        println("if")
    } else {
        println("else")
    }
    
    if (1!=1) {
        println("if")
    } else if (1==1) {
        println("else if")
    } else {
        println("else")
    }
}

```

> if  
> else  
> else if  


### 조건문 when
---

<span style="font-size:13pt">
when(any) 형식으로 any의 값이 일치 하는지 확인한다. <br/>
</span>

```kotlin

fun main() {
    whenCheck(1)
    whenCheck("when")
    whenCheck(1.1f)
    whenCheck("else")
    
    println(whenReturnCheck(1))
    println(whenReturnCheck("when"))
    println(whenReturnCheck(1.1f))
    println(whenReturnCheck("else"))
}

fun whenCheck(any: Any) {
    when(any) {
        1 -> println("input value 1")
        "when" -> println("input value when")
        !is String -> println("input value is not String")
        else -> println("else")
    }
}

fun whenReturnCheck(any: Any): Any {
    val result = when(any) {
        1 -> "input value 1"
        "when" -> "input value when"
        !is String -> "input value is not String"
        else -> "else"
    }
    return result
}

```

> input value 1  
input value when  
input value is not String  
else  
input value 1  
input value when  
input value is not String  
else  