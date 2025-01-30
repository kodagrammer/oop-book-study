## **Chapter 7. 주요 디자인 패턴**

#### **디자인 패턴이란**
프로그램 작성 도중 빈번히 발생하는 디자인 문제를 가장 효과적으로 해결할 수 있다고 알려진 방법을 정리한게 패턴이다. 패턴을 알고 있다면, 상황에 따라 알맞는 설계를 선택하는 데 도움이 된다.

#### **디자인 패턴 종류**
+ [행동 패턴 (Behavioral Pattern)](/part3/chapter07/dayoung_behavioral.md)
  + 전략(Strategy) 패턴
  + 템플릿 매서드(Template Method) 패턴
  + 상태(State) 패턴
  + 옵저버(Observer) 패턴
  + 미디에이터(Mediator) 패턴
+ [구조 패턴 (Structural Pattern)](/part3/chapter07/dayoung_structural.md)
  + 데코레이터(Decorator) 패턴
  + 프록시(proxy) 패턴
  + 어댑터(Adapter) 패턴
  + 파사드(Facade) 패턴
  + 컴포지트(Composite) 패턴
+ [생성 패턴 (Creational Pattern)](/part3/chapter07/dayoung_creational.md)
  + 싱글톤(Singleton) 패턴
  + 팩토리 메서드(Factory Method) 패턴
  + 추상 팩토리(Abstract Factory) 패턴
+ 기타
  + [널(Null) 객체 패턴](#null-객체-패턴)

<br>

---

### **Null 객체 패턴**

**NullPointerException 방지를 위해 사용하는 패턴**이다. 특정 객체가 null인지 판단 후 로직을 수행해야 하는 경우, 클라이언트 쪽에서 null 체크를 하지 않아 발생하는 NullPointerException을 방지할 수 있다.

**패턴 미적용 시 예제**
```java
class Ticket {
  private String ticketNumber;
  private LocalDate ticketingDate;
  private LocalDate useDate;
  private LocalDate effectiveDate;

  public boolean expired() {...}
  public void refund(){ ... }
  public void use(){ ... }
}
```

```java
class Passenger {
  private Ticket ticketInfo;

  public 

  public void refund() {
    if(this.ticketInfo != null && !ticketInfo.expired()) {
      ticketInfo.refund();
    }
  }

  public void useTicket() {
    // null 체크 중복
    if(this.ticketInfo != null && !ticketInfo.expired()) {
      ticketInfo.use();
    }
  }
}
```

**패턴 적용 시 예제**
```java
interface Ticket {
  void refund();
  void use();
}

class issuedTicket implements Ticket {
  private String ticketNumber;
  private LocalDate ticketingDate;
  private LocalDate useDate;
  private LocalDate effectiveDate;
  
  private boolean expired() {...}
  @Override
  boolean refund() {
    if(!expired()){ ... }
  }
  @Override
  void use() {
    if(!expired()){ ... }
  }
}

// Null 객체 생성
class NullTicket implements Ticket {
  @Override
  boolean refund() {
  }
  @Override
  void use() {
  }
}
```

```java
class Passenger {
  private Ticket ticketInfo;

  public 

  public void refund() {
    ticketInfo.refund();
  }

  public void useTicket() {
    ticketInfo.refund();
  }
}
```

**주의점**
+ Null 객체 패턴을 남용하면 예외나 에러를 탐지하기 어려움
+ 관리할 클래스와 코드가 과도하게 증가할 수 있음