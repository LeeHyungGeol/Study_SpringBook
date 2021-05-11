# 7장 스프링 삼각형과 설정 정보

## 스프링 삼각형(IoC/DI, AOP, PSA)
* 스프링 삼각형
  * POJO(Plain Old Java Objet)를 기반으로 IoC/DI, AOP, PSA라고 하는 스프링의 3대 프로그래밍 모델

---
---
---

## IoC/DI - 제어의 역전/의존성 주입

## 의존이란?

* 의존 관계는 단순하게 보면 **new로 표현**된다.
* 변수에 값을 할당하는 모든 곳, **대입 연산자(=)** 에 의해 **변수에 값이 할당되는 순간**에 ***의존이 생긴다.***
* **DI(Dependency Injection)** 는 ***외부에 있는 의존 대상을 주입하는 것***이다.


## 1. 전략 패턴(Strategy Pattern) , 관심사의 분리(SoC)(사용영역과 구성영역) -> AppConfig

```java
public class MemberServiceImpl implements MemberService{
    //private final MemberRepository memberRepository = new MemoryMemberRepository(); 
    private final MemberRepository memberRepository = new JdbcMemberReposiroty();
    //문제점 1 : SRP 위반 : 클라이언트 객체는 실행하는 책임 뿐만 아니라 서버 구현 객체를 생성 및 연결하는 역할까지 추가로 수행
    //문제점 2 : DIP 위반 :추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다.
    //문제점 3 : OCP 위반 :기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다! MemoryMemberRepository 를 JdbcMemberReposiroty 로 변경하는 순간 MemberServiceImpl 의 소스 코드도 함께 변경해야 한다!  
}
```


* 앞서 언급했듯이 **다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다.**
* 위의 코드와 같이 **대입연산자(=)와 new**으로 인해 클라이언트와 서버간의 ***의존관계***가 형성이 되어 버렸기 때문이다.
  * **SRP(단일 책임 원칙), OCP(확장 폐쇄 원칙), DIP(의존 역전 원칙)에 위배**하게 된다.


### 관심사의 분리(SoC)

<img src="https://user-images.githubusercontent.com/56071088/108619797-87e1a700-746a-11eb-9fb3-c71c4b9e9d43.PNG"  width="480" height="330">

```java
public class OrderServiceImpl implements OrderService{
    //"DIP 적용" : AppConfig 를 사용함으로써 구현체가 아닌 추상화(Interface)에만 의존하도록 만들었다.
    private final MemberRepository memberRepository; //final 선언시, 기본으로 할당 또는 생성자를 통해 할당을 해야한다.
    private final DiscountPolicy discountPolicy;
}

//애플리케이션 설정 정보, 구성 정보를 담당하는 파일이라는 뜻
//애플리케이션에 대한 환경 구성을 한다. //DI 컨테이너
//애플리케이션의 전체 동작 방식을 구성(config)하기 위해, 구현 객체를 생성하고, 연결하는 책임을 가지는 별도의 설정 클래스이다.
@Configuration
public class AppConfig { 
    //AppConfig를 보면 역할과 구현 클래스가 한눈에 들어온다.
    //애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다.
    @Bean
    public MemoryMemberRepository memberRepository() { 
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy()); 
    }
    
    @Bean
    public DiscountPolicy discountPolicy() {
        //return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}

public class OrderApp {
    public static void main(String[] args) {
        // AppConfig appConfig = new AppConfig();
        // MemberService memberService = appConfig.memberService();
        // OrderService orderService = appConfig.orderService();
        
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
        
        long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);
        
        Order order = orderService.createOrder(memberId, "itemA", 10000);
        
        System.out.println("order = " + order);
    }
}
```

* 관심사의 분리(SoC)를 이용하여 애플리케이션의 동작 방식을 사용 영역과 구성 영역으로 나눈다.


## 2. IoC/DI 그리고 컨테이너

