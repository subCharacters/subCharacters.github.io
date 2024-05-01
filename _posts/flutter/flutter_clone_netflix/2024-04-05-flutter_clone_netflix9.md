---
layout: single
title: "Flutter netflix클론코딩9"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-04-05
last_modified_at: 2024-05-01
---

# 넷플릭스 클론 코딩
---

파이어베이스에 있는 데이터를 사용하기 위해 파이어베이스와 연동하기.  
강좌에 나오지 않고 버전도 달라서 많이 애먹었다.  
파이어 베이스를 사용하기 위해서는 firebase cli를 설치해서 플러터와 연동을 해줘야한다.

## 데이터 연동
---

### pubspec.yaml 수정
1. cloud_firebase와 firebase_core를 넣어 필요한 라이브러리를 받는다.

``` yaml
cloud_firebase:
firebase_core:
```

### firebase cli 설치

1. node.js를 16버전 이상으로 업데이트 or 설치
* [node.js](https://nodejs.org/en)
2. npm도 최신 버전으로 해준다.
아래와 같이 명령어를 입력해서 업데이트 해줬다.(사실 기억이 잘 안남..)
```shell
npm ci
npm config set registry http://registry.npmjs.org/
npm cache clean
npm update
npm i -g node-gyp
```
3. node.js와 npm 버전 세팅이 되었으면 npm install -g firebase-tools를 입력하여 firebase를 다운받는다.
플러터 프로젝트가 있는 곳에서 실행하는게 좋다.
4. firebase login 명령어를 입력하여 로그인하고
5. dart pub global activate flutterfire_cli 명령어를 입력하여 firebase cli를 설치한다.
6. flutterfire configure 명령어를 입력하여 플러터와 연결한다.

위 단계까지 별 문제 없으면 main.dart 쪽을 수정한다.

### main.dart.main()

1. 아래 소스를 참고하여 수정한다.
2. 이니셜라이즈를 안해주면 실행은 되는데 화면이 안뜬다.
3. firebase_options.dart가 없다면 플러터와 파이어베이스 연동이 제대로 됐는지 확인한다.

```dart
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(MyApp());
}
```

### android/app/build.gradle

1. 아래 코드를 삽입한다.
2. Cannot fit requested classes in a single dex file에러로 메소드 수가 64K를 넘어 메모리 제한을 넘었다는 에러가 나올때의 대처이다.

```kotlin
...생략

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
    }
    defaultConfig {
        // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
        applicationId "com.example.clone_netflix"
        // You can update the following values to match your application needs.
        // For more information, see: https://docs.flutter.dev/deployment/android#reviewing-the-gradle-build-configuration.
        minSdkVersion flutter.minSdkVersion
        targetSdkVersion flutter.targetSdkVersion
        versionCode flutterVersionCode.toInteger()
        versionName flutterVersionName
        multiDexEnabled true // 추가
    }

    buildTypes {
dependencies {
    // Import the Firebase BoM
    implementation(platform("com.google.firebase:firebase-bom:32.8.0"))
    implementation 'androidx.multidex:multidex:2.0.1' // 추가
}
...생략
```

### firebase에 데이터 추가
1. 데이터베이스 생성. 리전은 적당히. 테스트 모드에서만 동작하게 만들었다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix22.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix23.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix24.PNG)
2. 컬렉션을 생성하고
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix25.PNG)
3. 데이터를 넣어 만든다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix26.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix27.PNG)

### main.dart

1. 더미데이터를 모두 지워준다.
2. 밑의 소스를 넣는다.

```dart
import 'package:clone_netflix/screen/home_screen.dart';
import 'package:clone_netflix/screen/more_screen.dart';
import 'package:clone_netflix/widget/bottom_bar.dart';
import 'package:firebase_core/firebase_core.dart';
import 'firebase_options.dart';
import 'package:flutter/material.dart';

// directory에 대해서
// model 데이터 베이스 모델 관련 파일
// screen 화면별 파일
// widget 반복 되어 자주 사용될 파일
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(options: DefaultFirebaseOptions.currentPlatform);
  runApp(MyApp());
}

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
              HomeScreen(),
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
              MoreScreen(),
            ],
          ),
          bottomNavigationBar: Bottom(),
        ),
      ),
    );
  }
}
```
3. 더미데이터를 불러들이는 것에서 파이어베이스에서 데이터를 취득 후 해당 데이터를 보여주는 로직으로 바뀌었다.

### ./images/ 문자열을 삭제.

1. 이미지 데이터가 프로젝트 내 이미지에서 외부 https에서 이미지 파일을 가져오는 것으로 바뀌었으므로 삭제해준다.
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix28.PNG)

2. // 찜하기 true false 표시 제어 <- 이 주석부분을 참조

### lib/widget/carousel_slider.dart 

1. 찜하기가 디비를 값을 참조하도록 수정한다.

