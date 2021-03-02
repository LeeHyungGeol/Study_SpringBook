# 7장 스프링 삼각형과 설정 정보

## 스프링 삼각형(IoC/DI, AOP, PSA)
* 스프링 삼각형
  * POJO(Plain Old Java Objet)를 기반으로 IoC/DI, AOP, PSA라고 하는 스프링의 3대 프로그래밍 모델

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
```

* 관심사의 분리(SoC)를 이용하여 애플리케이션의 동작 방식을 사용 영역과 구성 영역으로 나눈다.
* 여기서 전략 패턴(Strategy Pattern)이 사용된다.
    * **전략 객체** : 전략 메서드를 가진 전략 객체 : **memberRepository, orderService, discountPolicy**
    * **컨텍스트** : 전략 객체를 사용하는 컨텍스트 : **return new Object_Instance_Name()**
    * **클라이언트** : 전략 객체를 생성해 컨텍스트에 주입 : ***AppConfig***


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
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://
www.springframework.org/schema/beans/spring-beans.xsd">
 
    <bean id="memberService" class="hello.core.member.MemberServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository" />
    </bean>
    
    <bean id="memberRepository"
        class="hello.core.member.MemoryMemberRepository" />
 
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

[ **BeanDefinition : 역할, AppConfig.xxxx : 구현** ]

<img src="https://user-images.githubusercontent.com/56071088/109682595-e8d35280-7bc1-11eb-9071-ce295c6420f0.PNG"  width="500" height="300">

<img src="https://user-images.githubusercontent.com/56071088/109682813-1b7d4b00-7bc2-11eb-97fc-158c37ed757a.PNG"  width="500" height="350">

* AnnotationConfigApplicationContext는 AnnotatedBeanDefinitionReader 를 사용해서 AppConfig.class 를 읽고 BeanDefinition 을 생성한다.
* GenericXmlApplicationContext는 XmlBeanDefinitionReader 를 사용해서 appConfig.xml 설정 정보를 읽고 BeanDefinition 을 생성한다.
* 새로운 형식의 설정 정보 ***AppCongig.xxxx***가 추가되면, ***XxxxBeanDefinitionReader***를 만들어서 ***BeanDefinition을 생성***하면 된다.

```java

import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;

public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new
            AnnotationConfigApplicationContext(AppConfig.class);
    // GenericXmlApplicationContext ac = new
    GenericXmlApplicationContext("appConfig.xml");

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

## 4. 다양한 의존관계 주입 방법













