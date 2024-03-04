---
layout: single
title: "Flutter netflix클론코딩1"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-02-12
last_modified_at: 2024-02-12
---

# 넷플릭스 클론 코딩
---

인프런 강좌보고 따라해보았다.
안드로이드 스튜디오로 새 플러터 프로젝트를 만들고 나서 강의가 시작된다.

## 네이게이션 탭이 있는 앱 프로젝트 구조 설정하기
---

### 프로젝트 구조 설정

1. lib 폴더안에 model, screen, widget 폴더를 생성한다.
 * ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix00.PNG)
2. 3개의 폴더는 각 역할을 하는 코드들 끼리 모아 놓음으로써 관리하기 쉽게하기 위함.
 * model : 데이터베이스 모델 관련 파일
 * screen : 각 화면별 파일
 * widget : 여러번 반복되거나 자주 사용될 위젯에 대한 코드

### main.dart 수정
 * 강사는 MyHomePage는 사용하지 않는 방식을 선호한다고 한다.
 * StatelessWidget은 고정된 형태로 한번 그려지고 나면 변화가 없는 위젯
 * StatefulWidget은 내부에 오는 데이터가 바뀔때마다 다시 그려질 수 있는 위젯
 * 말 그대로 스테이트 관리를 하는지 정적인지를 나타내는 것 같다.
 * 강좌에서 테마 컬러를 설정할때 사용한 accentColor는 더 이상 사용하지 않는다고 한다.[레퍼런스 링크 참조](#reference)
 * 코드작성

``` dart  
import 'package:clone_netflix/widget/bottom_bar.dart';
import 'package:flutter/material.dart';

// directory에 대해서
// model 데이터 베이스 모델 관련 파일
// screen 화면별 파일
// widget 반복 되어 자주 사용될 파일
void main() => runApp(MyApp());

// MyApp을 StatelessWidget에서 StatefulWidget를 사용함으로서
// MyHomePage를 사용하지 않는 방식으로 변경.
class MyApp extends StatefulWidget {
  _MyAppState createState() => _MyAppState();
}

// MyApp상태 관리
class _MyAppState extends State<MyApp> {
  late TabController controller;

  @override
  Widget build(BuildContext context) {
    return MaterialApp(title: 'CloneFlix',
    // 테마 설정
    theme: ThemeData(
        brightness: Brightness.dark,
        primaryColor: Colors.black,

        // accentColor: 는 더 이상 사용되지 않음.
        ).copyWith(
        colorScheme: ThemeData().colorScheme.copyWith(secondary: Colors.white)
    ),
    // 홈 설정
      home: DefaultTabController(
        length: 4,
        child: Scaffold(
          // NeverScrollableScrollPhysics는 사용자가 손가락 모션을 통해서 스크롤 막기
          body: TabBarView(physics: NeverScrollableScrollPhysics(),
            children: <Widget>[
              Container(
                child: Center(
                  child: Text('home'),
                ),
              ),
              Container(
                child: Center(
                  child: Text('search'),
                ),
              ),
              Container(
                child: Center(
                  child: Text('save'),
                ),
              ),
              Container(
                child: Center(
                  child: Text('more'),
                ),
              )],
          ),
          bottomNavigationBar: Bottom(),
        ),
      ),
    );
  }
}
```

### bottom_bar.dart 생성

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix01.PNG)
* 이 소스는 main.dart에서 정의한 bottomNavigationBar: Bottom()에서 사용된다.
* 코드작성


```dart
import 'package:flutter/material.dart';

class Bottom extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      color: Colors.black,
      child: Container(
        height: 50,
        child: const TabBar(
          labelColor: Colors.white,
          unselectedLabelColor: Colors.white60,
          indicatorColor: Colors.transparent,
          tabs: <Widget>[
            Tab(
              icon: Icon(
                Icons.home,
                size: 18,
              ),
              child: Text('홈', style: TextStyle(fontSize: 9),
              ),
            ),
            Tab(
              icon: Icon(
                Icons.search,
                size: 18,
              ),
              child: Text('검색', style: TextStyle(fontSize: 9),
              ),
            ),
            Tab(
              icon: Icon(
                Icons.save_alt,
                size: 18,
              ),
              child: Text('저장한 컨텐츠 목록', style: TextStyle(fontSize: 9),
              ),
            ),
            Tab(
              icon: Icon(
                Icons.list,
                size: 18,
              ),
              child: Text('더보기', style: TextStyle(fontSize: 9),
              ),
            ),
          ],
        ),
      ),
    );
  }
}
```

### 실행결과

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix02.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix03.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix04.PNG)

하단 네비게이션이 bottom_bar.dart에서 생성한 결과물이고  
검은색 배경은 main.dart에서 설정한 컬러  
메인의 글씨는 main.dart의 child: const TabBar에서 설정한 결과인듯하다. 

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  
[ThemeData's accent properties have been deprecated](https://docs.flutter.dev/release/breaking-changes/theme-data-accent-properties)
