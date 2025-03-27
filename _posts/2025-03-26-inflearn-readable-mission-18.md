---
layout: single
title: 인프런 Readable-code Day 16 미션
categories: warmup-club
tag: [inflearn, mission]
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

## 📌 2025-03-26 Mission



### 미션 일자

Day 18

### 미션 내용
1. @Mock, @MockBean, @Spy, @SpyBean, @InjectMocks 의 차이를 한번 정리해 봅시다.
2. 아래 3개의 테스트가 있습니다. 
   내용을 살펴보고, 각 항목을 @BeforeEach, given절, when절에 배치한다면 어떻게 배치하고 싶으신가요?
   (@BeforeEach에 올라간 내용은 공통 항목으로 합칠 수 있습니다. ex. 1-1과 2-1을 하나로 합쳐서 @BeforeEach에 배치)

### 어노테이션 정리

#### @Mock

Mock 애노테이션은 구현되지 않은 `HTTP API`이라고 생각합니다.

어떤 요청을 보내도 정상적으로 요청은 들어가지만 개발자가 원하는 값을 반환하도록 하지 않으면 응답을 하지 않습니다.

사용자가 지정한 요청 값과 응답 값을 그대로 반환하는 객체를 만들어주는 기능입니다.



그러면 언제 쓸까? 컨테이너에서 의존성을 주입하는게 아니라 개발자가 직접 모의 객체를 만들고 사용자의 요청에 어떤 응답 값을 반환할지 지정하여 의존성을 주입해주면 해당 테스트에는 동일한 요청에 동일한 응답이 반환되도록 합니다.



#### @MockBean

Mock 애노테이션과 차이점은 스프링 컨테이너에서 사용하는 빈으로 대신 등록되는 빈을 만들어 등록합니다.

스프링 빈에서 주입받는 객체에 MockBean을 사용하면 진짜 객체가 아닌 `@Mock` 객체가 빈으로 등록됩니다.



#### @Spy

Mock 객체는 이미 명령대로 움직이는 로봇 강아지라면 이 친구는 실제 강아지입니다.

특정 기능만 훈련시켜서 원하는 동작을 할 수 있도록 합니다.

예를 들어 템플릿 메서드에서 특정 다른 메서드는 동일하게 동작하게 하고 특정 메서드에서 원하는 응답을 만들때 필요합니다.



#### @SpyBean

스프링 컨테이너가 원본 객체를 빈으로 등록합니다.

스프링 컨테이너가 테스트 모듈에 있는 빈 후처리기로 빈 초기화 전에 스파이 객체로 전환합니다.

그리고 트랜잭션 AOP가 적용 됩니다.

```java
@Test
public void testTransactionManagerActiveWithMocking() {
  Product product = Product.builder().productNumber("001").name("아메리카노").build();
  Order order = Order.builder().orderStatus(OrderStatus.INIT).build();

  // saveOrder 모킹 + 트랜잭션 매니저 상태 확인
  doAnswer(invocation -> {
    boolean isTxActive = TransactionSynchronizationManager.isActualTransactionActive();
    System.out.println("트랜잭션" + isTxActive);
    throw new IOException("");
    // 여기서 DB 작업 없이 단순 확인만

  }).when(productService).saveOrder(any(Order.class));

  // 실행
  productService.saveProductAndOrder(order, product);

  // 검증
  verify(productService).saveOrder(order);
}
```



#### @InJectMocks

Mock 객체나 Spy객체를 의존하는 테스트 대상 오브젝트에게 사용합니다.

원래대로 라면 해당 객체를 생성할때 필요한 의존성을 모두 개발자가 직접 넣어주고 생성자를 통해서 생성을 해야하지만

간단하게 어노테이션으로 @Mock, @Spy 객체를 등록하도록 도와줍니다.

없이 코드를 적용해야한다면 아래와 같이 작성해야합니다.

```java
public class UserServiceTest {

	// 모킹할 의존성
	@Mock
	private UserRepository userRepository;
	// 테스트 대상 객체
	private UserService userService;
  
	@BeforeEach
	public void init() {
		MockitoAnnotations.initMocks(this);
		userService = new UserService(userRepository);
	}
}
```

또는

```java
public class UserServiceTest {

	// 모킹할 의존성
	private UserRepository userRepository;

	// 테스트 대상 객체
	// @InjectMocks
	private UserService userService;

	@BeforeEach
	public void init() {
		userRepository = mock(UserRepository.class);
		userService = new UserService(userRepository);
	}
  
}
```





### 정리

1. @~Bean이 붙은것
2. @~Bean이 안붙은것

`@*Bean`이라고 붙은 것은 스프링 컨테이너가 빈 초기화 전에 프록시 모의 객체로 전환하여 감싸게 됩니다.

그래서 @AOP가 적용 안되는 것처럼 보일 수 있으나 실제는 @AOP는 호출하는 빈이 모의 객체인지 모릅니다.



### 코드 정리

> **체크리스트**
>
> ✔️ 게시판 게시물에 달리는 댓글을 담당하는 Service Test
> ✔️ 댓글을 달기 위해서는 게시물과 사용자가 필요하다. 
> ✔️ 게시물을 올리기 위해서는 사용자가 필요하다.

```java
"@BeforeEach 
void setUp() {
    1-1,2-1.3-1 사용자 생성에 필요한 내용 준비
    1-3,2-3.3-5 게시물 생성에 필요한 내용 준비  
    3-3 사용자2 생성에 필요한 내용 준비  
} 

@DisplayName(""사용자가 댓글을 작성할 수 있다."")
@Test
void writeComment() {
    // given
    1-2. 사용자 생성
    1-4. 게시물 생성
    1-5. 댓글 생성에 필요한 내용 준비

    // when
    1-6. 댓글 생성

    // then
    검증
}

@DisplayName(""사용자가 댓글을 수정할 수 있다."")
@Test
void updateComment() {
    // given
    2-2. 사용자 생성
    2-4. 게시물 생성
    2-5. 댓글 생성에 필요한 내용 준비
    2-6. 댓글 생성

    // when
    2-7. 댓글 수정

    // then
    검증
}

@DisplayName(""자신이 작성한 댓글이 아니면 수정할 수 없다."")
@Test
void cannotUpdateCommentWhenUserIsNotWriter() {
    // given
    3-2. 사용자1 생성
    3-4. 사용자2 생성
    3-6. 사용자1의 게시물 생성
    3-7. 사용자1의 댓글 생성에 필요한 내용 준비
    

    // when
    3-8. 사용자1의 댓글 생성

    // then
    검증        
}
```

테스트하려는 것은 쿠폰 기능이므로 사용자의 생성 준비나 게시글 생성준비가 아니라고 생각합니다.

1. 여기서 중요한 것은 댓글이 되는지 잘 안되는 지 확인합니다.
2. 그러면 댓글을 쓸때 게시글 정보와 회원 정보가 필요하나 어떻게 만드는지  준비 과정은 오히려 테스트의 목적을 흐리게 한다고 생각합니다.

### 출처

#### 인프런 워밍업 클럽

[인프런 워밍업 클럽 스터디 3기 - 백엔드 클린코드, 테스트 코드(Java, Spring Boot)](https://inf.run/gUBif)

#### 강의 링크

[Readable Code: 읽기 좋은 코드를 작성하는 사고법](https://inf.run/63LsM)

[Practical Testing: 실용적인 테스트 가이드](https://inf.run/g8nxP)
