# 5장 객체 지향 설계 5원칙 - SOLID

* 객체 지향 프로그램을 잘 설계하기 위한 5원칙

* **응집도는 높이고(High Cohesion), 결합도는 낮추라(Loose Coupling)** 는 고전 원칙을 객체 지향의 관점에서 재정립한 것이다.

* 응집도(Cohesion) : 하나의 모듈 내부에 존재하는 구성 요소들의 기능적 관련성
  *  응집도가 높은 모듈은 하나의 책임에 집중
  *  재사용이나 기능의 수정, 유지보수가 용이하다.

* 결합도(Coupling) : 모듈(Class) 간의 상호 의존 정도
  * 결합도가 낮으면 모듈 간의 상호 의존성이 낮아진다.
  * 객체의 재사용이나 수정, 유지보수에 용이하다.

* SoC(Separation Of Concerns) : 관심사의 분리 : 관심이 같은 것끼리는 하나의 객체 안으로 또는 친한 객체로 모으고, 관심이 다른 것은 가능한 한 따로 떨어져 서로 영향을 주지 않도록 분리하라는 것이다. ***관심사가 다르고 변화 시기가 다르면 분리하라는 것***      
  * SoC -> SRP, OCP, DIP 

---

* **SRP(Single Responsibility Principle)** : 단일 책임 원칙 : 한 클래스는 하나의 책임만 가져야 한다. 어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다. 

* **OCP(Open Closed Principle)** : 개방 폐쇄 원칙 : 자신의 확장에는 열려 있으나, 주변의 변화에 대해서는 닫혀 있어야 한다.

* **LSP(Liskov Substitution Principle )** : 리스코프 치환 원칙 : 서브 타입(Sub-Type)은 언제나 자신의 기반 타입(Base-Type)으로 교체할 수 있어야 한다.

* **ISP(Interface Segregation Principle)** : 인터페이스 분리 원칙 : 클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다. ***특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나 보다 낫다.***

* **DIP(Dependency Inversion Principle)** : 의존 역전 원칙 : 추상화에 의존하고, 구체화에 의존하면 안된다. ***구현 클래스에 의존하지 말고, 인터페이스에 의존하라***

---

## SRP(Single Responsibility Principle) : 단일 책임 원칙

* **하나의 클래스는 하나의 책임만을 가진다.**
 
* **"어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다" - 로버트.C.마틴** 

* 속성, 메서드, 클래스, 패키지, 모듈, 컴포넌트, 프레임워크에 적용하여 **각각 하나의 역할과 책임만을 갖게 해야 한다.**

* SRP와 가장 관계 있는 객체 지향 4대 특성 - ***추상화(모델링)*** 
  
  ex) 메서드가 SRP를 지키지 않은 경우 - **분기 처리를 위한 if문**
  ```java
     public class 강아지 {
     	final static Boolean 숫컷 = true;
     	final static Boolean 암컷 = false;
     	Boolean 성별;
     
     	void 소변보다() {
     		if (this.성별 == 숫컷) {
     			// 한쪽 다리를 들고 소변을 본다.
     		} else {
     			// 뒤다리 두 개로 앉은 자세로 소변을 본다.
     		}
     	}
     }
     ```

---

## OCP(Open Closed Principle) : 개방 폐쇄 원칙 

* **자신의 확장에는 열려 있고, 주변의 변화에 대해서는 닫혀 있어야 한다.**

* **B라는 기능을 추가**하고 싶을 때, ***A라는 원본 Code가 바뀌어서는 안된다***는 의미이다.

* 상위 클래스 또는 인터페이스를 중간에 둔다.

* OCP와 가장 관계 있는 객체 지향 4대 특성 - ***다형성(사용 편의성)*** 
    * 다형성으로 **인터페이스를 구현한 객체**를 ***실행 시점에 유연하게 변경***할 수 있다.

[OCP의 예 : JDBC]

