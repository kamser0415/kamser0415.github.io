---
layout: single
title: 스프링 부트의 이해
categories: spring
tag: [spring, java]
toc: true
toc_sticky: true
author_profile: false
sidebar:
    nav: "docs"
search: true
sidebar:
    nav: "counts"
typora-root-url: ../




---

## 스프링 부트의 이해

### 📚 목차
1. [개념 요약 (핵심 내용 정리)](#1️⃣-개념-요약-핵심-내용-정리)
2. [실무 적용 포인트](#2️⃣-실무-적용-포인트)
3. [주요 오해와 한계](#3️⃣-주요-오해와-한계)
4. [스프링 부트를 이해할 때 필요한 요소](#4️⃣-스프링-부트를-이해할-때-필요한-요소)
5. [스프링 부트를 이해하면 얻을 수 있는 것](#5️⃣-스프링-부트를-이해하면-얻을-수-있는-것)
6. [이해도 체크 질문](#6️⃣-이해도-체크-질문)

---

### 1️⃣ 개념 요약 (핵심 내용 정리)

#### 🏷️ 정의
스프링 부트(Spring Boot) 는 스프링 프레임워크의 설정을 단순화하고, 개발자가 비즈니스 로직에 집중할 수 있도록 도와주는 프레임워크이다.

#### 🔹 주요 기능
- **자동 설정(Auto Configuration)** – `@EnableAutoConfiguration` 을 기반으로 스프링 빈을 자동으로 구성
- **내장 웹 서버 제공** – Tomcat, Jetty, Undertow 등을 내장하여 별도 서버 설치 없이 실행 가능
- **의존성 관리(Spring Boot Starters)** – 필요한 기능별로 미리 정의된 의존성을 포함하는 starter 제공
- **외부 설정 지원** – `application.properties`, `application.yml` 등을 활용한 환경 설정
- **프로덕션 지원 기능** – Actuator를 통한 애플리케이션 모니터링 및 관리 기능 제공

#### 🏷️ 사용 목적
- 스프링 애플리케이션의 개발 속도 향상
- 반복적인 설정 작업 감소 – 기본적인 설정을 제공하여 개발자가 설정 작업에 신경 쓸 필요 없음
- 설정의 간소화와 확장성 – 기본 설정을 제공하지만 필요에 따라 자유롭게 확장 가능

---

### 2️⃣ 실무 적용 포인트
- 스프링 부트가 자동으로 제공하는 설정을 명확히 이해하고 활용
  - 예: `spring-boot-starter-data-jpa` 를 사용하면 Hibernate와 DataSource가 자동 설정됨
  - 하지만 DB 설정을 변경해야 하는 경우, `spring.datasource.*` 속성을 활용해야 함
- **외부 설정(`application.yml`, `application.properties`)을 활용한 설정 관리**
  - 운영, 개발, 로컬 환경별로 profile 을 나눠 관리 (`spring.profiles.active`)
- 기본 제공되는 자동 설정을 적극 활용하되, 필요 시 커스텀 설정을 적용
  - 예: `@ConfigurationProperties` 를 활용한 설정 값 매핑
- 실무에서 확장 및 변경이 필요한 부분을 인식해야 함
  - 예: `ErrorController` 를 재정의하여 기본 에러 응답 포맷을 커스텀

---

### 3️⃣ 주요 오해와 한계

| **오해**                                            | **설명**                                                     |
| --------------------------------------------------- | ------------------------------------------------------------ |
| 애플리케이션 기능 코드만 잘 작성하면 된다.          | 스프링 부트는 설정을 자동으로 해주지만, 기본 설정을 이해하고 적절히 활용해야 한다. |
| 스프링을 몰라도 개발을 잘 할 수 있다.               | 스프링 부트는 스프링 기반이므로, 스프링의 핵심 개념을 모르면 한계를 만날 수 있다. |
| 스프링 부트가 자동으로 설정해주는 것은 몰라도 된다. | 기본 설정을 모르면, 커스텀 설정이 필요할 때 문제 해결이 어려워질 수 있다. |
| 문제가 생기면 검색해서 Bean 설정을 추가하면 된다.   | 원인을 이해하지 않고 설정을 추가하는 것은 장기적으로 유지보수에 문제를 일으킬 수 있다. |

---

### 4️⃣ 스프링 부트를 이해할 때 필요한 요소
- 스프링 부트가 스프링의 어떤 기술을 활용하는지 이해해야 함
  - `@SpringBootApplication` 내부에 `@EnableAutoConfiguration`, `@ComponentScan`, `@Configuration` 이 포함됨
- **자동 설정(Auto Configuration)의 동작 원리**
  - `META-INF/spring.factories` 에 정의된 `@EnableAutoConfiguration` 클래스가 자동 로딩됨
- **디폴트 설정 값과 수정 가능한 포인트를 숙지해야 함**
  - `spring.datasource.url`, `spring.jpa.hibernate.ddl-auto` 등의 설정을 조정 가능
- **자동으로 등록되는 빈과 구성을 확인할 수 있어야 함**
  - `spring-boot-starter-*` 가 자동으로 추가하는 빈과 관련 설정을 확인해야 함
- **스프링 부트 애플리케이션을 확장할 수 있는 방법을 알아야 함**
  - 커스텀 Starter를 만들어 사내 공통 라이브러리로 활용 가능

---

### 5️⃣ 스프링 부트를 이해하면 얻을 수 있는 것
- 스프링 부트의 내부 동작을 이해하고, 커스텀 설정 및 확장이 가능해짐
- Spring Boot의 자동 구성과 디폴트 설정을 활용하여 유지보수성을 높일 수 있음
- 애플리케이션의 구조를 분석하고 최적화할 수 있음
- Actuator를 활용하여 운영 환경에서의 모니터링과 성능 개선 가능
- 스프링 부트 기반으로 확장 가능한 공통 모듈을 개발할 수 있음

---

### 6️⃣ 이해도 체크 질문

1. **Spring Boot의 자동 설정(Auto Configuration)은 어떤 원리로 동작하는가?**
   - `spring.factories` 파일과 `@ConditionalOnClass`, `@ConditionalOnProperty` 등을 기반으로 동작
2. **Spring Boot에서 내장 웹 서버를 사용하지 않고 외부 서버를 사용하는 방법은?**
   - `spring-boot-starter-web` 의존성을 제거하거나, `application.properties` 에서 `server.port=-1` 설정
3. **Spring Boot Starter의 역할과 장점은 무엇인가?**
   - 의존성 관리 간소화, 일관된 설정 제공, 개발 생산성 향상
4. **Spring Boot의 `application.yml` 과 `application.properties` 설정 차이는?**
   - `yml` 은 계층적 구조를 가지며 가독성이 높음, `properties` 는 단순 키-값 형태
5. **Spring Boot에서 `@SpringBootApplication` 이 포함하는 주요 애노테이션은?**
   - `@EnableAutoConfiguration`, `@ComponentScan`, `@Configuration`
6. **Spring Boot가 제공하는 기본적인 의존성 주입 방식은 무엇인가?**
   - 생성자 주입 방식 (권장), 필드 주입, Setter 주입
7. **Spring Boot의 Actuator를 사용하면 얻을 수 있는 이점은?**
   - 애플리케이션 상태 모니터링, 헬스 체크, 메트릭 확인 가능
8. **Spring Boot의 커스텀 Starter를 만들려면 어떤 요소가 필요한가?**
   - `spring.factories` 파일 등록, `@Configuration` 클래스 작성, 필요한 빈 설정

---

### ✅ 결론
스프링 부트를 단순히 “사용”하는 것이 아니라, 내부 구조와 동작 원리를 깊이 이해하면 더 효과적으로 확장하고 최적화할 수 있다.