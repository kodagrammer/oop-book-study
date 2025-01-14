## **구조 패턴(Structural Pattern)**

- [**구조 패턴(Structural Pattern)**](#구조-패턴structural-pattern)
  - [**데코레이터(Decorator) 패턴**](#데코레이터decorator-패턴)
  - [**프록시(proxy) 패턴**](#프록시proxy-패턴)
  - [**어댑터(Adapter) 패턴**](#어댑터adapter-패턴)
  - [**파사드(Facade) 패턴**](#파사드facade-패턴)
  - [**복합체(컴포지트: Composite) 패턴**](#복합체컴포지트-composite-패턴)

<br>

---

### **데코레이터(Decorator) 패턴**

<br>

---
### **프록시(proxy) 패턴**

<br>

---
### **어댑터(Adapter) 패턴**

<br>

---
### **파사드(Facade) 패턴**

라이브러리의 각 클래스와 메서드들이 어떤 목적의 동작인지 이해하기 어려워 바로 가져다 쓰기에는 난이도가 높을때, 이에 대한 적절한 네이밍과 정리를 통해 사용자로 하여금 **쉽게 라이브러리를 다룰수 있도록 인터페이스(API)를 구성하기 위한 패턴**이다.

> Facade 뜻: 건축물의 정면
> 
> 건축물의 정면은 보통 건축물의 이미지와 건축 의도를 나타내는데, 건축물 정면만 봐도 이 건물이 어떤 목적을 하는지 단번에 알수 있다는 특징을 차용하여 명명 지은 것
> 
> _출처: [GOF-💠-퍼사드Facade-패턴-제대로-배워보자](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%ED%8D%BC%EC%82%AC%EB%93%9CFacade-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)_

<br>

**파사드 패턴 구조**
![파사드 패턴 구조](/images/dayoung/facade-pattern-structure.png)

+ Facade - 서브시스템 기능을 편리하게 사용할 수 있도록 복잡한 로직을 재정리해놓은 인터페이스
+ SubSystem - 라이브러리 or 클래스
+ Client - 서브시스템을 직접 접근하지 않고 Facade를 사용

**파사드 패턴 특징**
+ 다른 패턴과 다르게 정형화된 구조가 없음
+ 적절하기 기능을 집약화해주는 객체가 있는 경우, 해당 객체가 파사드 클래스가 됨
+ 클라이언트가 복잡한 것을 의식하지 않도록 해줌
+ 필요에 의해 언제든 파사드를 재귀적으로 구성하여 의존할 수 있음

**파사드 패턴 장점**
+ 복잡하지 않은 API를 제공하여, 외부에서 시스템을 사용하기 쉬움
+ 하위 시스템 간의 의존 관계가 많을 경우, 이를 감소시키고 의존성을 한 곳에 모을 수 있음
+ 클라이언트의 변경 없이 서브 시스템을 변경할 수 있음

**파사드 패턴 단점**
+ 파사드가 모든 클래스에 결합될 수 있음
+ 파사드 클래스 자체가 하위 시스템에 대한 의존성을 가지게 됨
+ 유지보수 측면에서 공수가 더 많이 듦

> **책과 구글링 내용이 다른 부분**
>
> 책에서는 파사드 패턴을 통해 서브 시스템을 캡슐화 한다고 하지만, 구글링 결과에서는 파사드 패턴을 구현해도 클라이언트가 서브 시스템에 직접 접근하는 것을 제한할 수 없기 때문에 추상화에 가깝다고 함.

<br>

---
### **복합체(컴포지트: Composite) 패턴**

**전체-부분 관계를 갖는 객체들을 동일 인터페이스로 구현하여, 클라이언트에서 동일한 컴포넌트로 취급**하게 하는 패턴이다. 리눅스의 파일구조시스템에서 폴더와 파일은 다른 객체지만, 모두 `File`로 취급되는 것과 비슷하다고 보면 된다.

**복합체 패턴 구조**
![복팝체 패턴 구조](/images/dayoung/composite-pattern-structure.png)

+ Component - Leaf와 Composite의 공통 상위 인터페이스
+ Composite
  + Component 구현체들을 내부 리스트로 관리
  + `operation()` 호출 시, 내부의 Component들에게 실행 위임
+ Leaf - 단일 객체로 단순한 내용물 표시

```java
interface Component{
    void operation();
}
```
```java
class Leaf implements Component {
    @Override
    public void operation() {
        System.out.println(this + "호출");
    }
}
```
```java
class Composite implements Component {
    private List<Component> componentList = new ArrayList();

    @Override
    public void operation() {
        System.out.println(this + "호출");

        for(Component component : componentList) {
            component.operation();
        }
    }

    public void add(Component component) {
        componentList.add(component);
    }

    public void remove(Component component) {
        if(componentList.contains(component)) {
            componentList.remove(component);
        }
    }

    public List<Component> getChild() {
        return componentList;
    }
}
```

**복합체 패턴 장점**
+ 다형성 재귀를 통해 트리 구조를 편리하게 구성 가능
+ 수평적, 수직적 모든 방향으로 객체 확장 가능
+ 새로운 Leaf 클래스가 추가될 때, 단일 객체만 확장 가능하여 개방-폐쇄 원칙 준수

**복합체 패턴 단점**
+ 트리의 깊이(depth)가 늘어날수록 디버깅 어려움(재귀호출)
+ 복합 객체와 단일 객체를 동일 인터페이스로 처리해야 하기 때문에, 복합객체에서 구성요소에 제약을 걸기 어려움
  + 복합 객체가 가질 수 있는 단일 객체를 제한해야 할 때
  + 수평적으로만 확장을 제한해야 할 때