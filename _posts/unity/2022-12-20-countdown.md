---
layout: single
title: "타이머 카운트 다운"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - Timer
toc: true
toc_sticky: true
date: 2022-12-20
last_modified_at: 2022-12-20
---

### 타이머 구현하기
---

<span style="font-size:13pt">
디펜스 류 게임에서 많이 나오는 시간이 카운트 다운 되는 타이머에 대한 기록이다.<br/>
먼저 Hierachy에서 Canvas를 만들고 Canvas안에 Text를 만듦.<br/>
</span>

![image](..\..\images\unity\unity_tech_timer1.PNG)

<span style="font-size:13pt">
CountDown이라는 스크립트를 만들고 아래 스크립트를 추가.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class CountDown : MonoBehaviour
{
    [SerializeField]
    private Text timerText;

    public static float sec = 1;
    public static int min = 1;
    public static bool countDownCompleteFlag = false;

    void Update()
    {
        if (!countDownCompleteFlag)
        {
            Timer();
        }
    }

    void Timer()
    {
        sec -= Time.deltaTime;
        timerText.text = string.Format("{0:D2}:{1:D2}", min, (int)sec);

        if (sec <= 0) 
        {
            if (min == 0)
            {
                countDownCompleteFlag = true;
                return;
            }
            sec = 60;
            min--;
        }

    }
}
```

<span style="font-size:13pt">
countDownCompleteFlag 변수는 다른 스크립트에서 종료시 제어하기 위해 넣어둔 플래그이다.<br/>
min과 sec이 둘다 0일때 카운트 다운을 더 이상 하지 않는다.(음수 방지)<br/>
마지막으로 private Text timerText; 에 위에서 생성한 텍스트 오브젝트를 Inspector에서 추가 해주면 된다.<br/>
</span>

![image](..\..\images\unity\unity_tech_timer3.gif)