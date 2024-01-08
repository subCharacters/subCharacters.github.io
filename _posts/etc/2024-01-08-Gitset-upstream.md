---
layout: single
title: "git 로컬 브랜치에 origin브랜치 연결"
author_profile: true
sidebar:
  nav: "docs"
categories:
  - Git
tags:
  - Branch
toc: true
toc_sticky: true
date: 2024-01-08
last_modified_at: 2024-01-08
---

### 개요

---

<span style="font-size:13pt">
git 로컬 브랜치에 origin브랜치 연결하는 법.<br/>
git push시 origin을 지정 안하면 에러가 난다.<br/>
git push origin master와 같이 따로 지정 안해도 되는 법.<br/>
</span>

### 코드

---

```shell
git push --set-upstream origin master
```
