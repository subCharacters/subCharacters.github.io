---
layout: single
title: "Flutter netflix클론코딩2"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-03-04
last_modified_at: 2024-03-04
---

# 넷플릭스 클론 코딩
---

넷플릭스의 4가지 탭 중 첫번째인 홈 탭을 구성

## 홈 화면 제작
---
홈 화면을 구성하기 위해서 screen 폴더에 home_screen.dart파일을 생성

### home_screen.dart

1. screen 폴더안에 home_screen.dart파일을 생성
 * ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix05.PNG)
2. 상단바, 영화 포스터, 원형 및 사각형 위젯으로 화면을 구성.
 * ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix06.PNG)
3. 프로젝트 바로 아래에 images 폴더를 넣고 로고 이미지를 넣는다.  이미지는 강좌안에 있었다.
 * ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix07.PNG)
4. pubsplec.yaml파일을 열고 assets의 주석을 제외 시키고 이미지의 로고로 대체한다.
```dart
  # The following line ensures that the Material Icons font is
  # included with your application, so that you can use the icons in
  # the material Icons class.
  uses-material-design: true

  # To add assets to your application, add an assets section, like this:
  assets:
     - images/bbongflix_logo.png

  # An image asset can refer to one or more resolution-specific "variants", see
  # https://flutter.dev/assets-and-images/#resolution-aware

  # For details regarding adding assets from package dependencies, see
  # https://flutter.dev/assets-and-images/#from-packages
```

5. 생성한 home_screen.dart 파일에 코드를 작성

 ```dart
import 'package:flutter/material.dart';

// 영화의 정보는 백엔드에서 가져와야 하기에 StatefulWidget을 사용
class HomeScreen extends StatefulWidget {
  // createState()로 상태관리
  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
  }

// 상단 바의 경우 스택으로 구현이 가능
  @override
  Widget build(BuildContext context) {
    return TopBar();
  }
}

class TopBar extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.fromLTRB(20, 7, 20, 7),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceBetween, // 일정한 간격으로 띄우기
        children: <Widget>[
          Image.asset( // 이미지 불러오기
            'images/bbongflix_logo.png',
            fit: BoxFit.contain, // 설정한 크기 안에서 이미지 크기 조정
            height: 25,
          ),
          Container(
            padding: EdgeInsets.only(right: 1), // 특정 위젯의 상하좌우 여백을 두기 위함
            child: Text(
              'TV 프로그램',
              style: TextStyle(fontSize: 14),
            ),
          ),
          Container(
            padding: EdgeInsets.only(right: 1),
            child: Text(
              '영화',
              style: TextStyle(fontSize: 14),
            ),
          ),
          Container(
            padding: EdgeInsets.only(right: 1),
            child: Text(
              '내가 찜한 콘텐츠',
              style: TextStyle(fontSize: 14),
            ),
          ),
        ],
      ),
    );
  }
}

 ```

### main.dart 수정

```dart
import 'package:clone_netflix/screen/home_screen.dart';
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
              HomeScreen(), // 이 부분으로 수정
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

### 실행결과

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix08.PNG)

하단 네비게이션이 bottom_bar.dart에서 생성한 결과물이고  
검은색 배경은 main.dart에서 설정한 컬러  
메인의 글씨는 main.dart의 child: const TabBar에서 설정한 결과인듯하다. 

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  