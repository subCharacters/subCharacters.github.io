---
layout: single
title: "한 기점을 중심으로 돌기"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - 3d
  - RotateAround
toc: true
toc_sticky: true
date: 2023-04-06
last_modified_at: 2023-04-06
---

### 개요
---
> 한 기점을 중심으로 돌기

<span style="font-size:13pt">
헬기나 비행기가 한 기점을 기준으로 빙빙 도는 로직을 구현해본다.<br/>
유니티 버전은 2021.3.23f1버전이다.
</span>

### 구현
---

<span style="font-size:13pt">
먼저 중심이 되는 기점을 빈 오브젝트로 생성한다.<br/>
스크립트를 생성한다.<br/>
</span>

```c#
public class RotateAround : MonoBehaviour
{
    public Transform centerPoint; // 기점 오브젝트
    public float rotationSpeed = 0.3f; // 도는 스피드

    void Update()
    {
        float rotationAngle = rotationSpeed * Time.deltaTime;
        transform.RotateAround(centerPoint.position, Vector3.up * -1, rotationAngle); // 중심으로 도는 함수
    }
}

```

<span style="font-size:13pt">
움직일 오브젝트에 해당 스크립트를 추가하고 centerPoint에 기점 오브젝트를 넣어주면 된다.<br/>
</span>

### 결과
---

![image](..\..\images\unity\unity-tech\unity-tech-RotateAround.gif)
