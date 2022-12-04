---
layout: single
title: "캐쳐 프로토타입 07 제작 기록 -End"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-12-04
last_modified_at: 2022-12-04
---

### 종료화면 구현
---

<span style="font-size:13pt">
종료화면 자체는 05번째에서 이미 구현을 해버렸고 남아있는 TODO의 내용 자체도 금방 끝나는 것들이라 다 해버리고 이 시리즈는 마쳐야 할 듯.<br/>
화면은 이미 있으니 취득한 점수를 종료화면에 표시해주는 걸로 종료화면 구현을 끝내겠다.<br/>
</span>

[캐쳐 프로토타입 05 제작 기록](https://subcharacters.github.io/unity/catcher-prototype05/)


![image](..\..\images\unity\catcher-prototype46.PNG)

<span style="font-size:13pt">
GameOverScene을 열고 Hierarchy -> UI -> Text로 오브젝트를 생성해주고 Inspector 설정도 변경해준다.<br/>
그리고 ScoreController 스크립트를 add하면 끝.<br/>
</span>

### 터치 시 게임 시작
---

![image](..\..\images\unity\catcher-prototype47.PNG)

<span style="font-size:13pt">
다시 MainScene으로 돌아와서 Tab to Start라는 Text를 생성<br/>
GameManager오브젝트가 들고 있는 GameController스크립트를 수정한다.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameController : MonoBehaviour
{
    ... 중략

    private bool isStart = false; // 추가 코드

    public GameObject startText; // 추가 코드

    // Start is called before the first frame update
    void Start()
    {
        Time.timeScale = 0;
        StartCoroutine(CreateObstacleRoutine());
        StartCoroutine(CreateBonusRoutine());
        ScoreController.score = 0;
        isStart = false; // 추가 코드
        startText.SetActive(true); // 추가 코드
    }

    // Update is called once per frame
    void Update()
    {
        // 추가 코드
        if (!isStart && Input.GetMouseButtonDown(0))
        {
            startText.SetActive(false);
            isStart = true;
            Time.timeScale = 1;
        }
    }

    ... 중략
}

```

<span style="font-size:13pt">
유니티에는 Time.timeScale이라는 게임 전체 속도를 제어하는 아주 훌륭한 변수가 있다.<br/>
해당 timeScale을 0으로하면 0배속, 즉 게임을 멈출 수 있고 2로 하면 2배속이 된다.<br/>
이 변수 하나로 게임 전체 속도를 제어 하는 귀찮은 설계를 안해도 된다.<br/>
마우스 클릭하면 startText를 비활성화(숨김) 시키고 timeScale을 1로 돌려놓는 방식으로 구현했다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype48.PNG)

<span style="font-size:13pt">
그리고 위 코드에서 추가한 public GameObject startText; 게임 오브젝트에 Text오브젝트를 추가하면 구현이 끝난다.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
게임 종료화면 구현 끝 <br/>
터치 시 게임 스타트 끝 <br/>
이것으로 프로토 타입 제작 연습을 끝낸다. 현업도 있어서 한달 좀 안되게 걸렸지만 까먹었던 것도 새록새록 기억나서 좋은 듯.<br/>
다음에는 직접 출시할 게임을 제작할 것이고 이 부분은 메모 해놓고 싶은 기능들만 스팟성으로 기록할 것이다.<br/>
여튼 끝<br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [x] ~~<span style="font-size:13pt">메인 캐릭터 생성과 이동</span>~~
- [x] ~~<span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>~~
- [x] ~~<span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>~~
- [x] ~~<span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>~~
- [x] ~~<span style="font-size:13pt">게임 종료 화면 구현</span>~~
- [x] ~~<span style="font-size:13pt">터치 시 게임 스타트</span>~~