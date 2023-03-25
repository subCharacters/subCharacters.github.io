---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 스크립트 정리"
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
date: 2023-03-25
last_modified_at: 2023-03-25
---

### 개요
---
> 몬스터 스크립트 정리

<span style="font-size:13pt">
몬스터가 현재 근거리와 원거리로 나뉘어져있어서 코드가 조금 지저분하다.<br/>
앞으로 몬스터가 추가 될 것도 감안해서 몬스터 공통 부분과 근접 원거리로 나눠서 따로 클래스를 만들어본다.<br/>
</span>

### Monster스크립트작성
---

<span style="font-size:13pt">
Monster라는 스크립트를 만들고 공통 변수와 함수를 넣어줘서 관리하는 걸로 한다.<br/>
추후에 벽을 공격하고 몬스터가 죽는 것도 구현할거기에 미리 hp와 attackDamage를 추가해준다.<br/>
SetAttackAnimationSpeed()도 새로 추가해서 공격 속도당 애니메이션 모션을 빨리 재생하도록 해줬다.<br/>
</span>

```c#
abstract public class Monster : MonoBehaviour
{
    [SerializeField]
    protected int hp;
    [SerializeField]
    protected float moveSpeed;
    [SerializeField]
    protected float attackDamage;
    [SerializeField]
    protected float attackRange;
    [SerializeField]
    protected float attackCoolTime;

    protected Animator animator;
    protected Transform wall;
    private float localAttackCoolTime = 0f;

    protected void Start()
    {
        animator = GetComponent<Animator>();
        wall = GameObject.FindGameObjectWithTag("Wall").transform;
    }

    protected bool IsWalk()
    {
        if (Vector2.Distance(wall.position, this.transform.position) <= attackRange)
        {
            animator.SetBool("isWalk", false);
            return false;
        }
        animator.SetBool("isWalk", true);
        return true;
    }

    protected void MonsterMove()
    {
        transform.Translate(Vector2.left * moveSpeed * Time.deltaTime);
    }

    abstract protected void AttackMotion();

    protected void SetAttackAnimationSpeed()
    {
        if (attackCoolTime <= 1f) 
        {
            animator.SetFloat("attackSpeed", 1f / attackCoolTime);
        }
        else
        {
            animator.SetFloat("attackSpeed", 1f);
        }
    }

    protected bool AttackCoolTimeCheck()
    {
        if (localAttackCoolTime <= 0)
        {
            localAttackCoolTime = attackCoolTime;
            return true;
        }
        localAttackCoolTime -= Time.deltaTime;
        return false;
    }
}
```

### MeleeMonsterController스크립트작성

<span style="font-size:13pt">
Monster를 부모클래스로 상속 받는다.<br/>
왠만한 변수와 함수는 부모클래스에서 상속 받고 부모클래스가 해주기에 근접 공격 하는 몬스터만 사용하는 로직을 구현한다.<br/>
</span>

```c#
public class MeleeMonsterController : Monster
{
    [SerializeField]
    private Transform hitBoxPos; // 근접 공격 히트 박스 포지션
    [SerializeField]
    private Vector2 hitBoxSize; // 근접 공격 히트 박스 크기

    void Start()
    {
        base.Start();
    }

    void Update()
    {
        if (IsWalk())
        {
            MonsterMove();
        }
        else
        {
            AttackMotion();
        }
    }

    override protected void AttackMotion()
    {
        if (AttackCoolTimeCheck())
        {
            SetAttackAnimationSpeed();
            animator.SetTrigger("attack-stab");
        }
    }

    public void MeleeAttack()
    {
        Collider2D[] collider2Ds = Physics2D.OverlapBoxAll(hitBoxPos.position, hitBoxSize, 0);
        foreach (Collider2D collider in collider2Ds)
        {
            if (collider.tag == "Wall")
            {
                Debug.Log("Hit");
            }
        }
    }
}

```

### RangedMonsterController스크립트작성

<span style="font-size:13pt">
Monster를 부모클래스로 상속 받는다.<br/>
마찬가지로 원거리 몬스터가 갖는 특징을 구현해준다.<br/>
</span>

```c#
public class RangedMonsterController : Monster
{
    public GameObject frontItem; // 원거리 공격 투사체 생성 장소
    [SerializeField]
    private GameObject arrow; // 투사체 오브젝트

    void Start()
    {
        base.Start();
    }


    void Update()
    {
        if (IsWalk())
        {
            MonsterMove();
        }
        else
        {
            AttackMotion();
        }
    }

    protected override void AttackMotion()
    {
        if (AttackCoolTimeCheck())
        {
            SetAttackAnimationSpeed();
            animator.SetTrigger("attack-bow");
        }
    }

    public void RangedAttack()
    {
        GameObject t_arrow = Instantiate(arrow, new Vector2(frontItem.transform.position.x, frontItem.transform.position.y), frontItem.transform.rotation);
    }
}
```

### Inspector 설정
---

<span style="font-size:13pt">
근접, 원거리 몬스터에 스크립트를 새로 추가해주고 앞에서 했던 셋팅을 똑같이 해준다.<br/>
새로 추가된 부분은 적당히 값을 추가해주면 될 듯.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice22.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice23.PNG)

<span style="font-size:13pt">
필요한 부분만 설정되어서 깔끔해진듯하다.<br/>
애니매이션 이벤트 부분도 잊지말고 새로운 함수로 설정하자.<br/>
애니메이터도 아래와 같이 설정해주면 애니메이션 속도 설정이 가능하다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice24.PNG)

### 결과
---

<span style="font-size:13pt">
이전과 똑같이 돌아가는 듯.<br/>
</span>

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 공격(원거리)<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 스크립트 정리~~</span>
- [ ] <span style="font-size:13pt">벽에 데미지 주기</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>