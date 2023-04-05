---
layout: single
title: "2d디펜스 게임 연습 - 몬스터 사냥"
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
date: 2023-04-05
last_modified_at: 2023-04-05
---

### 개요
---
> 몬스터 사냥

<span style="font-size:13pt">
몬스터를 공격하고 몬스터가 데미지를 입고 hp가 0이되면 사망하는 부분을 구현.<br/>
</span>

### 스크립트 수정
---

<span style="font-size:13pt">
수정 할 스크립트가 많다.<br/>
</span>

```c#
public class PlayerController : MonoBehaviour
{
    ....

    public void PlayerAttack()
    {
        if (nearestTarget == null) // 이 부분을 추가. 공격 도중 몬스터가 사라지면 대상이 null이 되므로.
        {
            return;
        }
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

    ....

    private int penetrateCount = 1;

    ....

    void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Monster"))
        {
            penetrateCount--;
            if (penetrateCount >= 0) // 관통 로직을 추가. 몬스터가 겹쳐져있을 경우 복수개의 충돌 판정이 일어나게 되므로 그것을 방지
            {
                collision.gameObject.GetComponent<Monster>().Hit(damage);
                Destroy(gameObject);
            }
        }
    }
}

```

```c#
abstract public class Monster : MonoBehaviour
{

    ....

    protected bool deadFlag = false;

    ....

    public void Hit(int damage)
    {
        hp -= damage;
        if (hp <= 0)
        {
            gameObject.tag = "Untagged";
            deadFlag = true;
            StartCoroutine(FadeOutAndDestroyCoroutine());
            animator.SetTrigger("isDead");
        }
    }

    IEnumerator FadeOutAndDestroyCoroutine()
    {
        SpriteRenderer[] spriteRenderers = gameObject.GetComponentsInChildren<SpriteRenderer>();

        // 2초에 걸쳐서 서서히 사라지는 연출
        float alpha = 2f;
        while (alpha > 0f)
        {
            alpha -= Time.deltaTime;
            foreach (SpriteRenderer spriteRenderer in spriteRenderers)
            {
                Material material = spriteRenderer.material;
                material.color = new Color(material.color.r, material.color.g, material.color.b, alpha);
            }
            yield return null;
        }
        Destroy(gameObject.transform.root.gameObject); // 부모 오브젝트도 삭제. 자식 오브젝트는 자동으로 전부 삭제됨.
    }
}

```

```c#
public class RangedMonsterController : Monster
{

    ....

    void Update()
    {
        if (!deadFlag) // 부모 클래스에서 플래그로 확인. 죽었으면 걷지도 공격도 못하게 만들 필요 있음.
        // Melle쪽도 같이 수정 필요.
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
    }

    ....
}

```

### Inspector 설정
---

<span style="font-size:13pt">
몬스터에게 tag를 넣어주고 끝.<br/>
플레이어의 공격과 몬스터 hp설정도 잊지말고.<br/>
</span>

### 결과
---

<span style="font-size:13pt">
잘 동작 되는 듯 하다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice34.gif)

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
몬스터 사냥 완료.<br/>
</span>

- [x] <span style="font-size:13pt">~~몬스터 스폰~~</span>
- [x] <span style="font-size:13pt">~~몬스터 이동~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(근접)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 공격(원거리)~~</span>
- [x] <span style="font-size:13pt">~~몬스터 스크립트 정리~~</span>
- [x] <span style="font-size:13pt">~~벽에 데미지 주기~~</span>
- [x] <span style="font-size:13pt">~~플레이어 공격~~</span>
- [x] <span style="font-size:13pt">~~몬스터 사냥~~</span>
- [ ] <span style="font-size:13pt">플레이어 스킬 공격</span>
- [ ] <span style="font-size:13pt">보스 스테이지</span>