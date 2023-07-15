---
layout: single
title: "다국어 대응 - 요약"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - Localization
toc: true
toc_sticky: true
date: 2023-05-10
last_modified_at: 2023-05-10
---

### 개요
---
> 다국어 대응

<span style="font-size:13pt">
다국어 대응에 대해서 알아본다.<br/>
유니티 버전은 2021.3.23f1버전이다.<br/>
Localization은 2021.2버전부터 사용이 가능하다고 한다.<br/>
</span>

### 목표 결과물
---

![image](..\..\images\unity\localization-short\localization-short01.gif)


### 준비
---

<span style="font-size:13pt">
UI준비<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short02.PNG)

<span style="font-size:13pt">
다국어 대응 FontAsset 준비<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short03.PNG)

<span style="font-size:13pt">
국기 아이콘 준비<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short04.PNG)


### Package설치 & 설정
---

<span style="font-size:13pt">
unity PackageManager에서 Unity Registry로 이동.<br/>
중간에 쯤에 있는 Localization을 install.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short05.PNG)

![image](..\..\images\unity\localization-short\localization-short06.PNG)

<span style="font-size:13pt">
설치를 하면 파일이 생성.<br/>
생성된 파일을 선택하고 Inspector창에서 Locale Generator를 눌러서 생성.<br/>
+를 눌러 Specific Locale Selector를 언어를 하나 씩 추가.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short07.PNG)

![image](..\..\images\unity\localization-short\localization-short08.PNG)

![image](..\..\images\unity\localization-short\localization-short09.PNG)

![image](..\..\images\unity\localization-short\localization-short10.PNG)


### 언어 테이블 생성
---

<span style="font-size:13pt">
Window -> Asset Management -> Localiztion Tables ->  New Table Collection 선택.<br/>
혹은 Text(TMP)에서 add Component -> Localize String Evnet -> Create Table Collection 선택.<br/>
<span style="font-size:9pt;font-weight: bold;box-shadow: inset 0 -20px 0 #ffda0063;">
(TextMeshPro 컴포넌트 More Item(⋮)를 눌러 Localize를 누르면 자동으로 추가 가능)<br/>
</span>
</span>

![image](..\..\images\unity\localization-short\localization-short11.PNG)

<span style="font-size:13pt">
or<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short12.PNG)

<span style="font-size:13pt">
Type을 String Table Collection로 선택하고 Create를 눌러 테이블을 생성.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short13.PNG)


### 언어 테이블 설정
---

<span style="font-size:13pt">
언어 테이블 값 설정에는 3가지 정도의 방법이 존재하니 상황에 맞춰서 사용.<br/>
</span>

+ Localization Tables -> Edit Table Collection에서 add New Entry로 키와 밸류를 설정.

![image](..\..\images\unity\localization-short\localization-short14.PNG)

<br/>

+ Inspector창에서 직접 추가.

<span style="font-size:13pt">
Text GameObject -> Add Component -> Localization -> Localize String Event에서 설정 가능.<br/>
String Reference 클릭했는데도 아무것도 뜨지 않는다면 <br/>
Edit -> Preferences -> Localization에서 Asset Search Picker와 String Search Picker의 체크를 해제 후 다시 시도.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short15.PNG)

<br/>

+ csv 혹은 google spread sheet로 추가.

<span style="font-size:13pt">
생성한 테이블을 선택하고 Inspector창에서 조작.<br/>
csv 혹은 google spread sheet로 추가하고 싶을 경우 save를 먼저하면 템플릿을 얻을 수 있다.<br/>
google spread sheet의 경우 인증이 필요 하니 아래 링크를 참조.<br/>
[unity docs](https://docs.unity3d.com/Packages/com.unity.localization@1.4/manual/Google-Sheets.html)
</span>

![image](..\..\images\unity\localization-short\localization-short16.PNG)

![image](..\..\images\unity\localization-short\localization-short17.PNG)


### 아이콘 테이블 생성
---

<span style="font-size:13pt">
Window -> Asset Management -> Localiztion Tables ->  New Table Collection 선택.<br/>
혹은 Image에서 add Component -> Localize Sprite Evnet -> Create Table Collection 선택.<br/>
<span style="font-size:9pt;font-weight: bold;box-shadow: inset 0 -20px 0 #ffda0063;">
(Image 컴포넌트 More Item(⋮)를 눌러 Localize를 누르면 자동으로 추가 가능)<br/>
</span>
</span>

![image](..\..\images\unity\localization-short\localization-short11.PNG)

<span style="font-size:13pt">
or<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short19.PNG)

<span style="font-size:13pt">
Type을 
<span style="font-size:13pt;font-weight: bold;box-shadow: inset 0 -20px 0 #ffda0063;">
Asset Table Collection
</span>
로 선택하고 Create를 눌러 테이블을 생성.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short20.PNG)


