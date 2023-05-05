---
layout: single
title: "다국어 대응"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Unity
tags:
  - Localization
toc: true
toc_sticky: true
date: 2023-04-21
last_modified_at: 2023-04-21
---

### 개요
---
> 다국어 대응

<span style="font-size:13pt">
다국어 대응에 대해서 알아본다.<br/>
유니티 버전은 2021.3.23f1버전이다.<br/>
Localization은 2021.2버전부터 사용이 가능하다고 한다.<br/>
</span>

### 준비
---

<span style="font-size:13pt">
우선 언어를 변경을 확인 할 수 있는 간단한 UI를 만들 것이다.<br/>
TMP를 이용해서 만들었기때문에 다국어 폰트 에셋을 만들 필요가 있다.<br/>
Legacy의 text를 이용해도 폰트만 준비하면 문제없이 될거 같기도 하다.<br/>
국기 변경도 같이 할 예정이라서 국기 이미지도 넣어보았다.<br/>
</span>

![image](..\..\images\unity\localization\localization00.PNG)

### Package 사용준비
---

<span style="font-size:13pt">
간단한 ui배치가 끝났으면<br/>
unity PackageManager에서 Unity Registry로 이동.<br/>
중간에 쯤에 있는 Localization을 install해준다.<br/>
</span>

![image](..\..\images\unity\localization\localization01.PNG)

<span style="font-size:13pt">
edit -> project settings<br/>
Localization탭으로 이동 후 새로 생성해준다.<br/>
Locale이라는 폴더를 새로 만들어서 관리 하려고 한다.<br/>
새로 생성된 asset파일은 inspector창에서 조작이 가능.<br/>
</span>

![image](..\..\images\unity\localization\localization02.PNG)

![image](..\..\images\unity\localization\localization03.PNG)

<span style="font-size:13pt">
새로 생성된 asset파일은 Project Settings 창이나 inspector창에서 조작이 가능.<br/>
</span>


![image](..\..\images\unity\localization\localization04.PNG)

<span style="font-size:13pt">
inspector창에서 Command Line Locale Selector은 커맨드로 입력 했을때의 Localization 기능이고<br/>
System Locale Selector는 어플리케이션을 다운 받는 스토어에 국가를 자동으로 셋팅 해준다는 듯 하다.<br/>
Specific Locale Selector를 수정하여 국가별 언어를 설정 가능하게 해볼 것이다.<br/>
</span>

![image](..\..\images\unity\localization\localization05.PNG)

<span style="font-size:13pt">
Locale Generator를 선택하면 창이 하나가 뜨는데 여기에서 대응할 언어들을 선택해주면 된다.<br/>
언어들이 많으니 검색 창에서도 선택이 가능하다.<br/>
선택 후 Generate Locales를 선택하면 저장할 곳을 선택하라는 창이 뜨는데 위와 동일한 Locale폴더에 넣어주었다.<br/>
그리고 Specitic Locale Selector를 하나 씩 추가해준다.<br/>
</span>

![image](..\..\images\unity\localization\localization06.PNG)

![image](..\..\images\unity\localization\localization07.PNG)

<span style="font-size:13pt">
정상적으로 추가가 되었으면 Inspector창에서 +를 눌러 Specitic Locale Selector를 하나 씩 추가해준다.<br/>
아래에 있는 Project Locale Identifier에서 기본으로 사용할 언어를 선택 할 수 있다.<br/>
</span>

![image](..\..\images\unity\localization\localization08.PNG)

![image](..\..\images\unity\localization\localization09.PNG)

<span style="font-size:13pt">
기본 언어는 디폴트인 영어로 냅뒀다.<br/>
</span>

![image](..\..\images\unity\localization\localization10.PNG)

### 언어 테이블 설정
---

<span style="font-size:13pt">
Window -> Asset Management -> Localiztion Tables를 선택.<br/>
</span>

![image](..\..\images\unity\localization\localization11.PNG)

<span style="font-size:13pt">
New Table Collection탭으로 이동 한 후 Create를 눌러 새로운 테이블을 작성한다.<br/>
Type은 String Table Collection으로 하고 Name은 임의로 정한다.<br/>
</span>

