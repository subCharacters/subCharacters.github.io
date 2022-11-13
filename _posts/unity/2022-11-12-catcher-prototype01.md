---
layout: single
title: "캐쳐 프로토타입 01 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-12
last_modified_at: 2022-11-12
---

### 개요
---
> 캐쳐 게임 프로토타입 만들기

![image](..\..\images\unity\catcher-prototype01.jpg)

*<cite style="font-size:9pt">게임 거상의 한 장면</cite>*
{: .small}

<span style="font-size:13pt">
위 이미지와 같이 아이템을 받아 점수를 획득하는 게임으로 단순한 미니 게임 <br/>
프로토 타입이므로 임의의 이미지와 스프라이트로 점수를 취득하고 획득하면 안되는 아이템을 잡았을 경우 게임 오버까지 하는 프로토타입을 제작할 예정<br/>
유니티 2020.3.34f1 버전이며 사용언어는 C#<br/>
C# 툴은 VisualStudio2019를 사용<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
프로토 타입으로 구현할 내용을 리스트로 추리면 다음과 같다.<br/>
아래의 순서대로 구현할 예정이고 진행에 따라 기능이 추가 될 수도 있다.<br/>
</span>

- [ ] <span style="font-size:13pt">맵 생성</span>
- [ ] <span style="font-size:13pt">메인 캐릭터 생성과 이동</span>
- [ ] <span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>