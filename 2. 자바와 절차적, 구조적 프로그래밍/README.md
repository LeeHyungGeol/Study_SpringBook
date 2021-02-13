# 2장 자바와 절차적/구조적 프로그래밍

## 자바(JAVA)는 객체 지향 프로그래밍 언어이기 이전에 프로그래밍 언어로서, 절차적/구조적 프로그래밍 언어의 특징들도 함께 갖추고 있다. 그 특징들을 먼저 공부해보자.
---
## 자바 프로그램의 개발과 구동, 메모리 영역
---
 자바 개발 도구인 JDK를 통해 개발된 프로그램은 JRE에 의해 가상의 컴퓨터인 JVM 상에서 구동된다.

JDK는 ***자바 소스 컴파일러***인 **javac.exe**를 포함한다.

JRE는 ***자바 프로그램 실행기***인 **java.exe**를 포함한다.

편의를 위해 배포될 때는 JDK가 JRE를 포함하고, JRE는 JVM을 포함하는 형태로 배포된다.

* JDK : Java Development Kit / 자바 개발 도구 / JVM용 소프트웨어 개발 도구
* JRE : Java Runtime Environment / 자바 실행 환경 / JVM용 OS
* JVM : Java Virtual Machine / 자바 가상 기계 / 가상의 컴퓨터
  

<img src="https://user-images.githubusercontent.com/56071088/107841747-e5d61500-6e00-11eb-82f7-7e4123b7c103.PNG"  width="480" height="350">

[ **객체 지향 프로그램의 메모리 사용 방식** ]
![객체 지향 프로그램의 메모리 사용 방식](https://user-images.githubusercontent.com/56071088/107841996-32baeb00-6e03-11eb-9256-1d351c39dc7c.png)


  ### 데이터 저장 영역
  * ***Static 영역*** : 클래스(Class)들의 공간, java.lang 패키지, import된 패키지 및 프로그램상의 모든 Class들
  * ***Stack 영역*** : 메서드(Method)들의 공간, 스택 프레임(Stack Frame)
  * ***Heap 영역*** : 객체(Object)들의 공간

---
## 메서드 스택 프레임, 변수와 메모리, 블록 스택 프레임, 지역변수와 전역변수
---
### 1. JAVA에 존재하는 절차적/구조적 프로그래밍의 특징
   *  ***절차적 프로그래밍***은 **goto를 쓰지 말라는 것**이다. goto를 쓰게 되면 프로그램의 실행순서가 이리저리 이동하게 되어 인간이 이해하기에 너무 복잡해진다. 사용하지 말라고 예약어로 만들어 놓은 것이다.
  
   *  ***구조적 프로그래밍***은 **함수를 쓰라는 것**이다. 함수(Function)와 메서드(Method)는 전혀 다르지 않다. 조금의 차이점은 **메서드(Method)는 반드시 Class 정의 안에 존재해야 한다.**
   *  함수: 중복 코드를 한 곳에서 관리, 논리를 함수 단위로 분리
   *  공유 사용시 문제가 발생하는 전역변수 대신 **지역변수를 사용하자**
  
### 2. JAVA 프로그램 구동 중 발생하는 데이터 저장 영역의 변화와 특징

**Example)**
```java
public class Start {
    static int staticInt = 123;
    public static void main(String[] args) {
        int x;
        x = 10;
        int y = 20;

        System.out.println(x);//10
        System.out.println(y);//20
        System.out.println(staticInt); //123

        if(x==10) {
            int m = func(x);
            y = m;
        }
        else {
            int z = x + 9;
            y = z;
        }

        System.out.println(staticInt); //615
        System.out.println(x); //10
        System.out.println(y); //50
        //System.out.println(z); //Error
        //System.out.println(m); //Error
    }

    private static int func(int n) {
        int x = n;
        staticInt *= 5;
        x *= 5;
        return x;
    }

} 
```

  * JRE는 먼저 프로그램 안에 main() 메서드가 있는지 확인하고 있다면, JVM을 부팅시킨다. 부팅된 JVM은 목적 파일을 받아서 실행하고, 전처리 과정을 수행한다.
  
  * **전처리 과정** : **java.lang 패키지, import된 패키지**를 포함하여 **프로그램 상의 모든 Class들**의 데이터 저장 영역의 ***Static 영역***에 가져다 놓는다.
  
  * **스택 프레임(Stack Frame)** : main(), func()와 같은 메서드 들은 **여는 중괄호 '{'** 를 만날 때 마다 ***Stack영역에 Stack Frame이 하나씩 생성***되며 params를 포함한 지역 변수들은 Stack Frame내에 생성된다. 반대로 닫는 중괄호 '}' 를 만나면, 해당 Stack Frame이 Stack 영역에서 사라진다.
  
  *  ***블록 스택 프레임(Block Stack Frame)*** : 여는 중괄호 '{' 를 만나면, 스택 프레임이 생성되는 것과 마찬가지로 if~else문 과 같은 중괄호를 만나면 해당 메서드의 스택 프레임 내에 중첩되어 Block Stack Frame이 생성된다.
  
  * ***외부 스택 프레임에서 내부 스택 프레임의 변수에 접근하는 것은 불가능하나 그 역은 가능하다.***
  
  * main()과 func()와 같은 서로 다른 메서드 스택 프레임이기에 변수 x가 이름만 같지만 실제로는 서로 별도의 변수 공간을 갖는다. 또한, 서로가 서로에게 접근이 불가하다.
  
  * ***Call By Value(값에 의한 호출)*** : 변수 자체가 아니라 변수가 저장한 값만을 복제해서 전달
  
  * ***전역변수 사용하지 말자*** : 프로젝트 규모에 따라 코드가 커지면 어느 메서드가 전역변수를 변경하였는지 쉽게 파악하지 못한다.
      * Stack Frame에 종속적인 지역 변수
      * Stack Frame에 독립적인 전역 변수
      * Static 영역 : **클래서 멤버 변수**
      * Stack 영역 : **지역 변수**
      * Heap 영역 : **객체 멤버 변수**, 객체와 함께 Garbage Collector(GC)라고 하는 힙 메모리 회수기에 의해 사라진다.
---
## 멀티 스레드(Multi Thread), 멀티 프로세스(Multi Process)
---
 * ***멀티 스레드(Multi Thread)*** : Stack영역을 스레드 개수만큼 분할해서 사용한다. 
    * 하나의 데이터 저장 영역 안에서 스택 영역만 분할한다.
    * Stack 영역은 서로 접근 불가하지만, Static, Heap영역은 공유해서 사용한다.
    * 자바 웹 프로그래밍에서 서블릿(Servlet)은 요청당 Process가 아닌 Thread를 생성하기 때문에 더 효율적이다.
 * ***멀티 프로세스(Multi Process)*** : 다수의 데이터 저장 영역을 사용한다.
   * 하나의 프로세스가 다른 프로세스의 데이터 저장 영역을 절대 접근 불가하다. 
   * 메모리 사용량은 그만큼 크다. 