# 4장 자바가 확장한 객체 지향

## abstract 키워드 - 추상 메서드와 추상 클래스

* 추상 메서드(abstract method) : 선언부는 있는데, 구현부가 없는 메서드
* 추상 메서드를 하나라도 갖고 있는 class는 **반드시 추상 클래스(abstract class)로 선언해야 한다.**
* 추상 메서드는 하위 클래스에게 구현을 강제한다. **Overriding 강제**
* 추상 클래스는 인스턴스(instance), 즉 **객체를 만들 수 없는 클래스가 된다.**
```java
public abstract class Animal {
    abstract void roar();
}
```

---

## static 블록 - 클래스 생성 시의 실행 블록

* static block은 해당 클래스가 코드에서 맨 처음 사용될 때, 데이터 저장 영역(T 메모리)의 static 영역에 로딩되며, 이때 ***단 한번만*** 실행된다.  
  
* 주로 정적 변수(클래스 변수)를 초기화 하는데 사용한다.
* static 블록은 여러 개가 있을 수 있다.
* 클래스가 제일 처음 사용될 때는 다음 3가지 경우 중 하나다.
    * 클래스의 정적 속성을 사용할 때
  
    * 클래스의 정적 메서드를 사용할 때
    * 클래스의 인스턴스(객체)를 최초로 만들 때

```java
public class Driver {
    public static void main(String[] args) {
        System.out.println("main method start");//main method start
        Animal.age = 1;//Animal Class Ready1 //Animal Class Ready2
        System.out.println(Animal.age);//1                                       
    }
}
class Animal {
    static int age = 0;
    static {
        age = 10;
        System.out.println("Animal Class Ready1");
    }
    static {
        age = 20;
        System.out.println("Animal Class Ready2");
    }
}
```
---

## final 키워드

* final 클래스 : **상속을 허락하지 않겠다**는 의미, ***하위 클래스를 만들 수 없다.***

* final 변수 : final 키워드가 붙은 변수는 ***변경 불가능한 상수***가 된다.
* final 메서드 : ***재정의, 즉 오버라이딩(Overriding)***을 금지하게 된다.

---

## instanceof 연산자

* instanceof 연산자는 ***만들어진 객체가 특정 클래스의 인스턴스인지***를 물어보는 연산자, 결과값은 true 또는 false를 반환

* 객체 참조 변수의 타입이 아닌 **실제 객체의 타입**에 의해 처리한다.
* 클래스들의 상속 뿐만 아니라 **인터페이스의 구현 관계에서도 동일하게 적용**된다.
* 코드에 instanceof 연산자가 보인다면, 리팩토링의 대상이 아닌지 점검해봐야 한다. 객체 지향 설계 5원칙 중, LSP(리스코프 치환 원칙)를 위반하는 코드에서 주로 나타나는 연산자이기 때문이다.
---

## interface 키워드와 implements 키워드

interface는 ***public 정적 상수, public 추상 메서드***만 가질 수 있다.

* interface는 정적 상수와 추상 메서드만 가질 수 있기에 따로 메서드에 public과 abstract, 속성에 public, static, final을 붙이지 않아도 자동으로 JAVA가 알아서 붙여준다.

```java
public interface MemberService {
    public static final double PI = 3.14159;
    
    public abstract void join(Member member); 

    public abstract Member findMember(Long memberId);
}
```
---

## this 키워드

* 지역 변수와 속성(객체 변수, 정적 변수)의 이름이 같은 경우 지역 변수가 우선한다.

* 객체 변수와 이름이 같은 지역 변수가 있는 경우, **객체 변수를 사용하려면 this를 접두사**로 사용한다.

* 정적 변수와 이름이 같은 지역 변수가 있는 경우, **정적 변수를 사용하려면 클래스 명을 접두사**로 사용한다.
    

