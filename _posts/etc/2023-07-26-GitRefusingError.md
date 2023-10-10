---
layout: single
title: "git pull origin matser에러"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - Git
tags:
  - Error
  - Refusing
toc: true
toc_sticky: true
date: 2023-07-26
last_modified_at: 2023-07-26
---

### 개요

---

> git pull origin matser
> fatal: couldn't find remote ref matser
> fatal: refusing to merge unrelated histories

<span style="font-size:13pt">
Git에서 최신화 하려는데 위의 에러가 발생할 때.<br/>
</span>

### 원인

---

<span style="font-size:13pt">
신규 프로젝트 올릴때 많이 발생한다.<br/>
두 개의 이력이 서로 연관이 없을때 발생한다.<br/>
깃에서 리포지토리를 생성할때 리드미를 생성하고 깃에 올리려 하려고 할때 등..<br/>
</span>

#### 해결

```shell
git pull origin master --allow-unrelated-histories
```

<span style="font-size:13pt">
위의 명령어를 실행 하면 된다.<br/>
--allow-unrelated-histories: 이 옵션은 병합하는 두 브랜치 간에 관련이 없는(commit history가 공통 조상이 없는) 경우에도 병합을 허용하는 것을 의미한다.
</span>
