---
layout: single
title: "캐쳐 프로토타입 02 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-16
last_modified_at: 2022-11-17
---

### 프로젝트 생성
---

![image](..\..\images\unity\catcher-prototype02.PNG)

<span style="font-size:13pt">
유니티 허브를 이용해서 프로젝트를 생성한다. <br/>
</span>

![image](..\..\images\unity\catcher-prototype03.PNG)

<span style="font-size:13pt">
2D로 제작할 예정이므로 2D를 선택한다. <br/>
캐쳐의 프로토타입이므로 Catcher-Prototype으로 적당히 만들자. <br/>
</span>

### 맵 생성
---
![image](..\..\images\unity\catcher-prototype04.PNG)

<span style="font-size:13pt">
제작에 필요한 리소스는 이미 있으므로 에셋에서 구매 해놓은 리소스를 import. <br/>
경로는 window -> asset store <br/>
</span>

![image](..\..\images\unity\catcher-prototype05.PNG)

<span style="font-size:13pt">
스프라이트는 이미 슬라이스가 되어 있는 상태이다. <br/>
나중에 슬라이스 하는 법을 까먹을 때를 대비해여 슬라이스 하는 법을 기록하자면..<br/>
</span>

![image](..\..\images\unity\catcher-prototype06.PNG)

![image](..\..\images\unity\catcher-prototype07.PNG)

<span style="font-size:13pt">
위의 이미지와 같이 Inspector -> Sprite Editor -> Slice 에서 자르는 기준을 정하고 자르고 apply를 한다. <br/>
</span>

<span style="font-size:13pt">
화면으로 돌아와서... <br/>
</span>

![image](..\..\images\unity\catcher-prototype08.PNG)

<span style="font-size:13pt">
화면 비율을 모바일(9:16)로 변경 <br/>
</span>

![image](..\..\images\unity\catcher-prototype09.PNG)

<span style="font-size:13pt">
Hierarchy에서 우클릭 후 2D Object로 타일 맵을 생성. <br/>
</span>

![image](..\..\images\unity\catcher-prototype10.PNG)

<span style="font-size:13pt">
오브젝트의 이름은 Ground로 해줬다. <br/>
</span>

![image](..\..\images\unity\catcher-prototype11.PNG)

<span style="font-size:13pt">
Tile Palette에서 적당한 타일을 골라 Tilemap을 작성하자. <br/>
이것으로 맵 생성은 끝이 났다. <br/>
하지만 백그라운드가 허전하므로 하는 김에 백그라운드도 넣기로 했다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype12.PNG)

<span style="font-size:13pt">
조코딩 님의 강의를 예전에 따라하면서 받아놓은 스프라이트가 있어서 그대로 이용하기로 했다.<br/>
슬라이스가 안되어 있어서 위에 미리 적어놓은 이미지 슬라이스하는 순서대로 슬라이스하면..<br/>
</span>


![image](..\..\images\unity\catcher-prototype13.PNG)

<span style="font-size:13pt">
이쁘게 슬라이스가 된다. <br/>
만약 슬라이스가 안된다면 Inspector의 Sprite Mode가 Single로 되어 있으면 Multiple로 해주면 된다. <br/>
이쁘게 잘라진 이미지가 보인다. <br/>
</span>

![image](..\..\images\unity\catcher-prototype14.PNG)

<span style="font-size:13pt">
img_0을 끌어다가 Hierarchy창에 넣으면 백그라운드가 생성된다. <br/>
오브젝트 명을 Background로 바꾸면 끝! 이 아니고 타일과 백그라운드의 오브젝트가 겹쳐서 백그라운드 하나만 보이는 버그가 생겼다. <br/>
</span>

![image](..\..\images\unity\catcher-prototype15.PNG)

<span style="font-size:13pt">
유니티는 3D가 기본 메인이므로 2D로 생성했다 하더라도 z축이 존재한다. <br/>
백그라운드의 z축 포지션을 변경해서 타일이 먼저 나오도록 변경하면 잘 나온다. <br/>
</span>


### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
맵 생성 끝 <br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [ ] <span style="font-size:13pt">메인 캐릭터 생성과 이동</span>
- [ ] <span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>