![OCP의 예 ,JDBC](https://user-images.githubusercontent.com/56071088/108604649-883e5b80-73f2-11eb-8d60-5fdb7a5ddac7.png)

* JDBC 인터페이스
    * 자바 애플리케이션은 **데이터베이스라고 하는 주변의 변화에 닫혀 있다.**
    * 데이터베이스를 교체한다는 것은 **데이터베이스가 자신의 확장에는 열려 있다.**

---

## LSP(Liskov Substitution Principle ) : 리스코프 치환 원칙

* ***서브 타입(Sub-Type)은 언제나 자신의 기반 타입(Base-Type)으로 교체할 수 있어야 한다.***
    * 분류도

    * 하위 클래스 is a kind of 상위 클래스  - 하위 클래스는 상위 클래스의 **한 종류다.**

    * 구현 클래스 is able to 인터페이스 - 구현 분류는 인터페이스 **할 수 있어야 한다.**

* LSP와 가장 관계 있는 객체 지향 4대 특성 - ***상속(재사용과 확장)*** 

---

## ISP(Interface Segregation Principle) : 인터페이스 분리 원칙 

* ***특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나 보다 낫다.***

* **"클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다." - 로버트.C.마틴** 

    * 인터페이스를 통해 메서드를 외부에 제공할 때는 최소한의 메서드만 제공하라

    * 인터페이스가 명확해지고, 대체 가능성이 높아진다.

* ISP와 가장 관계 있는 객체 지향 4대 특성 - ***상속(재사용과 확장)*** 

ex) ISP 적용 전

```java 
public interface SmartPhone {
    String telephone();

    String mp3();

    String internet();
}

public class Iphone implements SmartPhone {
    @Override
    public String telephone() {
        return "ring ring";
    }

    @Override
    public String mp3() {
        return "play mp3";
    }

    @Override
    public String internet() {
        return "connect complete!";
    }
}

public class Galaxy implements SmartPhone {
    @Override
    public String telephone() {
        return "ring ring";
    }

    @Override //이 기능을 삭제하고 싶지만 강제로 구현해야 하는 문제가 발생
    public String mp3() {
        return "play mp3";
    }

    @Override
    public String internet() {
        return "connect complete!";
    }

    public String video() {
        return "play video";
    }
}
```

ex) ISP 적용 후

```java
public interface SmartPhone {
    String telephone();

    String internet();
}

public interface IphoneFunction {
    String mp3();
}

public interface GalaxyFunction {
    String video();
}

public class Iphone implements SmartPhone, IphoneFunction {
    @Override
    public String telephone() {
        return "ring ring";
    }

    @Override
    public String internet() {
        return "connect complete!";
    }

    @Override
    public String mp3() {
        return "play mp3";
    }
}

public class Galaxy implements SmartPhone, GalaxyFunction {
    @Override
    public String telephone() {
        return "ring ring";
    }

    @Override
    public String internet() {
        return "connect complete!";
    }

    @Override
    public String video() {
        return "play video";
    }
}

```

---

## DIP(Dependency Inversion Principle) : 의존 역전 원칙

* ***추상화에 의존하고, 구체화에 의존하지 마라***
  
  * 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻

* 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향 받지 않게 하는 것

* DIP와 가장 관계 있는 객체 지향 4대 특성 - ***다형성(사용 편의성)*** 
    * 다형성으로 **인터페이스를 구현한 객체**를 ***실행 시점에 유연하게 변경***할 수 있다.

ex) DIP 적용 예시

