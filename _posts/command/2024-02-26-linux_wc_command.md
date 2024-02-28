---
layout: single
title: "Linux wc 명령어로 텍스트 파일 특성 파악하기"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Command
tags:
  - Linux
  - wc
toc: true
toc_sticky: true
date: 2024-02-26
last_modified_at: 2024-02-26
---

# 개요
---

리눅스에서 wc 명령어는 "word count"의 약자로, 주어진 텍스트 파일의 행 수, 단어 수, 문자 수를 세는 데 사용된다.


## 사용 방법
---

```bash
wc [옵션] 파일명
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -l   | 행 수만 세기       |
| -w   | 단어 수만 세기     |
| -c   | 문자 수만 세기     |

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
wc example.txt
wc -l example.txt  # 행 수만 표시
wc -w example.txt  # 단어 수만 표시
wc -c example.txt  # 문자 수만 표시
```