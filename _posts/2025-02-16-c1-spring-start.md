---
layout: single
title: 스프링 부트에 대해서
categories: til
tag: [til]
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

# 📌 스프링 부트: 실무 중심의 심층 분석

## 📚 목차
1. [스프링 부트(Spring Boot)란?](#-스프링-부트spring-boot란)
2. [스프링 부트의 본질: 프레임워크 이상의 도구 모음](#-스프링-부트의-본질-프레임워크-이상의-도구-모음)
3. [스프링 부트가 해결한 문제들](#-스프링-부트가-해결한-문제들)
4. [스프링 부트의 핵심 목표](#-스프링-부트의-핵심-목표)
5. [스프링과 스프링 부트의 차이](#-스프링과-스프링-부트의-차이)
6. [스프링 부트의 실전 활용](#-스프링-부트의-실전-활용)
7. [결론](#-결론)

---

## 📚 스프링 부트(Spring Boot)란?

스프링 부트(Spring Boot)는 **스프링 프레임워크(Spring Framework)를 기반으로 한 확장 프레임워크**로,
독립 실행형(Standalone) 애플리케이션을 빠르게 개발하고 배포할 수 있도록 설계되었습니다.

---

## 🔹 스프링 부트의 본질: 프레임워크 이상의 도구 모음

스프링 부트는 단순한 프레임워크가 아니라, **스프링 개발을 위한 강력한 도구 세트(toolset)와 구성 패턴(configuration pattern)**을 제공합니다.
이를 통해 개발자는 스프링의 복잡한 설정을 최소화하면서도 프로덕션 수준의 애플리케이션을 신속하게 구축할 수 있습니다.

> “스프링 부트는 단순한 프레임워크가 아니라, 스프링 개발을 위한 표준화된 환경과 실행 가능한 애플리케이션 구조를 제공하는 플랫폼이다.”

과거 스프링 애플리케이션 개발 과정의 문제점:
- 개발자가 어떤 기술 스택을 선택할지 직접 결정해야 했고,
- 설정 파일(XML, Java Config 등) 작성이 필수적이었으며,
- 서버 배포 방식(WAR 배포, 컨테이너 설정 등)에 대한 고려가 필요했습니다.

---

## ✅ 스프링 부트가 해결한 문제들

스프링 부트는 이러한 문제를 해결하기 위해 다음과 같은 방식으로 개발자의 **선택 부담을 줄이고** 생산성을 높였습니다.

### 1️⃣ 자동 구성(Auto Configuration)
- 스프링 애플리케이션의 핵심 설정을 자동화하여 `application.properties` 또는 `application.yml`로 쉽게 관리 가능.
- 예: `spring.datasource.url=jdbc:mysql://localhost:3306/mydb`
  → 자동으로 DataSource 빈을 생성하고, HikariCP 등 적절한 Connection Pool을 설정.

### 2️⃣ 내장 서버(Embedded Server)
- 기존의 WAR 배포 방식과 달리, Tomcat, Jetty, Undertow와 같은 서버를 내장하여 **jar 파일만으로 실행 가능**.
- 실행 예시:
  ```sh
  java -jar myapp.jar
  ```

### 3️⃣ 의존성 관리 및 스타터(Starter Dependencies)
- `spring-boot-starter-web`, `spring-boot-starter-data-jpa` 등 목적에 맞는 의존성 조합을 미리 제공.
- 개발자는 필요한 **스타터만 추가하면 최적의 라이브러리 조합을 자동 적용**.
- 예: `spring-boot-starter-web`을 추가하면 Jackson(ObjectMapper)과 같은 JSON 직렬화 도구가 자동 포함됨.

### 4️⃣ 운영 환경 지원(Production-Ready Features)
- **Spring Boot Actuator**를 활용하여 헬스 체크, 메트릭(Metrics), 로그, 트레이싱 등 운영 필수 기능을 손쉽게 추가 가능.
- 예:
  ```
  /actuator/health
  /actuator/metrics
  ```

---

## 🚀 스프링 부트의 핵심 목표

스프링 부트의 설계 철학은 단순한 프레임워크 제공이 아니라, **스프링 개발을 보다 쉽고 강력하게 만드는 것**입니다.

### 1️⃣ 광범위한 스프링 개발 경험 제공
- 웹 애플리케이션, REST API, 메시지 큐, 배치 처리 등 다양한 애플리케이션 아키텍처 지원.
- 단순한 웹 애플리케이션부터 **마이크로서비스, 클라우드 네이티브 환경까지** 커버 가능.

### 2️⃣ 즉시 사용 가능한 강력한 기본 설정 제공 (Opinionated Defaults)
- **Convention over Configuration** 원칙을 적용하여 개발자의 선택을 최소화.
- 필요하면 쉽게 오버라이드 가능.
- 예: `spring.jpa.hibernate.ddl-auto=update` → 자동으로 Hibernate 설정을 적용하되, 원하는 대로 변경 가능.

### 3️⃣ 운영 환경(Production-Ready) 지원
- **내장형 서버, 외부 설정(Configuration Properties), 로깅(Logback), 모니터링(Actuator) 등을 기본 제공**.
- 즉, 배포 후 운영까지 고려한 프레임워크.

### 4️⃣ XML 설정 없는 개발 (Zero XML Configuration)
- 기존 스프링에서는 XML 설정 파일을 작성해야 했지만, 스프링 부트는 **Java 기반 설정(Java Config)**을 기본으로 사용.
- 즉, **XML 없이도 모든 설정 가능**, `@Configuration`을 통해 필요한 설정만 추가.

---

## 📌 스프링과 스프링 부트의 차이

🚨 **스프링 ≠ 스프링 부트** 🚨

스프링과 스프링 부트는 다르지만, 스프링 부트는 **스프링 위에 구축된 확장 프레임워크**입니다.

---

## 💡 스프링 부트의 실전 활용

✅ **언제 스프링 부트를 사용할까?**
- ✅ **빠른 MVP(Minimum Viable Product) 개발** → 설정 부담 없이 신속한 개발이 필요한 경우.
- ✅ **마이크로서비스 아키텍처(MSA) 구축** → 가벼운 독립 실행형 서비스 개발에 적합.
- ✅ **내장 서버를 활용한 컨테이너 기반 배포** → JAR 실행 방식으로 Kubernetes, Docker 등과 쉽게 통합 가능.
- ✅ **운영 환경에서의 모니터링 및 설정 관리** → Actuator, Health Check, Logging이 자동 포함됨.

---

## 🎯 결론

스프링 부트는 단순한 **프레임워크가 아니라, “스프링 개발을 위한 강력한 도구와 실행 환경을 제공하는 플랫폼”**이다.
즉, 빠른 개발, 운영 환경 지원, 독립 실행형 애플리케이션을 위한 최적의 선택이지만,
사용 목적에 따라 기존 스프링 프레임워크와의 트레이드오프를 고려해야 한다.

🚀 **스프링 부트의 핵심 개념 요약**
✅ 자동 구성(Auto Configuration) → 설정 부담 감소
✅ 의존성 관리(Starter Dependencies) → 일관된 기술 스택 제공
✅ 내장 서버(Embedded Server) → 단일 JAR 실행 가능
✅ 운영 지원(Actuator, Metrics, Logging) → 프로덕션 환경 최적화
