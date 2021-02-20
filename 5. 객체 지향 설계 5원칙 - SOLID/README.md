# 5장 객체 지향 설계 5원칙 - SOLID

* 객체 지향 프로그램을 잘 설계하기 위한 5원칙

* **응집도는 높이고(High Cohesion), 결합도는 낮추라(Loose Coupling)** 는 고전 원칙을 객체 지향의 관점에서 재정립한 것이다.

* 응집도(Cohesion) : 하나의 모듈 내부에 존재하는 구성 요소들의 기능적 관련성
  *  응집도가 높은 모듈은 하나의 책임에 집중
  *  재시용이나 기능의 수정, 유지보수가 용이하다.

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

![DIP](https://user-images.githubusercontent.com/56071088/108606078-51207800-73fb-11eb-84d3-008062e64846.PNG)

---

## Spring과 SOLID