* ***IoC 제어의 역전***
  * 기존 프로그램은 **클라이언트 객체**가 **스스로 서버 객체**를 생성하고 실행하며, 연결하는 책임까지 전부 갖고 있었다. 
    
    * 한마디로, 프로그램 제어의 흐름을 구현 객체가 조종했다.
  * **AppConfig**로 인해, ***프로그램 제어의 흐름을 모두 AppConfig가 가지고 있다.***
  
  * 이렇듯, **프로그램 제어의 흐름**을 직접 제어 하는 것이 아닌 ***외부에서 제어하는 것을 제어의 역전(IoC)*** 이라고 한다.

* ***DI 의존관계 주입***
  * **애플리케이션 실행 시점(런타임)** 에 ***외부에서 구현 객체를 생성하고 클라이언트에 연결***하여 클라이언트와 서버의 의존관계가 연결되는 것을 ***의존관계 주입(DI : Dependecy Injection)*** 이라고 한다.   
      
  * 의존관계 주입을 사용하면 
    * **정적인 클래스 의존관계**를 변경하지 않고 
    * **동적인 객체 인스턴스 의존관계**를 **쉽게 변경**할 수 있다.

* ***IoC 컨테이너, DI 컨테이너***
  
  * **AppConfig**처럼 객체를 생성 및 관리하며, 의존관계를 연결해 주는 것을 ***IoC 컨테이너 또는 DI 컨테이너***라고 한다.
  * 의존관계 주입에 초점을 두어 최근에는 **주로 DI 컨테이너**라 한다.

## 3. AppConfig.class , AppConfig.xml , AppConfig.xxxx -> BeanDefinition

* **ApplicationContext** 를 ***스프링 컨테이너***라 한다.
  
* ApplicationContext 는 **인터페이스**이다.
  
* 스프링 컨테이너는 XML을 기반으로 만들 수 있고, 애노테이션 기반의 자바 설정 클래스로 만들 수 있다.


