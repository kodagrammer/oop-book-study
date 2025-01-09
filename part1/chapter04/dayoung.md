### **Chapter 4. 재사용: 상속보단 조립**
<br>

#### **1. 재사용과 조립**

**◼ 상속의 단점**
+ 하위클래스가 많아질수록 상위클래스 변경 시 영향도가 큼
+ 다중 상속은 불가하기 때문에 유사한 기능 확장을 위해 불필요하게 클래스의 수가 증가함
+ 최초 개발 의도와 다르게 상속된 기능을 잘못 사용하여 기능이 제대로 작동하지 않을 수 있음

<br>

**◼ 객체 조립과 위임**

여러 객체를 묶어서 복잡한 기능을 제공하는 객체를 생성하는 것으로, 보통 필드에서 다른 객체를 참조하여 다를 객체의 기능을 사용하도록 하는 것이다.

```java
public class FlowController {
    // 필드로 조립
    private Encryptor encryptor = new Encryptor();

    public void process() {
        ...
        byte[] encryptedData = encryptor.encrypt(data);
    }
}
```

위임은 일반적으로 객체 조립을 통해 구현하는데, 다른 객체에게 할 일을 그대로 넘기는 것을 의미한다.

``` java
public abstract class Figure {
    private Bounds bounds = new Bounds();
 
    public boolean contains(Point point) {
        return bounds.contians(point.getX(), point.getY());
    }
}
```

<br>

**◼ 개인적인 의견**

저자는 유연한 설계를 강조하기 위해 잘못 사용된 상속을 강조하고 있는데, 자칫하면 "상속을 지양하자."라고 잘못 이해될 우려가 있다. 기능 확장을 위해 무분별한 상속을 지양하는 것과 상속 자체를 지양하는 것은 전혀 다른 이야기이다.

이 책 뿐만 아니라 상속과 인터페이스를 설명하기 위해 잘못된 상속 사용 방식에 중점을 두는 경우가 많다. 아마 OOP의 열풍이 불 때, 무분별한 상속의 사용으로 변경에 어려움이 많았던 실제 사례가 많았기 때문이라고 생각한다.

하지만 상속과 인터페이스는 상호보완적인 관계이며, 적절하게 섞어 사용해야 한다. 잘못된 이해로 무분별하게 상속이 필요한 곳에도 인터페이스나 객체조립을 사용하게 되면, 복잡한 구조로 인해 사람이 이해하지 못하고 잘못된 수정을 할 가능성이 있다.

 따라서 상속을 통해 코드의 중복을 줄여 재사용성을 높이고, 책임분리와 객체조립으로 응집도를 높이고 결합도를 낮출 수 있도록 적절한 구현이 필요하다.

  ---

  추가로 읽어보면 좋을 내용 기록
  + [상속을 자제하고 합성(Composition)을 이용하자](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5%EC%9D%98-%EC%83%81%EC%86%8D-%EB%AC%B8%EC%A0%9C%EC%A0%90%EA%B3%BC-%ED%95%A9%EC%84%B1Composition-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)