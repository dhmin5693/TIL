# Object 2장[객체지향 프로그래밍] 정리

## 객체지향에 대한 생각

일반적인 생각의 흐름은 아래처럼 이루어진다.

1. 개발하고자 하는 기능에 어떤 **클래스**가 필요한지 먼저 결정한다.
2. 클래스에 어떤 필드와 메소드가 필요한지를 생각한다.

그러나 객체지향은 클래스가 아니라 `객체` 에 집중해야 한다.

## 도메인과 객체지향

> 도메인은 사용자가 프로그램을 사용하는 **분야**를 말한다.

객체지향 패러다임이 강력한 이유는 도메인을 구성하는 개념을 클래스로 매끄럽게 연결시킬 수 있기 때문이다.

## 클래스 구현

도메인 개념을 구현하는 적절한 클래스를 설계했다면 캡슐화에 대해서 고려해보아야 한다.

- 클래스의 경계를 적절하게 나누었는가?
- 외부에 공개할 부분과 내부에서만 사용할 부분을 명확하게 구분해야 한다.
- 명확한 경계는 객체의 자율성을 보장한다.
- 객체의 자율성은 프로그래머의 자유를 보장한다. 모든 요소를 외부에 공개하면 외부의 간섭이 많아져서 자유롭게 수정을 하기 어렵다.

## 객체를 조금 더 의미있게

금액은 `long money;` 처럼 변수로도 표시할 수 있으나, `Money` 클래스를 사용하면 조금 더 의미있게 사용할 수 있다.

```java
public class Money {
    
    private final long amount;

    private Money(long amount) {
        this.amount = amount;
    }

    public static Money of(long amount) {
        return new Amount(amount);
    }

    public Money plus(long amount) {
        return new Money(this.amount + amount);
    }

    // ...
}
```

- `Money` 는 불변 클래스로, plus 등의 동작을 수행하면 `amount` 값을 변경하는게 아니라 새로운 `Money` 를 생성한다.
- `plus` 와 같은 메소드는 금액을 받아서 `Money` 와 상호작용하는 `메시지` 이다.
    - 데이터를 불러와서 구현하는 것보다 메시지로 처리하면 객체의 자율성을 높일 수 있다.

    ```java
    // 데이터를 전달하고 구현을 외부에 하는 방식
    Money m = new Money(money.getAmount() + amount);

    // 메시지를 보내어 내부 구현은 외부에서 알 수 없음
    Money m = money.plus(amount);
    ```

- `Money` 를 클래스로 만들면 돈에 관련된 도메인 코드를 작성하여 의미를 더 풍부하게 만들어줄 수 있다.
- 내부 구현은 최대한 감추고 public 인터페이스를 통해 접근할 수 있도록 해야 한다.

## 상속과 다형성을 활용한 유연한 클래스

상속과 다형성을 적절하게 활용하면 코드 간 의존성을 유연하게 조절할 수 있다.

```java
public class Product {
    private Money price;
    private DiscountPolicy discountPolicy;

    public long getSellingPrice() {
        return discountPolicy.discount(price);
    }
}
```

`DiscountPolicy` 는 할인 정책을 결정할 인터페이스로, `getSellingPrice` 가 실행되기 전에 주입되지 않으면 정상적으로 작동되지 않을 것이다.

할인 정책으로 `AmountDiscountPolicy` 혹은 `RateDiscountPolicy` 를 주입해줄 수 있다고 가정하자.

이 클래스는 코드 작성 시의 의존성과 코드 실행 시의 의존성이 다르며,

인터페이스의 특성 상 기능 동작을 위해 메시지를 전달하는 것이 강제된다.

```
- 코드 작성 시: Product → DiscountPolicy
- 런타임: Product → AmountDiscountPolicy / RateDiscountPolicy
```

코드 사이의 의존성을 유연하게 만들어줄 수 있는 설계이지만 디버깅은 조금 어려워질 수 있다.

유연성과 개발편의성 사이에서 발생하는 `trade-off` 이니 적정선을 잘 선택해야 한다.

## 상속과 합성

> 합성은 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함하여 재사용하는 방식이다.

`Money` 에서 `DiscountPolicy` 를 활용하던 방식이 바로 합성이다.

최종 할인가를 구하는 기능을 아래처럼 구현할 수도 있다.

- `Product` 를 최상단 클래스로 사용한다.
- 합성이 아니라 상속을 활용하는 방법이므로 `DiscountPolicy` 를 제거한다.
- `Product`를  상속받는 `RateDiscountProduct` 를 만들고 `getSellingPrice` 를 오버라이드한다.

그러나 이 방법은 설계가 유연해지지 못하기 때문에 좋은 설계가 아니다.

상속하여 새로 재정의한다는 것은 부모 클래스의 메소드를 내부 구조를 잘 알고 있어야 하기 때문이다.

`캡슐화`가 약해지며 부모 클래스와 강하게 결합되기 때문에 수정하기도 쉽지 않다.