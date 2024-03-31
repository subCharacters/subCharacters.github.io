---
layout: single
title: "Flutter netflix클론코딩6"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-03-26
last_modified_at: 2024-03-26
---

# 넷플릭스 클론 코딩
---

영화를 클릭했을때 영화의 세부 정보가 나오는 화면인 디테일 화면(DetailScreen)을 제작.

## 상세 화면 만들기
---

### detail_screen.dart

1. screen폴더 안에 detail_screen.dart를 생성
2. 아래 코드를 작성

```dart
import 'package:clone_netflix/model/model_movie.dart';
import 'package:flutter/material.dart';
import 'dart:ui'; // 이미지 블러처리를 위해서 필요

class DetailScreen extends StatefulWidget {
  final Movie movie;

  DetailScreen({required this.movie});

  _DetailScreenState createState() => _DetailScreenState();
}

class _DetailScreenState extends State<DetailScreen> {
  bool like = false;

  @override
  void initState() {
    super.initState();
    like = widget.movie.like;
  }

  @override
  Widget build(BuildContext context) {
    // 팝업 모달의 형태로 제작
    return Scaffold(
      body: Container(
        child: SafeArea(
            child: ListView(
          children: <Widget>[
            Stack(
              children: <Widget>[
                Container(
                  width: double.maxFinite,
                  // 이 부분까지만 작성하면 빈화면
                  decoration: BoxDecoration(
                    image: DecorationImage(
                      image: AssetImage('images/' + widget.movie.poster),
                      fit: BoxFit.cover,
                    ),
                  ),
                  // 블러처리된 이미지와 정 가운데에 이미지가 300 높이만큼 들어감
                  child: ClipRect(
                    child: BackdropFilter(
                      filter: ImageFilter.blur(sigmaX: 10, sigmaY: 10),
                      child: Container(
                        alignment: Alignment.center,
                        color: Colors.black.withOpacity(0.1),
                        child: Container(
                          child: Column(
                            children: <Widget>[
                              // 가운데 이미지
                              Container(
                                padding: EdgeInsets.fromLTRB(0, 45, 0, 10),
                                child: Image.asset(
                                    'images/' + widget.movie.poster),
                                height: 300,
                              ),
                              // 설명
                              Container(
                                padding: EdgeInsets.all(7),
                                child: Text(
                                  '99% 일치 2019 15+ 시즌 1개',
                                  textAlign: TextAlign.center,
                                  style: TextStyle(fontSize: 13),
                                ),
                              ),
                              // 영화제목
                              Container(
                                padding: EdgeInsets.all(7),
                                child: Text(
                                  widget.movie.title,
                                  textAlign: TextAlign.center,
                                  style: TextStyle(
                                      fontWeight: FontWeight.bold,
                                      fontSize: 13),
                                ),
                              ),
                              // 재생버튼
                              Container(
                                  padding: EdgeInsets.all(3),
                                  child: TextButton(
                                    style: TextButton.styleFrom(
                                        backgroundColor: Colors.red,
                                        // 모서리 각지게
                                        shape: RoundedRectangleBorder(
                                            borderRadius:
                                                BorderRadius.circular(0))),
                                    onPressed: () {},
                                    child: const Row(
                                      mainAxisAlignment:
                                          MainAxisAlignment.center,
                                      children: <Widget>[
                                        Icon(
                                          Icons.play_arrow,
                                          color: Colors.white,
                                        ),
                                        Text('재생',
                                            style:
                                                TextStyle(color: Colors.white))
                                      ],
                                    ),
                                  )),
                              // 영화 설명
                              Container(
                                padding: EdgeInsets.all(5),
                                child: Text(widget.movie.toString()),
                              ),
                              // 출연진 정보
                              Container(
                                padding: EdgeInsets.all(3),
                                alignment: Alignment.centerLeft,
                                child: Text(
                                  '출연: 현빈, 손예진, 서지혜\n제작자: 이정효, 박지은',
                                  style: TextStyle(
                                      color: Colors.white60, fontSize: 12),
                                ),
                              )
                            ],
                          ),
                        ),
                      ),
                    ),
                  ),
                ),
                // 상단 appbar생성, 닫기버튼은 기본적으로 적용되어있음.
                Positioned(
                    child: AppBar(
                  backgroundColor: Colors.transparent,
                  elevation: 0,
                ))
              ],
            ),
            Container(
              color: Colors.black26,
              child: Row(
                mainAxisAlignment: MainAxisAlignment.start,
                children: <Widget>[
                  Container(
                    padding: EdgeInsets.fromLTRB(20, 10, 20, 10),
                    child: InkWell(
                      onTap: () {},
                      child: Column(
                        children: <Widget>[
                          like ? Icon(Icons.check) : Icon(Icons.add),
                          const Padding(
                            padding: EdgeInsets.all(5),
                          ),
                          Text(
                            '내가 찜한 콘텐츠',
                            style:
                                TextStyle(fontSize: 11, color: Colors.white60),
                          )
                        ],
                      ),
                    ),
                  ),
                  Container(
                    padding: EdgeInsets.fromLTRB(20, 10, 20, 10),
                    child: Container(
                      child: const Column(
                        children: <Widget>[
                          Icon(Icons.thumb_up),
                          Padding(padding: EdgeInsets.all(5)),
                          Text(
                            '평가',
                            style:
                                TextStyle(fontSize: 11, color: Colors.white60),
                          )
                        ],
                      ),
                    ),
                  ),
                  Container(
                    padding: EdgeInsets.fromLTRB(20, 10, 20, 10),
                    child: Container(
                      child: const Column(
                        children: <Widget>[
                          Icon(Icons.send),
                          Padding(padding: EdgeInsets.all(5)),
                          Text(
                            '공유',
                            style:
                                TextStyle(fontSize: 11, color: Colors.white60),
                          )
                        ],
                      ),
                    ),
                  )
                ],
              ),
            )
          ],
        )),
      ),
    );
  }
}
```

### carousel_slider.dart 수정

1. 정보 버튼 컨테이너의 IconButton의 onPressed를 수정하여 새 화면이 푸쉬되도록 한다.

```dart
... 중략

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

... 중략
```

### circle_slider.dart

1. makeCircleImages 함수를 수정하고 파라미터 BuildContext context를 추가해준다.

```dart
... 중략
children: makeCircleImages(context, movies),
... 중략
List<Widget> makeCircleImages(BuildContext context ,List<Movie> movies) {
  List<Widget> results = [];
  for (var i = 0; i < movies.length; i++) {
    // InkWell : 클릭 가능하도록 만들어줌
    results.add(InkWell(onTap: () {
      Navigator.of(context).push(MaterialPageRoute<Null>(
          fullscreenDialog: true,
          builder: (BuildContext context) {
            return DetailScreen(
              movie: movies[i],
            );
          }));
    },
... 중략
```

### box_slider.dart

1. makeBoxImages 함수를 수정하고 파라미터 BuildContext context를 추가해준다.

```dart
... 중략
children: makeBoxImages(context, movies),
... 중략
List<Widget> makeBoxImages(BuildContext context, List<Movie> movies) {
  List<Widget> results = [];
  for (var i = 0; i < movies.length; i++) {
    results.add(InkWell(
      onTap: () {
        Navigator.of(context).push(MaterialPageRoute<Null>(
            fullscreenDialog: true,
            builder: (BuildContext context) {
              return DetailScreen(
                movie: movies[i],
              );
            }));
      },
... 중략
```

### 실행결과

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix11.PNG)

클릭하면

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix12.PNG)

화면이 전환된다.

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  