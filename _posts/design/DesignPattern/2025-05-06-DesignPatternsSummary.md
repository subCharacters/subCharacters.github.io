---
layout: single
title: "디자인패턴 요약"
author_profile: true
sidebar:
  nav: "docs"
categories: 
  - DesignPatterns
tags:
  - 디자인 패턴 요약
toc: true
toc_sticky: true
date: 2025-05-05
last_modified_at: 2025-05-05
---

**GoF 23 Design Patterns - 핵심 요약 정리**

---

### 상황별 패턴 선택 요약 (패턴명 기준 알파벳 순)

| 상황                              | 적합한 패턴                                                                       | 설명                               |
| ------------------------------- | ---------------------------------------------------------------------------- | -------------------------------- |
| ✅ 관련된 객체들을 한번에 생성하고 싶다          | [**Abstract Factory**](/designpatterns/AbstractFactoryPattern/)              | 객체군을 통째로 생성하는 팩토리 제공             |
| ✅ 기존 인터페이스를 원하는 형태로 변환하고 싶다     | [**Adapter**](/designpatterns/AdapterPattern/)                               | 호환되지 않는 인터페이스를 연결함               |
| ✅ 추상화와 구현을 독립적으로 확장하고 싶다        | [**Bridge**](/designpatterns/BridgePattern/)                                 | 인터페이스와 구현을 분리하여 독립 확장            |
| ✅ 복잡한 객체를 단계적으로 생성하고 싶다         | [**Builder01**](/designpatterns/BuilderPattern01/)  [**Builder02**](/designpatterns/BuilderPattern02/)                               | 유연한 구성 방식, 메서드 체이닝 지원            |
| ✅ 요청을 처리할 객체들을 체인 형태로 연결하고 싶다   | [**Chain of Responsibility**](/designpatterns/ChainOfResponsibilityPattern/) | 순차 처리 가능, 처리자 간 결합도 낮춤           |
| ✅ 기능을 명령처럼 캡슐화하고 실행/취소하고 싶다     | [**Command**](/designpatterns/CommandPattern/)                               | 요청을 객체로 캡슐화하여 실행/취소 지원           |
| ✅ 트리 구조를 일관되게 처리하고 싶다           | [**Composite**](/designpatterns/CompositePattern/)                           | 개별/복합 객체를 동일하게 취급                |
| ✅ 동적으로 객체의 기능을 확장하고 싶다          | [**Decorator**](/designpatterns/DecoratorPattern/)                           | 상속 없이 기능을 조합하여 확장                |
| ✅ 복잡한 서브시스템을 단순한 인터페이스로 감싸고 싶다  | [**Facade**](/designpatterns/FacadePattern/)                                 | 복잡한 로직을 단순화된 인터페이스로 제공           |
| ✅ 객체 생성을 서브클래스에 위임하고 싶다         | [**Factory Method**](/designpatterns/FactoryMethodPattern/)                  | 객체 생성을 하위 클래스에 위임                |
| ✅ 단순 언어 문법을 해석하고 실행하고 싶다        | [**Interpreter**](/designpatterns/InterpreterPattern/)                       | 문법 구조를 클래스 기반으로 표현               |
| ✅ 컬렉션을 일관된 방식으로 순회하고 싶다         | [**Iterator**](/designpatterns/IteratorPattern/)                             | 내부 구조 노출 없이 순회 가능                |
| ✅ 객체 간 복잡한 상호작용을 중재하고 싶다        | [**Mediator**](/designpatterns/MediatorPattern/)                             | 객체 간 직접 의존 제거, 중앙 제어자 도입         |
| ✅ 객체 상태를 저장/복원하고 싶다             | [**Memento**](/designpatterns/MementoPattern/)                               | 실행 취소, 저장/복원 기능 구현               |
| ✅ 상태 변화에 따라 관련 객체들에게 자동 알림하고 싶다 | [**Observer**](/designpatterns/ObserverPattern/)                             | 상태 변화 시 자동 알림 전달                 |
| ✅ 기존 객체를 복사해서 새로운 객체로 만들고 싶다    | [**Prototype**](/designpatterns/PrototypePattern/)                           | 복사 기반 객체 생성, 성능 최적화              |
| ✅ 실제 객체에 대한 접근을 제어하고 싶다         | [**Proxy**](/designpatterns/ProxyPattern/)                                   | 접근 제어, 지연 로딩, 로깅 등 구현 가능         |
| ✅ 하나의 인스턴스를 전역적으로 공유하고 싶다       | [**Singleton**](/designpatterns/SingletonPattern/)                           | 오직 하나의 인스턴스 보장                   |
| ✅ 객체의 상태에 따라 동작을 다르게 하고 싶다      | [**State**](/designpatterns/StatePattern/)                                   | 상태를 객체로 분리, 전이 가능                |
| ✅ 알고리즘을 교체 가능하게 만들고 싶다          | [**Strategy**](/designpatterns/StrategyPattern/)                             | 인터페이스 기반 알고리즘 교체 지원              |
| ✅ 알고리즘의 골격을 유지하며 일부만 오버라이딩하고 싶다 | [**Template Method**](/designpatterns/TemplateMethodPattern/)                | 상위 클래스에서 틀 제공, 세부 구현은 하위 클래스에 위임 |
| ✅ 데이터 구조에 다양한 기능을 추가하고 싶다       | [**Visitor**](/designpatterns/VisitorPattern/)                               | 기능을 구조 외부에서 정의하여 확장 가능           |

