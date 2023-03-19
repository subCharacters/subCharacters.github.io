---
layout: single
title: "캐쳐 프로토타입 06 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-30
last_modified_at: 2022-11-30
---

### 스코어 UI 생성
---

![image](..\..\images\unity\catcher-prototype40.PNG)

<span style="font-size:13pt">
Hierachy에서 우클릭 -> UI -> Canvas 생성<br/>
Canvas 우클릭 -> UI -> Text 생성<br/>
Text를 Score로 이름 변경해주었다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype41.PNG)

<span style="font-size:13pt">
Inspector에서 위치와 크기등을 조정해준다.<br/>
</span>

### 보너스 오브젝트 생성
---

![image](..\..\images\unity\catcher-prototype42.PNG)

<span style="font-size:13pt">
Obstacle과 똑같은 방식으로 오브젝트를 생성하고 Prefabs로 만들어주자.<br/>
색상을 노란색으로 달리했다.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameController : MonoBehaviour
{
    [SerializeField] // inspector에서는 접근 가능하지만 다른 class에선 접근 불가.
    private GameObject obstacle;

    // 코드 추가
    [SerializeField]
    private GameObject bonus;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(CreateObstacleRoutine());
        StartCoroutine(CreateBonusRoutine()); // 코드 추가
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

    // 코드 추가
    IEnumerator CreateBonusRoutine()
    {
        while (true)
        {
            CreateBonus();
            yield return new WaitForSeconds(1.0f); // 1초마다 코루틴 반복
        }
    }

    private void CreateObstacle()
    {
        Vector2 position = Camera.main.ViewportToWorldPoint(new Vector2(UnityEngine.Random.Range(0.0f, 1.0f), 1.1f)); // 카메라 안쪽에 랜덤하게 생성
        Instantiate(obstacle, position, Quaternion.identity); // Quaternion.identity 회전하지 않음.
    }

    // 코드 추가
    private void CreateBonus()
    {
        Vector2 position = Camera.main.ViewportToWorldPoint(new Vector2(UnityEngine.Random.Range(0.0f, 1.0f), 1.1f)); // 카메라 안쪽에 랜덤하게 생성
        Instantiate(bonus, position, Quaternion.identity);
    }
}

```

<span style="font-size:13pt">
GameController.cs에 bonus오브젝트도 생성되도록 한다.<br/>
Bonus에 Rigidbody 2D와 Circle Collider 2D도 잊지않고 추가해주고 Tag도 Bonus라는 Tag를 생성해서 부여했다.<br/>
GameManager오브젝트에 Bonus를 추가해준다.<br/>
하나의 코루틴으로 분기를 둬서 if나 switch로 제어 할수도 있지만 정식으로 출시할 게임 만들때 리펙토링 하기로하고 넘어간다.<br/>
</span>

![image](..\..\images\unity\catcher-prototype43.PNG)

![image](..\..\images\unity\catcher-prototype44.PNG)

<span style="font-size:13pt">
Bonus라는 스크립트를 만들어서 Obstacle과 똑같이 만들어준다.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Bonus : MonoBehaviour
{
    public GameObject go;

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "Ground")
        {
            Destroy(go);
        }

        if (collision.gameObject.tag == "Player")
        {
            Destroy(go);
        }
    }
}
```

![image](..\..\images\unity\catcher-prototype45.PNG)

<span style="font-size:13pt">
문제 없이 잘 나온다.<br/>
0.5초 1초 마다 나오게끔해서 두개가 동시에 떨어지는데 난수를 줘서 다르게 떨어지게 할수도 있지만 일단 프로토타입이고 귀찮으므로...<br/>
</span>

### 점수 카운트
---

<span style="font-size:13pt">
Score오브젝트에 ScoreController라는 스크립트를 생성하고 아래 소스를 넣는다.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class ScoreController : MonoBehaviour
{
    public static int score = 0;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        GetComponent<Text>().text = "Score : " + score.ToString();
    }
}

```

<span style="font-size:13pt">
GameController.cs<br/>
</span>

```c#
void Start()
    {
        StartCoroutine(CreateObstacleRoutine());
        StartCoroutine(CreateBonusRoutine());
        ScoreController.score = 0; // 코드 추가
    }
```

<span style="font-size:13pt">
Bonus.cs<br/>
</span>

```c#
private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "Ground")
        {
            Destroy(go);
        }

        if (collision.gameObject.tag == "Player")
        {
            Destroy(go);
            ScoreController.score++; // 코드 추가
        }
    }
```

<span style="font-size:13pt">
이러면 문제 없이 동작할 것이다.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
특정 낙하물에 닿으면 스코어 증가 끝 <br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [x] ~~<span style="font-size:13pt">메인 캐릭터 생성과 이동</span>~~
- [x] ~~<span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>~~
- [x] ~~<span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>~~
- [x] ~~<span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>~~
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>