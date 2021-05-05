# 6장 스프링이 사랑한 디자인 패턴

* 디자인 패턴

  - 기존 환경 내에서 반복적으로 일어나는 문제들을 어떻게 풀어나갈 것인가에 대한 일종의 솔루션

  - 디자인 패턴 계의 교과서로 불리는 [GoF의 디자인패턴]에서는 객체지향적 디자인 패턴의 카테고리를 "생성 패턴(Creational Pattern)", "구조 패턴(Structural Pattern)", "행동 패턴(Behavioral Pattern)" 3가지로 구분함

  - 생성 패턴

    - 인스턴스를 만드는 절차를 추상화하는 패턴
    - 객체를 생성, 합성하는 방법이나 객체의 표현 방법을 시스템과 분리해줌
    - 생성 패턴을 이용하면 무엇이 생성되고, 누가 이것을 생성하며, 이것이 어떻게 생성되는지, 언제 생성할 것인지 결정하는 데 유연성을 확보할 수 있게됨
    
  - 구조 패턴
    - 작은 클래스들을 상속과 합성을 이용하여 더 큰 클래스를 생성하는 방법을 제공하는 패턴
    - 서로 독립적으로 개발한 클래스 라이브러리를 마치 하나인 양 사용할 수 있음
    - 여러 인터페이스를 합성(Composite)하여 서로 다른 인터페이스들의 통일된 추상을 제공
  - 행동 패턴
    - 알고리즘을 어느 객체에 정의하는 것이 좋을지 등을 다룸
    - 객체나 클래스에 대한 패턴을 정의하는 것이 아니고, 그들 간의 교류방법에 대하여 정의하는 것이 주된 상항
    - 해당 패턴들을 통해 우리는 객체간의 제어 구조보다는 객체들을 어떻게 연결시킬 것인지에 더 중점을 두는 패턴
  
  | 생성 패턴(Creational Pattern)          | 구조 패턴(Structural Pattern) | 행동 패턴(Behavioral Pattern)                |
  | -------------------------------------- | ----------------------------- | -------------------------------------------- |
  | [Singleton](#Singleton-패턴)           | [Adapter](#Adapter-패턴)      | Command                                      |
  | Abstract Factory                       | Composite                     | Interpreter                                  |
  | [Factory Method](#Factory-Method-패턴) | [Decorator](#Decorator-패턴)  | Iterator                                     |
  | Builder                                | Facade                        | Mediator                                     |
  | Prototype                              | Flyweight                     | Memento                                      |
  |                                        | [Proxy](#Proxy-패턴)          | Observer                                     |
  |                                        |                               | State                                        |
  |                                        |                               | [Strategy](#Strategy-패턴)                   |
  |                                        |                               | [Template Method](#Template-Method-패턴)     |
  |                                        |                               | [Template Callback](#Template-Callback-패턴) |



---



#### Adapter 패턴

- 서로 다른 두 인터페이스 사이에 통신을 가능하게 하는 것

- Adapter 클래스 다이어그램

  ![Adapter클래스 다이어그램](https://user-images.githubusercontent.com/48685242/108542400-06cdc700-7327-11eb-973d-caf8aeaf1d90.png)

- 클라이언트에서 어댑터를 사용하는 방법

  1. 클라이언트에서 타겟 인터페이스를 사용하여 메소드를 호출함으로써 어댑터에 요청을 한다.
  2. 어댑터에서는 어댑티 인터페이스를 사용하여 그 요청을 어댑티 에 대한 하나 이상의 메소드를 호출로 변환한다.
  3. 클라이언트에서는 호출 결과를 받긴 하지만 중간에 어댑터가 껴 있는지는 전혀 알지 못한다.

- 구현 방법

  1. Class Adapter 방식 : 자바의 상속(Inheritance)을 이용한 방법

     ```java
     //Using inheritance for adapter pattern
     public class SocketClassAdapterImpl extends Socket implements SocketAdapter{
         @Override
         public Volt get120Volt() { return getVolt(); }
      
         @Override
         public Volt get12Volt() {
             Volt v= getVolt();
             return convertVolt(v,10);
         }
      
         @Override
         public Volt get3Volt() {
             Volt v= getVolt();
             return convertVolt(v,40);
         }
     	
         private Volt convertVolt(Volt v, int i) { return new Volt(v.getVolts()/i); }
      
     }
     ```

  2. Object Adapter 방식 : 자바의 합성(Composite)을 이용한 방법

     ```java
     public class SocketObjectAdapterImpl implements SocketAdapter{
         //Using Composition for adapter pattern
         private Socket sock = new Socket();
     	
         @Override
         public Volt get120Volt() { return sock.getVolt(); }
      
         @Override
         public Volt get12Volt() {
             Volt v= sock.getVolt();
             return convertVolt(v,10);
         }
      
         @Override
         public Volt get3Volt() {
             Volt v= sock.getVolt();
             return convertVolt(v,40);
         }
     	
         private Volt convertVolt(Volt v, int i) { return new Volt(v.getVolts()/i); }
     }
     ```

- Java의 JDK 안에서 Arrays.asList()나 InputStreamReader(InputStream), OutputStreamWriter(OutputStream) 등에서 사용

  

---



#### Proxy 패턴

* 어떤 다른 객체로 접근하는 것을 통제하기 위해서 그 객체의 대리자(surrogate)나 자리표시자(placeholder)의 역할을 하는 객체를 제공하는 패턴

* 만약에, 시스템 명령어를 실행하는 객체를 갖고 있을 때 그 객체를 개발자가 컨트롤 하는 것이라면 괜찮지만 만약 그 객체를 클라이언트에게 제공하려고 한다면 클라이언트 프로그램이 개발자가 원치 않는 파일을 삭제하거나 설정을 변경하는 등의 명령을 내릴 수 있기 때문에 심각한 문제를 초래할 수 있음. 클라이언트와 시스템 명령어를 실행하는 객체사이에 Proxy패턴을 적용하여 특정 명령어에 대해서는 클라이언트가 수행하지 못하도록 통제(Control access)할 수 있음

  ```java
  # CommandExecutor.java
  public interface CommandExecutor {
      public void runCommand(String cmd) throws Exception;
  }
  
  
  # CommandExecutorProxy.java
  public class CommandExecutorProxy implements CommandExecutor {
      private boolean isAdmin;
      private CommandExecutor executor;
  	
      public CommandExecutorProxy(String user, String pwd){
          if("ReadyKim".equals(user) && "correct_pwd".equals(pwd))
              isAdmin = true;
          executor = new CommandExecutorImpl();
      }
  	
      @Override
      public void runCommand(String cmd) throws Exception {
          if(isAdmin){
              executor.runCommand(cmd);
          }else{
              if(cmd.trim().startsWith("rm")){
                  throw new Exception("rm command is not allowed for non-admin users.");
              }else{
                  executor.runCommand(cmd);
              }
          }
      }
  }
  
  
  # ProxyPatternTest.java
  public class ProxyPatternTest {
   
      public static void main(String[] args){
          CommandExecutor executor = new CommandExecutorProxy("ReadyKim", "wrong_pwd");
          try {
              executor.runCommand("ls -ltr");
              executor.runCommand("rm -rf abc.pdf");
          } catch (Exception e) {
              System.out.println("Exception Message::"+e.getMessage());
          }	
      }
  }
  ```

* 이처럼 프록시 패턴은 실제 서비스 메소드의 반환 값에 가감하는 것을 목적으로 하지 않고 "제어의 흐름"을 변경하거나 다른 로직을 수행하기 위해 사용함



---



#### Decorator 패턴

* 데코레이터 패턴은 메소드 호출의 반환값에 변화를 주기 위해 중간에 장식자를 두는 패턴

* 상속(Inheritance)과 합성(Composition)을 사용하여 객체에 동적으로 책임을 추가할 수 있게 함

* 데코레이터 패턴은 런타임에서 유연하게 객체의 기능들을 수정하고 조합하는데 유용하게 사용되는 패턴

  ```java
  # Car.java
  public interface Car {
  	public void assemble();
  }
      
      
  # BasicCar.java
  public class BasicCar implements Car {
  	@Override
  	public void assemble() { System.out.print("Basic Car."); }
  }
  
  
  # CarDecorator.java
  public class CarDecorator implements Car {
  	protected Car car;
  	
  	public CarDecorator(Car c){ this.car=c; }
  	
  	@Override
  	public void assemble() { this.car.assemble(); }
  }
  
  
  # SportsCar.java
  public class SportsCar extends CarDecorator {
  	public SportsCar(Car c) { super(c); }
   
  	@Override
  	public void assemble(){
  		super.assemble();
  		System.out.print(" Adding features of Sports Car.");
  	}
  }
      
      
  # LuxuryCar.java
  public class LuxuryCar extends CarDecorator {
  	public LuxuryCar(Car c) { super(c); }
  	
  	@Override
  	public void assemble(){
  		super.assemble();
  		System.out.print(" Adding features of Luxury Car.");
  	}
  }
  
  
  # DecoratorPatternTest.java
  public class DecoratorPatternTest {
  	public static void main(String[] args) {
  		Car sportsCar = new SportsCar(new BasicCar());
  		sportsCar.assemble();
          
  		System.out.println("\n*****");
  		
  		Car sportsLuxuryCar = new SportsCar(new LuxuryCar(new BasicCar()));
  		sportsLuxuryCar.assemble();
  	}
  }
  
  
  # 출력
  Basic Car. Adding features of Sports Car.
  *****
  Basic Car. Adding features of Luxury Car. Adding features of Sports Car.
  ```

* JDK 에서 FileReader, BufferedReader 등 IO 클래스에 사용되는 패턴



---



#### Singleton 패턴

- 특정 클래스의 인스턴스가 오직 하나임을 보장하며, 이 인스턴스에 접근할 수 있는 전역적인 접촉점을 제공하는 패턴

- 커넥션 풀, 쓰레드 풀, 디바이스 설정 객체등 인스턴스를 여러개 만들게 되면 불피요한 자원을 사용하게 되고, 예상치 못한 결과를 낳을 수 있기 때문에 고안됨

- Singleton패턴의 공통점

  1. private 생성자만을 정의해 외부 클래스로부터 인스턴스 생성을 차단함
  2. 싱글톤을 구현하고자 하는 클래스 내부에 멤버 변수로써 private static 객체 변수를 만듬
  3. public static 메소드를 통해 외부에서 싱글톤 인스턴스에 접근할 수 있도록 접점을 제공함

- 싱글톤 구현방법

  1. **Eager Initialization**

     가장 간단한 형태의 구현 방법으로 싱글톤 클래스의 인스턴스를 클래스 로딩 단계에서 생성하는 방법

     로딩 단계에서 생성하기 때문에 어플리케이션에서 해당 인스턴스를 사용하지 않더라도 낭비가 발생할 수 있음

     싱글톤 클래스의 적은 리소스를 다룰 때 사용함

     ```java
     public class Singleton {
         private static final Singleton instance = new Singleton();
     
         // private constructor to avoid client applications to use constructor
         private Singleton(){}
         public static Singleton getInstance(){ return instance; }
     }
     ```

  2. **Static Block Initialization**

     Eager Initialization와 유사하지만, static block을 통해서 Exception Handling에 대한 옵션을 제공함

     ```java
     public class Singleton {
         private static Singleton instance;
         
         private Singleton(){}
         //static block initialization for exception handling
         static{
             try{
                 instance = new Singleton();
             }catch(Exception e){
                 throw new RuntimeException("Exception occured in creating singleton instance");
             }
         }
         
         public static Singleton getInstance(){ return instance; }
     }
     ```

  3. **Lazy Initialization**(책에서 소개된 방법)

     앞선 두 방식과는 달리 나중에 초기화하는 방법으로 사용하지 않았을 경우의 메모리 낭비문제를 해결할 수 있음

     인스턴스가 생성되지 않은 시점에 여러 thead가 동시에 getInstance()를 호출한다면 예상치 못한 결과를 얻을 수 있으므로, multi-thread 환경에서 동기화 문제점을 가지고있음

     ```java
     public class Singleton {
         private static Singleton instance;
         
         private Singleton(){}
         
         public static Singleton getInstance(){
             if(instance == null){
                 instance = new Singleton();
             }
             return instance;
         }
     }
     ```

  4. **Thread Safe Singleton**

     Lazy Initialization의 문제를 해결하기 위한 방법으로, getInstance() 메소드에 synchronized를 걸어두는 방식

     synchronized 키워드는 임계 영역(Critical Section)을 형성해 해당 영역에 오직 하나의 쓰레드만 접근 가능하게 함

     synchronized 키워드를 통해 메소드 내에 진입하는 쓰레드가 하나로 보장받기 때문에 multi-thread환경에서도 정상 동작하게됨

     synchronized 키워드 자체에 대한 비용이 크기 때문에 싱글톤 인스턴스 호출이 잦은 어플리케이션의 성능을 저하시킬 수 있음

     ```java
     public class Singleton {
         private static Singleton instance;
         
         private Singleton(){}
         
         public static synchronized Singleton getInstance(){
             if(instance == null){
                 instance = new Singleton();
             }
             return instance;
         }
     }
     ```

  5. **Bill Pugh Singleton Implementaion**

     Bill Pugh가 고안한 방식으로, private inner static class를 두어 싱글톤 인스턴스을 두어 싱글톤 인스턴스를 가지고 있게하는 방식

     앞선 방식이 안고 있는 문제점들을 대부분 해결하여 **현재 가장 널리 쓰이는 싱글톤 구현 방법**임

     Eager Initialization, Static Block Initialization와 차이점은 SingletonHelper 클래스는 Singleton 클래스가 Load 될 때에도 Load 되지 않다가 getInstance()가 호출됐을 때 비로소 JVM 메모리에 로드되고, 인스턴스를 생성하게 된다는 것

     synchronized을 사용하지 않았기 때문에 성능 저하 또한 해결

     ```java
     public class Singleton {
         private Singleton(){}
         
         private static class SingletonHelper{
             private static final Singleton INSTANCE = new Singleton();
         }
         
         public static Singleton getInstance(){
             return SingletonHelper.INSTANCE;
         }
     }
     ```



---



#### Template Method 패턴

- 알고리즘의 구조를 메소드에 정의하고, 하위 클래스에서 알고리즘 구조의 변경없이 알고리즘을 재정의 하는 패턴

- **상위 클래스**에 **공통 로직을 수행하는 템플릿 메서드**와 하위 클래스에 **오버라이딩을 강제하는 추상 메서드** 또는 **선택적 오버라이딩이 가능한 Hook 메소드**를 두는 패턴

* 전체적으로는 동일하면서 부분적으로는 다른 구문으로 구성된 메서드의 ***코드 중복을 최소화 할 때 유용하다.*** 

* 다른 관점에서 보면 동일한 기능을 상위 클래스에서 정의하면서, 확장/변화가 필요한 부분만 서브 클래스에서 구현할 수 있도록 한다. 


EX 1)
```java
public abstract class CaffeineBeverageWithHook {
    void final prepareRecipe() { // 템플릿 메서드(Template Method)
        boilWater();
        brew();
        pourInCup();
        if ( customerWantsCondiments() ) {
            addcondiments();
        }
    }

    abstract void brew(); // 추상 메서드(Abstract Method)

    abstract void addcondiments(); // 추상 메서드(Abstract Method)

    void boilWater() {
        System.out.println("물 끓이는 중");
    }

    void pourInCup() {
        System.out.println("컵에 따르는 중");
    }
    
    // 후크 메서드(Hook Method)
    boolean customerWantsCondiments() {    //이 메소드는 서브클래스에서 필요에 따라 오버라이드 할수 있는 메소드이므로 후크(Hook)이다.
    return true;                                   
    }
}

public class CoffeeWithHook extends CaffeineBeverageWithHook {
    @Override
    void brew() {
        System.out.println("필터를 통해 커피를 우려내는 중");
        }

    @Override
    public void addCondiments() {
        System.out.println("설탕과 우유를 추가하는 중");
    }
    
    @Override
    public boolean customerWantsCondiments() {
        String answer = getUserInput();

        if( answer.toLowerCase().startWith("y")) {
            return true;
        } else { 
            return false;
        }
    }
    
    private String getUserInput() {
        // 입력받는 로직
    }
}
```

EX 2)
- Template Method클래스 다이어그램

  ![Template Method클래스 다이어그램](https://user-images.githubusercontent.com/48685242/108399765-d2420880-725d-11eb-8a11-22f451601da1.jpg)


참고: https://jusungpark.tistory.com/24 [정리정리정리], https://www.crocus.co.kr/1531 [Crocus]



---



#### Factory Method 패턴

- 객체를 생성하는 인터페이스는 미리 정의하되, 인스턴스를 만들 클래스의 결정은 서브 클래스 쪽에서 내리는 패턴

- **여러 개의 서브 클래스를 가진 슈퍼 클래스가 있을 때 인풋에 따라 하나의 자식 클래스의 인스턴스를 리턴해주는 방식**

- 어떤 클래스가 자신이 생성해야 하는 객체의 클래스를 예측할 수 없을 때, 생성할 객체를 기술하는 책임을 자신의 서브클래스가 지정했으면 할 때 사용함

  ```java
  # Super Class
  public abstract class Computer {
      public abstract String getRAM();
      public abstract String getHDD();
      public abstract String getCPU();
  	
      @Override
      public String toString(){
          return "RAM= "+this.getRAM()+", HDD="+this.getHDD()+", CPU="+this.getCPU();
      }
  }
  
  
  # Sub Class - 1
  public class PC extends Computer {
      private String ram;
      private String hdd;
      private String cpu;
  	
      public PC(String ram, String hdd, String cpu){
          this.ram=ram;
          this.hdd=hdd;
          this.cpu=cpu;
      }
      @Override
      public String getRAM() {
          return this.ram;
      }
   
      @Override
      public String getHDD() {
      return this.hdd;
      }
  
      @Override
      public String getCPU() {
          return this.cpu;
      }
  }
  
  
  # Sub Class - 2
  public class Server extends Computer {
      private String ram;
      private String hdd;
      private String cpu;
  	
      public Server(String ram, String hdd, String cpu){
          this.ram=ram;
          this.hdd=hdd;
          this.cpu=cpu;
      }
      @Override
      public String getRAM() {
          return this.ram;
      }
   
      @Override
      public String getHDD() {
          return this.hdd;
      }
   
      @Override
      public String getCPU() {
          return this.cpu;
      }
  }
  
  # Factory Class
  public class ComputerFactory {
      public static Computer getComputer(String type, String ram, String hdd, String cpu){
          if("PC".equalsIgnoreCase(type))
              return new PC(ram, hdd, cpu);
          else if("Server".equalsIgnoreCase(type))
              return new Server(ram, hdd, cpu);
  		
          return null;
      }
  }
  
  
  # Application
  public class TestFactory {
      public static void main(String[] args) {
          Computer pc = ComputerFactory.getComputer("pc","2 GB","500 GB","2.4 GHz");
          Computer server = ComputerFactory.getComputer("server","16 GB","1 TB","2.9 GHz");
          System.out.println("Factory PC Config::"+pc);
          System.out.println("Factory Server Config::"+server);
      }
  }
  ```

  팩토리 메소드 패턴을 사용하게 된다면 인스턴스를 필요로 하는 Application에서 Computer의 Sub 클래스에 대한 정보는 모른 채 인스턴스를 생성할 수 있게 됨. 이를 통해 앞으로 Computer 클래스에 더 많은 Sub 클래스가 추가된다 할지라도 getComputer()를 통해 인스턴스를 제공받던 Application의 코드는 수정할 필요가 없게 됨.

- 팩토리 패턴은 클라이언트 코드로부터 서브 클래스의 인스턴스화를 제거하여 **서로 간의 종속성을 낮추고, 결합도를 느슨하게 하며(Loosely Coupled), 확장을 쉽게**하는 장점이 있음 ( 위 예시에서 PC class에 대해 수정 혹은 삭제가 일어나더라도 클라이언트는 알 수 없기 때문에 코드를 변경할 필요가 없음 )

- java.util 패키지에 있는 Calendar, ResourceBundle, NumberFormat 등의 클래스에서 정의된 **getInstance()** 메소드에 사용됨

- Boolean, Integer, Long 등 Wrapper class 안에 정의된 **valueOf()** 메소드에 사용됨



---



#### Strategy 패턴

* Strategy(전략) 패턴을 구성하는 세 요소

  1. 전략 메소드를 가진 **전략 객체**
  2. 전략 객체를 생성해 컨텍스트에 주입하는 **클라이언트(전략 객체의 생성자)**
  3. 전략 객체를 사용하는 **컨텍스트(전략 객체의 사용자/소비자)**

* 객체가 할 수 있는 행위들 각각을 전략으로 만들어 놓고, 동적으로 행위의 수정이 필요한 경우 전략을 바꾸는 것만으로 행위의 수정이 가능하도록 만든 패턴

  ex) 한 과일 매장은 상황에 따라 다른 가격 할인 정책을 적용하고 있다. 제일 먼저 온 손님에게 10%를 할인해주고 마지막 손님은 20% 그리고 신선도가 떨어진 과일에 대해서는 20% 할인을 해주고 있다

  ```java
  # 전략패턴이 적용되지 않은 코드
  public class Calculator {
       public double calculate(boolean isFirstGuest, boolean isLastGuest, List<Item> items) {
           double sum = 0;
           for (Item item : items) {
               if (isFirstGuest) {
                   sum += item.getPrice() * 0.9;
               } else if (!item.isFresh()) {
                   sum += item.getPrice() * 0.8;
               } else if (isLastGuest) {
                   sum += item.getPrice() * 0.8;
               } else {
                   sum += item.getPrice();
               }
           }
           return sum;
       }
   }
      
  public class Item {
      private final String name;
      private final int price;
  
      public Item(String name, int price) {
          this.name = name;
          this.price = price;
      }
  
      public int getPrice() { return price; }
  
      public boolean isFresh() { return true; }
  }
  ```

  전략 패턴이 적용되지 않은 코드는 특정한 할인정책을 사용하기 위해서는 할인 조건이 충족하는지를 if-else 분기를 타며 해결하고 있다. 이러한 코드는 하나의 메소드에 너무 많은 확인 로직이 추가되고 변경에 유연하지 않다는 단점이 존재한다. 또한 시간이 지날수록 코드의 분석이 어려워져 유지보수비용이 비싸다.

  

  ```java
  # 전략패턴이 적용된 코드
  public interface DiscountPolicy {
      double calculateWithDisCountRate(Item item);
  }
  
  public class FirstCustomerDiscount implements DiscountPolicy{
      @Override
      public double calculateWithDisCountRate(Item item) {
          return item.getPrice() * 0.9;
      }
  }
  
  public class LastCustomerDiscount implements DiscountPolicy{
      @Override
      public double calculateWithDisCountRate(Item item) {
          return item.getPrice() * 0.8;
      }
  }
  
  public class UnFreshFruitDiscount implements DiscountPolicy{
      @Override
      public double calculateWithDisCountRate(Item item) {
          return item.getPrice() * 0.8;
      }
  }
  
  
  # Calculator.java
  public class Calculator {
      private final DiscountPolicy discountPolicy;
  
      public Calculator(DiscountPolicy discountPolicy) { this.discountPolicy = discountPolicy; }
  
      public double calculate(List<Item> items) {
          double sum = 0;
          for (Item item : items) {
              sum += discountPolicy.calculateWithDisCountRate(item);
          }
          return sum;
      }
  }
  
  
  # FruitController.java
  public class FruitController {
      public static void main(String[] args) {
          // 클라이언트(FruitController)가 컨텍스트(Calculator)에게 전략 객체(FirstCustomerDiscount)를 전달
          Calculator calculator = new Calculator(new FirstCustomerDiscount());
          calculator.calculate(Arrays.asList(
              new Item("Apple", 3000),
              new Item("Banana", 3000),
              new Item("Orange", 2000),
              new Item("Pitch", 4000)
          ));
      }
  }
  ```

  전략 패턴을 통한 장점은 다음과 같다.

  1. 새로운 정책(전략)이 추가되었을 때 기존의 소스코드를 수정하는 것이 아니라, 새로운 전략 클래스를 추가함

  2. 전략 객체 / 클라이언트 / 컨텍스트의 분류를 명확히하여 유지보수성을 높임

     

---



#### Template Callback 패턴

* **Spring DI(의존성 주입)에 사용**

* Strategy(전략) 패턴과 동일하지만, 전략을 익명 내부 클래스로 정의해서 사용한다는 특징이 있음

  ex) 위 과일가게에 Template Callback 패턴을 적용

  ```java
  # Template Callback패턴이 적용된 코드
  public interface DiscountPolicy {
      double calculateWithDisCountRate(Item item);
  }
  
  # Calculator.java
  public class Calculator {
      private final DiscountPolicy discountPolicy;
  
      public Calculator(DiscountPolicy discountPolicy) { this.discountPolicy = discountPolicy; }
  
      public double calculate(List<Item> items) {
          double sum = 0;
          for (Item item : items) {
              sum += discountPolicy.calculateWithDisCountRate(item);
          }
          return sum;
      }
  }
  
  
  # FruitController.java
  public class FruitController {
      public static void main(String[] args) {
          // 클라이언트(FruitController)가 컨텍스트(Calculator)에게 전략 객체(DiscountPolicy)를 전달
          // 전략패턴과 달리 전략을 익명 내부 클래스로 정의해서 사용
          Calculator calculator = new Calculator(new DiscountPolicy(){
              @Override
              public double calculateWithDisCountRate(Item item) {
                  return item.getPrice() * 0.9;
              }
          });
          calculator.calculate(Arrays.asList(
              new Item("Apple", 3000),
              new Item("Banana", 3000),
              new Item("Orange", 2000),
              new Item("Pitch", 4000)
          ));
      }
  }
  ```
