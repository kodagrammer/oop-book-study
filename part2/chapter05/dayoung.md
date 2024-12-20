### **Chapter 5. SOLID 원칙**

#### **SOLID 원칙**
+ 단일 책임 원칙 (SRP: Single responsibility principle)
+ 개방-폐쇄 원칙 (OCP: Open-closed principle)
+ 리스코프 치환 원칙 (LSP: Liskov substitution principle)
+ 인터페이스 분리 원칙 (ISP: Interface segregation principle)
+ 의존 역전 원칙 (DIP: Dependency inversion principle)

<br>

---

#### **1. 단일 책임 원칙**
**"클래스는 단 한 개의 책임을 갖는다."** 
단일 책임 원칙 준수 유무를 판단하는 가장 큰 척도는, '기능 변경이 생겼을 때의 파급 효과가 적은가' 이다. 하나의 객체에서 다양한 기능(책임)을 갖게 되면 불필요한 의존관계가 형성된다.

> !Tip
>
> 더 와닿기 쉽게 예를 들자면, 청소기 클래스는 청소 메소드만 잘 구현하면 되지 화분에 물을 주고 드라이 까지 할 책임은 없다.
> 
> 물론 다재다능한 청소기는 좋아보이지만, 만일 청소기가 고장나면 다른 기능까지도 사용을 못해지기 때문이다.
> 즉, 청소기는 청소만 잘하면 된다는 책임만 가지면 된다. 쉽게 말해 하나의 클래스로 너무 많은 일을 하지 말고 딱 한 가지 책임만 수행하라는 뜻이다.
>
> _출처: https://inpa.tistory.com/entry/OOP-💠-아주-쉽게-이해하는-SRP-단일-책임-원칙 [Inpa Dev 👨‍💻:티스토리]_

<br>

예제

+ 단일 책임 원칙이 위배된 구현
    ```java
    public class DataViewer{
        public void display() {
            // 데이터를 문자열로 읽어와서
            String data = loadHtml();
            // GUI를 갱신한다
            updateGui(data);
        }

        public String loadHtml(){
            HttpClient client = new HttpClient();
            client.connect(url);
            return client.getResponse();
        }

        private void updateGui(String data){
            GuiData guiModel = parseDataToGuiData(data);
            tableUI.changeData(guiModel);
        }
        private GuiData parseDataToGuiData(String data) {
            // 파싱 처리 코드
        }
    }
    ```

    `DataViewer` 클래스는 '데이터를 읽어오기'와 'GUI데이터로 파싱하여 데이터를 변경'이라는 두 가지 책임을 갖는다. 이 때, 제공되는 데이터가 HTML이 아니라 Socket으로 받아오는 방식으로 변경된다면, `display()` 메소드 뿐만 아니라 `updateGui(String data)` 메소드가 받는 파라미터 타입 역시 변경되어야 한다. 데이터 읽기의 기능(책임)이 변경됐는데, GUI 데이터로 보여주는 기능까지 변경되는 불상사가 일어난다.

+ 단일 책임 원칙을 지킨 구현
    ```java
    // 어떤 수단으로 오든 표기해야하는 데이터로 변환하도록 추상화
    public interface DataLoader {
        Data load();
    }

    public class DataViewer{
        public void display() {
            // 데이터를 어떻게 받아오든 DataViewer는 변경이 없음
            Data data = dataLoader.load();
            updateGui(data);
        }

        private void updateGui(Data data){
            GuiData guiModel = parseDataToGuiData(data);
            tableUI.changeData(guiModel);
        }
    }
    ```

    위 코드에서 더 나아가서, Viewer 환경이 변경되는 것까지 고려하여 `DataViewer`를 추상화하여 환경에 따른 `display()` 를 구현하도록 할 수 있다.


<br>

---
#### **2. 개방 폐쇄 원칙**

**"확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다."** 풀어서 이야기하면, 기존 코드를 수정하지 않으면서 기능을 추가할 수 있도록 설계해야한다는 의미이다. 개방 폐쇄 원칙은 유연함과 관련된 원칙이며, 지켜지지 않는다는 것은 변화되는 부분에 대한 추상화가 충분하게 이뤄지지 않았다는 것이다.

개방 폐쇄 원칙을 어기는 코드의 전형적인 특징은 "다운 캐스팅"을 하거나 비슷한 if-else문이 반복되는 경우이다. 

```java
public void drawCharacter(Character character) {
    if(character instanceof Missile) {
        Missile missile = (Missile) character;
        mssile.drawSpecific();
    } else {
        character.draw();   
    }
}
```

위와 같은 경우, `Missile`인 경우 별도 처리를 해주는 것이 아니라 `drawSpecific()`을 알맞게 추상화하여 모든 Character 타입에 추가해주어야 한다.

