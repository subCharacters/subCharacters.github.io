---
title: "변수와 자료형"  
sidebar:
  nav: "docs"
categories: 
  - Kotlin
tags:
  - 변수와 자료형
toc: true
toc_sticky: true
date: 2023-02-16
last_modified_at: 2023-02-16
---

### 기본 자료형
---

|타입|크기|선언|자료형|
|:---:|:---:|:---:|:---:|
|Byte|8bits|var a: Byte = 1|정수형|
|Short|16bits|var b: Short = 1|정수형|
|Int|32bits|var c: Int = 1|정수형|
|Long|bits|var d: Long = 1L|정수형|
|Float|32bits|var e: Float = 1.0f|실수형|
|Double|64bits|var f: Double = 1.0|실수형|
|Char|16bits|var g: Char = 'a'|문자형(UTF-16 BE)|
|Boolean|bits|var h: Boolean = ture / false|논리형|
|String|bits|var h: String = "String" 혹은 """이렇게 여러줄을 작성"""|문자열(기본자료형은 아니지만 작성)|

<span style="font-size:13pt">
정수형으로 진수를 표기할때에는 10진수는 그냥 숫자를 작성. <br/>
16진수는 0x(hexadecimal)를 앞에 붙여서 표기. <br/>
2진수는 0b(binary)를 앞에 붙여서 표기. <br/>
8진수는 지원하지 않음. <br/>
</span>

### 기본적인 선언 방법
---

```kotlin
fun main() {
    var variable: Int = 0; // 선언 후에도 변경가능.
    val value: Int = 0; // 선언 후엔 변경 불가.
    
    var variable2: Int
    // error발생. 
    // 코틀린은 자바 같은 다른 언어와는 다르게 기본 자료형에서도 null이나 초기화 값을 가지지 않으므로 무조건 초기화가 필요.
    println(variable2) 
    
    // null을 다루기 위해서는 ? 연산자를 써줘야한다.
    var variableNullable: Int? = null

    var variable = 0; // 타입추론이 가능한 언어이므로 타입은 생략이 가능.
}

```
