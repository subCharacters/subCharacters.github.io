---
layout: single
title: "Flutter netflix클론코딩5"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-03-20
last_modified_at: 2024-03-20
---

# 넷플릭스 클론 코딩
---

홈 화면에 있는 원형 위젯(미리보기)과 사각형 위젯(지금 뜨는 콘텐츠)들을 만들어보는 시간

## 원형 슬라이드, 박스 슬라이드 만들기
---

### circle_slider.dart

1. wigdet폴더 안에 circle_slider.dart를 생성
2. 이미지 위젯을 하는 코드를 작성

```dart
import 'package:flutter/material.dart';
import 'package:clone_netflix/model/model_movie.dart';

// 상태변화가 없는 위젯이므로 StatelessWidget
class CircleSlider extends StatelessWidget {
  // moive에서 정보를 가져와야 하므로
  final List<Movie> movies;

  CircleSlider({required this.movies});

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(7),
      // Column : 수직(세로)방향 배치위젯
      child: Column(
        // 가로정렬 start는 default 값으로 왼쪽에 붙게 한다.
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Text('미리보기'),
          Container(
            height: 120,
            // 횡단 스크롤이 되는 ListView
            child: ListView(
              // 좌우로 스크롤이 가능
              // vertical은 위아래로 스크롤이 가능(디폴트)
              scrollDirection: Axis.horizontal,
              // makeCircleImages 함수를 만들어 사용하여 원형 이미지 위젯을 생성
              children: makeCircleImages(movies),
            ),
          )
        ],
      ),
    );
  }
}

List<Widget> makeCircleImages(List<Movie> movies) {
  List<Widget> results = [];
  for (var i = 0; i < movies.length; i++) {
    // InkWell : 클릭 가능하도록 만들어줌
    results.add(InkWell(onTap: () {},
    child: Container(
      padding: EdgeInsets.only(right: 10),
      // Align : 자식 위젯을 원하는 방향으로 정리하고 싶을 때 Align 위젯을 사용하여 정렬할 수 있다.
      child: Align(
        // 정렬하는 것(왼쪽끝)
        alignment: Alignment.centerLeft,
        // 원형 이미지를 만들어냄
        child: CircleAvatar(
          // poster파일명에 images/를 붙여놓으면 일일이 안적어도 됨..
          backgroundImage: AssetImage('images/' + movies[i].poster),
          radius: 48,
        ),
      ),
    ),
    ));
  }

  return results;
}
```

### box_slider.dart

1. wigdet폴더 안에 box_slider.dart를 생성
2. circle slider와 거의 같다.

```dart
import 'package:flutter/material.dart';

import '../model/model_movie.dart';

class BoxSlider extends StatelessWidget {
  final List<Movie> movies;

  BoxSlider({required this.movies});

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(7),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          Text('지금 뜨는 콘텐츠'),
          Container(
            height: 120,
            child: ListView(
              scrollDirection: Axis.horizontal,
              children: makeBoxImages(movies),
            ),
          )
        ],
      ),
    );
  }
}

List<Widget> makeBoxImages(List<Movie> movies) {
  List<Widget> results = [];
  for (var i = 0; i < movies.length; i++) {
    results.add(InkWell(
      onTap: () {},
      child: Container(
        padding: EdgeInsets.only(right: 10),
        child: Align(
          alignment: Alignment.centerLeft,
          child: Image.asset('images/' + movies[i].poster),
        ),
      ),
    ));
  }
  return results;
}
```

### home_screen.dart

1. Stack밑에 CircleSlider와 BoxSlider를 추가

```dart
import 'package:clone_netflix/model/model_movie.dart';
import 'package:clone_netflix/widget/box_slider.dart'; // <-- 추가
import 'package:clone_netflix/widget/carousel_slider.dart';
import 'package:clone_netflix/widget/circle_slider.dart'; // <-- 추가
import 'package:flutter/material.dart';

// 영화의 정보는 백엔드에서 가져와야 하기에 StatefulWidget을 사용
class _HomeScreenState extends State<HomeScreen> {
  ... 중략
// 상단 바의 경우 스택으로 구현이 가능
  @override
  Widget build(BuildContext context) {
    return ListView(children: <Widget>[
      // Stack은 자료구조 특성 그대로 순서대로 바닥에 깔리게된다.
      // CarouselImage가 제일 밑. 그다음 TopBar의 위젯이 배치된다.
      Stack(children: <Widget>[
        CarouselImage(movies: movies),
        TopBar(),
        ],
      ),
      CircleSlider(movies: movies,), // <-- 추가
      BoxSlider(movies: movies,), // <-- 추가
    ],
    );
  }
  ... 중략
```

### 실행결과

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix10.PNG)

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  