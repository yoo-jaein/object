# Chapter 06 메시지와 인터페이스

## 클라이언트-서버 모델(client-server model)
협력 안에서 메시지를 전송하는 객체를 **클라이언트**, 메시지를 수신하는 객체를 **서버**라고 부른다. 협력은 클라이언트가 서버의 서비스를 요청하는 단방향 상호작용이다.

## 메시지(message)
객체가 다른 객체와 협력하기 위해 사용하는 의사소통 매커니즘. 일반적으로 객체의 오퍼레이션이 실행되도록 요청하는 것을 메시지 전송이라 부른다.

## 오퍼레이션(operation)
객체가 다른 객체에게 제공하는 추상적인 서비스다. 오퍼레이션은 메시지를 수신하는 객체의 인터페이스를 강조한다. 메시지 전송자는 고려하지 않은 채 메시지 수신자의 관점만을 다룬다.

## 메서드(method)
메시지에 응답하기 위해 실행되는 코드 블록을 메서드라고 부른다. 메서드는 오퍼레이션의 구현이다. 동일한 오퍼레이션이라고 해도 메서드는 다를 수 있다. 이는 다형성의 개념과 연결된다.

## 퍼블릭 인터페이스(public interface)
객체가 협력에 참여하기 위해 외부에서 수신할 수 있는 메시지의 묶음. 클래스의 퍼블릭 메서드들의 집합이나 메시지의 집합을 가리키는 데 사용된다. **객체를 설계할 때 가장 중요한 것은 훌륭한 퍼블릭 인터페이스를 설계하는 것이다.**

## 시그니처(signature)
시그니처는 오퍼레이션 또는 메서드의 이름과 파라미터 목록을 합쳐서 정의한 것이다.

## 좋은 퍼블릭 인터페이스 설계하기

### 디미터 법칙
- 낯선 자에게 말하지 말라
- 오직 인접한 이웃하고만 말하라
- 오직 하나의 도트만 사용하라

협력의 경로를 제한하면 결합도를 효과적으로 낮출 수 있다.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Movie movie = screening.getMovie();
        //...
        if (condition.getType() == DiscountConditionType.PERIOD) {
            discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                    condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                    condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
        //...
    }
}
```
이 코드의 가장 큰 단점은 Screening과 ReservationAgency 사이의 결합도가 너무 높다는 것이다. 결합도가 너무 높기 때문에 Screening의 내부 구현을 변경할 때마다 ReservationAgency도 함께 변경된다. 이 문제의 원인은 ReservationAgency가 Screening뿐만 아니라 Movie, DiscountCondition에도 직접 접근하기 때문이다. 디미터 법칙을 적용한 코드는 다음과 같다.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Money fee = screening.calculateFee(audienceCount);
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

ReservationAgency가 Screening의 내부 구조에 결합돼 있지 않기 때문에 함께 변경될 필요가 없다. 클라이언트와 서버 사이에 낮은 결합도를 유지할 수 있게 되었다.

### 묻지 말고 시켜라

앞에서 ReservationAgency는 Screening에게 직접 요금을 계산하도록 요청했다. 이처럼 객체의 상태에 관해 묻지 않고 원하는 것을 시키는 스타일을 묻지 말고 시켜라 원칙이라고 한다.

### 의도를 드러내는 인터페이스

```java
public class PeriodCondition {
    public boolean isSatisfiedByPeriod(Screening screening) {...}
}

public class SequenceCondition {
    public boolean isSatisfiedBySequence(Screening screening) {...}
}
```

위의 스타일은 두 가지 관점에서 좋지 않다.
1. 메서드에 대해 제대로 커뮤니케이션하지 못한다. 클라이언트의 관점에서 isSatisfiedByPeriod와 isSatisfiedBySequence 모두 할인 조건을 판단하는 동일한 작업을 수행한다. 하지만 메서드의 이름이 다르기 때문에 두 메서드의 내부 구현을 정확하게 이해하지 못한다면 두 메서드가 동일한 작업을 수행한다는 사실을 알아채기 어렵다.
2. 더 큰 문제는 메서드 수준에서 캡슐화를 위반한다는 것이다. 이 메서드들은 클라이언트로 하여금 협력하는 객체의 종류를 알도록 강요한다. 

다음은 개선된 코드이다.

```java
public interface DiscountCondition {
    boolean isSatisfiedBy(Screening screening);
}

public class PeriodCondition implements DiscountCondition {
    public boolean isSatisfiedBy(Screening screening) {...}
}

public class SequenceCondition implements DiscountCondition {
    public boolean isSatisfiedBy(Screening screening) {...}
}
```

각 메서드의 이름을 변경하여 메서드가 무엇을 하는지 드러냈다. 또한, 클라이언트가 두 메서드를 가진 객체를 동일한 타입으로 간주할 수 있도록 인터페이스를 정의하고 이 인터페이스에 isSatisfiedBy 오퍼레이션을 정의했다.

### 명령-쿼리 분리

#### 프로시저(procedure)
- 객체의 상태를 수정하는 오퍼레이션
- 명령(command)
- 부수효과를 발생시킬 수 있지만 값을 반환할 수 없다.

#### 함수(function)
- 객체와 관련된 정보를 반환하는 오퍼레이션
- 쿼리(query)
- 값을 반환할 수 있지만 부수효과를 발생시킬 수 없다.

명령-쿼리 분리 원칙을 한 문장으로 표현하면 "질문이 답변을 수정해서는 안 된다"는 것이다. 명령과 쿼리를 분리하면 코드를 예측하고 이해하기 쉬워지고 디버깅과 유지보수가 수월해진다.

## 결론
1. 디미터 법칙, 묻지 말고 시켜라 스타일, 의도를 드러내는 인터페이스, 명령-쿼리 분리 원칙을 지키자.