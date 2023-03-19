---
layout: single
title: "캐쳐 프로토타입 04 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-23
last_modified_at: 2022-11-23
---

### 장애물 오브젝트 생성
---

![image](..\..\images\unity\catcher-prototype22.PNG)

![image](..\..\images\unity\catcher-prototype23.PNG)

<span style="font-size:13pt">
메인 캐릭터를 만드는 것과 동일하게 2D오브젝트로 동그라미 도형을 생성하고 색상 및 크기, 위치를 조정했다. <br/>
충돌효과와 물리효과를 적용하기 위해 CircleCollider2D와 Rigidbody2D의 컴포넌트를 추가했다.  <br/>
이것으로 장애물 오브젝트 자체는 위에서 아래로 떨어질 것이다.<br/>
</span>

### 장애물 생성해서 떨어뜨리기
---

![image](..\..\images\unity\catcher-prototype25.PNG)

<span style="font-size:13pt">
Obstacle오브젝트를 끌어다가 Prefabs화 시킨다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype24.PNG)

<span style="font-size:13pt">
Hierachy -> 우 클릭 -> CreateEmpty로 빈 오브젝트를 생성.<br/>
Add Component -> Script생성 아래 코드를 입력<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameController : MonoBehaviour
{
    [SerializeField] // inspector에서는 접근 가능하지만 다른 class에선 접근 불가.
    private GameObject obstacle;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(CreateObstacleRoutine());
    }

    // Update is called once per frame
    void Update()
    {

    }

    IEnumerator CreateObstacleRoutine()
    {
        while (true)
        {
            CreateObstacle();
            yield return new WaitForSeconds(0.5f); // 0.5초마다 코루틴 반복
        }
    }

    private void CreateObstacle()
    {
        Vector2 position = Camera.main.ViewportToWorldPoint(new Vector2(UnityEngine.Random.Range(0.0f, 1.0f), 1.1f)); // 카메라 안쪽에 랜덤하게 생성
        Instantiate(obstacle, position, Quaternion.identity); // Quaternion.identity 회전하지 않음.
    }
}

```

![image](..\..\images\unity\catcher-prototype26.PNG)

<span style="font-size:13pt">
소스를 입력하고 Prefabs를 스크립트 변수로 넘겨준다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype27.PNG)

<span style="font-size:13pt">
문제 없이 장애물 오브젝트는 생성되고 위에서 아래로 떨어지게 된다.<br/>
하지만 오브젝트가 생성만 될 뿐 사라지지 않는다.<br/>
이 부분은 다음 TODO에 넣어서 구현하자.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
낙하물 생성하여 중력을 구현 끝 <br/>
게임 종료와 스코어 증가의 순서를 바꿈 <br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [x] ~~<span style="font-size:13pt">메인 캐릭터 생성과 이동</span>~~
- [x] ~~<span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>~~
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>