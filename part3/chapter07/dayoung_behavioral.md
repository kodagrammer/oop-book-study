## **행동 패턴(Behavioral Pattern)**

- [**행동 패턴(Behavioral Pattern)**](#행동-패턴behavioral-pattern)
  - [**전략(Strategy) 패턴**](#전략strategy-패턴)
  - [**템플릿 메서드(Template Method) 패턴**](#템플릿-메서드template-method-패턴)
  - [**상태(State) 패턴**](#상태state-패턴)
  - [**옵저버(Observer) 패턴**](#옵저버observer-패턴)
  - [**중재자(미디에이터: Mediator) 패턴**](#중재자미디에이터-mediator-패턴)

<br>

---

### **전략(Strategy) 패턴**

전략 패턴은 **비슷한 기능에 대해 정책/알고리즘이 빈번하게 바뀌어야 할 때** 적합한 패턴이다. 정책을 결정하는 콘텍스트를 클라이언트에게 전달 받아 기능을 수행함으로써 실행 중에 실시간으로 객체 동작을 변경할 수 있도록 한다.

```java
// 전략 추상화
interface IStrategy{
  void doSomething();
}

// 전략별 알고리즘 구현
class ConcreteStrategyA implements IStrategy{
	@Override
	public void doSomething() {}
}
class ConcreteStrategyB implements IStrategy{
	@Override
	public void doSomething() {}
}
```
```java
// 전략 실행 클래스
class Context {
	IStrategy strategy;
	// 전략 교체 메서드
	void setStrategey(IStrategy strategy){
		this.strategy = strategy;
	}
	
	// 전략 실행
	void doSomething() {
		this.strategy.doSomething();
	}
}
```

위 클래스에서 정책 C가 추가되게 되면, `IStrategy`를 상속하는 구현체 `ConcreteStrategyC` 클래스를 추가해주기만 하면 된다. 대표적인 예로는 Collection의 `sort()` 시 사용하는 `Comparator`가 있다.

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(2);
numbers.add(3);
numbers.add(1);

Collections.sort(numbers, new Comparator<Integer>(){
	// 비교시 사용하는 전략 알고리즘을 직접 지정
	@Override
	public int compare(Integer o1, Interger o2) {
		return o1 - o2;
	}
});
```

클라이언트에서 전략에 대한 상세구현에 의존하는 것이 문제처럼 보일 수 있지만, 보통 이 경우 전략의 구현 클래스와 클라이언트 코드가 쌍을 이루기 때문에 유지보수 문제가 발생할 가능성이 적다.

**전략 패턴 주의점**
+ 알고리즘이 많아질수록 관리해야할 객체 수 증가
+ 알고리즘 변경이 잦지 않으면 오히려 프로그램 복잡도 증가
+ 개발자가 적절한 전략을 선택하기 위해 전략 간 차이점을 파악하고 있어야 함
  
<br>

---
### **템플릿 메서드(Template Method) 패턴**
템플릿 메서드는 **동일한 실행순서를 가지고 있는 알고리즘을 템플릿으로 만들고 내부의 세부 동작은 하위 클래스마다 다르게 동작하도록 구현**하는 패턴이다. 상위 클래스에 알고리즘의 뼈대(템플릿)을 만들어 변경을 최소화 하고, 자주 변경/확장될 수 있는 기능은 하위 클래스에서 상속받아 "메소드의 동작 순서는 고정하고 세부 실행 내용은 다양화"할 수 있게 한다.

```java
abstract class AbstractTemplate{
    public final void templateMethod(){
        step1();
        step2();

        if(hook()) {
            ...
        }

        step3();
    }

    boolean hook() {return true;}

    protected abstract void step1();
    protected abstract void step2();
    protected abstract void step3();
}
```
```java
public ImplementationA extends AbstractTemplate{
    @Override
    protected void step1() {}
    @Override
    protected void step2() {}
    @Override
    protected void step3() {}
}

public ImplementationB extends AbstractTemplate{
    @Override
    protected void step1() {}
    @Override
    protected void step2() {}
    @Override
    protected void step3() {}
    @Override
    protected void hook() {return false;}
}
```

**템플릿 메소드 장점**
+ 대규모 알고리즘의 특정 부분만 재정의할 수 있어, 알고리즘의 다른 부분에서 발생하는 변경사항의 영향이 적음
+ 상위 클래스에서 로직을 공통화하여 코드의 중복을 줄임
+ 핵심 로직을 상위클래스에서 관리하고 변경을 적게하여 유지보수가 용이

**템플릿 메소드 단점**
+ 템플릿에 의해 알고리즘 유연성이 제한됨
+ 알고리즘이 복잡할수록 템플릿 구조를 유지하기 어려움
+ 추상 메소드의 증가로 클래스 생성/관리가 어려울 수 있음
+ 알고리즘 골격이 변경되면, 모든 하위 클래스의 변경이 필요함
+ 하위 클래스를 통해 기본 단계 구현을 억제하여 리스코프 치환 법칙을 위반할 여지가 있음

<br>

---
### **상태(State) 패턴**

상태 패턴은 "상태"를 객체화/추상화 하여 상태에 따라 다른 알고리즘이 동작할 수 있도록 위임한 패턴을 말한다. 전략 패턴과 유사하지만 전략 패턴은 "전략 알고리즘"을 객체화 하여 선택하도록 하는 것이라면, 상태 패턴은 "상태"를 객체화 하여 클라이언트에게 선택하도록 하는 차이점이 있다. 또한 상태는 굳이 여러개의 객체로 중복하여 생성할 필요가 없기 때문에 싱글톤으로 생성하여 사용한다.

```java
interface AbstractState{
    void requestHandle(Context cxt);
}

class ConcreteStateA implements AbstractState{
    @Override
    public void requestHandle(Context cxt){}
}

class ConcreteStateB implements AbstractState{
    @Override
    public void requestHandle(Context cxt){
        // 동작에 따라서, 기능 후 상태를 변경하는 케이스도 존재. ex.On/Off 기능
        ctx.setState(ConcreteStateC.getInstance());
    }
}

class ConcreteStateC implements AbstractState{
    @Override
    public void requestHandle(Context cxt){}
}
```
```java
class Context{
    private AbstractState state;
    public void setState(AbstractState state) {
        this.state = state; 
    }
    // 상태에 의존한 처리 메소드로서 state 객체에 처리를 위임
    void request(){
        state.requestHandle(this);
    }
}
```

**상태 패턴의 장점**
+ 상태에 따른 동작을 개별 클래스로 관리할 수 있음
+ 상태와 관련된 동작을 각각의 상태 클래스에 분산시켜 코드 복잡성을 낮춤
+ 싱글톤으로 일관성 없는 상태 주입을 방지하는데 도움이 됨

**상태 패턴의 단점**
+ 상태별로 클래스를 생성하여 관리해야 할 클래스 수 증가
+ 상태가 많고 상태 규칙 변화가 많으면, Context의 상태 변경 코드가 복잡해짐
+ 상태 개수가 적고, 상태 변경이 자주 일어나지 않는 경우, 패턴 적용이 과도할 수 있음

<br>

---
### **옵저버(Observer) 패턴**

**대상자(subject)의 상태가 변화했을 때, 상태 변화에 대해 관찰자(Observer) 객체에 전달**하고 싶을 때 사용하는 패턴이다. 1:N의 의존성을 가지며 주로 분산형 이벤트 핸들링 시스템을 구현할 때 사용한다. (Ex. Jira 메일링 서비스)

**Subject 객체 역할**
+ 옵저버 목록을 관리
+ 옵저버 등록/제거 메서드 제공
+ 상태 변경 발생 시, 옵저버 객체 메서드 호출

```java
interface ISubject{
    void addObserver(IObserver o);
    void removeObserver(IObserver o);
    void notify();
}

class ConcreteSubject implements ISubject {
    private List<IObserver> observerList = new ArrayList<>();

    @Override
    public void addObserver(IObserver o){
        observerList.add(o);
    }

    @Override
    public void removeObserver(IObserver o) {
        if(observerList.contians(o)){
            observerList.remove(o);
        }
    }

    @Override
    public void notify(){
        for(IObserver o : observerList){
            o.update();
        }
    }
}
```
```java
interface IObserver{
    void update();
}

class ObserverA implements IObserver {
    @Override
    public void update(){
        System.out.println("ObserverA 한테 이벤트 알림이 왔습니다.");
    }
}

class ObserverB implements IObserver {
    @Override
    public void update(){
        System.out.println("ObserverB 한테 이벤트 알림이 왔습니다.");
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        ISubject publisher = new ConcreteSubject();

        IObserver o1 = new ObserverA();
        IObserver o2 = new ObserverB();
        publisher.registerObserver(o1);
        publisher.registerObserver(o2);

        publisher.notifyObserver();
        // ObserverA 한테 이벤트 알림이 왔습니다.
        // ObserverB 한테 이벤트 알림이 왔습니다.

        publisher.removeObserver(o1);
        publisher.notifyObserver();
        //ObserverB 한테 이벤트 알림이 왔습니다.
    }
}
```

**옵저버 패턴 장점**
+ 대상자 객체 상태를 주기적으로 조회하지 않아도 됨
+ 런타임 시점에서 옵저버를 등록할 수 있음
+ 대상자와 관찰자(옵저버) 객체 간의 결합을 느슨하게 할 수 있음

**옵저버 패턴 단점**
+ 알림순서를 제어할 수 없음(무작위 순서로 알림 전송)
+ 옵저버 패턴을 자주 구성하면 구조와 동작을 알아보기 힘들어, 코드 복잡도 증가
+ 다수의 옵저버 객체를 등록하고 해지하지 않으면 메모리 누수가 발생할 수 있음

<br>

**Java에 내장된 라이브러리로 옵저버 패턴 구현**
```java
// Subject 구현
class WeatherApi extends Observable {
    float temp; // 온도
    float humidity; // 습도
    float pressure; // 기압

    void measurementsChanged() {
        // 현재의 온습도 데이터를 랜덤값으로 얻는 것으로 비유하였다.
        temp = new Random().nextFloat() * 100;
        humidity = new Random().nextFloat() * 100;
        pressure = new Random().nextFloat() * 100;

        /* 부모 클래스 Observable의 부모 메서드 */
        setChanged(); // 내부 플래그를 true 로 만들어 알림이 동작하게 끔 한다
        notifyObservers(); // 옵저버들에게 알림 전파
    }
}
```
```java
class KoreanUser implements Observer {
    String name;

    KoreanUser(String name) {
        this.name = name;
    }

    public void display(WeatherAPI api) {
        System.out.printf("%s님이 현재 날씨 상태를 조회함 : %.2f°C %.2fg/m3 %.2fhPa\n", name, api.temp, api.humidity, api.pressure);
    }

    @Override
    public void update(Observable o, Object arg) {
        // 발행자가 WeatherAPI 인 경우 (Observable을 상속한 모든 클래스에서 발행이 가능하니 구분해 주어야 한다)
        if(o instanceof WeatherAPI) {
            WeatherAPI w = (WeatherAPI) o; // 다운 캐스팅
            display(w);
        }
    }
}
```

**내장 옵저버 객체의 한계**
+ Observable이 클래스로 구현되어 단일 상속만 가능
+ `setChanged()` 메소드가 protected로 선언되어 메서드 위임도 불가

<br>

---
### **중재자(미디에이터: Mediator) 패턴**

미디에이터 패턴은 각 객체들이 직접 메시지를 주고 받는 대신, 중간에 **중계 역할을 수행하는 미디에이터 객체를 두고 미디에이터를 통해서 각 객체들이 간접적으로 메시지를 주고 받도록 하는 패턴**이다. 해당 패턴은 객체간 많은 의존관계를 가지고 있거나 상호작용이 복잡할 때 사용하며, 중재자 패턴이라고도 한다.

**예제 - 채팅 기능**
```java
interface Mediator{
    void addUser(User user);
    void deleteUser(User user);
    void sendMessage(User user, String message);
}

class User{
    private Mediator mediator;
    private String name;

    public User(Mediator mediator, String name){
        this.mediator = mediator;
        this.name = name;
    }

    public void send(String message) {
        System.out.println(this.name+": Sending Message="+message);
        mediator.sendMessage(message, this);
    }
    public void receive(String message) {
        System.out.println(this.name+": Received Message:"+message);
    }
}

class ConcreteMediator implements Mediator{
    private final List<User> userList;

    @Override
    public void addUser(User user) {
        if(userList == null){
            userList = new ArrayList<>();
        }
        userList.add(user);
    }

    @Override
    public void deleteUser(User user) {
        if(userList != null && userList.contains(user)) {
            userList.remove(user);
        }
    }

    @Override
    public void sendMessage(User user, String message) {
        for(User u : userList){
            if(u != user){
                u.receive(message);
            }
        }
    }
}
```
```java
class Client {
    void run(){
        Mediator mediator = new ConcreteMediator()
        ;
        User user1 = new User(mediator, "lee");
        User user2 = new User(mediator, "kim");
        User user3 = new User(mediator, "park");
        User user4 = new User(mediator, "yon");
        mediator.addUser(user1);
        mediator.addUser(user2);
        mediator.addUser(user3);
        mediator.addUser(user4);

        user1.send("안녕하세요");
        // lee: Sending Message=안녕하세요
        // kim: Received Message:안녕하세요
        // park: Received Message:안녕하세요
        // yon: Received Message:안녕하세요

        mediator.deleteUser(user4);

        user2.send("yon없지?");
        // kim: Sending Message=yon없지?
        // lee: Received Message:yon없지?
        // park: Received Message:yon없지?
    }
}
```


**미디에이터 패턴 장점**
+ 미디에이터가 각 객체간의 통신을 담당하면서, 다른 객체간의 의존과 결합을 줄여줌

**미디에이터 패턴 단점**
+ 특정 어플리케이션에 맞춰 구현되기 때문에 재사용하기가 어려움

**유사한 패턴과의 차이점**
+ 옵저버 패턴 - 옵저버 패턴은 1:N인 것에 비해 미디에이터는 N:M 통신
+ 파사드 패턴 - 파사드 패턴은 단반향 통신임에 비해 미디에이터는 양방향 통신