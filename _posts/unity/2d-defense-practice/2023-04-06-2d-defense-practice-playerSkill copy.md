---
layout: single
title: "2d디펜스 게임 연습 - 플레이어 스킬 공격"
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
date: 2023-04-06
last_modified_at: 2023-04-06
---

### 개요
---
> 플레이어 스킬 공격

<span style="font-size:13pt">
플레이어가 스킬 사용 버튼을 누르면 적에게 스킬 공격을 하는 부분을 구현한다.<br/>
스킬을 사용하면 쿨타임이 돌아가서 연속으로 사용 못하도록 해본다.<br/>
</span>

### 스크립트
---

<span style="font-size:13pt">
Skill과 Meteor 부분이 새롭게 추가.<br/>
Skill은 버튼 이벤트에 추가하는 스크립트<br/>
Meteor는 스킬 이펙트 프리팹에 사용<br/>
</span>

```c#
public class Skill : MonoBehaviour
{

    public Image fillImage; // 가리는 이미지
    public Text coolTimeText; // 현재 쿨타임 텍스트

    public float coolTime;

    private float currentCoolTime; // 현재 쿨타임

    private bool isCoolTime = false; //스킬을 사용할 수 있는지 확인하는 변수


    public GameObject meteorPrefab; // 떨어질 메테오 프리팹
    public float meteorInterval; // 메테오 생성 간격
    public float meteorSpeed; // 메테오 떨어지는 속도
    public int meteorCount; // 생성될 메테오 개수

    public float distanceTraveled; // 메테오가 이동할 거리
    private int meteorSpawned; // 생성된 메테오 개수

    void Start()
    {
        fillImage.fillAmount = 0; // 스킬 보이게 
    }

    public void SkillCoolTime()
    {
        // 쿨타임 돌고 있는 중인지 체크
        if (isCoolTime)
        {
            Debug.Log("아직 스킬을 사용할 수 없습니다.");
        }
        else
        {
            meteorSpawned = 0;
            InvokeRepeating("SpawnMeteor", meteorInterval, meteorInterval);


            currentCoolTime = coolTime;
            coolTimeText.text = "" + currentCoolTime;
            coolTimeText.enabled = true;
            fillImage.fillAmount = 1; // 스킬 버튼을 가림
            StartCoroutine("Cooltime");
            isCoolTime = true;
        }
    }

    IEnumerator Cooltime()
    {
        while (fillImage.fillAmount > 0)
        {
            fillImage.fillAmount -= 1 * Time.smoothDeltaTime / coolTime;
            currentCoolTime -= Time.smoothDeltaTime;
            coolTimeText.text = currentCoolTime.ToString("F1");
            yield return null;
        }

        isCoolTime = false; //스킬 쿨타임이 끝나면 스킬을 사용할 수 있는 상태로 바꿈
        coolTimeText.enabled = false;
        yield break;
    }

    void SpawnMeteor()
    {
        // 지정된 개수만큼 메테오 생성
        if (meteorSpawned < meteorCount)
        {
            Debug.Log("SpawnMeteor");
            // 랜덤한 위치에서 메테오 생성
            float xPos = Random.Range(30f, 40f);
            Vector3 spawnPos = new Vector3(xPos, 13f, 0f);
            GameObject meteor = Instantiate(meteorPrefab, spawnPos, Quaternion.Euler(0 ,0 , -45f));
            meteor.GetComponent<Meteor>().init(meteorSpeed, distanceTraveled); // 메테오 정보 입력

            // 생성된 메테오 개수 증가
            meteorSpawned++;
            return;
        }
        CancelInvoke("SpawnMeteor");
    }
}
```

```c#
public class Meteor : MonoBehaviour
{
    float meteorSpeed; // 메테오 떨어지는 속도

    private float distanceTraveled; // 메테오 이동 거리
    private float distance;

    Rigidbody2D rb;

    public void init(float meteorSpeed, float distanceTraveled)
    {
        this.meteorSpeed = meteorSpeed;
        this.distanceTraveled = distanceTraveled;
    }

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        rb.gravityScale = 3f;
    }

    void Update()
    {
        // 메테오가 이동한 거리 측정
        distance += Mathf.Abs(rb.velocity.x) * Time.deltaTime;
        rb.AddForce(Vector2.right.normalized * meteorSpeed);
        // 일정 거리 이상 이동하면 삭제
        if (distance >= distanceTraveled)
        {
            Destroy(gameObject);
        }
    }

    void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.CompareTag("Monster"))
        {
            collision.gameObject.GetComponent<Monster>().Hit(20);
            Destroy(gameObject);
        }
    }
}
```

### Inspector 설정
---

<span style="font-size:13pt">
버튼 관련 부분 생성 UI생성 하고 적절한 위치에 배치.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice35.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice36.PNG)

<span style="font-size:13pt">
버튼과 버튼의 자식 이미지의 컴포넌트를 설정한다. Text는 적당히 크기를 맞추면된다.<br/>
버튼의 자식 이미지는 쿨타임 효과를 내기 위한 이미지이므로 검정색을 주었고 아웃라인을 줘서 ui적 요소를 추가했다. <br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice37.PNG)

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice38.PNG)

<span style="font-size:13pt">
메테오 프리팹에 대한 설정은 다음과 같다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice39.PNG)

### 결과
---

<span style="font-size:13pt">
이펙트 부분이나 스킬 범위 표시 등 디테일하게 손봐야 할 곳은 많지만 동작은 되는 듯 하다.<br/>
</span>

![image](..\..\images\unity\2d-defense-practice\2d-defense-practice40.gif)

### <span style="color: #73a3fb;">TODO</span>
---
<span style="font-size:13pt">
플레이어 스킬 공격 완료.<br/>
이제 마지막 하나.<br/>
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
- [ ] <span style="font-size:13pt">보스 스테이지</span>