---
layout: single
title: Spring MVC에서 Forward와 Redirect 완벽 이해
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

## 1. 오늘의 학습 목표
> 목표 : 리다이렉트와 포워드의 근본적인 차이점과 사용 목적을 완전히 이해하기

+ 왜 리다이렉트와 포워드가 필요할까?
+ 리다이렉트와 포워드가 각각 무엇인지 정의해보기
+ 각각의 구동 방식과 내부 동작 원리를 이해한다.

### 확인 질문

1. 리다이렉트와 포워드의 HTTP 상태 코드 차이
2. 리다이렉트 시와 포워드 시 URL과 브라우저 히스토리는 어떻게 저장되는가
3. Http Request 와 Response 객체는 각각 어떻게 처리되는가?

## 2. 학습 내용

### 상태코드 차이

```java
HTTP/1.1 200 
Content-Type: text/html;charset=UTF-8
Content-Language: ko-KR
Transfer-Encoding: chunked
Date: Sun, 23 Feb 2025 02:17:53 GMT
  
Body: 포워드 데이터가 들어있음
```

+ 포워드시 응답 객체

```java
HTTP/1.1 302 
Location: http://localhost:8080/hello
Content-Language: ko-KR
Content-Length: 0
Date: Sun, 23 Feb 2025 02:19:52 GMT
  
Body: <Response body is empty>
```

+ 리다이렉트 응답 객체
  + 상태코드 302
  + Location: 이동하려는 위치
  + Body에 아무런 값이 없음



### URL 변경 차이

+ 리다이렉트 : 클라이언트의 URL이 변경된다.
+ 포워드 : 클라이언트의 URL이 변경되지 않고 포워드 주소의 데이터를 반환해준다.



### 기본 개념 확인

리다이렉트

1. 클라이언트에게 새로운 URL로 이동하라고 응답합니다.
2. HTTP 상태 코드는 302 또는 301을 사용합니다.
3. **새로운 요청이 생성**되므로 Request 객체가 초기화됩니다.
4. URL이 변경되며 브라우저 히스토리에 남는다.

포워드

1. 서버 내부에서 요청을 다른 자원으로 전달합니다.
2. URL 변경이 없습니다
3. Request 객체와 Response 객체가 유지됩니다.
4. 같은 요청 스코프에서 처리되며, 클라이언트는 이동 사실을 모릅니다.

> 단, 포워드된 자원 안에서만 유지되며, 클라이언트에게 응답이 완료되면 소멸합니다.

### 예외 상황 발생

1. 리다이렉트시 GET 파라미터 길이제한 문제

   쿼리스트링이 긴 경우 URL 길이 제한에 걸리면 Post-Rediect-Post패턴사용 또는 세션저장

2. URL 변경을 안하는 포워드 처리는 브라우저 새로고침으로 발생할 수잇는 문제

   세션 플래그 또는 Post-Rediect-Get 패턴 적용

3. 리다이렉트시 루프 발생  

   리다이렉트 조건을 명확히 설정한다.

   리다이렉트 카운트를 두어 무한 루프를 방지한다.



