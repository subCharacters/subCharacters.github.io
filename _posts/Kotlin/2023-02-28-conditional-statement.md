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
date: 2023-02-28
last_modified_at: 2023-02-28
---

### 조건문 사용 방법
---

```kotlin

fun main() {
    sayHello()
    println(returnInputWord("Good Bye"))
}

fun sayHello() {
    println("Hello")
}

fun returnInputWord(str: String): String {
    return str
}

```

> Hello  
> Good Bye


### 조건문
---

```kotlin

fun main() {
    sayHello()
    println(returnInputWord("Good Bye"))
}

fun sayHello() = println("Hello")

fun returnInputWord(str: String) = str

```

> Hello  
> Good Bye
