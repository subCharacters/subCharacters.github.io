---
layout: single
title: "IntelliJ에서 바이트코드 확인하기"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - other
tags:
  - Java
  - InteliJ
toc: true
toc_sticky: true
date: 2025-01-02
last_modified_at: 2025-01-02
---

## 개요

---

<span style="font-size:13pt">
IntelliJ를 이용하여 바이트코드를 확인하는 방법이다.  
실제 코드가 어떻게 변환되어 동작하는 지 확인이 가능해서 코드가 효율적으로 컴파일 되었는지 확인이 가능하다.  
</span>

## 바이트 코드 보기

---
<span style="font-size:13pt">
버전 별로 다를 수는 있으나 크게 바뀌진 않을 듯 하다.  
상단의 View -> Show Bytecode를 누르면 별도의 창이 뜬다.  
당연한 얘기지만 빌드를 하지 않으면 볼 수가 없으니 빌드, 혹은 코드 실행 후 보도록 한다.  
</span>

### 코드
<span style="font-size:13pt">
자바 17의 코드를 아래와 같이 작성했을때 나오는 바이트 코드는 
</span>

```java
public class Main {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < 10000; i++) {
            list.add("String Value " + i);
        }

        int size = list.size();
        for (int i = 0; i < size; i++) {
            System.out.print(list.get(i));
        }

        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i));
        }

        String word = "Word1";
        for (int i = 2; i <= 10; i++) {
            word += "Word" + i;
        }
    }
}
```

<span style="font-size:13pt">
다음과 같다.
</span>

```bytecode
// class version 61.0 (61)
// access flags 0x21
public class Main {

  // compiled from: Main.java
  // access flags 0x19
  public final static INNERCLASS java/lang/invoke/MethodHandles$Lookup java/lang/invoke/MethodHandles Lookup

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 4 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this LMain; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 6 L0
    NEW java/util/ArrayList
    DUP
    INVOKESPECIAL java/util/ArrayList.<init> ()V
    ASTORE 1
   L1
    LINENUMBER 7 L1
    ICONST_0
    ISTORE 2
   L2
   FRAME APPEND [java/util/List I]
    ILOAD 2
    SIPUSH 10000
    IF_ICMPGE L3
   L4
    LINENUMBER 8 L4
    ALOAD 1
    ILOAD 2
    INVOKEDYNAMIC makeConcatWithConstants(I)Ljava/lang/String; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/StringConcatFactory.makeConcatWithConstants(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
      // arguments:
      "String Value \u0001"
    ]
    INVOKEINTERFACE java/util/List.add (Ljava/lang/Object;)Z (itf)
    POP
   L5
    LINENUMBER 7 L5
    IINC 2 1
    GOTO L2
   L3
    LINENUMBER 11 L3
   FRAME CHOP 1
    ALOAD 1
    INVOKEINTERFACE java/util/List.size ()I (itf)
    ISTORE 2
   L6
    LINENUMBER 12 L6
    ICONST_0
    ISTORE 3
   L7
   FRAME APPEND [I I]
    ILOAD 3
    ILOAD 2
    IF_ICMPGE L8
   L9
    LINENUMBER 13 L9
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    ALOAD 1
    ILOAD 3
    INVOKEINTERFACE java/util/List.get (I)Ljava/lang/Object; (itf)
    CHECKCAST java/lang/String
    INVOKEVIRTUAL java/io/PrintStream.print (Ljava/lang/String;)V
   L10
    LINENUMBER 12 L10
    IINC 3 1
    GOTO L7
   L8
    LINENUMBER 16 L8
   FRAME CHOP 1
    ICONST_0
    ISTORE 3
   L11
   FRAME APPEND [I]
    ILOAD 3
    ALOAD 1
    INVOKEINTERFACE java/util/List.size ()I (itf)
    IF_ICMPGE L12
   L13
    LINENUMBER 17 L13
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    ALOAD 1
    ILOAD 3
    INVOKEINTERFACE java/util/List.get (I)Ljava/lang/Object; (itf)
    CHECKCAST java/lang/String
    INVOKEVIRTUAL java/io/PrintStream.print (Ljava/lang/String;)V
   L14
    LINENUMBER 16 L14
    IINC 3 1
    GOTO L11
   L12
    LINENUMBER 20 L12
   FRAME CHOP 1
    LDC "Word1"
    ASTORE 3
   L15
    LINENUMBER 21 L15
    ICONST_2
    ISTORE 4
   L16
   FRAME APPEND [java/lang/String I]
    ILOAD 4
    BIPUSH 10
    IF_ICMPGT L17
   L18
    LINENUMBER 22 L18
    ALOAD 3
    ILOAD 4
    INVOKEDYNAMIC makeConcatWithConstants(Ljava/lang/String;I)Ljava/lang/String; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/StringConcatFactory.makeConcatWithConstants(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
      // arguments:
      "\u0001Word\u0001"
    ]
    ASTORE 3
   L19
    LINENUMBER 21 L19
    IINC 4 1
    GOTO L16
   L17
    LINENUMBER 24 L17
   FRAME CHOP 1
    RETURN
   L20
    LOCALVARIABLE i I L2 L3 2
    LOCALVARIABLE i I L7 L8 3
    LOCALVARIABLE i I L11 L12 3
    LOCALVARIABLE i I L16 L17 4
    LOCALVARIABLE args [Ljava/lang/String; L0 L20 0
    LOCALVARIABLE list Ljava/util/List; L1 L20 1
    // signature Ljava/util/List<Ljava/lang/String;>;
    // declaration: list extends java.util.List<java.lang.String>
    LOCALVARIABLE size I L6 L20 2
    LOCALVARIABLE word Ljava/lang/String; L15 L20 3
    MAXSTACK = 3
    MAXLOCALS = 5
}

```

<span style="font-size:13pt">
처음 보면 어려워 보이지만 막상 별거 없다.  
LINENUMBER가 정의되어 있기때문에 실제 코드의 라인을 보면서 매칭하면 알기 쉽다.  
이 예제 코드에서 2가지를 확인 가능한데  
첫번째로는 for에서 list.size()를 사용할 경우 L11를 보면 INVOKEINTERFACE java/util/List.size ()I (itf)  
로 매번 루프 시 .size로 객체 참조를 부르는 걸 알 수 있다.  
극한의 효율을 생각한다면 귀찮더라도 사이즈를 미리 변수로 선언하고 이용하면 좋을 것이다.  
두번째로는 String타입을 +로 결합 했을때인데 구버전 자바에서는 StringBuilder를 이용해서 결합을 했어야 했는데  
8부터는 컴파일 시 자동으로 Builder를 사용하고 9+부터는 L18와 같이 makeConcatWithConstants를 불러 결합을 한다.
</span>

## 마무리

---
<span style="font-size:13pt">
인텔리제이를 안쓰고 한다면 별의 별 방법을 써서 힘들게 봐야 하지만 툴로 간단하게 볼 수 있어서 좋다.  
단순히 stream은 느리니 for로 작성하고 문자열 결합은 Builder를 쓰는 단순히 외워서 하는 것이 아니라  
바이트 코드를 분석해서 코드를 짜면 1나노초라도 효율이 좋은 코드를 작성 할 수 있을 것이다.  
단, 효율성과 가독성은 다른 문제이고 유지보수 측면도 생각해야 하는 거라 어려운 문제이다.
</span>