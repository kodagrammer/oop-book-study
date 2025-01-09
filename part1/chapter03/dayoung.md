### **Chapter 3. 다형성과 추상타입**
<br>

#### **1. 다형성과 상속**

**◼ 다형성이란?**

한 객체가 여러가지 타입을 가질 수 있는 것

![다형성과 상속](/images/dayoung/polymorphism_diagram.png)

위 그림 기준으로 Seller는 Person, Agent 타입 두 가지 모두 될 수 있으며, Java 기준으로 다형성은 두 가지 방법으로 구현할 수 있다.
+ 인터페이스 상속
  + 인터페이스는 기능에 대한 정의만 제공, 상속 클래스에서 구현
+ 상위 클래스 상속
  + 상위 클래스는 기능 구현, 상속 클래스에서는 상위 클래스의 기능을 수정하거나 새로운 기능을 구현 가능

```java
public interface Person {
    boolean isExpired(); // 기능만 정의
}

public class Seller implements Person {
    private Date expiredDate;
    private long sellerId;

    public boolean isExpired() {
        // 기능구현
    }
}
```
```java
// 상위 클래스
public class Agent { 
    private long agentId; 
    private List<long> productList;

    public long getAgentId() {
        return agentId;
    }

    public void addProduct(long productId) {
        if(productList == null) {
            productList = new ArrayList<>();
        }
        productList.add(productId);
    }
}
// 상속 클래스
public class Seller extend Agent implements Person {
    private Date expiredDate;
    private long sellerId;

    public boolean isExpired() {
        // 기능구현
    }

    // 메서드 기능 수정
    @Override
    public void addProduct(long productId) {
        if(sellerId != agentId) {
            new InsufficientPermissionsException();
        }
        super.addProduct(productId);
    }

    public void sell(long productId) {
        if(productList != null && productList.contains(productId)) {
            // 기능 구현
        }
    }
}
```
이러한 상속을 통해 객체는 여러개의 타입을 갖고, 각 타입에 정의된 기능을 사용할 수 있게 된다.
```java
Agent agent = new Agent();
agent.addProduct(1);    //Agent에 구현된 메서드 실행

Agent agent = new Seller();
agent.addProduct(2);    // Seller에 구현된 메서드 실행

Person person = new Seller();
person.isExpired();     // Seller에 구현된 메서드 실행
```

<br>

---
#### **2. 추상화**

**◼ 추상화란?**

데이터나 프로세스 등을 의미가 비슷한 개념이나 표현으로 정의하는 과정이다. 추상화된 타입은 큰 개념에 대한 의미만 제공할 뿐, 상세 구현에 대해서는 알 수 없다. Java에서는 주로 인터페이스로 C++에서는 추상클래스를 이용해 추상 타입을 정의한다. 추상화는 인터페이스 뿐만 아니라 모델링, 단순 로직 등도 일종의 추상화에 해당한다.

<br>

**◼ 추상화가 필요한 이유**

컴파일러는 추상클래스가 아니라 구현클래스를 직접 생성하여 사용하여도 오류로 보지 않지만, 직접 구현클래스를 사용하게 되면 또 다시 변경이 어려운 코드가 된다.

```java
public class FileDataReader implements LogReader {
    public byte[] read() {...}   
}

public class SocketDataReader implements LogReader {
    public byte[] read() {...}   
}

public class FlowController {
    public void logCollect(boolean useFile) {
        if(useFile) {
            FileDataReader reader = new FileDataReader();
            reader.read();
        } else {
            SocketDataReader reader = new SocketDataReader();
            reader.read();
        }
    }
}
```
위 코드에서 파일과 소켓이 아닌 DB에서 로그를 읽는 케이스, HTTP로 읽어오는 케이스 등이 늘어난다면, `useFile` 을 매개변수로 받아오는 구조부터 변경해야 하며 해당 메서드를 사용하는 클래스를 또 변경해주어야 한다. 그 뿐 아니라 `else if` 문도 무한히 증식될 것이다.

해당 코드를 변경에 유연하고 재사용하기 편하게 만들기 위해선 다음과 같은 단계가 필요하다.
+ `FlowController`에서 로그데이터 유형에 따른 `LogReader` 객체 생성의 책임 분리
+ 변경되는 부분 추상화하여 `if-else`로만 처리되는 경직성 해소

```java
// 로그 데이터 유형 enum으로 정의
Enum LogType { File, Socket, Http, DB }

// if-else 구문 개선
LogReader logReader;
switch(logType){
    case File -> { logReader = new FileDataReader();}
    case Socket -> { logReader = new SocketDataReader();}
    case Http -> { logReader = new HttpDataReaduer();}
    case DB: -> { logReader = new DbReader();}
}
```

```java
// LogReader 객체 생성 책임 분리
public class LogReaderFactory {
    // 싱글톤패턴
    private static LogReaderFactory instance = new LogReaderFactory();
    public static LogReaderFactory getInstance() {
        return instance;
    }

    private LogReaderFactory() {}

    // 객체생성
    public LogReader create(LogType logType) {
        LogReader reader = null;

        switch(logType){
            case File -> { logReader = new FileDataReader();}
            case Socket -> { logReader = new SocketDataReader();}
            case Http -> { logReader = new HttpDataReaduer();}
            case DB: -> { logReader = new DbReader();}
        }
        return reader;
    }
}

// LogReader를 사용하는 클래스 변경 없이 새로운 요구사항 적용 가능
public class FlowController {
    public void logCollect(LogType logType) {
        LogReader logReader = LogReaderFactory.getInstance().create(logType);
        byte[] data = logReader.read();
    }
}
```

정리하면, 추상화를 통해 `FlowController`는 `LogType`이 변경된다 하더라도 수정되지 않게 되었으며, 데이터를 읽는 대상이 바뀌더라도 `FlowController`의 수정 없이 재사용 가능한 코드가 되었다.

<br>

**◼ 추상화 Tip**
+ 기능에 대한 상세 구현을 작성하기 전, 인터페이스로 추상화된 명세를 먼저 생성하고 상세 구현 시작
+ 추상 타입이 너무 증가하면 복잡도가 올라가기 때문에, 변화 가능성이 높은 경우에만 사용
+ 인터페이스 오퍼레이션 명은 사용자 입장에서 명확하게 작성
+ 인터페이스 테스트를 위해 Mock 객체 활용