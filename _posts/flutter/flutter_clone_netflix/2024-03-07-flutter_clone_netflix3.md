---
layout: single
title: "Flutter netflix클론코딩3"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter_clone_netflix
tags:
  - 클론코딩
toc: true
toc_sticky: true
date: 2024-03-07
last_modified_at: 2024-03-07
---

# 넷플릭스 클론 코딩
---

넷플릭스 프로젝트에 들어갈 영화 더미데이터 모델 만들기

## 영화 더미데이터 모델 만들기
---

### model_movie.dart

1. model 폴더안에 model_movie.dart파일을 생성
2. 생성한 model_movie.dart 파일에 코드를 작성

 ```dart
class Movie {
  final String title; // 제목
  final String keyword; // 키워드
  final String poster; // 포스터의 url
  final bool like; // 찜 기능

  // fromMap은 map 데이터 구조를 초기화하는 생성자 메서드
  // Dynamic 타입은 타입 체크를 하지 않고 런타임에 객체의 타입을 결정
  // 타입 안정성을 보장하지 않지만, 동적으로 객체의 타입을 변경하는 경우에 유용
  Movie.fromMap(Map<String, dynamic> map)
      : title = map['title'],
        keyword = map['keyword'],
        poster = map['poster'],
        like = map['like'];

  @override
  String toString() => "Movie<$title:$keyword>";
}

 ```

### pubspec.yaml 수정

1. 강의 안에 샘플 이미지 파일이 있으므로 이미지 파일을 다운로드
2. 다운로드한 이미지는 images폴더로 이동
3. pubspec.yaml 수정

```yaml
  # To add assets to your application, add an assets section, like this:
  assets:
     - images/bbongflix_logo.png
     - images/test_movie_1.png # 새로 추가한 것
```

### home_screen.dart 수정

1. class _HomeScreenState 안에 이하의 코드를 추가

```dart
class _HomeScreenState extends State<HomeScreen> {
  // 더미데이터
  List<Movie> movies = [
    Movie.fromMap(
        {
          'title': '사랑의 불시착',
          'keyword': '사랑/로맨스/판타지',
          'poster': 'test_movie_1.png',
          'like': false
        }
    )
  ];
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
  }
```

## Reference
[인프런](https://www.inflearn.com/course/flutter-netflix-clone-app)  