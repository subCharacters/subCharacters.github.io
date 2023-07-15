---
layout: single
title: "폰트에셋 만들기"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - Localization
toc: true
toc_sticky: true
date: 2023-04-21
last_modified_at: 2023-04-21
---

### 개요
---
> 폰트에셋 만들기

<span style="font-size:13pt">
TMP(Text Mesh Pro)에서 사용 가능한 Font에셋을 만들어 다국어에 사용 가능하도록 한다.<br/>
일반적인 Text라면 다국어 폰트로도 대응이 가능하지만 TMP에서는 영어만 지원하기에 다국어를 사용하려면 font asset을 만들 필요가 있다.<br/>
유니티 버전은 2021.3.23f1버전이다.<br/>
</span>

### 폰트 에셋 만들기
---

<span style="font-size:13pt">
적용하기에 앞서 크게 2가지 정도 준비할 것이 필요하다.<br/>
다국어 대응이 가능한 폰트와 해당 국가의 문자 리스트가 필요하다.<br/>
문자리스트는 ㄱㄴㄷㄹ부터 시작해서 하햐허혀와 같이 글자 하나하나가 필요하다.<br/>
특정 단어 혹은 문장만 필요하다면 그 부분만 대응이 가능하도록 지정이 가능하다.<br/>
폰트 에셋이 적용되기 전에는 아래와 같이 글자가 깨져서 나오게 된다.<br/>
</span>

![image](..\..\images\unity\fontasset\fontasset00.PNG)

<span style="font-size:13pt">
폰트 에셋을 만들기 위해 먼저 새로운 폰트 에셋을 작성한다.<br/>
Window -> TextMeshPro -> Font Asset Creator<br/>
그럼 아래와 같은 화면이 뜬다.<br/>
</span>

![image](..\..\images\unity\fontasset\fontasset01.PNG)

<span style="font-size:13pt">
미리 다운 받아놓은 폰트를 넣고 Sampling Point Size, Character Set, Render Mode등을 설정하고 <br/>
Custom Character List에 사용할 문자를 추가한다.<br/>
Generate Font Atlas를 눌러서 조금 기다리면 font asset이 생성되는데 save를 하면 된다.(크기가 크면 많이 오래 걸림)<br/>
</span>

![image](..\..\images\unity\fontasset\fontasset02.PNG)

<span style="font-size:13pt">
그리고 TMP의 Inspector에 들어가서 해당 에셋을 추가해주면 정상적으로 표기가 된다.<br/>
</span>

![image](..\..\images\unity\fontasset\fontasset03.PNG)

![image](..\..\images\unity\fontasset\fontasset04.PNG)