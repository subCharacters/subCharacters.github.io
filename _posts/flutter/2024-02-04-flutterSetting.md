---
layout: single
title: "Flutter 환경설정"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Flutter
tags:
  - 환경설정
toc: true
toc_sticky: true
date: 2024-02-04
last_modified_at: 2024-02-04
---

# 플러터 설치하기
---

## 사전 준비
---

### 안드로이드 스튜디오 설치

1. 공식 홈페이지에서 안드로이드 스튜디오를 다운로드 & 설치를 진행한다.
 * <https://developer.android.com/studio>

### 안드로이드 SDK 및 Plugin 설치

1. 위에서 설치한 안드로이드 스튜디오를 실행.
2. 첫 실행하면 아마 다른 창이 나올 것인데 화면의 상관없이 SDK Manager를 연다.
 * ![image](..\..\images\flutter\flutter02.PNG)
3. SDK Tools탭으로 넘어가 관련 SDK를 설치한다.
 * ![image](..\..\images\flutter\flutter03.PNG)
4. 추가로 Plugins로 넘어가서 Flutter와 Dart를 설치한다.
 * Flutter 플러그인을 설치하면 자동으로 Dart도 설치해주는데 없다면 추가로 설치해주면 된다.
 * ![image](..\..\images\flutter\flutter04.PNG)

## 플러터 설치
---

1. 공식 홈페이지에서 플러터를 다운로드 받는다.
    * <https://flutter-ko.dev/get-started/install/windows>  
2. space, 특수 문자, Program Files 패스 이외의 폴더에 압축을 푼다.
    * ex) C:\flutter
3. 시스템 환경 변수 설정
    * 압축을 푼 경로 /bin 을 path에 추가한다.
    * ![image](..\..\images\flutter\flutter00.PNG)
4. flutter doctor를 실행
    * cmd화면에서 
    ``` shell 
    flutter doctor 
    ```
    를 실행시킨다.
    * ![image](..\..\images\flutter\flutter01.PNG)
    * 위 화면처럼 전부 체크 표시가 나오면 설치 끝.
    * ※IntelliJ, VisualStudio, VS Code는 필수는 아닌듯 하며 원하는 IDE를 설치하면 되는 듯 하다.  나는 그냥 다 가지고 있었을 뿐..


### 에러 대응

에러가 나오면 기본적으로 해당 내용으로 구글링하면 잘 나온다.

1. 
```shell
[!] Visual Studio - develop for Windows (Visual Studio Community 2019 16.11.33)
    X Visual Studio is missing necessary components. Please re-run the Visual Studio installer for the "Desktop
      development with C++" workload, and include these components:
        MSVC v142 - VS 2019 C++ x64/x86 build tools
         - If there are multiple build tool versions available, install the latest
        C++ CMake tools for Windows
        Windows 10 SDK
```
 * [안드로이드 스튜디오 설치로 이동](#C++/C#-개발-셋-설치)
2. 
``` shell
[-] Android toolchain - develop for Android devices
    • Android SDK at D:\Android\sdk
    ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
    • Try re-installing or updating your Android SDK,
      visit https://docs.flutter.dev/setup/#android-setup for detailed instructions.
```
 * [안드로이드 SDK 설치로 이동](#라이센스-등록)

#### C++/C# 개발 셋 설치

1. 도구 > 도구 및 기능 가져오기
2. [C++를 사용한 데스크톱 개발]을 다운로드 받는다.
3. 다운로드를 해도 에러가 날 경우 [C++를 사용한 데스크톱 개발]를 선택하면 오른쪽에 설치 세부정보가 표시되는데  
에러 메시지를 잘 읽고 체크가 빠져서 설치가 안된 부분이 있는지 확인하고 다시 받으면 된다.

#### 라이센스 등록
``` shell
flutter doctor --android-licenses
```
커맨드를 실행해서 라이센스 동의를 하면 된다.

### 프로젝트 생성

1. 안드로이드 스튜디오를 실행한 후 Flutter를 선택한다.
 * ![image](..\..\images\flutter\flutter05.PNG)
2. 프로젝트 명과 패스등을 설정하고 Create하면 끝!
 * ![image](..\..\images\flutter\flutter06.PNG)

## Reference
<https://flutter-ko.dev/get-started/install/windows>
