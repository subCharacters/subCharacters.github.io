---
layout: single
title: "React + Chakra UI 앱의 GitHub Actions + AWS S3 배포 자동화"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - other
tags:
  - React
  - ChakraUI
  - GitHubActions
  - S3
  - CloudFront
toc: true
toc_sticky: true
date: 2025-06-15
last_modified_at: 2025-06-15
---

## 목표

```
React로 만든 정적 웹사이트를 AWS S3에 배포하고, GitHub Actions를 통해 자동화된 CI/CD 환경을 구축한다.
```

## 개요

<span style="font-size:13pt">
이번 글에서는 Chakra UI로 구성된 정적 React 앱을 AWS S3 + CloudFront + Route 53 기반으로 배포하고, GitHub Actions를 이용해 자동 배포 파이프라인을 구축한 과정을 정리한다.
</span>

---

## 1. 개발 환경 구성

<span style="font-size:13pt">
먼저 `create-react-app`을 이용해 프로젝트를 생성하고 Chakra UI를 설치한다.<br>
Node.js 설치 이후 아래 명령어로 환경을 구성하였다.<br>
Node.js 버전은 22.16
</span>

```bash
npx create-react-app subcharacters-web
cd subcharacters-web
npm install @chakra-ui/react@2.8.2 @emotion/react @emotion/styled framer-motion
```

`App.js`에 ChakraProvider를 적용해 기본 화면이 보이도록 구성하였다.

---

## 2. AWS S3 + CloudFront 구성

<span style="font-size:13pt">
예전에 미리 해놓았던 AWS S3 + CloudFront + Route 53 를 그대로 이용했다.
</span>

- S3 버킷명: `블로그 용 s3 버킷`
- CloudFront 배포 ID: `EXXXXXXXX`
- `index.html` 및 에러 문서도 동일하게 `index.html`로 설정 (SPA 라우팅용)

---

## 3. IAM 사용자 생성 및 권한 부여

<span style="font-size:13pt">
GitHub Actions에서 AWS 리소스에 접근할 수 있도록 최소 권한 IAM 사용자를 생성하고, 아래 정책을 연결하였다.<br>
정책은 아래와 같이 s3와 cf에만 접근 가능하도록 하였고, cf에 무효화 설정하여서 캐시 초기화 하도록 하였다.<br>
사용자를 생성하고 해당 사용자 안에 들어가 엑세스 키를 생성할 필요가 있다.<br>
생성할 엑세스 키는 서드 파티 서비스를 선택하면 되고 한번 생성한 키는 나중에 못 보니 따로 저장했다.
</span>

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::블로그 용 s3 버킷",
        "arn:aws:s3:::블로그 용 s3 버킷/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "cloudfront:CreateInvalidation",
      "Resource": "*"
    }
  ]
}
```

---

## 4. GitHub Actions 자동 배포 구성

```yaml
# .github/workflows/deploy.yml
name: Deploy to S3 and Invalidate CloudFront

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Build the app
        run: npm run build

      - name: Upload to S3
        uses: jakejarvis/s3-sync-action@master
        with:
          args: --delete
        env:
          AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
          SOURCE_DIR: build

      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.DISTRIBUTION_ID }} \
            --paths "/*"
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: ${{ secrets.AWS_REGION }}
```

---

## 5. 에러 및 삽질 기록

### ❌ npx 실행 불가
<span style="font-size:13pt">
npx 로드 실패하였다.
</span>
```
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```
→ 파워쉘에서 위 코드를 실행 후 npx실행.

### ❌ create-react-app 오류 (대문자 포함)
<span style="font-size:13pt">
리액트 프로젝트를 생성할때 폴더 명에 대문자가 있어서 에러가 났다.
</span>
```
Cannot create a project named "subCharacters-web" because of npm naming restrictions.
```
→ 폴더명을 `subcharacters-web`로 소문자 처리하여 해결

### ❌ Chakra UI `theme._config` 오류
<span style="font-size:13pt">
테스트용으로 만든 app.js의 코드가 최신 차크라에서 제대로 동작이 안되어서 다운그레이드 하였다.
</span>
```
Cannot read properties of undefined (reading '_config')
```
→ Chakra UI 최신 버전 문제로, `@chakra-ui/react@2.8.2`로 다운그레이드하여 해결

### ❌ `--acl public-read` 에러
<span style="font-size:13pt">
배포 중 해당 에러가 발생하여 with: args: 이 부분을 수정.
</span>
```
An error occurred (AccessControlListNotSupported)
```
→ 최신 S3는 ACL을 지원하지 않으므로 `--acl public-read` 옵션 제거

### ✅ 최종적으로 모든 에러 해결 후 자동 배포 성공

---

## 마무리

<span style="font-size:13pt">
이제 코드만 푸시하면 React 앱이 S3에 자동으로 배포되고, CloudFront 캐시도 무효화되므로 변경사항이 바로 반영된다.<br>
단순한 정적 호스팅부터 고급 CI/CD까지 경험할 수 있는 좋은 실습이었다.
</span>
