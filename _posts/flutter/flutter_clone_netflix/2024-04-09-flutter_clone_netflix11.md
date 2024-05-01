---
layout: single
title: "Flutter netflix클론코딩11"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-04-09
last_modified_at: 2024-05-01
---

# 넷플릭스 클론 코딩
---

찜하기 한 콘텐츠 목록 보여주는 화면

## 내가 찜한 콘텐츠 화면 만들기
---

### like_screen.dart

1. screen폴더 안에 like_screen.dart를 생성
2. 아래 코드를 작성

```dart
import 'package:clone_netflix/model/model_movie.dart';
import 'package:clone_netflix/screen/detail_screen.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flutter/material.dart';

class LikeScreen extends StatefulWidget {
  _LikeSreenState createState() => _LikeSreenState();
}

class _LikeSreenState extends State<LikeScreen> {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Column(
        children: <Widget>[
          Container(
            padding: EdgeInsets.fromLTRB(20, 50, 20, 7),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.start,
              children: <Widget>[
                Image.asset(
                  'images/bbongflix_logo.png',
                  fit: BoxFit.contain,
                  height: 25,
                ),
                Container(
                  padding: EdgeInsets.only(left: 30),
                  child: Text(
                    '내가 찜한 콘텐츠',
                    style: TextStyle(fontSize: 14),
                  ),
                )
              ],
            ),
          ),
          _buildBody(context),
        ],
      ),
    );
  }

  Widget _buildBody(BuildContext context) {
    return StreamBuilder(
      stream: FirebaseFirestore.instance
          .collection('movie') // 파이어베이스에서 movie를 조회
          .where('like', isEqualTo: true) // 조건으로 like가 true인 애들로 해서
          .snapshots(), // 가져온다.
      builder: (context, snapshot) {
        if (!snapshot.hasData) {
          return LinearProgressIndicator();
        } else {
          return _buildList(context, snapshot.data!.docs);
        }
      },
    );
  }

  Widget _buildList(BuildContext context, List<DocumentSnapshot> snapshot) {
    return Expanded(
        child: GridView.count(
      crossAxisCount: 3,
      childAspectRatio: 1 / 1.5,
      padding: EdgeInsets.all(3),
      children: snapshot.map((e) => _buildListItem(context, e)).toList(),
    ));
  }

  Widget _buildListItem(BuildContext context, DocumentSnapshot date) {
    final movie = Movie.fromSnapShot(date);
    return InkWell(
      child: Image.network(movie.poster),
      onTap: () {
        Navigator.of(context).push(MaterialPageRoute<Null>(
            fullscreenDialog: true,
            builder: (BuildContext context) {
              return DetailScreen(movie: movie);
            }));
      },
    );
  }
}
```

### main.dart

1. 저장 부분을 대체했던 컨테이너를 삭제하고 LikeScreen 부른다.

```dart
import 'package:clone_netflix/screen/home_screen.dart';
import 'package:clone_netflix/screen/like_screen.dart'; // 추가
import 'package:clone_netflix/screen/more_screen.dart';
import 'package:clone_netflix/screen/search_screen.dart';
import 'package:clone_netflix/widget/bottom_bar.dart';
... 중략
class _MyAppState extends State<MyApp> {
            children: <Widget>[
              HomeScreen(),
              SearchScreen(),
              LikeScreen(), // 추가
              MoreScreen(),
            ],
          ),
... 중략
```

### 실행결과

좋아요한 콘텐츠가 잘 표시된다.

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix33.PNG)

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  
[git](https://github.com/subCharacters/clone_netflix/tree/class11)