### 아이콘 테이블 설정
---

<span style="font-size:13pt">
Localization Tables -> Edit Table Collection에서 add New Entry로 키와 밸류를 설정.<br/>
혹은 Image GameObject -> Add Component -> Localization -> Localize Sprite Event에서 설정.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short18.PNG)

<span style="font-size:13pt">
or<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short21.PNG)


### 게임 상에서 언어 설정
---

<span style="font-size:13pt">
Text(TMP) game object의 Inspector에 들어가서 Localize String Event 컴포넌트로 가서 String Reference의 값을 생성한 테이블과 사용할 키를 선택. <br/>
아래의 Update String(String)을 추가하고 TextMeshPro 컴포넌트를 오브젝트로 넣고 Edit And Run과 text 함수를 선택. <br/>
Edit And Run으로 하면 게임을 실행하지 않고도 에디터상에서 확인이 가능.<br/>
Update String(String)은 More Item으로 컴포넌트를 추가했다면 자동으로 추가 되므로 생략 가능.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short22.PNG)

<span style="font-size:13pt">
아이콘도 마찬가지로 테이블과 키를 선택해서 설정.<br/>
Image game object의 Inspector에 들어가서 Localize Sprite Event 컴포넌트로 가서 Localized Asset Reference의 값을 생성한 테이블과 사용할 키를 선택. <br/>
아래의 Update Asset(Sprite)을 추가하고 Image 컴포넌트를 오브젝트로 넣고 Edit And Run과 sprite 함수를 선택. <br/>
Edit And Run으로 하면 게임을 실행하지 않고도 에디터상에서 확인이 가능.<br/>
Update Asset(Sprite)은 More Item으로 컴포넌트를 추가했다면 자동으로 추가 되므로 생략 가능.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short23.PNG)

<span style="font-size:13pt">
언어에 따라서 길이가 다르므로 국가 아이콘을 추가할경우 위치 조정이 필요할때가 있다.<br/>
Window -> Asset Management -> Localization Scene Controls 창을 연 후 Track Changes에 체크를 넣고 Active Locale에서 언어를 변경.<br/>
이미지 위치를 변경하고 싶은 언어를 선택하고 Hierarchy창에서 이미지 오브젝트를 선택한 후 위치를 조정.<br/>
Game Object Localizer라는 스크립트 컴포넌트가 추가 되면서 해당 로컬에 맞춰서 이미지의 위치가 변경.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short24.PNG)


<span style="font-size:13pt">
설정이 완료되었다면 스크립트를 생성하고 로직을 작성.<br/>
</span>

```c#

public class LocaleManager : MonoBehaviour
{
    private bool isChanging;

    public void ChangeLacale(int index)
    {
        if (isChanging)
        {
            return;
        }

        StartCoroutine(ChangeLacaleCoroutine(index));
    }

    IEnumerator ChangeLacaleCoroutine(int index)
    {
        isChanging = true;

        yield return LocalizationSettings.InitializationOperation;
        LocalizationSettings.SelectedLocale = LocalizationSettings.AvailableLocales.Locales[index];

        isChanging = false;
    }
}

```

<span style="font-size:13pt">
작성이 완료된 스크립트를 빈 오브젝트 혹은 canvas등 컨트롤 할 오브젝트의 컴포넌트로 추가.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short25.PNG)

<span style="font-size:13pt">
적용할 버튼 오브젝트의 On click()에 작성한 함수를 추가하고 파라미터를 설정하고 게임을 실행 시켜서 확인하면 완료.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short26.PNG)



### Tip
---

<span style="font-size:13pt">
자신이 작성한 텍스트나 아이콘의 로컬라이즈한 값이 제대로 적용이 되는지 테스트 하는 법<br/>
게임 실행 후 우측 상단의 셀렉트 바로 변경으로 확인가능.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short27.gif)

<span style="font-size:13pt">
혹은 Window -> Asset Management -> Localization Scene Controls창에서 Active Locale 값을 변경해서 확인가능.<br/>
이 경우에는 Localize String Event 혹은 Localize Sprite Event의 함수가 Editor And Runtime으로 되어 있어야 한다.<br/>
</span>

![image](..\..\images\unity\localization-short\localization-short28.gif)


<span style="font-size:13pt">
이외에도 파라미터 값과 로컬라이즈한 텍스트를 조합해서 표시하거나 게임 중 실시간으로 언어를 변경하는 등 다양한 기능이 있으므로 자세한 내용은 아래의 공식 문서를 참고<br/>
[About Localization](https://docs.unity3d.com/Packages/com.unity.localization@1.4/manual/index.html)
</span>
