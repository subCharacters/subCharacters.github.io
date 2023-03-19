---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 스폰"
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
date: 2023-03-18
last_modified_at: 2023-03-19
---

### 개요
---
> 몬스터 스폰 

<span style="font-size:13pt">
콜라이더 범위를 이용해서 몬스터를 스폰하는 법에 대해서 작성.<br/>
이외에도 월드좌표나 게임오브젝트의 위치를 받아서 하는 법도 있는데 콜라이더가 가장 간단한듯.<br/>
</span>

### 스폰 에리어 작성
---

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice01.png)

<span style="font-size:13pt">
2D Object -> Sprites -> Square로 이미지를 만든다.<br/>
네모난 범위보다는 일직선으로 랜덤하게 생성해 줄거니까 얇게 만들었다.<br/>
알기 쉽게 검은색으로 바꾸었다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice02.png)

<span style="font-size:13pt">
그리고 Inspector창에서 Box Collider 2D를 추가한다.<br/>
<br/>
</span>

### 스크립트 작성
---

<span style="font-size:13pt">
스크립트를 작성한다.<br/>
</span>

```c#
public class SpawnController : MonoBehaviour
{
    [SerializeField]
    private List<GameObject> mosterList; // 몬스터 리스트

    [SerializeField]
    private float spawnTerm; // 리스폰 텀

    private float localSpawnTerm; // 스폰 텀 클래스내 변수

    public Collider2D spawnArea; // 스폰 에리어

    void Start()
    {
        localSpawnTerm = spawnTerm;
    }

    void Update()
    {
        if (localSpawnTerm <= 0)
        {
            Spawn();
            localSpawnTerm = spawnTerm;
        }
        localSpawnTerm -= Time.deltaTime;
    }

    private void Spawn()
    {
        // 몬스터 생성
        Instantiate(mosterList[0], GetRandomSpawnPoint(), Quaternion.identity);
    }

    private Vector2 GetRandomSpawnPoint()
    {
        Vector2 spawnPoint = Vector2.zero;
        if (spawnArea != null)
        {
            // Collider2D 영역 내에서 랜덤한 위치를 생성
            spawnPoint = new Vector2(
                spawnArea.bounds.max.x,
                Random.Range(spawnArea.bounds.min.y, spawnArea.bounds.max.y)
            );
        }
        return spawnPoint;
    }
}
```

<span style="font-size:13pt">
SpawnArea의 Inspector창에서 스폰 텀 설정과 자기자신을 인자로 설정한다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice03.png)

### 충돌 설정
---
<span style="font-size:13pt">
내 프로젝트에서 몬스터는 콜라이더를 가지고 있다.<br/>
양쪽다 콜라이더를 가진 경우 부딪히며 서로 영향을 주는데 무시하도록 해야한다.<br/>
Inspector창에서 레이어를 추가하거나 설정한 후 아래와 같이 프로젝트 물리 설정 체크 된 곳을 잘보고 변경한다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice04.png)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice05.png)

### 결과
---

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice06.png)

<span style="font-size:13pt">
50여마리 정도 생성했는데 훌륭하게 잘 돌아간다.<br/>
파란색 박스는 신경쓰지 말자.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 스폰 끝<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [ ] <span style="font-size:13pt">몬스터 이동</span>
- [ ] <span style="font-size:13pt">몬스터 공격(근접)</span>
- [ ] <span style="font-size:13pt">몬스터 공격(원거리)</span>
- [ ] <span style="font-size:13pt">몬스터 스크립트 정리</span>
- [ ] <span style="font-size:13pt">벽에 데미지 주기</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>