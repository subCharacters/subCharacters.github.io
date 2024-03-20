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
find [PATH] [OPTION...] [EXPRESSION...]
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -P   | 심볼릭 링크를 따라지 않고, 심볼릭 링크 자체 정보 사용       |
| -D   | 디버그 메시지 출력     |
| -L   | 심볼릭 링크에 연결된 정보 사용     |

주요 표현식은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -name   | 지정된 문자열 패턴에 해당하는 파일 검색.       |
| -empty   | 빈 디렉토리 또는 크기가 0인 파일 검색.     |
| -delete   | 검색된 파일 또는 디렉토리 삭제.     |
| -exec   | 검색된 파일에 대해 지정된 명령 실행.     |
| -newermt   | 'yyyy-mm-dd' 형식으로 특정 일 지정. 그 이후에 수정된 파일 검색.     |
| -mindepth   | 검색을 시작할 하위 디렉토리 최소 깊이 지정.     |
| -maxdepth   | 검색할 하위 디렉토리의 최대 깊이 지정.     |
| -maxdepth   | 검색할 하위 디렉토리의 최대 깊이 지정.     |
| -ctime   | 파일 내용 및 속성 변경(change) 시각을 기준으로 파일 검색.     |
| -mtime   | 파일의 데이터 수정(modify) 시각을 기준으로 파일 검색.     |

표현식은 두 개 이상으로 조합이 가능하다.

| 표현식                                      | 설명                        |
|-------------------------------------------|-----------------------------|
| (expression)                              | expression 우선순위 지정.  |
| !expression <br> -not expression         | expression 결과에 NOT 연산. |
| expression -a expression <br> expression -and expression <br> expression expression | expression 간 AND 연산.    |
| expression -o expression <br> expression -or expression | expression 간 OR 연산.     |


예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
find example.txt

# 현재 디렉토리 아래에서 1단계까지만 파일을 찾음
# 찾은 파일은 정규 파일이어야 함
# 수정된 시간이 2024년 2월 27일 이후이고, 2024년 3월 8일 이전이어야 함
# 확장자가 ".txt"인 파일만 검색
find ./ -maxdepth 1 -type f -newermt 2024-02-27 ! -newermt 2024-03-08 -name "*.txt"

# "/path/to/search" 디렉토리 아래에서 파일을 찾음
# 찾은 파일은 정규 파일이어야 함
# 수정된 시간이 30일 이내여야 함
# 검색된 파일은 ls -l 명령으로 자세한 정보를 출력
find /path/to/search -type f -newermt "$(date -d '30 days ago' +'%Y-%m-%d')" -exec ls -l {} +

# 현재 디렉토리에 있는 파일 및 디렉토리 리스트 표시
find

# 대상 디렉토리에 있는 파일 및 디렉토리 리스트 표시
find [PATH]

# 현재 디렉토리 아래 모든 파일 및 하위 디렉토리에서 파일 검색
find . -name [FILE]

# 전체 시스템(루트 디렉토리)에서 파일 검색
find / -name [FILE]

# 파일 이름이 특정 문자열로 시작하는 파일 검색
find . -name "STR*"

# 파일 이름에 특정 문자열이 포함된 파일 검색
find . -name "*STR*"

# 파일 이름이 특정 문자열로 끝나는 파일 검색
find . -name "*STR"

# 빈 디렉토리 또는 크기가 0인 파일 검색
find . -empty

# 특정 확장자를 가진 모든 파일 검색 후 삭제
find . -name "*.EXT" -delete

# 검색된 파일 리스트를 줄 바꿈 없이 이어서 출력하기
find . -name [FILE] -print0

# 파일 또는 디렉토리만 검색하기
find . -name [FILE] -type f

# 파일 크기를 사용하여 파일 검색
find . -size +[N]c -and -size -[M]c

# 검색된 파일에 대한 상세 정보 출력. (find + ls)
find . -name [FILE] -exec ls -l {} \;

# 검색된 파일의 라인 수 출력. (find + wc)
find . -name [FILE] -exec wc-l {} \;

# 검색된 파일에서 문자열 찾기. (find + grep)
find . -name [FILE] -exec grep "STR" {} \;

# 검색 결과를 파일로 저장. (find, redirection)
find . -name [FILE] > [SAVE_FILE]

# 검색 중 에러 메시지 출력하지 않기 (find, redirection)
find . -name [FILE] 2> /dev/null

# 하위 디렉토리 검색하지 않기
find . -maxdepth 1 -name [FILE]

# 검색된 파일 복사. (find + cp)
find . -name [FILE] -exec cp {} [PATH] \;

```