---
layout: single
title: 스프링 viewResolver, 포워드와 리다이렉트
categories: spring
tag: [spring, viewResolver]
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

## 📌 ViewResolver 학습

## 1. 오늘의 학습 주제
### 



## 2. 학습 내용
### 기본 원리와 이해

클라이언트 요청에 대해 컨트롤러가 응답을 할때 View의 이름을 반환하면, ViewResolver가 이름을 해석하여 실제 view 파일로 연결해주는 역할을 합니다.  
**컨트롤러와 view의 의존성을 낮추기 위해 사용됩니다.**

#### ViewResolver가 없다면

컨트롤러가 View의 구체적인 경로와 확장자를 알아야 하므로, View가 바뀌면 컨트롤러도 수정해야합니다.

#### 유연성을 추가로 준다.

기존 서버 사이드 랜더링된 코드를 그대로 유지하고 json 형식으로 반환 할 수 도 있습니다.

스프링은 컨트롤러뿐만 아니라 view 경로까지도 컨트롤러가 직접 아는게 아니라 DI를 하게 합니다.

## 3. 핵심 트레이드 오프 및 고민 포인트
- 내장 톰캣: 배포 및 개발 환경의 단순함 vs. 인스턴스 수 증가 시 자원 오버헤드
- 외장 톰캣: 서버 자원 효율성 vs. 단일 인스턴스 내 애플리케이션 간 커넥션 관리의 어려움

## 4. 적용 방안 및 향후 계획
- 현재 프로젝트에 맞는 톰캣 선택 기준 확립  
- 로드 밸런서 도입 및 다중 인스턴스 테스트 진행  
- Parallel Deployment 기능 추가 검토

## 5. 참고 자료 및 링크
- [Spring Boot 공식 문서](https://spring.io/projects/spring-boot)
- [Apache Tomcat 공식 문서](https://tomcat.apache.org/)

## 6. 추가 학습 메모
- 톰캣의 세밀한 커넥션/스레드 관리 설정에 대해 추가 조사 필요
- 무중단 배포 구현 사례 추가 분석  

---