```dart
import 'package:clone_netflix/screen/detail_screen.dart';
import 'package:flutter/material.dart';
import 'package:clone_netflix/model/model_movie.dart';
import 'package:carousel_slider/carousel_slider.dart';

// 단순 이미지 슬라이드가 아니라 찜하기, 상세성보등이 있기에 StatefulWidget을 상속
class CarouselImage extends StatefulWidget {
  final List<Movie> movies;

  CarouselImage({required this.movies});

  _CarouselImageState createState() => _CarouselImageState();
}

class _CarouselImageState extends State<CarouselImage> {
  // 스테이트 관리 변수들 선언
  late List<Movie> movies;
  late List<Widget> images;
  late List<String> keywords;
  late List<bool> likes;
  int _currentPage = 0; // 현재 위치
  late String _currentKeyword;

  @override
  void initState() {
    super.initState();
    // 상위 클래스인 StatefulWidget에서 가져온 movies를 참조할 수 있도록 세팅
    movies = widget.movies;
    images = movies.map((e) => Image.network(e.poster)).toList();
    keywords = movies.map((e) => e.keyword).toList();
    likes = movies.map((e) => e.like).toList();
    _currentKeyword = keywords[0];
  }

  // EdgeInsets는 padding,margin과 같이 크기 조정할때 자주 사용하는 클래스
  // EdgeInsets.only 특정영역에만 여백을 지정하고 싶을때 사용
  // EdgeInsets.fromLTRB 왼쪽,위,오른쪽,밑 순으로 지정이 가능
  // EdgeInsets.symmetric symmetric은 대칭적인 이라는 의미로 수평(horizontal), 수직(vertical)을 기준으로 여백지정이 가능
  // 수직(vertical) = 위,아래 수평(horizontal) = 왼쪽,오른쪽
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: <Widget>[
          Container(
            padding: EdgeInsets.all(20),
          ),
          // 강의와 다른 버전으로 작성법이 달라졌다.
          CarouselSlider(
              items: images,
              options: CarouselOptions(onPageChanged: (index, reason) {
                setState(() {
                  _currentPage = index;
                  _currentKeyword = keywords[_currentPage];
                });
              })),
          // 키워드 출력.
          Container(
            padding: EdgeInsets.fromLTRB(0, 10, 0, 3),
            child: Text(
              _currentKeyword,
              style: TextStyle(fontSize: 11),
            ),
          ),
          // 메뉴바
          Container(
              child: Row(
            // 따닥따닥 붙어있는 메뉴 목록들을 보기좋게 정렬
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: <Widget>[
              Container(
                child: Column(
                  children: <Widget>[
                    // 찜하기 true false 표시 제어
                    likes[_currentPage]
                        ? IconButton(
                            onPressed: () {
                              setState(() {
                                // 찜하기 버튼이 눌리면 현재 페이지의 찜이 트루 폴스 변경됨
                                likes[_currentPage] = !likes[_currentPage];
                                // 실제 데이터를 업데이트
                                movies[_currentPage]
                                    .reference
                                    .update({'like': likes[_currentPage]});
                              });
                            },
                            icon: Icon(Icons.check))
                        : IconButton(
                            onPressed: () {
                              // 찜하기 버튼이 눌리면 현재 페이지의 찜이 트루 폴스 변경됨
                              likes[_currentPage] = !likes[_currentPage];
                              // 실제 데이터를 업데이트
                              movies[_currentPage]
                                  .reference
                                  .update({'like': likes[_currentPage]});
                            },
                            icon: Icon(Icons.add)),
                    Text('내가 찜한 콘텐츠', style: TextStyle(fontSize: 11))
                  ],
                ),
              ),
              // 재생 버튼
              Container(
                  padding: EdgeInsets.only(right: 10),
                  // 강의에서 FlatButton이지만 TextButton으로 대체되어 변경
                  child: TextButton(
                    style: TextButton.styleFrom(
                      backgroundColor: Colors.white,
                    ),
                    onPressed: () {},
                    child: const Row(
                      children: <Widget>[
                        Icon(Icons.play_arrow, color: Colors.black),
                        Padding(padding: EdgeInsets.all(3)),
                        Text('재생', style: TextStyle(color: Colors.black))
                      ],
                    ),
                  )),
              // 정보 버튼
              Container(
                  padding: EdgeInsets.only(right: 10),
                  child: Column(
                    children: <Widget>[
                      IconButton(
                          onPressed: () {
                            Navigator.of(context).push(MaterialPageRoute<Null>(
                                fullscreenDialog: true,
                                builder: (BuildContext context) {
                                  return DetailScreen(
                                    movie: movies[_currentPage],
                                  );
                                }));
                          },
                          icon: Icon(Icons.info)),
                      Text(
                        '정보',
                        style: TextStyle(fontSize: 11),
                      )
                    ],
                  ))
            ],
          )),
          // makeIndicator 함수를 호출하여 그 결과를 Row 위젯의 자식(children)으로 사용하여 화면에 표시
          Container(
            child: Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: makeIndicator(likes, _currentPage),
            ),
          )
        ],
      ),
    );
  }
}

// 주어진 목록에 대한 페이지 인디케이터UI를 생성하는 함수, 각 항목은 현재 페이지에 따라 색이 다르게 표시
List<Widget> makeIndicator(List list, int _currentPage) {
  List<Widget> results = [];
  for (var i = 0; i < list.length; i++) {
    results.add(Container(
      width: 8,
      height: 8,
      margin: EdgeInsets.symmetric(vertical: 10.0, horizontal: 2.0),
      decoration: BoxDecoration(
          shape: BoxShape.circle,
          color: _currentPage == i
              ? Color.fromRGBO(255, 255, 255, 0.9)
              : Color.fromRGBO(255, 255, 255, 0.4)),
    ));
  }
  return results;
}
```

### 실행결과

이미지도 잘 나오고 디비 값의 따라 내가 찜한 콘텐츠의 이미지 값이 변한 것도 볼 수 있다.

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix29.PNG)
* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix30.PNG)


## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  
[git](https://github.com/subCharacters/clone_netflix/tree/class9)  
[Flutter 앱에 Firebase 추가](https://firebase.google.com/docs/flutter/setup?platform=ios&hl=ko#install-cli-tools)