---

(아래는 각 패턴별 정의 및 예시 - 알파벳 순)

### 1. Abstract Factory Pattern

* **목적**: 관련된 객체들의 집합을 생성하는 인터페이스 제공
* **사용 예**: GUI 테마 스킨, OS별 UI 위젯 생성

### 2. Adapter Pattern

* **목적**: 기존 클래스의 인터페이스를 클라이언트가 기대하는 형태로 변환
* **사용 예**: 외부 라이브러리 클래스 포장, 호환성 문제 해결

### 3. Bridge Pattern

* **목적**: 추상화와 구현을 분리하여 독립적으로 확장 가능하게
* **사용 예**: 다양한 기기 + 리모컨 조합, 뷰 + 데이터 소스

### 4. Builder Pattern 01 (기본형)

* **목적**: 복잡한 객체 생성을 단계별로 나눠서 처리
* **사용 예**: 생성자 파라미터가 많은 객체, JSON/XML 구성 등

### 5. Builder Pattern 02 (플루언트 인터페이스)

* **목적**: 메서드 체이닝을 통해 가독성 높고 유연한 객체 생성 지원
* **사용 예**: Lombok @Builder, JPA Criteria API

### 6. Chain of Responsibility Pattern

* **목적**: 요청을 처리할 수 있는 객체들을 체인으로 연결하고, 순차적으로 처리 위임
* **사용 예**: 이벤트 핸들러, 로깅 체인

### 7. Command Pattern

* **목적**: 요청을 객체로 캡슐화하여 실행/취소/재실행을 지원
* **사용 예**: 되돌리기, 매크로 녹화, 큐잉 시스템

### 8. Composite Pattern

* **목적**: 트리 구조를 동일한 인터페이스로 처리 (개별 객체와 그룹 객체를 동일하게)
* **사용 예**: 폴더-파일 구조, UI 컴포넌트

### 9. Decorator Pattern

* **목적**: 객체에 책임을 동적으로 추가 (상속 대신 합성)
* **사용 예**: 스트림 처리, 필터 체인, 무기 강화

### 10. Facade Pattern

* **목적**: 복잡한 서브시스템을 단순한 인터페이스로 감싸서 사용
* **사용 예**: 시스템 초기화, 복잡한 API 감싸기

### 11. Factory Method Pattern

* **목적**: 객체 생성을 서브 클래스에 위임하여 확장성을 높임
* **사용 예**: Document 생성기, Notification 객체 생성

### 12. Interpreter Pattern

* **목적**: 간단한 언어의 문법을 클래스 구조로 표현하고 해석
* **사용 예**: 수식 계산기, SQL 파서, 게임 스크립트

### 13. Iterator Pattern

* **목적**: 컬렉션 구조를 노출하지 않고 일관된 방식으로 순회 가능하게
* **사용 예**: List, Set, Tree 구조 순회

### 14. Mediator Pattern

* **목적**: 객체 간 복잡한 의존 관계를 중앙 중재자 객체로 분리
* **사용 예**: 채팅방 메시지 분배, UI 컴포넌트 상호작용

### 15. Memento Pattern

* **목적**: 객체의 상태를 저장하고 복원할 수 있도록 캡슐화
* **사용 예**: 실행 취소, 게임 저장/로드

### 16. Observer Pattern

* **목적**: 한 객체의 상태 변화 시 관련 객체들에게 자동 알림 전달
* **사용 예**: UI 업데이트, 이벤트 리스너, 모델-뷰 동기화

### 17. Prototype Pattern

* **목적**: 기존 객체를 복사해서 새 객체 생성 (깊은 복사 지원)
* **사용 예**: 게임 몬스터 복제, 도형 복사

### 18. Proxy Pattern

* **목적**: 실제 객체 접근을 제어하는 대리 객체 제공
* **사용 예**: 지연 로딩, 접근 제어, 캐싱

### 19. Singleton Pattern

* **목적**: 클래스의 인스턴스를 오직 하나만 생성하고 전역적으로 접근 가능하게
* **사용 예**: 설정 관리자, 로깅, 캐시

### 20. State Pattern

* **목적**: 객체의 상태에 따라 동작을 변경하고, 상태 전이를 객체로 분리
* **사용 예**: 게임 플레이어 상태, TCP 연결 상태

### 21. Strategy Pattern

* **목적**: 알고리즘을 인터페이스로 정의하고, 런타임에 교체 가능하게
* **사용 예**: 정렬 방법, 경로 탐색 알고리즘, 요금 정책

### 22. Template Method Pattern

* **목적**: 알고리즘의 골격을 정의하고, 일부 단계를 서브클래스가 구현하게 함
* **사용 예**: 프레임워크 후크 메서드, 게임 턴 처리

### 23. Visitor Pattern

* **목적**: 고정된 데이터 구조에 다양한 처리를 외부에서 확장 가능하게
* **사용 예**: AST 트리 분석기, 파일 시스템 순회 처리

