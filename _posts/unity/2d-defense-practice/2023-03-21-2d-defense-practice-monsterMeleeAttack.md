---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 공격(근접)"
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
date: 2023-03-21
last_modified_at: 2023-03-21
---

### 개요
---
> 몬스터 공격(근접) 

<span style="font-size:13pt">
몬스터가 벽과 가까워지고 공격 가능한 범위가 되었을때 공격하도록 한다.<br/>
</span>

### 공격 모션 재생 스크립트 작성
---

<span style="font-size:13pt">
MonsterController 스크립트에 추가로 작성.<br/>
</span>

```c#
public class MonsterController : MonoBehaviour
{
    private Animator animator;

    [SerializeField]
    private float AtkCoolTime; // 공격 쿨타임
    private float localAtkCoolTime = 0.0f; // 로컬 공격 쿨타임
    private Transform wall;

    void Start()
    {
        animator = GetComponent<Animator>();
        wall = GameObject.FindGameObjectWithTag("Wall").transform; // tag이름으로 벽 찾기
    }

    // -> FixedUpdate에서 Update로 바꿨다. 처음엔 velocity로해서 물리연산update로 했는데 불필요해졌다.
    void Update()
    {
        if(IsWalk())
        {
            MonsterMove();
        }
        else
        {
            Attack();
        }
    }

    // 걷는지 유무 확인
    private bool IsWalk()
    {
        if (Vector2.Distance(wall.position, this.transform.position) <= 3.5f)
        {
            animator.SetBool("isWalk", false);
            return false;
        }
        animator.SetBool("isWalk", true);
        return true;
    }

    // 공격실행
    private void Attack()
    {
        if (AttackCoolTimeCheck())
        {
            animator.SetTrigger("attack-stab"); // 공격 모션 실행
        }
    }

    // 공격가능 쿨타임 체크
    private bool AttackCoolTimeCheck()
    {
        if (localAtkCoolTime <= 0)
        {
            localAtkCoolTime = atkCoolTime;
            return true;
        }
        localAtkCoolTime -= Time.deltaTime;
        return false;
    }

    // 몬스터 이동
    private void MonsterMove()  
    {
        transform.Translate(Vector2.left * 1.5f * Time.deltaTime);
    }
}
```

<span style="font-size:13pt">
공격 모션은 애니스테이트에서 트리거로 동작되게끔 해놨다.<br/>
공격을 할지 말지의 체크는 Vector2.Distance(wall.position, this.transform.position) <= 3.5f에서 분기를 탄다.<br/>
wall과의 거리가 3.5f이하일때 걷는게 취소되고 공격을 한다.<br/>
</span>

### Inspector 설정
---

<span style="font-size:13pt">
쿨타임 설정하고<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice11.PNG)

<span style="font-size:13pt">
wall에 태그도 설정.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice12.PNG)

<span style="font-size:13pt">
이러면 기본적으로 벽 앞에 도달하면 공격 모션을 한다.<br/>
하지만 정작 중요한 hit부분이 없기에 무기로 적을 맞추면 hit이라는 로그가 출력 되도록 해보자.<br/>
</span>


### 충돌 부분 설정
---
<span style="font-size:13pt">
먼저 스크립트에 추가 필요한 부분 추가.<br/>
</span>

```c#
public class MonsterController : MonoBehaviour
{
    private Animator animator;

    [SerializeField]
    private float AtkCoolTime; // 공격 쿨타임
    private float localAtkCoolTime = 0.0f; // 로컬 공격 쿨타임

    [SerializeField]
    private Transform hitBoxPos; // 근접 공격 히트 박스 포지션
    [SerializeField]
    private Vector2 hitBoxSize; // 근접 공격 히트 박스 크기

    private Transform wall;

    void Start()
    {
        animator = GetComponent<Animator>();
        wall = GameObject.FindGameObjectWithTag("Wall").transform; // tag이름으로 벽 찾기
    }

    // -> FixedUpdate에서 Update로 바꿨다. 처음엔 velocity로해서 물리연산update로 했는데 불필요해졌다.
    void Update()
    {
        if(IsWalk())
        {
            MonsterMove();
        }
        else
        {
            AttackMotion();
        }
    }

    // 걷는지 유무 확인
    private bool IsWalk()
    {
        if (Vector2.Distance(wall.position, this.transform.position) <= 3.5f)
        {
            animator.SetBool("isWalk", false);
            return false;
        }
        animator.SetBool("isWalk", true);
        return true;
    }

    // 공격 모션 실행
    private void AttackMotion()
    {
        if (AttackCoolTimeCheck())
        {
            animator.SetTrigger("attack-stab");
        }
    }

    // 공격가능 쿨타임 체크
    private bool AttackCoolTimeCheck()
    {
        if (localAtkCoolTime <= 0)
        {
            localAtkCoolTime = atkCoolTime;
            return true;
        }
        localAtkCoolTime -= Time.deltaTime;
        return false;
    }

    // 몬스터 이동
    private void MonsterMove()  
    {
        transform.Translate(Vector2.left * 1.5f * Time.deltaTime);
    }

    // 근접 공격 히트 판정 계산
    public void MeleeAttack()
    {
        Collider2D[] collider2Ds = Physics2D.OverlapBoxAll(hitBoxPos.position, hitBoxSize, 0); // 특정 범위안에 있는 충돌체들을 감지
        foreach (Collider2D collider in collider2Ds)
        {
            if (collider.tag == "Wall")
            {
                Debug.Log("Hit");
            }
        }
    }

    private void OnDrawGizmos()
    {
        Gizmos.color = Color.blue;
        Gizmos.DrawWireCube(hitBoxPos.position, hitBoxSize);
    }
}
```

<span style="font-size:13pt">
비어있는 object를 하위에 만들고 위치를 공격 모션했을때 무기 부분으로 맞춰주면 된다.<br/>
MeleeAttack()는 애니메이션 이벤트에서 불러서 사용할 것이다.<br/>
Inspector와 애니메이션 부분도 맞춰준다.<br/>
애니메이션 이벤트는 원하는 프레임에서 우클릭하면 만들 수 있다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice13.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice14.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice15.PNG)


### 결과
---

<span style="font-size:13pt">
실행 해보면 문제 없이 돌아가는 듯 하다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice16.PNG)


### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 공격(근접)<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [ ] <span style="font-size:13pt">몬스터 공격(원거리)</span>
- [ ] <span style="font-size:13pt">몬스터 스크립트 정리</span>
- [ ] <span style="font-size:13pt">벽에 데미지 주기</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>