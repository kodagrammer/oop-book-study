## **생성 패턴(Creational Pattern)**

- [**생성 패턴(Creational Pattern)**](#생성-패턴creational-pattern)
  - [**싱글톤(Singleton) 패턴**](#싱글톤singleton-패턴)
  - [**팩토리 메서드(Factory Method) 패턴**](#팩토리-메서드factory-method-패턴)
  - [**추상 팩토리(Abstract Factory) 패턴**](#추상-팩토리abstract-factory-패턴)

<br>

---
### **싱글톤(Singleton) 패턴**

<br>

---
### **팩토리 메서드(Factory Method) 패턴**

팩토리 메서드 패턴은 **객체 생성 로직을 팩토리 클래스로 캡슐화하여 클라이언트에서 `new` 연산사로 직접 객체를 생성하지 않고 팩토리 클래스를 통해 객체를 생성**하도록 하는 패턴이다. 또한 객체 생성에 필요한 과정을 템플릿처럼 미리 구성해두고 객체 생성 전 또는 후처리를 통해 객체를 유연하게 정할 수 있게 한다.

**팩토리 메서드 패턴 구조**
![팩토리 메서드 패턴 구조](/images/dayoung/factory-method-pattern-structure.png)

+ AbstractFactory - 최상위 공장
  + createOperation() : 객체생성에 대한 전처리, 후처리를 템플릿화한 메서드
  + createProuct() : 서브 팩토리 클래스에서 재정의할 객체 생성 추상메서드
+ ConcreteFactory - 제품 하나당 공장 객체 구현, 객체 생성 메서드 재정의
+ Product - 제품 구현체를 추상화
+ ConcreteProduct - 제품 구현체

```java
interface IProduct {
    void setting();
}

class ProudctA implements IProduct {
    @Override
    public void setting(){}
}
class ProudctB implements IProduct {
    @Override
    public void setting(){}
}
```
```java
abstract class AbstractFactory {
    // 객체 생성 전처리 후처리 메소드 (final로 오버라이딩 방지, 템플릿화)
    final IProduct createOperation() {
        IProduct product = createProduct(); 
        // 제품 생성 후, 후처리
        product.setting();
        return product;
    }

    abstract protected IProduct createProduct();
}

class ConcreteFactoryA extends AbstractFactory {
    @Override
    public IProduct createProduct() {
        return new ProductA();
    }
}

class ConcreteFactoryB extends AbstractFactory {
    @Override
    public IProduct createProduct() {
        return new ProductB();
    }
}
```

**팩토리 메서드 패턴 장점**
+ 생성자와 구현 객체의 강한 결합을 방지
+ 객체 생성 전, 후에 필요한 작업을 템플릿화 하여 코드 중복 감소
+ 캡슐화, 추상화를 통해 객체의 구체적인 타입을 감출 수 있음

**팩토리 메서드 패턴 단점**
+ 각 제품 구현체마다 팩토리 객체를 모두 구현해주어야 하여, 관리할 클래스 수 및 코드 복잡성 증가

<br>

---
### **추상 팩토리(Abstract Factory) 패턴**

추상 팩토리 패턴은 **제품'군' 집합을 타입 별로 구현화**하는 생성 패턴이다. 대표적으로 Spring에서 제공하는 `BeanFactory`가 대표적인 추상 팩토리 객체이다.

**추상 팩토리 구조 및 구현**
![추상 팩토리 구조](/images/dayoung/abstract-factory-patter-structure.png)

+ AbstractFactory - 최상위 공장 클래스. 여러 제품을 생성하는 공장들의 메서드를 추상화
+ ConcreteFactory - 제품 타입에 맞게 객체를 생성하도록 메서드를 재정의
+ AbstractProduct - 각 타입의 제품을 추상화
+ ConcreteProduct - 각 타입 제품의 구현체
+ Client - 구체적인 공장, 제품에 대해서는 알지 못함

```java
// Product A 제품군
interface AbstractProductA {}

class ConcreteProductA1 implements AbstractProductA {}
class ConcreteProductA2 implements AbstractProductA {}

// Product B 제품군
interface AbstractProductB {}

class ConcreteProductB1 implements AbstractProductB {}
class ConcreteProductB2 implements AbstractProductB {}
```
```java
// 최상위 공장
interface AbstractFactory {
    AbstractProductA createProductA();
    AbstractProductB createProductB();
}

// Product A1와 B1 제품군을 생산하는 공장군 1 
class ConcreteFactory1 implements AbstractFactory {
    public AbstractProductA createProductA() {
        return new ConcreteProductA1();
    }
    public AbstractProductB createProductB() {
        return new ConcreteProductB1();
    }
}

// Product A2와 B2 제품군을 생산하는 공장군 2
class ConcreteFactory2 implements AbstractFactory {
    public AbstractProductA createProductA() {
        return new ConcreteProductA2();
    }
    public AbstractProductB createProductB() {
        return new ConcreteProductB2();
    }
}
```
```java
class Client {
public static void main(String[] args) {
    	AbstractFactory factory = null;
        
        factory = new ConcreteFactory1();
        AbstractProductA product_A1 = factory.createProductA();
        System.out.println(product_A1.getClass().getName()); // ConcreteProductA1

        
        factory = new ConcreteFactory2();
        AbstractProductA product_A2 = factory.createProductA();
        System.out.println(product_A2.getClass().getName()); // ConcreteProductA2
    }
}
```

**추상 팩토리 패턴 장점**
+ 객체를 생성하는 코드를 분리하여 클라이언트와의 결합도를 낮출 수 있음
+ 추상화를 통해 제품군을 쉽게 대체할 수 있음

**추상 팩토리 패턴 단점**
+ 팩토리 패턴의 공통적인 문제점으로, 각 구현체마다 팩토리 객체를 구현해주어야 하기 때문에 클래스가 증가하여 코드 복잡성이 증가
+ 추상 팩토리의 세부사항이 변경되면 모든 팩토리에 대한 변경 필요
+ 새로운 제품 추가 시, 팩토리 구현 로직 자체를 변경해야 함

<br>

**추상 팩토리 패턴 vs 팩토리 메서드 패턴** 

추상 팩토리 패턴을 팩토리 메서드 패턴의 상위 호환으로 헷갈릴 수 있는데, 두 패턴은 명백히 다른 패턴이다. 팩토리 메서드 패턴은 객체 생성 이후 해야 할 일의 공통점을 정의하는데 초점을 맞추는 반면, 추상 팩토리 패턴은 생성해야 할 객체 집합 군의 공통점에 초점을 맞춘다.

두 패턴을 조합한 복합 패턴을 구현하면, 여러 타입의 객체 군을 생성하면서 동시에 템플릿 메서드를 통해 전처리, 후처리 작업을 해주는 것이 가능해진다.

**공통점**
+ 객체 생성 과정을 추상화한 인터페이스 제공
+ 객체 생성을 캡슐화함으로써 구체적인 타입을 감추고 느슨한 결합 구조를 가짐

**차이점**

| | 팩토리 메서드 패턴 | 추상 팩토리 패턴|
|:----:|:-----------|:-----------|
|목적|객체 생성 과정을 하위 클래스로 옮기는 것이 목적|관련있는 여러 객체들을 구체적인 클래스에 의존하지 않고 만들 수 있게 하는 것이 목적|
|생성객체|한 팩토리당 한 종류의 객체 생성|한 팩토리에서 서로 연관된 여러 종류의 객체 생성|
|핵심로직|객체 생성에 관한 전처리, 후처리 로직|여러 타입의 객체군 생성 로직|