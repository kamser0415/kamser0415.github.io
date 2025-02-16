---
layout: single
title: HTTP API 테스트와 HelloController 설명 보충
categories: spring
tag: [spring]
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

## HTTP API 테스트와 HelloController 설명 보충

### 목차
1. [HelloController 예제](#HelloController-예제)
2. [REST Controller와 GET 요청](#REST-Controller와-GET-요청)
3. [브라우저 테스트와 한계](#브라우저-테스트와-한계)
4. [HTTP 요청/응답 디테일 확인하기](#HTTP-요청응답-디테일-확인하기)
5. [HTTP API 테스트 도구](#HTTP-API-테스트-도구)
6. [HTTP 요청과 응답 구조](#HTTP-요청과-응답-구조)
7. [시니어 개발자 관점에서 보충 설명](#시니어-개발자-관점에서-보충-설명)
8. [추가로 알아두면 좋은 키워드/인사이트](#추가로-알아두면-좋은-키워드인사이트)

---

## HelloController 예제

```java
package toby.hello;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(String name) {
        return "Hello " + name;
    }
}
```

### 설명
- `@RestController`를 사용하면, HTML View를 반환하는 대신 HTTP Body에 직접 문자열, JSON 등 필요한 데이터를 담아 보낼 수 있습니다.
- `@GetMapping("/hello")`는 `/hello` 경로로 GET 방식 요청이 들어올 때 해당 메서드가 실행되도록 합니다.
- 메서드의 파라미터로 `String name`을 받으면, 쿼리스트링(`?name=값`)으로 넘어오는 값을 자동 바인딩해 사용할 수 있습니다.

---

## REST Controller와 GET 요청

### REST
- **Representational State Transfer**의 약자로, 서버 리소스에 대해 HTTP 메소드(행위)를 통해 조작하는 패턴을 말합니다.
- Spring에서 `@RestController`는 API 응답을 JSON, XML, Plain Text 등으로 반환할 때 주로 사용합니다.

### GET 요청
- 데이터 조회 또는 서버에 리소스를 요청할 때 사용되는 HTTP 메소드입니다.
- 보통 쿼리스트링(`?key=value`)으로 파라미터를 전달합니다.

---

## 브라우저 테스트와 한계

### 단순 브라우저 테스트
- 브라우저 주소창에 `http://localhost:8080/hello?name=Spring`을 입력하고 응답 결과를 바로 확인할 수 있습니다.
- 브라우저 화면에 보이는 텍스트가 기대한 값과 일치하는지 확인이 가능합니다.

### 한계
- 브라우저에서 확인할 수 있는 정보(바디 내용, 상태코드)만으로는 충분하지 않을 때가 많습니다.
- 헤더(Headers)나 상태코드, 정확한 요청/응답 구조를 세밀하게 살펴보기 어려울 수 있습니다.

---

## HTTP 요청/응답 디테일 확인하기

### 요청(Http Request) 헤더 예시
```
GET /hello?name=doll HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,
        image/avif,image/webp,image/apng,*/*;q=0.8,
        application/signed-exchange;v=b3;q=0.7
Host: localhost:8080
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
```

### 응답(Http Response) 예시
```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 10
Date: Sat, 11 Nov 2023 10:33:14 GMT
Connection: keep-alive
```

---

## HTTP API 테스트 도구

1. **크롬 개발자 도구**
2. **curl** (CLI에서 간단한 HTTP 요청 가능)
3. **HTTPie** (더 읽기 쉬운 CLI 도구)
4. **IntelliJ IDEA Ultimate - HTTP Request 기능**
5. **Postman API Platform** (GUI 환경에서 다양한 API 테스트 가능)
6. **JUnit Test** (`MockMVC` 활용 가능)

---

## HTTP 요청과 응답 구조

### 요청(Request)
1. Request Line: Method, Path, HTTP Version
2. Headers
3. Message Body (주로 POST, PUT 요청에 사용)

### 응답(Response)
1. Status Line: HTTP Version, Status Code, Status Text
2. Headers
3. Message Body (실제 전송할 데이터)

---

## 보충 설명

### 1. API 설계 원칙과 버전 관리
- RESTful 규칙(리소스 명명, URI 구조, 계층적 관계 등) 적용 방법.
- 장기적으로 API를 운영할 때의 버전 관리(`v1`, `v2` 등).

### 2. Content Negotiation(컨텐츠 협상)
- `Accept` 헤더로 클라이언트가 원하는 응답 타입(`application/json`, `application/xml`)을 명시할 때, 서버가 해당 타입에 맞는 응답을 제공하는 방법.
- `@RestController`와 `HttpMessageConverter`의 동작 방식 숙지.

### 3. Validation
- 요청 파라미터 검증(`@Validated`, `@Valid`)과 예외 처리(`@ExceptionHandler`) 방법.

### 4. 응답 바디와 에러 처리
- 일관된 에러 포맷(JSON)으로 제공.
- 적절한 HTTP Status Code 사용.

### 5. 보안 측면
- 인증/인가(Authentication & Authorization)
- HTTPS 적용, CORS 정책, XSS/CSRF 방어 등 보안 모범 사례.

### 6. 성능 및 확장성
- 캐싱, 효율적인 DB 접근, 로드밸런싱 고려.
- Spring Boot Actuator로 모니터링 및 헬스 체크 수행.

### 7. 테스트 전략
- 단위 테스트, 통합 테스트, E2E(End-to-End) 테스트 분리하여 자동화.

### 8. 로깅 & 트레이싱
- 로깅 프레임워크 활용.
- Zipkin, Jaeger를 통한 분산 트레이싱 적용.

---

## 추가로 알아두면 좋은 키워드/인사이트

- **HTTP/2 & HTTP/3**: 최신 HTTP 프로토콜 이해.
- **HATEOAS**: REST 확장 개념.
- **REST vs. gRPC**: 마이크로서비스 간 통신 비교.
- **Spring Cloud**: 마이크로서비스 환경에서 API Gateway, Service Discovery 활용.
- **Async/Reactive 프로그래밍**: WebFlux 활용.
- **GraphQL**: 유연한 데이터 질의를 위한 대안.

위 내용들을 종합하면, 간단한 HTTP 요청/응답 처리부터 복잡한 시스템 전반을 고려하는 시니어 개발자의 시야를 갖출 수 있습니다.