![image](..\..\images\unity\localization\localization12.PNG)

<span style="font-size:13pt">
저장하는 창이 나오고 저장을 하면 아래와 같은 창으로 변경이 될 것이다.<br/>
</span>

![image](..\..\images\unity\localization\localization13.PNG)

<span style="font-size:13pt">
Add New Entry를 눌러서 필요한 다국어 대응을 하나씩 추가 해주면 된다.<br/>
Map이나 Dictionary와 같이 key, value로 되어 있어서 key를 지정함으로써 안에 있는 value를 사용하는 방식이다.<br/>
언어 테이블 설정에는 3가지 방식이 있다.<br/>
첫번째가 방금했던 Localiztion Tables로 이동해서 하나씩 추가하는 방법.<br/>
두번째가 Inspector창에서 바로바로 추가하는 방법.<br/>
세번째가 File import 방법.<br/>
</span>

> Localiztion Tables

![image](..\..\images\unity\localization\localization14.PNG)

> Inspector

<span style="font-size:13pt">
Inspector창에서 추가하려면 해당 Text GameObject -> Add Component -> Localization -> Localize String Event 로 Component를 추가하면 된다.<br/>
Create Table Collection을 눌러서 아까와 같이 테이블을 설정하면 된다.<br/>
이미 테이블이 추가된 상태에서 값만 추가하고 싶다면 String Reference에서 생성한 테이블을 추가한 후 Add Table Entry로 값을 바로 추가 가능하다.<br/>
</span>

![image](..\..\images\unity\localization\localization15.PNG)

![image](..\..\images\unity\localization\localization16.PNG)

<span style="font-size:13pt">
테이블을 생성하고 String Reference 클릭했는데도 아무것도 뜨지 않는다면 <br/>
Edit -> Preferences -> Localization에서 Asset Search Picker와 String Search Picker의 체크를 풀고 다시 해보자.<br/>
</span>

![image](..\..\images\unity\localization\localization17.PNG)

> File import

<span style="font-size:13pt">
테이블을 만든 후 테이블을 클릭해서 Inspector창을 연다.<br/>
Extends에 있는 +를 누르면 google sheet와 csv 두개를 선택 할 수 있다.<br/>
</span>

![image](..\..\images\unity\localization\localization18.PNG)

![image](..\..\images\unity\localization\localization19.PNG)

<span style="font-size:13pt">
먼저 csv<br/>
csv를 확인 해보면 키 컬럼과 지역 컬럼으로 나뉘어져있다.<br/>
save를 누르면 csv파일로 출력이 가능하며 편집툴로 수정이 가능하다.<br/>
open을 하면 csv를 불러들여서 테이블로 저장이 가능하다.<br/>
</span>

![image](..\..\images\unity\localization\localization20.PNG)

<span style="font-size:13pt">
다음은 구글 시트<br/>
시작하기에 앞서서 Google Cloud Console에 API나 OAuth를 먼저 등록을 해놓아야 이용이 가능하다.<br/>
구글 시트의 경우 Assets > Create > Localization > Google Sheets Service 로 이동하여 Provider를 생성한다.<br/>
생성한 Provider를 클릭하면 Inspector창이 활성화 된다.<br/>
인증 방식에는 OAuth방식과 API Key방식 두가지가 있다.<br/>
여기서는 OAuth방식으로 인증을 진행했다.<br/>
</span>

![image](..\..\images\unity\localization\localization21.PNG)

<span style="font-size:13pt">
정상적으로 인증이 끝났다면 Table로 돌아와서 Inspector창을 열어서 사용하면 된다.<br/>
방금 만든 Provider를 선택하고 시트를 생성.<br/>
+를 눌러서 테이블의 컬럼들을 선택해서 Push로 내보내거나 Pull로 불러들일 수 있다.<br/>
</span>

![image](..\..\images\unity\localization\localization22.PNG)


### 문자열 적용
---

<span style="font-size:13pt">
적용 할 text 오브젝트의 Inspector 창을 연다.<br/>
add Component -> Localization -> Localize String Event 컴포넌트를 추가<br/>
아래와 같은 화면이 나올것이다.<br/>
</span>

![image](..\..\images\unity\localization\localization23.PNG)

