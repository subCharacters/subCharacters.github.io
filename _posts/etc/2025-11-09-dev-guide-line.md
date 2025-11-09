---
layout: single
title: "바이브 코딩을 위한 설정들"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - other
tags:
  - AI
  - VIBE
toc: true
toc_sticky: true
date: 2025-11-09
last_modified_at: 2025-11-09
---

---
필요에 따라 추가 삭제

# 엔지니어링 가이드라인

## 0) 목표·범위
- 목표: 가독성·안전성·예측가능성 향상, 리뷰/테스트 비용 최소화
- 범위: Java 21, Spring Boot 3.x (Web, Data, Batch 선택)

## 1) 네이밍·구조
- 클래스/인터페이스: PascalCase, 메서드/필드: camelCase, 상수: UPPER_SNAKE_CASE
- 테스트: ClassNameTests, method_shouldDoX_whenY()
- 패키지: **web / service / data** (컨트롤러·서비스·리포지토리 분리, 교차 의존 금지) **이거는 디자인 md에서 해야하는게 맞을거 같은데**
- 컬렉션 복수형, 불린은 긍정형(isXxx / hasXxx)

## 2) 의존성 주입·객체 생성
- 생성자 주입만(필드 주입 금지), 가능한 필드 final
- Controller/Service에서 new 금지 → Bean/Factory/Builder 사용
- HTTP/DB/파일 등 외부 자원은 인터페이스로 추상화하고 구현은 data에 둠

## 3) 불변성·파라미터
- DTO/값 객체는 불변(세터 금지), 컬렉션은 불변뷰/복사본 노출
- 파라미터 3개 초과 시 요청 DTO로 묶기
- null 반환 지양(서비스 내부), 컨트롤러 경계에서는 Optional 반환 금지

## 4) 예외·검증
- 경계 선검증: 컨트롤러에서 Bean Validation
- 사용자 메시지와 내부 로그 메시지 분리, 민감정보 금지
- 오류 응답: Spring ProblemDetail + 에러코드 `APP-####`
  - 400/401/403/404/409|422/500 매핑 표준화

## 5) 로깅·보안
- 구조적 로그 + Correlation-ID(`X-Correlation-Id`) 수용/생성
- PII/시크릿 마스킹, 요청/응답은 화이트리스트 필드만 로그
- 레벨: ERROR/WARN/INFO/DEBUG 기준 명시

## 6) 설정·프로필
- application-local/dev/prod.yml 분리, env·비밀관리 사용
- Timeout: HTTP c=1s r=2s t=3s, DB c=1s q=2s
- 회복탄력성: 재시도 3회(200ms 지수), 서킷 fail≥50%/≥10요청, half-open=5

## 7) REST (있는 경우)
- 컨트롤러는 매핑·검증·권한·에러 변환만 담당(비즈 로직은 서비스)
- 요청/응답 DTO ↔ 엔티티(또는 데이터 모델) 분리, OpenAPI 우선
- 페이징/정렬: page(0..), size(≤100), sort=field,asc|desc
- CI에서 spectral lint + openapi-diff

## 8) 데이터 접근(JPA/JDBC)
- 트랜잭션 시작은 서비스(@Transactional), 읽기/쓰기 분리
- N+1 방지, 대량 조회는 페이징/스트리밍
- 엔티티는 상태 최소화, 이벤트 남용 금지

## 9) 동시성·성능
- 공유 상태 최소화, 필요 시 Concurrent 컬렉션/락
- 블로킹 작업은 비동기/큐 고려, 캐시는 키·TTL·폴백 정의

## 10) 테스트
- 피라미드: 단위(70) > 슬라이스(25: @WebMvcTest/@DataJpaTest) > 통합(5: @SpringBootTest)
- 단위는 스프링 컨텍스트 미사용, 외부자원은 목/스텁
- 결정성: Clock/UUID/랜덤 주입으로 고정
- DB 표준: Testcontainers(권장) 또는 H2로 팀 단일화
- 커버리지 게이트: Line≥80%, Branch≥60% (핵심 모듈 Line≥90%)

## 11) 스프링 배치(선택)
- 청크 기본 200(`batch.chunk-size`), Reader/Processor/Writer 무상태
- 재시도/스킵 정책과 멱등 키 기록, 잡 메트릭 수집

## 12) 금지 목록
- 와일드카드 임포트, 하드코딩 시크릿, 과도한 리플렉션/매직
- 거대 클래스/메서드(LOC>500, 메서드>50) 경고
- 요청/응답 원문 전체 로그 금지(특히 개인정보/토큰)


---
PR 최소한의 규칙

## 수정 내용 설명
- 증상 요약, 기대 동작

## 재현 절차
1) …
2) …
3) …

## 수정 요약
- 핵심 변경점 한두 줄

## 검증
- 환경/명령/기대 결과

## 체크리스트
- [ ] 테스트 추가/통과
- [ ] 린트/빌드 통과


