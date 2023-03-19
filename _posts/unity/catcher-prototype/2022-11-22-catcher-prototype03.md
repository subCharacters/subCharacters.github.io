---
layout: single
title: "캐쳐 프로토타입 03 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-22
last_modified_at: 2022-11-23
---

### 메인 캐릭터 생성
---

![image](..\..\images\unity\catcher-prototype16.PNG)

![image](..\..\images\unity\catcher-prototype17.PNG)

<span style="font-size:13pt">
2D오브젝트로 사각형 도형을 생성하고 색상 및 크기, 위치를 조정했다. <br/>
</span>

![image](..\..\images\unity\catcher-prototype20.PNG)

![image](..\..\images\unity\catcher-prototype21.PNG)

<span style="font-size:13pt">
타일맵(위)과 메인 캐릭터(아래) 오브젝트에 위와 같은 컴포넌트를 설정해준다. <br/>
충돌설정을 하는 collider와 물리효과를 주는 rigidyBody를 추가.<br/>
</span>

### 메인 캐릭터 이동
---
![image](..\..\images\unity\catcher-prototype18.PNG)

<span style="font-size:13pt">
메인 캐릭터에 Add Component로 새로운 스크립트를 만들고 아래와 같이 코드를 추가한다. <br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class MainCharacter : MonoBehaviour
{
    public float moveSpeed = 10.0f;

    Rigidbody2D rigidbody2D;
    private float scWidth = Screen.width / 2; // 화면 너비의 절반값

    // Start is called before the first frame update
    void Start()
    {
        rigidbody2D = GetComponent<Rigidbody2D>();
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetMouseButton(0)) //마우스 클릭(터치) 중 일때 계속해서 실행
        {
            Vector2 touchPosition = Input.mousePosition; // 마우스 클릭 (터치) 한 곳의 위치를 취득
            if (touchPosition.x <= scWidth)
            {
                rigidbody2D.velocity = Vector2.left * moveSpeed; // 왼쪽이동
            }
            else if (touchPosition.x >= scWidth)
            {
                rigidbody2D.velocity = Vector2.right * moveSpeed; // 오른쪽 이동
            }
        }
        else if (Input.GetMouseButtonUp(0)) // 마우스, 터치를 땟을때
        {
            rigidbody2D.velocity = Vector2.zero; // 이동을 바로 멈춤.
        }
        ScreenChk(); // 화면 밖으로 나가지 않도록 체크
    }

    private void ScreenChk()
    {
        Vector3 worlpos = Camera.main.WorldToViewportPoint(this.transform.position);
        if (worlpos.x < 0f) worlpos.x = 0f;
        if (worlpos.x > 1f) worlpos.x = 1f;
        this.transform.position = Camera.main.ViewportToWorldPoint(worlpos);
    }
}

```

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
메인 캐릭터 생성과 이동 끝 <br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [x] ~~<span style="font-size:13pt">메인 캐릭터 생성과 이동</span>~~
- [ ] <span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>