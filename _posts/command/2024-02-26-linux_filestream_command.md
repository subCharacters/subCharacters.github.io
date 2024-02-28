---
layout: single
title: "Linux 파일스트림 관련 명령어"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - Command
tags:
  - Linux
  - grep
  - cat
  - head
  - tail
  - more
toc: true
toc_sticky: true
date: 2024-02-28
last_modified_at: 2024-02-28
---

# 개요
---

파일 스트림 관련 리눅스 커맨드이다.


## head
---

파일의 첫부분부터 출력한다.

### 사용방법

```bash
head [옵션] 파일명
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -n   | 출력할 행 수 지정       |
| -c   | 출력할 바이트 수 지정     |

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
head example.txt # 처음 10행 출력
head -n 20 example.txt  # 처음부터 지정한 20행 출력
head -c 100 example.txt  # 처음 100바이트 출력
```

## tail
---

파일의 끝 부분을 출력한다.

### 사용방법

```bash
tail [옵션] 파일명
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -n   | 출력할 행 수 지정     |
| -c   | 출력할 바이트 수 지정     |
| -f   | 파일의 끝을 모니터링하여 실시간으로 출력     |

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
tail example.txt # 마지막 10행 출력
tail -n 20 example.txt  # 마지막 20행 출력
tail -c 100 example.txt  # 마지막 100바이트 출력
tail -f example.txt # 실시간 출력
```

## cat
---

cat은 파일 전체 내용을 출력하는데 시간이 매우 오래 걸리기 때문에 특정 부분만 찾고자할때는 less나 grep head등을 사용하는게 좋다.  
또한 text파일에 대해서만 동작하며 바이너리 내용은 해석하지 못한다.

### 사용방법

```bash
cat [파일1]
cat [파일1] [파일2]...
cat [파일1] > [파일2]
cat [파일1] ... >> [파일2]
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                |
|------|---------------------|
| -n   | 라인 번호와 함께 출력     |
| -b   | 빈 라인을 제외하고 문자열이 있는 라인만 번호와 함께 출력     |
| -s   | 빈 라인들을 한줄로 출력     |

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.  

```bash
cat example.txt # 파일 내용 출력
cat example.txt example2.txt # 두개 파일을 연속적으로 출력
cat example.txt > example2.txt # example.txt을 example2.txt에 덮어씌움
cat example.txt >> example2.txt # example.txt을 example2.txt에 추가(cat example.txt example2.txt >> example3.txt와 같이 사용 가능)
cat -n example.txt  # 줄 번호 표시
cat -b example.txt  # 빈줄 제외하고 줄번호 표시
cat -s example.txt # 비어있는 줄 제거
```

## more
---

주로 파일의 내용을 한 번에 한 페이지씩 출력하는 데 사용되며 화면을 스크롤하면서 파일의 내용을 확인할 수 있다.  
space 키를 눌러 다음 페이지로 이동하고, q 키를 눌러 나갈 수 있다.  
more는 아래로만 페이지 이동이 된다.  
less를 쓰는게 낫다.

### 사용방법

```bash
more [파일1]
```

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
more example.txt # 파일 내용 출력
cat example.txt | more # 와 같이 사용도 가능
```

## less
---

more와 동작이 같다.  
방향키로 이동하거나 space로 페이지를 넘길 수 있고 q 키를 눌러 나갈 수 있다.  
more와 달리 위 아래로 스크롤이 가능하다.  
less가 more보다 세련되었으므로 more를 사용하느니 less를 사용하는게 좋다.  

### 사용방법

```bash
less [파일1]
```

예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
less example.txt # 파일 내용 출력
cat example.txt | less # 와 같이 사용도 가능
```

## grep
---

주로 파일에서 지정된 패턴을 검색하는 데 사용되는 명령어이다.  
특정 문자열이나 정규식과 일치하는 라인을 찾아 출력한다.

### 사용방법

```bash
grep [옵션] [패턴] [파일]
```

주요 옵션은 아래와 같다.

| 옵션 | 설명                               |
|------|------------------------------------|
| -i   | 대소문자 구분 없이 검색            |
| -n   | 일치하는 라인의 행 번호 출력      |
| -v   | 지정된 패턴과 일치하지 않는 라인 출력  |
| -c   | 일치하는 라인의 개수만 출력       |
| -r   | 하위 디렉토리까지 재귀적으로 검색  |
| -A   | 일치하는 라인 뒤에 몇 줄을 추가로 출력 |
| -B   | 일치하는 라인 앞에 몇 줄을 추가로 출력 |
| -C   | 일치하는 라인 앞뒤로 몇 줄을 추가로 출력 |
| -E   | 정규식을 적용 |


예를 들어 example.txt가 있다고 가정한다면 아래와 같이 사용 가능하다.

```bash
grep "test" example.txt # 파일내 test가 있는 행을 출력
grep -i "TEST" exemple.txt # 대소문자 구분없이 히트한 행을 출력
grep -r --exclude-dir={dir1,dir2} "pattern" /path/to/search # --exclude-dir 옵션을 사용하여 특정 디렉터리를 제외
grep -n "test" example.txt # 일치하는 라인의 번호를 출력
grep -v "test" example.txt # 지정된 패턴과 일치하지 않는 라인을 출력
grep -c "test" example.txt # 일치하는 라인의 개수를 출력
grep "word1\|word2" example.txt # \|를 사용하여 여러 단어로 이루어진 패턴을 검색
grep -E "[0-9]{3}" example.txt # 정규식을 적용
```

### 정규식 패턴

| 정규식  | 설명                                      |
|---------|-------------------------------------------|
| `.`     | 어떤 한 문자와 일치                      |
| `*`     | 앞선 문자 또는 그룹이 0회 이상 반복      |
| `?`     | 앞선 문자 또는 그룹이 0회 또는 1회 등장 |
| `[]`    | 대괄호 안의 어떤 문자와 일치            |
| `^`     | 문자열이나 라인의 시작 부분을 나타냄      |
| `$`     | 문자열이나 라인의 끝 부분을 나타냄        |
| `()`    | 그룹화를 나타냄                           |
| `\`     | 특수 문자를 이스케이프                    |
