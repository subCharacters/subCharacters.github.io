---
layout: single
title: "Linux find 명령어"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Command
tags:
  - Linux
  - find
toc: true
toc_sticky: true
date: 2024-03-08
last_modified_at: 2024-03-08
---

# 개요
---

리눅스에서 find 명령어는 파일을 찾는데 사용된다.


## 사용 방법
---

```bash
find [OPTION...] [PATH] [EXPRESSION...]
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -P   | 심볼릭 링크를 따라지 않고, 심볼릭 링크 자체 정보 사용       |
| -D   | 디버그 메시지 출력     |
| -L   | 심볼릭 링크에 연결된 정보 사용     |

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
find example.txt
find ./ -maxdepth 1 -type f -newermt 2024-02-27 ! -newermt 2024-03-08 -name "*.txt"

```
