---
layout: single
title: 내장 톰캣과 서블릿 프론트 컨트롤러 구현을 통한 스프링 MVC 이해
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



## 학습목표

1. **내장 톰캣(Embedded Tomcat) 구동 방식 이해**

   • 서버 설치 없이 자바 코드에서 톰캣을 실행하는 “컨테이너리스(Container-less)” 개념을 익힌다.

2. **서블릿 기반 프론트 컨트롤러 패턴 구현**

   • 단일 서블릿으로 모든 요청을 수신하고 공통 로직 처리 후 필요한 비즈니스 로직(핸들러)에 위임하는 구조를 직접 경험한다.

3. **스프링 MVC(DispatcherServlet)의 핵심 구조 파악**

   • 서블릿 + 프론트 컨트롤러 패턴이 어떻게 추상화되어 스프링 MVC(@Controller, @GetMapping 등)로 발전했는지 이해한다.

## 목차
1. [HTTP 요청/응답 구조 이해](#http-요청응답-구조-이해)
2. [톰캣을 내장(Embedded) 형태로 실행하는 코드](#톰캣을-내장embedded-형태로-실행하는-코드)
3. [서블릿 직접 등록하기](#서블릿-직접-등록하기)
4. [프론트 컨트롤러(Front Controller) 패턴](#프론트-컨트롤러front-controller-패턴)
5. [컨트롤러(핸들러) 분리](#컨트롤러핸들러-분리)
6. [스프링 MVC와의 관계](#스프링-mvc와의-관계)
7. [핵심 요약 & 키워드 체크](#핵심-요약--키워드-체크)
8. [추가 키워드 설명](#추가-키워드-설명)
9. [결론](#결론)

---

## HTTP 요청/응답 구조 이해

### 1) Request (요청)
- **Request Line**: Method, Path(URL), HTTP Version
- **Method**: GET, POST, PUT, DELETE 등 (리소스에 대한 CRUD 성격)
- **Path(URL)**: 리소스 식별자 (예: /hello)
- **HTTP Version**: 일반적으로 HTTP/1.1 또는 HTTP/2
- **Headers**: 클라이언트와 서버 간 부가 정보 교환 (예: Content-Type, User-Agent, Accept 등)
- **Message Body**: 실제 데이터(텍스트, JSON, 폼 데이터 등)가 들어오는 본문 부

### 2) Response (응답)
- **Status Line**: HTTP Version, Status Code, Status Text
- **Status Code**: 200(OK), 404(Not Found), 500(Server Error) 등
- **Headers**: 서버가 응답할 때 부가 정보 (예: Content-Type, Content-Length 등)
- **Message Body**: HTML, JSON, 단순 텍스트 등 실제 응답 데이터

#### 키워드:
- **Status Code**: HTTP 표준 상태 번호 (2xx: 성공, 4xx: 클라이언트 오류, 5xx: 서버 오류 등)
- **Content-Type**: 바디에 담긴 리소스의 형식을 클라이언트에게 알림

---

## 톰캣을 내장(Embedded) 형태로 실행하는 코드

스프링 부트는 "내장 톰캣(Embedded Tomcat)" 방식을 사용하여, 서버 설치 없이 애플리케이션 내부에서 톰캣을 실행할 수 있게 해줍니다.

```java
public class HelloApplication {
    public static void main(String[] args) {
        // (1) 톰캣을 생성해주는 팩토리
        ServletWebServerFactory webServerFactory = new TomcatServletWebServerFactory();

        // (2) WebServer 인터페이스로 반환받음 (추상화)
        WebServer webServer = webServerFactory.getWebServer(
                /* ServletContextInitializer... */
        );

        // (3) 서버 시작
        webServer.start(); 
    }
}
```

#### 키워드:
- **TomcatServletWebServerFactory**: 톰캣 객체(TomcatWebServer)를 손쉽게 만들고 설정까지 해주는 팩토리 클래스
- **WebServer**: 톰캣, 제티, 언더토우 등 특정 서버 기술을 추상화한 공통 인터페이스 (start/stop/port 등 메서드)
- **컨테이너리스(Container-less)**: WAR 배포 없이, main() 메서드로 서버까지 동시에 구동하는 방식

---

### 서블릿 직접 등록하기

### ServletContextInitializer 사용

ServletContextInitializer는 서블릿 컨텍스트를 초기화(등록)하는 인터페이스입니다.

```java
WebServer webServer = webServerFactory.getWebServer(servletContext -> {
    // 1) HttpServlet 구현체를 생성
    HttpServlet myServlet = new HttpServlet() {
        @Override
        protected void service(HttpServletRequest req, HttpServletResponse resp) 
                throws ServletException, IOException {
            // 서블릿 처리 로직
        }
    };
    
    // 2) 서블릿을 등록하고 URL 매핑
    servletContext.addServlet("helloServlet", myServlet).addMapping("/hello");
});
webServer.start();
```

#### 키워드:
- **ServletContext**: 서블릿 컨테이너의 전역적 정보를 담고 있는 객체
- **HttpServlet**: 자바 서블릿 기술에서 기본적으로 오버라이딩해서 사용 (service/doGet/doPost 등)
- **Mapping**: URL 패턴(/hello)과 서블릿 객체를 연결

---

### 프론트 컨트롤러(Front Controller) 패턴

### 구현 예시
```java
servletContext.addServlet("frontController", new HttpServlet() {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) 
            throws ServletException, IOException {

        // 1) 공통 처리(예: 인증, 로그, etc.)
        // 2) 요청 URL과 Method에 따라 분기
        String requestURI = req.getRequestURI();
        String method = req.getMethod();

        if (requestURI.equals("/hello") && "GET".equals(method)) {
            String name = req.getParameter("name");
            resp.setStatus(HttpStatus.OK.value());
            resp.setHeader(HttpHeaders.CONTENT_TYPE, MediaType.TEXT_PLAIN_VALUE);
            resp.getWriter().println("hello " + name);
        } else {
            resp.setStatus(HttpStatus.NOT_FOUND.value());
        }
    }
}).addMapping("/*");
```

#### 키워드:
- **프론트 컨트롤러**: 모든 요청을 한 곳에서 받아 핸들러로 위임하는 패턴
- **DispatcherServlet**: 스프링 MVC의 프론트 컨트롤러

---

### 컨트롤러(핸들러) 분리

#### 구현 예시
```java
public class HelloController {
    public String hello(String name) {
        return "Hello " + name;
    }
}
```

#### 키워드:
- **Binding**: 웹 파라미터를 자바 변수로 변환
- **핸들러(Controller)**: 비즈니스 로직 실행 주체

---

## 결론
- 서블릿, 톰캣, HTTP 프로토콜 기본기를 알면 스프링 MVC 사용 시 디버깅과 확장이 수월함
- "프론트 컨트롤러 + 핸들러" 구조 이해는 스프링 MVC 아키텍처 이해의 핵심
- 스프링 부트는 이 과정을 "컨테이너리스" 방식으로 자동화하여 개발자가 비즈니스 로직 구현에 집중할 수 있도록 함
