---
layout: single
title: "Flutter netflix클론코딩8"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-04-03
last_modified_at: 2024-04-03
---

# 넷플릭스 클론 코딩
---

플러터 파이어베이스 연동

## Firebase 연동
---

1. [Flutter / Firebase 연동하기](https://firebase.google.com/docs/flutter/setup?hl=ko#add_flutterfire_plugins) 링크로 이동.
2. 기본적인 세팅은 이미 되어 있으므로 해당 링크 안에 적혀있는 Firebase에 로그인 링크를 따라 로그인.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix14.PNG)
3. 프로젝트 추가를 눌러 프로젝트를 추가한다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix15.PNG)
4. 먼저 프로젝트 이름을 정한다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix16.PNG)
5. 다음으로 넘어가면 구글 애널리틱스 사용을 물어보는데 이번 연습용 프로젝트에선 해제
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix17.PNG)
6. 프로젝트를 생성한다.
7. 강의에선 ios지만 안드로이드를 만들것이므로 안드로이드를 선택
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix18.PNG)
8. 앱 닉네임과 디버그 성명엔 임의의 값을 넣으면 된다.
9. Android 패키지 이름은 프로젝트에 있는 패키지 이름을 넣으면 된다. 나머지 입력은 선택사항.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix19.PNG)
10. 그럼 구성 파일 다운로드 화면이 나오는데 다운 받아 android/app/ 하위에 넣는다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix20.PNG)
11. 다음으로 넘어가면 SDK추가화면이 나온다. 해당 코드를 복사해서 각각 build.gradle에 넣으면 된다.  
android/app/build.gradle  
android/build.gradle  
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix21.PNG)


## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  
[git](https://github.com/subCharacters/clone_netflix/tree/class8)