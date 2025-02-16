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

### **목차**

•	[스프링 IoC와 DispatcherServlet](#스프링-ioc와-dispatcherservlet)

•	[자바 코드 구성 정보와 의존성 주입(DI)](#자바-코드-구성-정보와-의존성-주입di)

•	[ComponentScan과 @Configuration](#componentscan과-configuration)

•	[Bean 생명주기와 Hook 메소드](#bean-생명주기와-hook-메소드)

•	[객체지향 설계(SOLID) 적용 사례](#객체지향-설계solid-적용-사례)

•	[트랜잭션 관리와 트레이드오프](#트랜잭션-관리와-트레이드오프)

•	[실무 적용 시 고려사항](#실무-적용-시-고려사항)

•	[추가 인터뷰 질문](#추가-인터뷰-질문)

위 학습 목표를 달성하기 위해 **스프링 IoC**, **DI**, **템플릿 메소드 패턴**, **Bean 생명주기**, **ComponentScan** 등의 개념을 단계별로 정리하였습니다.

**스프링 IoC와 DispatcherServlet**

**1. 스프링 IoC(Inversion of Control)**

+ **IoC** 는 전통적인 방식에서 애플리케이션 코드가 객체를 생성하고 관리하던 흐름을 역전시킵니다.

+ **스프링 컨테이너(ApplicationContext)**가 객체(빈, Bean)들을 대신 생성 및 관리하며, 필요한 시점에 의존성을 주입해줍니다.

**2. DispatcherServlet**

​	•	스프링 MVC에서의 **Front Controller** 역할을 담당하는 서블릿입니다.

​	•	모든 웹 요청을 받아서 스프링 컨테이너 안에서 매핑된 **컨트롤러**(예: @Controller, @RestController)를 찾아 위임합니다.

​	•	매핑, 바인딩, 예외 처리 등의 부수적인 작업을 내부적으로 처리하여 개발자는 비즈니스 로직에만 집중할 수 있게 해줍니다.



**간단 예시 코드**

```
ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
WebServer webServer = serverFactory.getWebServer(servletContext -> {
    // DispatcherServlet 생성 및 스프링 컨테이너 연결
    servletContext.addServlet(
        "dispatcherServlet", 
        new DispatcherServlet(applicationContext) // WebApplicationContext 필요
    ).addMapping("/*");
});
webServer.start();
```

​	•	DispatcherServlet은 **스프링 컨테이너**(예: AnnotationConfigServletWebApplicationContext)와 연결되어

요청 처리에 필요한 **Controller** 및 **Service**를 찾아서 호출합니다.

**자바 코드 구성 정보와 의존성 주입(DI)**



**1. 자바 코드로 구성 정보를 등록하는 이유**

​	•	XML 기반 설정에서 벗어나 **가독성과 유지보수성**을 높이기 위함

​	•	**팩토리 메소드(@Bean)**를 활용해 **의도를 드러내는** 방식으로 객체를 생성

​	•	필요한 시점에 생성 로직이나 의존 관계를 자유롭게 커스터마이징 가능



**2. DI(Dependency Injection) 방식**

​	1.	**생성자 주입**

​	2.	**Setter 주입**

​	3.	**필드 주입**(권장되지 않음. 테스트·확장성 측면에서 불리)



**예시 코드: 팩토리 메소드 방식**

```
@Configuration
public class HelloApplication {

    // DispatcherServlet과 Tomcat 설정(스프링 부트에서는 자동화됨)
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }
    
    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    // Controller와 Service를 스프링 빈으로 등록
    @Bean
    public HelloService helloService() {
        return new SimpleHelloService();
    }

    @Bean
    public HelloController helloController(HelloService helloService) {
        // 생성자 주입
        return new HelloController(helloService);
    }
}
```

​	•	@Configuration 클래스는 스프링 컨테이너가 참조할 **구성 정보**를 담고 있습니다.

​	•	@Bean 메서드들은 각각 스프링 빈을 생성하고, **의존성을 연결**합니다.

​	•	다른 빈에서 HelloService helloService와 같이 **인자로 받아** 주입받는 방식(생성자 주입)을 쓰면,

**테스트**, **확장** 시에 보다 편리합니다(의존 대상 교체 등).

**ComponentScan과 @Configuration**



**1. @ComponentScan**

​	•	**@ComponentScan**이 선언된 패키지와 그 **하위 패키지**에서 @Component(메타 애노테이션 포함)

가 붙은 클래스를 **자동으로 스프링 빈으로 등록**합니다.

​	•	@Controller, @Service, @Repository 등은 모두 @Component를 메타 애노테이션으로 갖고 있어

자동 스캐닝 대상이 됩니다.

```
@Configuration
@ComponentScan
public class HelloApplication {

    // 톰캣 서버 및 DispatcherServlet Bean 등록
    @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }

    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

    public static void main(String[] args) {
        AnnotationConfigServletWebApplicationContext applicationContext 
            = new AnnotationConfigServletWebApplicationContext() {
                @Override
                protected void onRefresh() {
                    super.onRefresh();
                    // 서블릿 컨테이너 등록 및 실행
                    ServletWebServerFactory serverFactory = this.getBean(ServletWebServerFactory.class);
                    DispatcherServlet servlet = this.getBean(DispatcherServlet.class);
                    WebServer webServer = serverFactory.getWebServer(servletContext -> {
                        servletContext.addServlet("dispatcherServlet", servlet).addMapping("/*");
                    });
                    webServer.start();
                }
        };
        // 구성 정보 등록
        applicationContext.register(HelloApplication.class);
        applicationContext.refresh();
    }
}
```

​	•	이렇게 HelloApplication 클래스에 @ComponentScan이 붙어 있으면,

**동일 패키지**(또는 지정된 패키지) 하위에 @Controller, @Service, @Repository 등을 붙여 놓기만 해도

알아서 스캔 및 빈 등록이 이루어집니다.



**2. @Configuration과 @ComponentScan의 차이**

​	•	@Configuration 클래스는 **직접 빈을 정의(@Bean)하거나** 특정 설정 정보를 담아 놓을 때 사용합니다.

​	•	@ComponentScan은 **지정된 패키지 이하의 빈을 자동으로 검색**해서 등록합니다.

​	•	일반적으로 @SpringBootApplication에는 내부적으로 @Configuration, @EnableAutoConfiguration,

@ComponentScan이 모두 포함되어 있습니다.

**Bean 생명주기와 Hook 메소드**



**1. refresh()와 템플릿 메소드 패턴**

​	•	applicationContext.refresh()를 호출하면 **스프링 컨테이너 초기화**가 진행됩니다.

​	•	내부적으로 템플릿 메소드(onRefresh()) 등이 호출되어 여러 단계(Bean 팩토리 초기화 → 후처리기 등록 → Bean 생성)가 진행됩니다.

```
@Override
protected void onRefresh() {
    super.onRefresh();
    // 서블릿 웹 서버를 띄운다거나, 추가 작업을 여기서 진행할 수 있음
}
```

​	•	**Hook 메소드**: 부모 클래스(AbstractApplicationContext)의 템플릿 메소드(refresh())가 호출되는 과정에서

**자식 클래스**가 특정 지점에 개입(onRefresh 등)하여 **확장**할 수 있게 만드는 구조입니다.



**2. Bean 생명주기 콜백**

​	•	**Bean 객체가 생성**된 후 **@PostConstruct**, **InitializingBean.afterPropertiesSet()** 같은

초기화 메소드가 호출될 수 있습니다.

​	•	**DisposableBean.destroy()** 등으로 **소멸 메소드**를 활용할 수도 있습니다.

​	•	**ApplicationContextAware** 등을 구현하면, **컨테이너(스프링 컨텍스트)**를 주입받을 수도 있습니다.

**객체지향 설계(SOLID) 적용 사례**



**1. SRP(Single Responsibility Principle)**

​	•	**Controller**는 **웹 요청**을 받아 검증 및 결과 반환 형식 처리에만 집중

​	•	**Service**는 **비즈니스 로직** 실행에 집중

​	•	**Repository**는 **데이터 엑세스**(DB 접근)에 집중

​	•	역할을 명확히 분리하면 유지보수성과 확장성이 높아집니다.



**2. DIP(Dependency Inversion Principle)**

​	•	**추상(인터페이스)에 의존**하도록 구조를 설계

​	•	구체 클래스(예: SimpleHelloService)가 아니라 **HelloService 인터페이스**에 의존

​	•	스프링 DI를 통해서 **런타임**에 적절한 구현체를 주입받게 만듦



**간단 예시 코드**

```
public interface HelloService {
    String sayHello(String name);
}

@Service
public class SimpleHelloService implements HelloService {
    @Override
    public String sayHello(String name) {
        return "Hello, " + name;
    }
}

@RestController
public class HelloController {
    private final HelloService helloService;

    // 생성자 주입
    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    @GetMapping("/hello")
    public String hello(String name) {
        if (name == null) throw new IllegalArgumentException("이름이 비어있습니다.");
        return helloService.sayHello(name);
    }
}
```

**트랜잭션 관리와 트레이드오프**



**1. 스프링 트랜잭션 관리**

​	•	@Transactional 애노테이션을 활용해 **ACID** 속성을 보장(원자성, 일관성, 고립성, 지속성)

​	•	**Isolation Level**(격리 수준)을 지정해 **동시성** 이슈를 제어할 수 있음

​	•	**Propagation** 속성으로 트랜잭션 전파 방식을 정의할 수 있음



**2. 트레이드오프(트랜잭션 범위 설정)**

| **선택 사항**                       | **장점**                          | **단점**                                     |
| ----------------------------------- | --------------------------------- | -------------------------------------------- |
| **넓은 범위의 트랜잭션**            | 일관성(Consistency)을 최대한 보장 | 오랜 시간 잠금이 걸릴 수 있어 성능 저하 우려 |
| **좁은 범위(메소드 단위) 트랜잭션** | 병렬 처리나 성능에 유리           | 여러 트랜잭션 간의 중간 상태가 발생할 가능성 |

​	•	**MSA** 환경에서는 **분산 트랜잭션**을 위해 **SAGA 패턴** 등이 사용됨.

하지만 **분산 락** 등의 복잡성이 발생하므로, 비즈니스 요구사항에 따라 신중히 결정해야 합니다.

**실무 적용 시 고려사항**

​	1.	**Spring Boot 사용**

​	•	톰캣, 제티 등의 서블릿 컨테이너 자동 설정

​	•	@SpringBootApplication(내부적으로 @EnableAutoConfiguration, @ComponentScan)을 통해

필요한 Bean 자동 등록 및 설정

​	2.	**객체지향 설계 vs 성능**

​	•	지나치게 세분화된 구조(클래스, 인터페이스 남발)는 **초기 개발 속도** 저하

​	•	그러나 대규모 시스템에서는 **유지보수성**, **확장성**을 확보하는 편이 궁극적으로 이득

​	3.	**테스트**

​	•	DI 구조에서는 **Mock**(가짜 구현체)로 교체가 쉬워져 **단위 테스트**가 용이

​	•	통합 테스트 시에는 실제 DB, 메시지 브로커, 외부 서비스 연동도 Spring Test 지원 기능으로 수월

​	4.	**트랜잭션 범위**

​	•	**@Transactional**을 클래스 단위로 걸어둘지 메소드 단위로 할지 고민 필요

​	•	실제 비즈니스 로직의 트랜잭션 경계를 정확히 파악해 불필요한 Lock으로 인한 성능 저하를 방지

**추가 인터뷰 질문**

​	1.	**Spring의 IoC(Inversion of Control)은 전통적인 자바 객체 관리와 비교했을 때 어떤 장점이 있나요?**

​	2.	**SOLID 원칙 중 DIP(Dependency Inversion Principle)를 스프링 DI와 연관지어 설명한다면?**

​	3.	**트랜잭션 격리 수준(Isolation Level)의 설정에 따른 장단점은 무엇이고, 어떻게 결정해야 할까요?**

​	4.	**@ComponentScan과 @Configuration의 차이는 무엇이며, 어떤 상황에서 각각을 활용하나요?**

​	5.	**스프링 Bean의 라이프사이클에서 생기는 후처리(Post-Processing)는 실무에서 어떻게 활용할 수 있나요?**

**결론**



위 내용을 종합하면, **스프링 컨테이너**를 중심으로 **DI**와 **템플릿 메소드 패턴**, **Hook 메소드**를 적절히 활용하면

**비즈니스 로직(POJO)**을 깔끔히 유지하면서도, **IoC**를 통한 객체 관리와 확장성이 뛰어난 구조를 얻을 수 있습니다.

또한, **트랜잭션 관리**와 **객체지향 설계(SOLID)**를 접목시켜 **대규모 애플리케이션**에서도 효율적인 유지보수가 가능해집니다.



​	“스프링 부트”를 쓰면 위 설정이 **자동화**되지만,

내부 동작 원리를 이해하면 **디버깅**, **최적화**, **아키텍처 결정**에 큰 도움이 됩니다.
