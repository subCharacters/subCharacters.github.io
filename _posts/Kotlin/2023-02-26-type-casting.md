---
title: "형변환"  
sidebar:
  nav: "docs"
categories: 
  - Kotlin
tags:
  - 형변환
toc: true
toc_sticky: true
date: 2023-02-26
last_modified_at: 2023-02-26
---

### 형변환
---

<span style="font-size:13pt">
형변환 </br>
기본 자료형은 호환되는 다른 기본 자료형으로 변환이 가능.</br>
코틀린은 명시적 형변환만 지원하므로 암시적 형변환 언어처럼 형변환을 생략하는건 불가능.
</span>

|함수|
|:---:|
|toByte|
|toShort|
|toInt|
|toLong|
|toFloat|
|toDouble|
|toChar|

### 함수 사용 방법
---

```kotlin

fun main() {
    var variable: Int = 123456;
    
    var variableLong: Long = variable.toLong()
}

```