![DIP적용 후](https://user-images.githubusercontent.com/48685242/108036670-ddd5d980-707b-11eb-8485-e05e5eb32a67.jpg)

* 자동차가 구체적인 타이어들(스노우, 일반, 광폭 타이어)이 아닌 ***추상화된 타이어 인터페이스에만 의존***하게 함으로써 스오우 타이어에서 다른 구체적인 타이어로 변경해도 자동차는 이제 그 영향을 받지 않은 형태로 구성된다.
  
---

# Spring과 SOLID

![DIP](https://user-images.githubusercontent.com/56071088/108606078-51207800-73fb-11eb-84d3-008062e64846.PNG)

## 1. 다형성의 실세계 비유 -> "역할과 구현을 분리"

역할과 구현으로 구분하면, **유연해지며 변경이 용이해진다.**

* **클라이언트(운전자)** 는 대상의 역할(인터페이스)만 알면 된다.
* **클라이언트**는 **구현 대상의 내부 구조를 몰라도 되고, 변경되어도 영향을 받지 않는다.**
* **클라이언트** 는 **구현 대상 자체를 변경**해도 영향을 받지 않는다.
  
## 2. 객체의 협력이라는 관계부터 생각

* 혼자 있는 객체는 없다.
* 수 많은 객체 클라이언트와 객체 서버는 서로 협력 관계를 가진다.
    * 클라이언트: 요청, 서버: 응답

## 3. 자바 언어의 다형성 적용

* 자바의 오버라이딩(Overriding) 적용
* 다형성으로 인터페이스(interface)를 구현한 객체를 **실행시점에 유연하게 변경 가능하다.**

## 4. 다형성만으로는 부족하다 -> OCP, DIP에 위배

<img src="https://user-images.githubusercontent.com/56071088/108619199-78605f00-7466-11eb-963d-ba8b73a10d49.PNG"  width="400" height="250">

* **다형성 만으로는 구현 객체를 변경할 때 클라이언트 코드도 함께 변경된다.**

```java
public class MemberServiceImpl implements MemberService{
    //private final MemberRepository memberRepository = new MemoryMemberRepository(); 
    private final MemberRepository memberRepository = new JdbcMemberReposiroty();
    //문제점 1 : DIP 위반 :추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다.
    //문제점 2 : OCP 위반 :기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다! MemoryMemberRepository 를 JdbcMemberReposiroty 로 변경하는 순간 MemberServiceImpl 의 소스 코드도 함께 변경해야 한다!  
}

```

## 5. Spring의 DI 컨테이너(AppConfig)

**관심사의 분리(SoC)**

* 기존에는 클라이언트가 의존하는 서버 구현 객체를 직접 생성하고, 실행하였다.
*  AppConfig를 두어, 애플리케이션의 전체 동작 방식을 크게 사용 영역과 구성영역으로 나눈다.
*  AppConfig는 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, **구현 객체를 생성하고 연결**하는 책임을 가진다.
* **클라이언트는 자신의 실행하는 역할만 수행하면 된다** -> 책임이 명확해졌다(SRP)

ex) 관심사의 분리

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
    //AppConfig 를 보면 역할과 구현 클래스가 한눈에 들어온다.
    //애플리케이션 전체 구성이 어떻게 되어있는지 빠르게 파악할 수 있다.
    @Bean
    public MemoryMemberRepository memberRepository() { //역할(추상화(Interface))에 대한 것과 그에 따른 구현이 한 눈에 보인다.
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {//각각의 추상화(Interface)에 대한 역할들이 잘 들어나게 Refactoring 한다.
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(memberRepository(), discountPolicy()); 
    }
    
    @Bean
    public DiscountPolicy discountPolicy() {
        //return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}

```

* **SRP 단일 책임 원칙 적용**
    * ***한 클래스는 하나의 책임만 가져야 한다.***
    * SRP 단일 책임 원칙을 따르면서 ***관심사를 분리***함.
    * **클라이언트 객체**는 ***실행하는 책임***만 담당
    * **AppConfig**는 ***구현 객체를 생성하고 연결하는 책임***을 담당

* **DIP 의존관계 역전 원칙 적용**
  * ***추상화에 의존해야지, 구체화에 의존하면 안된다.***
  * 클라이언트 코드(MemberServiceImpl)가 discountPolicy(추상화 인터페이스)에만 의존하도록 코드를 변경했다.
  * AppConfig가 FixDiscountPolicy 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드
에 의존관계를 주입했다.  

* **OCP 개방 폐쇄 원칙 적용**
  * ***확장에는 열려 있으나 변경에는 닫혀 있어야 한다.***
  * AppConfig가 FixDiscountPolicy에서 RateDiscountPolicy로 변경해서 클라이언트에 주입하므로 클라이언트 코드는 변경하지 않아도 된다.
  * **소프트웨어 요소를 새롭게 확장해도 사용 역영의 변경은 닫혀 있다**
 





  



  
















