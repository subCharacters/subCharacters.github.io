---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 공격(원거리)"
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
date: 2023-03-22
last_modified_at: 2023-03-22
---

### 개요
---
> 몬스터 공격(원거리) 

<span style="font-size:13pt">
몬스터가 공격 가능한 범위가 되었을때 투사체를 발사해서 공격하도록 한다.<br/>
투사체는 포물선을 그린다.<br/>
</span>

### 공격 모션 재생 스크립트 작성
---

<span style="font-size:13pt">
기존 스크립트에 추가하고 새로운 스크립트 하나를 더 추가한다.<br/>
MonsterController 스크립트에 추가로 작성.<br/>
Arrow스크립트는 신규로 생성했다.<br/>
</span>

```c#
public class MonsterController : MonoBehaviour
{
    private Animator animator;

    [SerializeField]
    private float AtkCoolTime; // 공격 쿨타임
    private float localAtkCoolTime = 0.0f; // 로컬 공격 쿨타임
    private Transform wall;

    public GameObject frontItem; // 원거리 공격 투사체 생성 장소
    [SerializeField]
    private GameObject arrow; // 투사체 오브젝트

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
    // TODO 리펙토링
    private bool IsWalk()
    {
        // 원거리형 몬스터 일 경우
        if (this.gameObject.name == "Skeleton_bow")
        {
            if (Vector2.Distance(wall.position, this.transform.position) <= 8.5f)
            {
                animator.SetBool("isWalk", false);
                return false;
            }
            animator.SetBool("isWalk", true);
            return true;
        }

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
            if (this.gameObject.name == "Skeleton_bow")
            {
                animator.SetTrigger("attack-bow"); // 공격 모션 실행
            }
            else
            {
                animator.SetTrigger("attack-stab"); // 공격 모션 실행
            }
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

    // 사격
    public void Fire()
    {
        GameObject t_arrow = Instantiate(arrow, new Vector2(frontItem.transform.position.x, frontItem.transform.position.y), frontItem.transform.rotation);
    }
}
```

<span style="font-size:13pt">
frontItem은 투사체가 발사 될 곳이다.<br/>
모델링에 화살이 생성되는 부분이라 사용한 것이고 없다면 빈 오브젝트를 원하는 위치에 생성하거나 하면 될 듯.<br/>
</span>

```c#
public class Arrow : MonoBehaviour
{
    Rigidbody2D rigidbody;
    public float arrowSpeed = 0.0f;

    void Start()
    {
        Invoke("DestroyArrow", 1.2f); // 생성 후 1.2초뒤에 삭제
        rigidbody = GetComponent<Rigidbody2D>();
        rigidbody.AddForce(transform.position * arrowSpeed * -1);
    }

    void FixedUpdate()
    {
        transform.up = rigidbody.velocity;
    }

    // 투사체 삭제
    void DestroyArrow()
    {
        Destroy(gameObject);
    }
}
```

<span style="font-size:13pt">
AddForce로 힘을 가해 앞으로 나아가도록 했고 -1은 왼쪽으로 가도록 하기 위해서 설정.<br/>
transform.up = rigidbody.velocity;를 줘서 앞쪽에 힘을 주어 고꾸라지도록 해주었다.<br/>
고꾸라지는 건 모델링 방향에 따라 다름.<br/>
float angle = Mathf.Atan2(rigidbody.velocity.y, rigidbody.velocity.x) * Mathf.Rad2Deg;<br/>
transform.eulerAngles = new Vector3(0, 0, angle)<br/>
이런식으로 호도법?을 이용하여 앞뒤가 원을 그리면서 움직이게 하는 법도 있는듯하나 잘 안됐다.<br/>
</span>

### Inspector 설정
---

<span style="font-size:13pt">
투사체 쪽 설정<br/>
투사체가 될 이미지를 프리팹으로 만들고 Arrow스크립트와 rigidboby2d를 컴포넌트로 추가한다.<br/>
그리고 투사체의 스피드도 적절하게 설정.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice17.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice18.PNG)

<span style="font-size:13pt">
몬스터 쪽에 투사체 생성위치와 투사체 프리팹을 넣어준다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice19.PNG)

<span style="font-size:13pt">
근접 공격때 보다는 좀 더 먼거리에서 공격을 하고 있다.<br/>
원거리도 마찬가지로 투사체가 벽에 맞으면 hit이라는 로그가 출력 되도록하고 맞는 순간 사라지도록 만들어보자.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice20.PNG)


### 충돌 부분 설정
---
<span style="font-size:13pt">
스크립트에 추가 필요한 부분 추가.<br/>
필드와 Hit함수가 추가 되었다.<br/>
</span>

```c#
public class Arrow : MonoBehaviour
{
    [SerializeField]
    private float distance;
    [SerializeField]
    private LayerMask isLayer;

    Rigidbody2D rigidbody;
    public float arrowSpeed = 0.0f;

    void Start()
    {
        Invoke("DestroyArrow", 1.2f);
        rigidbody = GetComponent<Rigidbody2D>();
        rigidbody.AddForce(transform.position * arrowSpeed * -1);
    }

    void FixedUpdate()
    {
        transform.up = GetComponent<Rigidbody2D>().velocity;
        transform.up = rigidbody.velocity;
        Hit();
    }

    // 투사체가 맞았는지 검사.
    private void Hit()
    {
        RaycastHit2D ray = Physics2D.Raycast(transform.position, transform.up, distance, isLayer);
        if (ray.collider != null)
        {
            if (ray.collider.tag == "Wall")
            {
                Debug.Log("Hit");
                DestroyArrow();
            }
        }
    }

    // 투사체 삭제
    void DestroyArrow()
    {
        Destroy(gameObject);
    }
}
```

<span style="font-size:13pt">
Raycast로 일종의 레이저를 쏘아서 맞은 곳의 충돌값을 반환한다.<br/>
현재 위치에서 y방향으로 거리만큼 레이저를 쏘고 맞은게 isLayer면 ray를 반환한다.<br/>
혹시나 하는 마음에 tag체크도 넣었다.<br/>
중심부에서 레이저를 쏘는거라 화살촉과의 거리가 있어서 거리를 조금 주었다.<br/>
맞고 나면 화살이 사라지게도 했는데 그대로 박히는 걸 하고 싶다면 이프문으로 진행을 막으면 될듯.<br/>
Instector창에는 태그와 거리를 지정해주면 끝.<br/>
</span>

### 결과
---

<span style="font-size:13pt">
실행 해보면 문제 없이 돌아가는 듯 하다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice21.PNG)


### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 공격(원거리)<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [ ] <span style="font-size:13pt">몬스터 스크립트 정리</span>
- [ ] <span style="font-size:13pt">벽에 데미지 주기</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>