---
layout: single
title: "Flutter netflix클론코딩7"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-03-31
last_modified_at: 2024-03-31
---

# 넷플릭스 클론 코딩
---

프로필 화면 만들기.(더보기 화면)  
계정, SNS 공유등의 대한 기능은 만들지 않고 프로필 화면으로 대체.

## 프로필 화면 만들기
---

### pubspec.yaml 수정
1. dependencies: 부분에 flutter_linkify와 url_launcher를 추가하여 라이브러리를 다운받는다.
2. flutter_linkify는 앱에서 링크를 하이퍼링크로 바꿔준다.
3. url_launcher는 앱에서 외부 URL을 여는데 사용된다.

``` yaml
flutter_linkify:
url_launcher:
```

### more_screen.dart

1. screen폴더 안에 more_screen.dart를 생성
2. 아래 코드를 작성. (링크 처리부분은 강의와 다름)

```dart
import 'package:flutter/material.dart';
import 'package:flutter_linkify/flutter_linkify.dart';
import 'package:url_launcher/url_launcher.dart';

class MoreScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      child: Center(
        child: Column(
          children: <Widget>[
            Container(
              padding: EdgeInsets.only(top: 50),
              child: CircleAvatar(
                radius: 100,
                backgroundImage: AssetImage('images/bbongflix_logo.png'),
                backgroundColor: Colors.black, // 디폴트가 화이트라 강좌와 똑같이 하기 위해 블랙
              ),
            ),
            Container(
              padding: EdgeInsets.only(top: 15),
              child: Text(
                'Jeong',
                style: TextStyle(
                  fontWeight: FontWeight.bold,
                  fontSize: 25,
                  color: Colors.white
                ),
              ),
            ),
            Container(
              padding: EdgeInsets.all(10),
              width: 140,
              height: 5,
              color: Colors.red,
            ),
            Container(
              padding: EdgeInsets.all(10),
              // 실제 클릭 가능하게 하기 위해 flutter_linkify와 url_launcher패키지를 설치한다.
              // 강좌에서 사용된 로직은 비추천이므로 최신 코드로 대체
              child: Linkify(onOpen: (link) async {
                Uri url = Uri.parse(link.url);
                print(link.url);
                if (await canLaunchUrl(url)) {
                  await launchUrl(url);
                }
              },
                text: 'https://github.com/subCharacters',
                style: TextStyle(fontWeight: FontWeight.bold, fontSize: 20),
                linkStyle: TextStyle(color: Colors.white),
              ),
            ),
            Container(
              padding: EdgeInsets.all(10),
              child: TextButton(
                style: TextButton.styleFrom(
                  backgroundColor: Colors.red,
                  shape: RoundedRectangleBorder(
                    borderRadius: BorderRadius.circular(0)
                  )
                ),
                onPressed: () {},
                child: Container(
                  child: Row(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                      Icon(Icons.edit, color: Colors.white,),
                      SizedBox(width: 10,),
                      Text(
                        '프로필 수정하기',
                        style: TextStyle(color: Colors.white),
                      )
                    ],
                  ),
                ),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

### main.dart 수정

1. more가 있는 더보기 컨테이너부분에 MoreScreen으로 바꾼다.

```dart
... 중략

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
              MoreScreen(), // 이 부분
            ],
          ),
          bottomNavigationBar: Bottom(),
        ),
... 중략
```

### 실행결과

* ![image](..\..\images\flutter\clone\netflix\flutter_clone_netflix13.PNG)

애뮬레이터에서는 링크가 먹히지 않는다.

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  
[git](https://github.com/subCharacters/clone_netflix/tree/class7)