<br>

---
#### **3. 리스코프 치환 원칙**

리스코프 치환 원칙은 "**상속과 다형성에 관련하여 계약과 확장에 대한 규칙**"이다. 상위 타입을 사용하는 객체 입장에서 하위 타입으로 변경이 있다하더라도 기능이 이전과 동일하게 작동해야 한다. 즉, 부모와 자식 클래스 사이에 행위의 일관성이 있어야 한다는 뜻이다. 리스코프 치환 원칙을 위반하게 되면, 다형성이 깨지게 되어 개방 폐쇄 원칙도 지킬 수 없게 된다.


예제
```java
public class Cupon {
    private double discountRate;
    public int calcDiscAmt(Item item){
        if(item instanceof SpecialItem){  // 개방 폐쇄 원칙 위배
            return 0;
        }
        return item.getPrice() * discountRate;
    }
}
```
개선 코드
```java
public class Item {
    public boolean canDiscount() { return true; }
}

public class SpecialItem extends Item {
    @Override
    public boolean canDiscount() { return false; }
}

public class Cupon {
    private double discountRate;
    public int calcDiscAmt(Item item){
        if(!item.canDiscount()){
            return 0;
        }
        return item.getPrice() * discountRate;
    }
}
```

추가 참고 자료 추천
+ [인파님의 블로그](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EC%95%84%EC%A3%BC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94-LSP-%EB%A6%AC%EC%8A%A4%EC%BD%94%ED%94%84-%EC%B9%98%ED%99%98-%EC%9B%90%EC%B9%99)

<br>

---
#### **4. 인터페이스 분리 원칙**

**"인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다."** 즉, 인터페이스 분리 원칙이란 인터페이스를 잘게 분리함으로써, 클라이언트의 목적과 용도에 적합한 인터페이스 만을 제공하는 것이다. 범위가 크게 분리된 인터페이스의 경우, 해당 인터페이스를 상속받는 클래스에서는 사용하지 않는 기능임에도 불필요하게 구현해야하는 경우가 생긴다.

주의해야 할 점은 한번 인터페이스를 분리하여 구성해놓고 다시 한 번 인터페이스들을 분리하지 말아야 한다. 이미 구현되어 있는 프로젝트에 또 인터페이스들을 분리한다면, 이미 구현된 프로그램에서 분리에 의한 영향도가 크고 문제가 발생할 확률이 높기 때문이다.


> Tip!
> 
> 인터페이스 분리 원칙은 마치 단일 책임 원칙과 비슷하게 보이는데, SRP 원칙이 클래스의 단일 책임을 강조한다면, ISP는 **인터페이스의 단일 책임**을 강조한다고 말할 수 있다. 
> 
>다만 유의할 점은 인터페이스는 클래스와 다르게 추상화이기 때문에 여러개의 역할을 가지는데 있어 제약이 없긴 하다.
즉, SRP 원칙의 목표는 클래스 분리를 통하여 이루어진다면, ISP 원칙은 인터페이스 분리를 통하여 이루어 진다고 볼 수 있다.
>
>또한 SRP 원칙의 클래스 책임의 범위에 대해 분리 기준이 다르듯이, 인터페이스를 분리하는 기준은 상황에 따라 다르다.
핵심은 관련 있는 **기능끼리 하나의 인터페이스에 모으되 지나치게 커지지 않도록 크기를 제한**하라는 점이다.
>
> _출처: https://inpa.tistory.com/entry/OOP-💠-아주-쉽게-이해하는-ISP-인터페이스-분리-원칙 [Inpa Dev 👨‍💻:티스토리]_

<br>

---
#### **5. 의존 역전 원칙**

**"고수준 모듈은 저수준 모듈의 구현에 의존해서는 안된다."** 이는 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 하기 위함이다.  상위클래스, 인터페이스, 추상클래스와 같은 고수준 모듈일수록 변하지 않을 가능성이 크기 때문에 변화가 많은 저수준 모듈(하위클래스, 구현체)을 직접 의존하지 말아야 한다.

예제
+ 위존 역전 원칙 위반한 구현
  ```java
  public interface Airline {
    String getAir2Code();
  }
  public class KoreanAir implements Airline {}
  public class Asiana implements Airline {}

  public class Flight{
    KoreanAir airline; // 저수준모듈
    
    public Airline getAirline() { return airline; }
    public String getFlightNumber() { }
  }
  ```
  이렇게 구현할 일은 없겠지만, 항공편 클래스에 항공사의 타입을 대한항공이라는 저수준 모듈을 의존하게 해두면, 항공편의 항공사가 변할 때 마다 Fight 클래스는 변경이 일어나게 된다.