[ **xml 기반의 스프링 빈 설정 정보** ]  src/main/resources/**appConfig.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
 
    <bean id="memberService" class="hello.core.member.MemberServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository" />
    </bean>
    
    <bean id="memberRepository" class="hello.core.member.MemoryMemberRepository" />
 
    <bean id="orderService" class="hello.core.order.OrderServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository" />
        <constructor-arg name="discountPolicy" ref="discountPolicy" />
    </bean>

    <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
</beans>

```

## 스프링은 어떻게 다양한 설정 방식을 지원할 수 있을까? -> 역할과 구현을 개념적으로 나눈 것

- **BeanDefinition 이라는 추상화**
- 앞서 얘기했던 ***역할과 구현***을 개념적으로 나눈 것
- 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다.
- **BeanDefinition 을 빈 설정 메타정보**라고 한다.

BeanDefinition 정보

* BeanClassName: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)

* factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름, 예) appConfig
* factoryMethodName: 빈을 생성할 팩토리 메서드 지정, 예) memberService
* Scope: 싱글톤(기본값)
* lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한
생성을 지연처리 하는지 여부
* InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
* DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
* Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할
의 빈을 사용하면 없음)

[ **BeanDefinition : 역할, AppConfig.xxxx : 구현** ]

<img src="https://user-images.githubusercontent.com/56071088/109682595-e8d35280-7bc1-11eb-9071-ce295c6420f0.PNG"  width="500" height="300">

<img src="https://user-images.githubusercontent.com/56071088/109682813-1b7d4b00-7bc2-11eb-97fc-158c37ed757a.PNG"  width="500" height="350">

* AnnotationConfigApplicationContext는 AnnotatedBeanDefinitionReader 를 사용해서 AppConfig.class 를 읽고 BeanDefinition 을 생성한다.
* GenericXmlApplicationContext는 XmlBeanDefinitionReader 를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition 을 생성한다.
* 새로운 형식의 설정 정보 ***AppCongig.xxxx***가 추가되면, ***XxxxBeanDefinitionReader***를 만들어서  appConfig.xxxx 설정 정보를 읽고 ***BeanDefinition을 생성***하면 된다.

```java

import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    // GenericXmlApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");

    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {

        String[] beanDefinitionNames = ac.getBeanDefinitionNames();

        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
            
            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName" + beanDefinitionName + " beanDefinition = " + beanDefinition);
            }
        }
    }
}
```

## 4. 다양한 의존관계 주입 방법 - 생성자 주입을 선택하자!

* 생성자 주입
* 수정자 주입(setter 주입)
* 필드 주입
* 일반 메서드 주입

Spring에는 다양한 의존관계 주입 방법이 있다. 이 책에서는 필드 주입, 속성을 통한 의존성 주입을 설명하였다. 하지만, 실무에서는 **스프링을 포함한 DI 프레임워크 대부분**이
***생성자 주입을 권장***한다. 그 이유는 다음과 같다.

**불변**
* 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료시점까지 의존관계를 변경할 일이 없다. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안된다.(불변해야 한다.)

* *수정자 주입*을 사용하면, setXxx 메서드를 ***public***으로 열어두어야 한다.
  
* **누군가 실수로 변경할 수 도 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아니다.**
  
* ***생성자 주입***은 **객체를 생성할 때 딱 1번만 호출**되므로 이후에 호출되는 일이 없다. 따라서 **불변하게 설계할 수 있다.**


### 생성자 주입

* 생성자를 통해서 의존 관계를 주입 받는 방법이다.
  * 생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.
  * ***불변, 필수 의존관계***에 사용

```java
@Component
public class OrderServiceImpl implements OrderService {
    
    //final 키워드 - 생성자에서 혹시라도 값이 설정되지 않는 오류를 컴파일 시점에 막아준다.
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

### 수정자 주입

* setter라 불리는 필드의 값을 변경하는 수정자 메서드(속성 메서드)(property method)를 통해서 의존관계를 주입하는 방법이다.
  * ***선택, 변경*** 가능성이 있는 의존관계에 사용
  * 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법이다.
    * **자바빈 프로퍼티 규약** : 자바에서는 과거부터 필드의 값을 직접 변경하지 않고, setXxx, getXxx 라는 메서드를 통해서 값을 읽거나 수정하는 규칙

```java
@Component
public class OrderServiceImpl implements OrderService {
 
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
 
    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
 
    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

### 필드 주입

* 필드에 바로 주입하는 방법
  
  * 코드가 간결해서 많은 개발자들을 유혹하지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 치명적인 단점이 있다.
  
  * DI 프레임워크가 없으면 아무것도 할 수 없다. ***사용하지 말자!***
    * 애플리케이션의 실제 코드와 관계 없는 테스트 코드 혹은
    * 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용

```java
@Component
public class OrderServiceImpl implements OrderService {
    @Autowired
    private MemberRepository memberRepository;
    @Autowired
    private DiscountPolicy discountPolicy;
}
```

### 일반 메서드 주입

* 일반 메서드를 통해서 의존 관계를 주입하는 방법
  
  * 한번에 여러 필드를 주입 받을 수 있다.
  * 일반적으로 잘 사용하지 않는다.
  
```java
@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

## 옵션 처리 - @Autowired(required=false), @Nullable, Optional<>

주입할 스프링 빈이 없어도 동작해야 할 때가 있다.

@Autowired만 사용하면, required옵션 값이 true로 설정되어 있어서 자동 주입 대상이 없으면 오류가 발생한다.

* @Autowired(required=false) : 자동 주입할 대상이 없으면 ***수정자 메서드*** **자체가 호출 안됨.**
  
* org.springframework.lang.***@Nullable*** : 자동 주입할 대상이 없으면 **null값이 입력**
* ***Optional<>*** : 자동 주입할 대상이 없으면 **Optional.empty**가 입력된다.
* : ***@Nullable, Optional<>*** 은 **스프링 전반에 걸쳐서 지원된다.** 예를 들어서 생성자 자동 주입에서 특정 필드에만 사용해도 된다.

ex)

```java
//호출 안됨
@Autowired(required = false)
public void setNoBean1(Member member) {
    System.out.println("setNoBean1 = " + member);
}
//null 호출
@Autowired
public void setNoBean2(@Nullable Member member) {
    System.out.println("setNoBean2 = " + member);
}
//Optional.empty 호출
@Autowired(required = false)
public void setNoBean3(Optional<Member> member) {
    System.out.println("setNoBean3 = " + member);
}
//출력결과
setNoBean2 = null
setNoBean3 = Optional.empty
```
## 조회 빈이 2개 이상 - 문제 발생 -> @Qualifier, @Primary

@Primary, @Qualifier 활용
* 코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. 
* 메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary 를 적용해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier 를 지정해서 명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다. 
* 물론 이때 메인 데이터베이스의 스프링 빈을 등록할 때 @Qualifier 를 지정해주는 것은 상관없다.

**우선순위**
* @Primary 는 기본값 처럼 동작하는 것이고, @Qualifier 는 매우 상세하게 동작한다. 
* 이런 경우 어떤 것이 우선권을 가져갈까? 
* 스프링은 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 선택권이 우선 순위가 높다. 
* 따라서 여기서도 ***@Qualifier 가 우선권이 높다.***


---
---
---

## AOP - Aspect Oriented Programming - 관점 지향 프로그래밍 - 핵심 관심사? 횡단 관심사?

* **스프링 AOP : 로직(Code) 주입**, 스프링 DI : 의존성(new)에 대한 주입

* AOP : 모듈의 핵심 관심사(핵심 비즈니스 로직)와 공통으로 나타나는 로직(횡단 관심사)로 나누어, **핵심 관심이 실행되기 전**, ***공통 로직(Code)을 주입***하는 것

* 코드 = 핵심 관심사 + 횡단 관심사
  * 핵심 관심사(Core Concern) : 모듈별로 다른 핵심 비즈니스 로직
  * 횡단 관심사(Cross-Cutting Concern) : 다수의 모듈별로 반복되어 중복해서 나타나는 부분
  
* 스프링 AOP의 핵심 3가지
  * 스프링 AOP는 인터페이스(interface)기반이다.
  * 스프링 AOP는 프록시(proxy)기반이다.
  * 스프링 AOP는 런타임(Runtime)기반이다.

* 객체 지향에서 로직(Code)가 있는 곳은 당연히 메서드 안쪽이다.
* 메서드에 코드를 주입할 수 있는 곳은 총 5가지
  * @Around : 메서드 전 구역
  * @Before : 메서드 시작 전
  * @After : 메서드 시작 후
  * @AfterReturning : 메서드 정상 종료 후
  * @Afterthrowing : 메서드에서 예외가 발생하면서 종료된 후

ex) memberController, Service, Repository에 시간 측정 로직을 공통으로 추가하고 싶을 때

**AOP 적용 전**

![image](https://user-images.githubusercontent.com/56071088/110094077-c0746f80-7dde-11eb-8803-d9fea1c527c7.png)


```java

@Transactional
public class MemberService {
    /**
     * 회원가입
     */
    public Long join(Member member) {
        long start = System.currentTimeMillis();
        try {
            validateDuplicateMember(member); //중복 회원 검증
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join " + timeMs + "ms");
        }
    }
    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers() {
        long start = System.currentTimeMillis();
        try {
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("findMembers " + timeMs + "ms");
        }
    }
}

