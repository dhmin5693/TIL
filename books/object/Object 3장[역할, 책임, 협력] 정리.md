# Object 3장[역할, 책임, 협력] 정리

## 객체지향의 핵심

- 역할
    - 협력 안에서 수행하는 책임들이 모여 객세가 수행하는 역할을 구성한다.
- 책임
    - 객체가 협력에 참여하기 위해 수행하는 로직을 책임이라고 부른다.
- 협력
    - 객체가 애플리케이션의 기능을 구현하기 위해 수행하는 상호작용을 협력이라고 한다.

## 협력

객체는 고립된 존재가 아니다. 다른 객체와 협력하는 사회적 존재이다.

협력을 조금 더 확실하게 표현하자면 `한 객체가 다른 객체에게 무엇인가를 요청하는 것` 이다.

객체 사이 협력은 `메시지 전송`을 통해 이루어진다.

```java
public class Screening {

    private Movie movie;

    public int getFee() {
        return movie.calculateMovieFee(this);
    }    
}
```

`Screening` 은 `Movie` 에게 요금을 계산해달라는 메시지를 전송하여 협력을 요청한다.

`Movie` 에게 요금 계산의 처리를 위임하지 않는다면 `Screening` 이 요금과 관련된 데이터를 갖고 있어야 수행할 수 있다.

## 책임

책임은 `하는 것` 과 `아는 것` 으로 분류할 수 있다.

객체지향 개발에서 가장 중요한 능력은 `책임을 능숙하게 객체에 할당하는 것`이다.

### 하는 것

- 객체 생성
- 계산 수행
- 다른 객체의 행동을 시작시킴
- 다른 객체의 활동 제어 및 조절

### 아는 것

- 사적인 정보에 관해 아는 것
- 관련 객체의 정보를 아는 것
- 자신이 유도하거나 계산할 수 있는 것에 관해 아는 것

### 예제에서의 책임

`Screening` 에서 책임을 분류해보자.

- Screening
    - 영화 예매(하는 것)
- Movie
    - 요금 계산(하는 것)
        - 요금 계산 정책(아는 것)

### 책임 주도 설계(Responsibility-Driven Design)

- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다.
- 시스템 책임을 더 작은 책임으로 분할한다.
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다.
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다.
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 한다.

## 역할

역할을 모델링할 때에는 메시지를 전달하는 순서대로 진행해보자.

1. 영화를 예매한다.
    - `Screening`
2. 예매한 영화표의 가격을 계산한다.
    - `Movie`

### 유연하고 재사용 가능한 협력

역할을 통해 유연하고 재사용 가능한 협력을 얻을 수 있다.

`Movie` 는 가격을 계산한다.

가격은 쿠폰에 따라서 정률 혹은 정액 할인을 적용하여 계산한다.

```java
1. Movie -> AmountDiscount
2. Movie -> RateDiscount
3. Movie -> NoneDiscount
```

두 가지를 모두 한 객체에 우겨넣으면 코드가 조건문으로 난잡해질 가능성이 매우 높아진다.

또한 코드 중복에 대한 우려도 존재할 수 있다.

역할을 객체가 아니라 `책임` 에 초점을 맞추어서 다시 설계해보자.

```java
Movie -> Discount (인터페이스 혹은 추상 클래스)
Discount의 구현체는 AmountDiscount, RateDiscount, NoneDiscount가 있다.
```

원래 가격 할인을 담당하던 클래스를 포괄적으로 표현할 수 있으면서 추상적인 이름으로 정해야 한다.

### 추상화

추상화를 이용하면 세부적인 구현 로직에 억눌리지 않고 상위 수준의 정책을 쉽고 간단하게 표현할 수 있다.

`Discount` 는 실제 할인 적용 여부가 어떻게 될 지는 전혀 모르는 추상화 객체이다. `Discount::discount` 할인을 적용하는 메소드로 적용된 정책에 따라 적절하게 할인 여부를 결정할 것이다.

실제 구현체가 무엇이든 상관없이 상위 수준에서는 `Discount::discount` 메소드를 활용하기만 하면 된다.

`Discount` 는 구현체가 가진 역할을 하나로 축소하여 표현한 객체인 셈이다.