<span style="font-size:13pt">
String Reference를 누르고 생성한 테이블을 선택하면 key값이 나오는데 해당 텍스트에 해당하는 key를 선택한다.<br/>
</span>

![image](..\..\images\unity\localization\localization24.PNG)


<span style="font-size:13pt">
갱신할 텍스트 컴포넌트를 주입하기 위해서 Update String (String)의 +를 누른다.<br/>
업데이트를 Editor And Runtime때 한다고 변경하고 Text 컴포넌트를 끌어다가 주입한다.<br/>
Function 부분에 Text클래스로 넘어가서 Text함수를 선택해준다.<br/>
</span>

![image](..\..\images\unity\localization\localization25.PNG)

![image](..\..\images\unity\localization\localization26.PNG)

<span style="font-size:13pt">
잘 바뀌는 듯 하다.<br/>
</span>

![image](..\..\images\unity\localization\localization27.gif)

### 국가 아이콘 변경
---

<span style="font-size:13pt">
Window -> Asset Management -> Localization Tables<br/>
New Table Collection에서 테이블을 새로 만든다.<br/>
이번에는 String Table이 아니라 Asset Table Collection으로 생성한다.<br/>
</span>

![image](..\..\images\unity\localization\localization28.PNG)

![image](..\..\images\unity\localization\localization29.PNG)

<span style="font-size:13pt">
각각의 이미지를 끌어다가 올려 놓으면 완성이다.<br/>
이미지는 화살표 버튼으로 열어서 나오는 이미지로 해야 sprite가 된다.(안하면 texture가 됨.)<br/>
</span>

![image](..\..\images\unity\localization\localization30.PNG)

<span style="font-size:13pt">
적용할 이미지 오브젝트의 Inspector창을 연다.<br/>
Iamge 컴포넌트의 More Item(⋮)을 열어서 Localize를 선택하면 알맞은 컴포넌트가 추가 된다.<br/>
</span>

![image](..\..\images\unity\localization\localization31.PNG)

<span style="font-size:13pt">
Localize Sprite Event가 추가 되었다면 Localized Asset Reference에 아까 만든 테이블 값을 선택하자.<br/>
More Item(⋮)으로 열어서 추가하면 위에서 한 것처럼 하나하나씩 설정을 안해줘도 알아서 이미지, function, 적용환경을 설정해준다.<br/>
</span>

![image](..\..\images\unity\localization\localization32.PNG)

<span style="font-size:13pt">
그리고 확인을 해보면 잘 작동한다.<br/>
하지만 국기 아이콘이 한국어와 일본어에서 좀 떨어져 있어서 위화감이 있다.<br/>
</span>

![image](..\..\images\unity\localization\localization33.gif)


### 언어 별 아이콘 위치 설정
---

<span style="font-size:13pt">
Window -> Asset Management -> Localization Scene Controls 창을 연다.<br/>
Track Changes에 체크를 넣고 Active Locale에서 언어를 변경 시킨다.<br/>
이미지 변경하고 싶은 언어를 선택하고 Hierarchy창에서 이미지 오브젝트를 선택한 후 위치를 조정한다.
Game Object Localizer라는 스크립트 컴포넌트가 추가 되면서 해당 로컬에 맞춰서 이미지의 위치가 변경된다.<br/>
변경사항이 있는 Pos는 초록색 배경으로 바뀐다.<br/>
</span>

![image](..\..\images\unity\localization\localization34.PNG)

### 게임 상에서 언어 설정
---

<span style="font-size:13pt">
먼저 스크립트를 생성한다.<br/>
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
빈 오브젝트 혹은 canvas에 위에서 만든 스크립트를 컴포넌트로 추가한다.<br/>
</span>

![image](..\..\images\unity\localization\localization35.PNG)

<span style="font-size:13pt">
버튼 오브젝트에 Onclick에 +를 눌러 클릭 이벤트를 추가한다.<br/>
스크립트를 추가한 오브젝트를 넣어주고 ChangeLacale(int index)함수가 실행 되게끔 추가하면 끝.<br/>
</span>

![image](..\..\images\unity\localization\localization36.PNG)

### 최종 결과
---

![image](..\..\images\unity\localization\localization37.gif)