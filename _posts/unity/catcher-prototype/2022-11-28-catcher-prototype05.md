---
layout: single
title: "캐쳐 프로토타입 05 제작 기록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - prototype
toc: true
toc_sticky: true
date: 2022-11-28
last_modified_at: 2022-11-29
---

### 장애물 오브젝트 삭제
---

![image](..\..\images\unity\catcher-prototype28.PNG)

![image](..\..\images\unity\catcher-prototype29.PNG)

<span style="font-size:13pt">
생성한 오브젝트가 지면, 메인 캐릭터에 충돌했을 경우 삭제를 하기 위해서는 해당 오브젝트들에 tag를 만들 필요가 있다.<br/>
장애물, 메인 캐릭터, 지면에 각각 태그를 만들어주자.<br/>
</span>

![image](..\..\images\unity\catcher-prototype30.PNG)

<span style="font-size:13pt">
Script를 생성하여 Prefabs Obstacle에 추가하고 아래의 내용을 코딩하고 go변수에 Prefabs를 넣어주자.<br/>
collision.gameObject.tag == "Ground"는 보면 알겠지만 충돌한 오브젝트의 태크를 취득해서 비교한다.<br/>
OnCollisionEnter2D() 함수는 오브젝트가 충돌하면 실행하는 오브젝트이다.<br/>
</span>


```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Obstacle : MonoBehaviour
{
    public GameObject go;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

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

![image](..\..\images\unity\catcher-prototype31.PNG)

<span style="font-size:13pt">
더 이상 지면에 쌓이지 않고 없어진다.<br/>
</span>

### 게임 오버 화면 만들기
---

<span style="font-size:13pt">
게임 오버에는 2가지 정도 방법이 있다.<br/>
현재 씬에서 UI를 띄우는 방법과 씬 이동으로 종료하는 법.<br/>
프로토 타입은 씬 교체로 종료하는 법으로 코딩 할 예정.<br/>
</span>

![image](..\..\images\unity\catcher-prototype32.PNG)

<span style="font-size:13pt">
File -> New Scene -> 2D -> Create로 새로운 씬을 생성.<br/>
clrt + s로 이름 지정하면서 저장.<br/>
</span>

![image](..\..\images\unity\catcher-prototype33.PNG)

![image](..\..\images\unity\catcher-prototype34.PNG)

![image](..\..\images\unity\catcher-prototype35.PNG)

![image](..\..\images\unity\catcher-prototype36.PNG)

<span style="font-size:13pt">
우클릭 -> UI -> Canvas 오브젝트 생성<br/>
Canvas 우클릭 -> UI -> Panel 생성<br/>
Inspector -> Background -> 색상 변경.<br/>
Panel 우클릭 -> UI -> Text<br/>
Panel 우클릭 -> UI -> Button<br/>
Text와 Button은 위 이미지를 참조<br/>
참고로 Anchors는 화면 비율에 맞춰서 생성해 주는 설정이다.<br/>
</span>

### 게임 오버 씬으로 전환
---

<span style="font-size:13pt">
메인 씬으로 넘어온다.<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class Obstacle : MonoBehaviour
{
    public GameObject go;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "Ground")
        {
            Destroy(go);
        }

        if (collision.gameObject.tag == "Player")
        {
            Destroy(go);
            SceneManager.LoadScene("GameOverScene");
        }
    }
}

```

<span style="font-size:13pt">
Obstacle 클래스에 Player와 부딪혔을 경우에 게임 오버가 되므로 SceneManager.LoadScene("GameOverScene");를 추가해준다.<br/>
</span>

### 재시작 하기
---

![image](..\..\images\unity\catcher-prototype37.PNG)

<span style="font-size:13pt">
GameOver씬으로 넘어와서 우클릭 -> Create Empty로 빈 오브젝트를 생성해준다.<br/>
생성한 빈 오브젝트에 Add Component로 새로운 스크립트 Retry를 생성하고 아래 코드를 입력해준다.<br/>
<br/>
</span>

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class Retry : MonoBehaviour
{
    public void DoRetry()
    {
        SceneManager.LoadScene("MainScene");
    }
}

```

<span style="font-size:13pt">
코드 내용은 단순하다. 반대로 씬을 메인으로 넘겨주기만 하면 끝.<br/>
</span>

![image](..\..\images\unity\catcher-prototype38.PNG)

![image](..\..\images\unity\catcher-prototype39.PNG)

<span style="font-size:13pt">
코드를 추가 했으면 생성한 빈 오브젝트(ReTry)에 스크립트가 들어가 있을 것이다.<br/>
스크립트가 추가된 ReTry오브젝트를 Canvas -> Panel -> reTryButton의 Inspector -> Button -> onClick()에서 +를 누르고 오브젝트를 끌어다 추가한다.<br/>
그리고 Runtime Only옆에 No Function을 클릭해서 리스트에서 해당 스크립트를 선택 하면 선택할 수 있는 변수나 함수가 나온다.<br/>
위에서 추가한 DoRetry()를 선택한다.<br/>
MainScene으로 가서 게임을 실행하면 씬 변경에는 문제가 없다.<br/>
</span>

### 버그 수정
---

<span style="font-size:13pt">
처음에 문제없이 잘 왔다갔다 이동이 되는데 메인 씬으로 돌아오고 나서 마우스 클릭 위치와 상관없이 왼쪽으로만 이동하는 버그가 있어서 수정했다.<br/>
너비 취득에 문제가 있는 듯 하여 <span style="font-size:15pt; font-weight: bold;">MainCharacter</span> 스크립트의 void Start() 안에 scWidth = Screen.width / 2;를 추가하여 처음 시작할때 화면 너비를 취득하도록 변경하였다.<br/>
</span>

```c#
void Start()
    {
        rigidbody2D = GetComponent<Rigidbody2D>();
        scWidth = Screen.width / 2; // 추가 코드
    }

```

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
특정 낙하물에 닿으면 게임 종료 끝 <br/>
</span>

- [x] ~~<span style="font-size:13pt">맵 생성</span>~~
- [x] ~~<span style="font-size:13pt">메인 캐릭터 생성과 이동</span>~~
- [x] ~~<span style="font-size:13pt">낙하물 생성하여 중력을 구현</span>~~
- [x] ~~<span style="font-size:13pt">특정 낙하물에 닿으면 게임 종료</span>~~
- [ ] <span style="font-size:13pt">특정 낙하물에 닿으면 스코어 증가</span>
- [ ] <span style="font-size:13pt">게임 종료 화면 구현</span>
- [ ] <span style="font-size:13pt">터치 시 게임 스타트</span>