```

**AOP 적용 후**

![image](https://user-images.githubusercontent.com/56071088/110095512-5a88e780-7de0-11eb-8750-691d44e27e66.png)

```java

package hello.hellospring.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class TimeTraceAop {
    
    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString()+ " " + timeMs + "ms");
        }
    }
}

```

## AOP 용어 정리

| 용어      | 의미                     |
| -- | -- |
| Aspect    | Advisor의 집합체! -> Advice들 + Pointcut들       |
| Advisor   | 어디서, 언제, 무엇을! -> 한 개의 Advice + 한 개의 Pointcut|
| Advice    | 언제, 무엇을!            |
| JoinPoint | 연결 가능한 지점!        |
| Pointcut  | Aspect 적용 위치 지정자! -> 어디에! |

* Pointcut - ***Aspect 적용 위치 지정자***
  * 위의 예시에서 *( * hello.hellospring..*(..))* 부분이다.
  
  * @Around("execution(* hello.hellospring..*(..))")의 의미
    * 지금 선언하고 있는 메서드(public Object execute)를 * hello.hellospring..*(..))"가 실행되는 전 구역 동안(@Around)에 실행하라는 의미
    * **public Object execute**는 ***횡단 관심사를 실행하는 메서드***이다.
  
  * **Pointcut** : **횡단 관심사를 적용할 타깃 메서드를 선택하는 지시자**(메서드 선택 필터) -> **타깃 클래스의 타깃 메서드 지정자**
  
  * 다른 AOP 프레임워크에서는 메서드 뿐만 아니라 속성 등에도 Aspect를 적용할 수 있기에 포괄적으로 ***Aspect 적용 위치 지정자*** 라고 한다.
  
  * [접근 제한자] 리턴 타입 [패키지&클래스] 메소드이름(파라미터) [throws 예외] // []는 생략가능을 의미 

* JoinPoint - **연결 가능한 지점**
  
  * ***JoinPoint : Aspect 적용이 가능한 모든 지점***
  
  * 스프링 AOP에서 JoinPoint란 스프링이 관리하는 모든 빈(Bean)의 모든 메서드
  
  * 예시에서 ProceedingJoinPoint joinPoint에 해당한다.
    * 광의의 JoinPoint : Aspect 적용이 가능한 모든 지점
    * 협의의 JoinPoint : 호출된 객체의 메서드 

* Advice - **언제, 무엇을!**
  
  * Advice : ***Pointcut에 언제, 무엇을 적용할지 정의한 메서드***이다.
  
  * Advice를 타깃 객체의 타깃 메서드에 적용될 부가 기능이라고 표현한 책도 있다. 
  
  * 예시에서 @Around
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable를 의미한다. -> Pointcut이 시작되는 전 구역(@Around)에 execute() 메서드를 실행하라는 의미

* Aspect - **Advisor들의 집합체!**
  
  * ***Aspect = Advice들 + Pointcut들***
  
  * Advice : [언제, 무엇을], Pointcut : [어디에]
  * Aspect : [언제, 어디에, 무엇을]

* Advisor - **언제, 어디에, 무엇을!**
  
  * ***Advisor = 한 개의 Advice + 한 개의 Pointcut***
  
  * Advisor는 스프링 AOP에서만 사용하는 용어, **다른 AOP 프레임워크에서는 사용하지 않는다.**
  * ***스프링 버전이 올라가면서 이제는 사용하지 말라고 권고하는 기능이기도 하다.***
  * Aspect가 나왔기 때문에, 하나의 Advice와 하나의 Pointcut만을 결합하는 Advisor를 사용할 필요가 없어졌기 때문이다.   

---
---
---

## PSA - 일관성 있는 서비스 추상화 - Portable Service Abstraction

Spring 프레임워크에서는 서비스 추상화를 위해 다양한 어댑터를 제공하고있다. 예를 들어, OXM(Object XML Mapping:객체와 XML매핑) 기술만 하더라도 Castor, JAXB, XMLBeans, JiBX등 다양한 기술이 있고, 이 다양한 기술들이 제공하는 API는 모두 제각각이다. 스프링은 제각각인 API를 위한 어댑터를 제공함으로써 실제로 어떤 OXM 기술을 쓰든 일관된 방식으로 코드를 작성할 수 있게한다. 또한 기존의 OXM기술에서 다른 OXM기술로 변경할 때 큰 변화 없이 세부 기술을 교체해서 사용할 수 있게 해주고있다. 이처럼 서비스 추상화를 해주면서 그것도 일관성 있는 방식을 제공한다고 해서 이를 PSA(일관성 있는 서비스 추상화)라고 한다. Spring은 OXM뿐 아니라, ORM, 캐시, 트랜잭션등 여러 기술에대해 PSA를 제공하고 있다.










