---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 이동"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - 2d
  - defense
toc: true
toc_sticky: true
date: 2023-03-19
last_modified_at: 2023-03-19
---

### 개요
---
> 몬스터 이동 

<span style="font-size:13pt">
스폰된 몬스터가 벽이 있는 곳으로 이동하는 부분이다.<br/>
벽의 위치를 지정해서 이동하는 건 아니고 x좌표 왼쪽으로 위치값을 변경해서 이동 시켜주는 것 뿐이다.<br/>
</span>

### 벽 작성
---

<span style="font-size:13pt">
벽도 마찬가지로 2D Object -> Sprites -> Square로 이미지를 만든다.<br/>
네모나게 만들고 흰색으로 만들었다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice07.PNG)

<span style="font-size:13pt">
이런 식으로 적당히 만들어 둔다.<br/>
</span>

### 스크립트 작성
---

<span style="font-size:13pt">
스크립트를 작성한다.<br/>
</span>

```c#
public class MonsterController : MonoBehaviour
{
    private Animator animator;

    void Start()
    {
        animator = GetComponent<Animator>();
        animator.SetBool("isWalk", true);
    }

    void FixedUpdate()
    {
        MonsterMove();
    }

    private void MonsterMove()  
    {
        transform.Translate(Vector2.left * 1.5f * Time.deltaTime);
    }
}
```

<span style="font-size:13pt">
해당 스크립트를 몬스터의 Inspector창 component로 넣어준다.<br/>
애니메이션은 되어있다고 가정한다.<br/>
isWalk가 참이면 idle에서 walk모션이 나오도록 되어 있다.<br/>    
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice08.PNG)

<span style="font-size:13pt">
그리고 walk모션은 loop설정 해두었다.<br/>    
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice10.PNG)

### 충돌 설정
---
<span style="font-size:13pt">
이렇게하면 몬스터끼리 충돌이 일어나므로 같은 Monster레이어끼리는 무시하도록 설정해준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice04.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice05.PNG)

### 결과
---

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice09.PNG)

<span style="font-size:13pt">
매우 간단하게 끝이 났다.<br/>
이동하는 건 프로토타입에서도 해봤고 그걸 바탕으로 앱도 만들어서 출시도 해봤어서 별 문제 없이 끝났다.<br/>
물리력을 이용해서 이동해보려고 velocity로 해보려고 했으나 deltatime이 잘 안먹혀서 편한 Translate로 했다.<br/>
다음엔 몬스터가 이동 후 공격범위에 벽이 있으면 공격(근접)하는 걸 연습해야겠다.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 이동 끝<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [ ] <span style="font-size:13pt">몬스터 공격(근접)</span>
- [ ] <span style="font-size:13pt">몬스터 공격(원거리)</span>
- [ ] <span style="font-size:13pt">몬스터 스크립트 정리</span>
- [ ] <span style="font-size:13pt">벽에 데미지 주기</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>