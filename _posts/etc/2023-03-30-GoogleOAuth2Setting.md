---
layout: single
title: "Google Oauth2 등록"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - other
tags:
  - OAuth
  - Google
toc: true
toc_sticky: true
date: 2023-03-30
last_modified_at: 2023-03-31
---

### 개요
---
> Google OAuth2 등록

<span style="font-size:13pt">
Spring Boot에서 구글 인증 연습을 하기에 앞서 구글 OAuth등록에 대해서 작성.<br/>
</span>

### 구글 API등록
---

<span style="font-size:13pt">
<a href="https://console.cloud.google.com/" target="_blank">구글 클라우드</a>로 접속 및 로그인<br/>
왼쪽 상단에 프로젝트를 클릭하고 새 프로젝트를 만들어준다.<br/>
</span>

#### 프로젝트 작성

![image](..\..\images\other\GoogleOAuth\GoogleOAuth01.PNG)

![image](..\..\images\other\GoogleOAuth\GoogleOAuth02.PNG)

#### 동의 화면 작성

<span style="font-size:13pt">
내외부 설정하고 앱이름, 사용자 지원 이메일과 맨 아래에 연락 메일만 적으면 된다.<br/>
</span>

![image](..\..\images\other\GoogleOAuth\GoogleOAuth03.PNG)

![image](..\..\images\other\GoogleOAuth\GoogleOAuth04.PNG)

![image](..\..\images\other\GoogleOAuth\GoogleOAuth05.PNG)

![image](..\..\images\other\GoogleOAuth\GoogleOAuth06.PNG)

#### 사용자 인증정보 작성

<span style="font-size:13pt">
승인할 url을 설정해준다.<br/>
승인된 url만이 oauth 인증을 해준다.<br/>
나중에 추가 삭제 가능하니 적당히 적어놓음.<br/>
</span>

![image](..\..\images\other\GoogleOAuth\GoogleOAuth07.PNG)

![image](..\..\images\other\GoogleOAuth\GoogleOAuth08.PNG)

#### ID, PW보관 

<span style="font-size:13pt">
이게 없으면 인증이 안되니 잘 보관하자.<br/>
</span>

![image](..\..\images\other\GoogleOAuth\GoogleOAuth09.PNG)
