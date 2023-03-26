---
layout: single
title: "2d디펜스 게임 연습 - 벽에 데미지 주기"
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
> 벽에 데미지 주기

<span style="font-size:13pt">
몬스터가 벽을 공격해서 파괴되도록 만든다.<br/>
</span>

### Wall스크립트작성
---

<span style="font-size:13pt">
Wall이라는 스크립트를 작성하고 hp와 takeDamage함수를 넣어서 벽의 정보를 담는 클래스를 작성.<br/>
</span>

```c#
public class Wall : MonoBehaviour
{

    [SerializeField]
    private int hp;

    public void TakeDamage(int damage)
    {
        hp -= damage;
        if (hp <= 0)
        {
            Destroy(this.gameObject);
        }
    }
}
```

### Monster스크립트수정

<span style="font-size:13pt">
벽의 데미지가 0이되면 wall의 게임오브젝트가 파괴된다.<br/>
IsWalk()부분에 벽의 위치를 계산하는 부분이 있는데 여기에서 null pointer가 난다.<br/>
IsWalk() 부분을 수정.<br/>
</span>

```c#
abstract public class Monster : MonoBehaviour
{
    ....중략

    protected bool IsWalk()
    {
        if (wall != null)
        {
            if (Vector2.Distance(wall.position, this.transform.position) <= attackRange)
            {
                animator.SetBool("isWalk", false);
                return false;
            }
        }
        animator.SetBool("isWalk", true);
        return true;
    }

    ....중략
}
```

### MeleeMonsterController스크립트수정

<span style="font-size:13pt">
hit된 collider에서 컴포넌트 Wall스크립트를 가져와 TakeDamage함수를 호출한다.<br/>
그럼 알아서 계산되서 hp가 깎일것.<br/>
</span>

```c#
public class MeleeMonsterController : Monster
{
    ... 중략

    public void MeleeAttack()
    {
        Collider2D[] collider2Ds = Physics2D.OverlapBoxAll(hitBoxPos.position, hitBoxSize, 0);
        foreach (Collider2D collider in collider2Ds)
        {
            if (collider.tag == "Wall")
            {
                collider.GetComponent<Wall>().TakeDamage(attackDamage);
                Debug.Log("Hit");
            }
        }
    }
}
```

### Inspector 설정
---

<span style="font-size:13pt">
벽 오브젝트에 wall 스크립트를 추가하고 hp를 설정한다.   <br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice25.PNG)

### 결과
---

<span style="font-size:13pt">
hp가 0이되면 벽이 사라지고 벽이 사라지면 몬스터는 앞으로 이동한다.<br/>
몬스터의 데미지 설정은 이전 리팩토링 포스팅에 있으니 별도로 셋팅은 안해도 돌아감.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice26.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice27.PNG)

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
벽에 데미지 주기<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 스크립트 정리~~</span>
- [x] <span style="font-size:13pt">~~벽에 데미지 주기~~</span>
- [ ] <span style="font-size:13pt">플레이어 공격</span>
- [ ] <span style="font-size:13pt">몬스터 사냥</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>