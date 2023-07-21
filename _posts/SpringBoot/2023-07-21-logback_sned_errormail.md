---
layout: single
title: "2d디펜스 게임 연습 - 보스 스테이지"
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
date: 2023-04-14
last_modified_at: 2023-04-14
---

### 개요
---
> 보스 스테이지

<span style="font-size:13pt">
거창하게 보스 스테이지라고 했지만 간단하게 보스가 스폰되는 것만 할 예정.<br/>
보스가 공격하는 것은 근접 몬스터의 재탕이라 새로운것을 공부하기엔 별로 인듯해서 등장씬까지만.<br/>
일반 몬스터가 일정 수 이상 나오게 되면 보스몹이 등장하고 카메라는 보스 몹을 보여준다.<br/>
특정 모션을 보여 준 후 벽으로 진격 한다.<br/>
</span>

### 스크립트
---

<span style="font-size:13pt">
Boss라는 스크립트를 만들어서 제작.<br/>
</span>

```c#
public class Boss : Monster
{
    Camera mainCamera;

    public Camera bossCamera;

    private int motionCount = 1;

    private Animator animator;

    bool isEndBossSpawnScene = false;

    void Start()
    {
        mainCamera = Camera.main;
        mainCamera.enabled = false;
        bossCamera.enabled = true;
        animator = gameObject.GetComponent<Animator>();
        animator.SetTrigger("attack-smash");
        StartCoroutine("BossSpawnScene");
        base.Start();
    }

    void Update()
    {
        if (isEndBossSpawnScene)
        {
            if (IsWalk())
            {
                MonsterMove();
            }
        }
    }

    IEnumerator BossSpawnScene()
    {
        Animator animator = gameObject.GetComponent<Animator>();
        AnimatorStateInfo stateInfo = animator.GetCurrentAnimatorStateInfo(0);
        if (stateInfo.normalizedTime >= 1.0f && stateInfo.IsName("attack-smash"))
        {
            if (motionCount >= 2)
            {
                SetCamera();
                // 레이어 조정으로 Hit판정을 주게 하려고 레이어 변경.
                // 보스의 카메라로 Boss레이어 이외에는 안보이게 했으므로.
                gameObject.layer = 6; 
                isEndBossSpawnScene = true;
                StopCoroutine("BossSpawnScene");
                yield break;
            }
            motionCount++;
            animator.SetTrigger("attack-smash");
        }
        yield return new WaitForSeconds(0.1f);
        StartCoroutine("BossSpawnScene");
    }
    
    private void SetCamera()
    {
        mainCamera.enabled = true;
        bossCamera.enabled = false;
    }

    protected override void AttackMotion()
    {
        throw new System.NotImplementedException();
    }
}
```

### Inspector 설정
---

<span style="font-size:13pt">
보스몹 프리팹을 생성하고 안쪽에 카메라를 생성해서 넣어준다.<br/>
카메라 설정은 이미지와 같이.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice40.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice41.PNG)

<span style="font-size:13pt">
보스몹이 될 프리팹에 컴포넌트를 추가한다.<br/>
애니메이션의 트랜지션은 적절히 설정해주면 된다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice42.PNG)

### 결과
---

<span style="font-size:13pt">
컷씬등으로 만들면 더 좋겠지만 아직 실력이 안된다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice43.gif)

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
보스 스테이지 완료.<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 스크립트 정리~~</span>
- [x] <span style="font-size:13pt">~~벽에 데미지 주기~~</span>
- [x] <span style="font-size:13pt">~~플레이어 공격~~</span>
- [x] <span style="font-size:13pt">~~몬스터 사냥~~</span>
- [x] <span style="font-size:13pt">~~플레이어 스킬 공격~~</span>
- [x] <span style="font-size:13pt">~~보스 스테이지~~</span>