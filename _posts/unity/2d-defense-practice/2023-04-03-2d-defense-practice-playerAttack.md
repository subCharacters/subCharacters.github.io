---
layout: single
title: "2d디펜스 게임 연습 - 플레이어 공격"
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
date: 2023-04-03
last_modified_at: 2023-04-04
---

### 개요
---
> 플레이어 공격

<span style="font-size:13pt">
몬스터가 플레이어 사정거리 안에 들어오면 자동으로 공격하기.<br/>
</span>

### 오브젝트 생성
---

<span style="font-size:13pt">
플레이어가 될 유닛을 준비한다.<br/>
그리고 투사체가 발사될 부분에 빈 객체를 생성해서 위치를 조정해준다.<br/>
투사체도 프리팹으로 만들어준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice28.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice29.PNG)

### PlayerController와 Ball스크립트작성
---

<span style="font-size:13pt">
PlayerController와 Ball 스크립트 두개가 필요하다.<br/>
</span>

```c#
public class PlayerController : MonoBehaviour
{
    [SerializeField]
    private int attackDamage;

    [SerializeField]
    private float attackSpeed;

    [SerializeField]
    private float attackRange;

    public LayerMask targetLayer;

    public RaycastHit2D[] targets;

    public Transform nearestTarget;

    [SerializeField]
    private GameObject ballObj;

    [SerializeField]
    private Transform skillParts;

    private Animator anim;

    float coolTime;

    void Awake()
    {
        anim = GetComponent<Animator>();
        coolTime = attackSpeed;
    }

    private void FixedUpdate()
    {
        // 캐스팅 시작위치, 원의 반지름, 캐스팅 방향, 캐스팅 길이, 대상 레이어
        targets = Physics2D.CircleCastAll(transform.position, attackRange, Vector2.zero, 0, targetLayer);
        nearestTarget = GetNearest();

        if (nearestTarget != null)
        {
            if (coolTime <= 0)
            {
                anim.SetTrigger("attack");
                coolTime = attackSpeed;
            }
        }
        coolTime -= Time.deltaTime;
    }

    // 가장 가까운 유닛 검색
    Transform GetNearest()
    {
        Transform result = null;
        float diff = 100;
        foreach (RaycastHit2D target in targets)
        {
            Vector3 playerPos = transform.position;
            Vector3 targetPos = target.transform.position;
            float curDiff = Vector3.Distance(playerPos, targetPos);

            if (curDiff < diff)
            {
                diff = curDiff;
                result = target.transform;
            }
        }

        return result;
    }

    private void OnDrawGizmos() // 기모즈로 범위 확인 가능.
    {
        /*RaycastHit2D raycastHit = Physics2D.CircleCast(transform.position, scanRange, Vector2.zero, 0, targetLayer);
        Gizmos.color = Color.red;
        if (raycastHit.collider != null)
        {
            Gizmos.DrawRay(transform.position, Vector2.down * raycastHit.distance);
            Gizmos.DrawSphere(transform.position + Vector3.down * raycastHit.distance, 15);
        }*/
    }

    public void PlayerAttack()
    {
        Vector3 targetPos = nearestTarget.position;
        Vector3 dir = targetPos - transform.position;
        dir = dir.normalized;
        GameObject ball = Instantiate(ballObj, new Vector2(skillParts.position.x, skillParts.position.y), transform.rotation);
        ball.GetComponent<Ball>().init(attackDamage, dir);
    }
}
```

```c#
public class Ball : MonoBehaviour
{

    public int damage;

    Rigidbody2D rigid;

    void Awake()
    {
        rigid = GetComponent<Rigidbody2D>();
    }

    public void init(int damage, Vector3 dir)
    {
        this.damage = damage;
        rigid.velocity = dir * 15f; // 투사체 속도
    }

    void OnTriggerEnter2D(Collider2D collision)
    {
        Debug.Log(collision.tag);
        if (collision.CompareTag("Monster"))
        {
            Destroy(gameObject);
        }
    }
}

```


### Inspector 설정
---

<span style="font-size:13pt">
플레이어에는 PlayerController를 넣어주고 공격 모션 애니메이션에서 적당한 프레임에 이벤트를 넣어서 PlayerAttack()를 실행 시켜준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice30.PNG)

<span style="font-size:13pt">
에너지 볼에는 Ball스크립트와 RigidBody2D, Circle Collider 2D를 추가해준다.<br/>
RigidBody2D에는 gravity scale을 0으로 해준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice31.PNG)

<span style="font-size:13pt">
몬스터에게는 태그를 달아준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice32.PNG)

### 결과
---

<span style="font-size:13pt">
잘 동작 되는 듯 하다.<br/>
다음에는 몬스터에게 데미지를 주고 몬스터가 죽는거까지.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice33.gif)

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
플레이어 공격 완료<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 스크립트 정리~~</span>
- [x] <span style="font-size:13pt">~~벽에 데미지 주기~~</span>
- [x] <span style="font-size:13pt">~~플레이어 공격~~</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>