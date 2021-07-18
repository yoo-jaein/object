# Chapter 02 객체지향 프로그래밍
## 객체지향
객체지향 프로그램을 작성할 때 대부분의 사람들은 먼저 어떤 클래스가 필요한지 고민한다. 안타깝게도 클래스 자체는 객체지향의 본질과는 거리가 멀다. 진정한 객체지향 패러다임으로의 전환은 클래스가 아닌 **객체에 초점을 맞출 때**에만 얻을 수 있다.  

**1. 어떤 객체들이 필요한지 고민하라.**  
**2. 객체를 협력하는 공동체의 일원으로 봐라.**

## 도메인
객체지향 패러다임이 강력한 이유는 요구사항을 분석하는 초기 단계부터 프로그램을 구현하는 마지막 단계까지 **객체라는 동일한 추상화 기법**을 사용할 수 있기 때문이다. 일반적으로 클래스 이름은 대응되는 도메인 개념 이름과 동일하거나 유사하게 지어야 한다. 클래스 사이의 관계도 최대한 도메인 개념 사이에 맺어진 관계와 유사하게 만들어서 프로그램의 구조를 이해하고 예상하기 쉽게 만들어야 한다.  

| 개념           | 클래스                |
|----------------|-----------------------|
| 영화           | Movie                 |
| 상영           | Screening             |
| 할인 정책      | DiscountPolicy        |
| 금액 할인 정책 | AmountDiscountPolicy  |
| 비율 할인 정책 | PercentDiscountPolicy |
| 순번 조건      | SequenceCondition     |
| 기간 조건      | PeriodCondition       |
| 예매           | Reservation           |

이와 같이 클래스의 구조는 도메인의 구조와 유사한 형태를 띠어야 한다.

## 클래스 구현하기
```java
public class Screening {
  private Movie movie; // (1)
  private int sequence;
  private LocalDateTime whenScreened;
  
  public Screening(Movie movie, int sequence, LocalDateTime whenScreened) { // (1)
    this.movie = movie;
    this.sequence = sequence;
    this.whenScreened = whenScreened;
  }
  
  public LocalDateTime getStartTime() {
    return whenScreened;
  }
  
  public boolean isSequence(int sequence) {
    return this.sequence == sequence;
  }
  
  public Money getMovieFee() {
    return movie.getFee();
  }
  //...
}
```

(1) : 인스턴스 변수의 가시성은 private, 메서드의 가시성은 public이다. 

## 클래스의 내부와 외부 구분하기
클래스는 내부와 외부로 구분할 수 있다. 좋은 클래스를 설계하기 위한 핵심은 어떤 부분을 외부에 공개하고 어떤 부분을 감출지를 결정하는 것이다. 위의 코드처럼 객체의 속성은 외부에서 직접 접근할 수 없도록 막고(private) 적절한 public 메서드를 통해서만 내부 상태를 변경할 수 있게 해야 한다.

그렇다면 클래스의 내부와 외부를 구분해야 하는 이유는 무엇일까? 그 이유는 경계의 명확성이 **객체의 자율성**을 보장하기 때문이다.

## 자율적인 객체
> 객체지향의 핵심은 스스로 상태를 관리하고, 판단하고, 행동하는 자율적인 객체들의 공동체를 구성하는 것이다.

객체가 자율적인 존재가 되려면 외부의 간섭을 최소화해야 한다. 외부에서는 객체에게 원하는 것을 요청하고는 객체가 스스로 최선의 방법을 결정할 수 있을 것이라는 점을 믿고 기다려야 한다.  

캡슐화와 접근 제어는 객체를 외부와 내부로 나눈다. 외부에서 접근 가능한 부분은 **퍼블릭 인터페이스**(public interface)이고, 오직 내부에서만 접근 가능한 부분은 **구현**(implementation)이다. 이렇게 인터페이스와 구현의 분리(separation of interface and implementation) 원칙은 훌륭한 객체지향 프로그램을 만들기 위해 따라야 하는 핵심 원칙이다.  

|           | 퍼블릭 인터페이스 | 구현 |
|-----------|-------------------|------|
| private   | X                 | O    |
| protected | X                 | O    |
| public    | O                 | X    |

## 협력하는 객체
```java
public class Screening {
  //...
  public Reservation reserve(Customer customer, int audienceCount) {
    return new Reservation(customer, this, calculateFee(audienceCount), audienceCount); // (1)
  }
  
  private Money calculateFee(int audienceCount) {
    return movie.calculateMovieFee(this).times(audienceCount); // (1) 
  }
}
```

```java
public class Reservation {
  private Customer customer;
  private Screening screening;
  private Money fee;
  private int audienceCount;
  
  public Reservation(Customer customer, Screening screening, Money fee, int audienceCount) { // (1)
    this.customer = customer;
    this.screening = screening;
    this.fee = fee;
    this.audienceCount = audienceCount;
  }
}
```

(1) : 영화를 예매하는 과정에서 Movie의 calculateMovieFee() 메서드를 호출하며, 반환 결과 Money의 times() 메서드를 호출한다. 이렇게 Screening, Movie, Money, Reservation 인스턴스들은 서로의 메서드를 호출하며 상호작용한다. 시스템의 어떤 기능을 구현하기 위해 객체들 사이에 이뤄지는 상호작용을 **협력**(Collaboration)이라고 부른다.  

## 메시지의 전송과 수신
객체가 다른 객체와 상호작용할 수 있는 유일한 방법은 **메시지를 전송**(send a message)하는 것 뿐이다. 다른 객체에게 요청이 도착할 때 해당 객체가 **메시지를 수신**(receive a message)했다고 이야기한다.  

## 메시지와 메서드
위의 코드에서 '메서드를 호출한다'고 말했지만 사실 '메시지를 전송한다'라고 말하는 것이 더 적절한 표현이다. 메시지와 메서드를 구분하는 것은 **다형성**(polymorphism)의 개념에서 매우 중요하다. 외부로부터 받은 메시지를 통해 객체 스스로가 메서드를 선택하기 때문이다.  

## 협력 시작하기
```java
public class Movie {
  private String title;
  private Duration runningTime;
  private Money fee;
  private DiscountPolicy discountPolicy;
  
  public Money calculateMovieFee(Screening screening) {
    return fee.minus(discountPolicy.calculateDiscountAmount(screening)); // (1)
  }
  //...
}
```

(1) : calculateMovieFee() 메서드는 discountPolicy에 calculateDiscountAmount() 메시지를 전송해 할인 요금을 반환받는다. 그런데 이 메서드에는 어떤 할인 정책을 적용할지 결정하는 코드가 없다. 

## 추상 클래스
할인 정책은 금액 할인 정책 AmountDiscountPolicy, 비율 할인 정책 PercentDiscountPolicy로 구분된다. 두 클래스는 대부분의 코드가 유사하고 할인 요금을 계산하는 방식만 조금 다르다. 따라서 두 클래스 사이의 중복 코드를 제거하기 위해 공통 코드를 보관할 장소가 필요하다.  

여기서는 부모 클래스인 DiscountPolicy 안에 중복 코드를 두고 두 클래스가 이 클래스를 상속받게 했다. DiscountPolicy는 **추상 클래스**(abstract class)로 구현했다.  

> **추상 클래스**  
> 1. abstract가 명시된 클래스이다.  
> 2. 구현부가 없는 상태로 선언된 메서드인 추상 메서드를 포함할 수 있다.  
> 3. 상속을 위한 클래스이기 때문에 인스턴스를 생성할 수 없다.  
> 4. 예시 : GraphicObject 추상 클래스를 상속하는 Rectangle, Line, Circle 클래스

```java
public abstract class DiscountPolicy {
  private List<DiscountCondition> conditions = new ArrayList<>();
  
  public DiscountPolicy(DiscountCondition ... conditions) {
    this.conditions = Arrays.asList(conditions);
  }
  
  public Money calculateDiscountAmount(Screening screening) {
    for(DiscountCondition each : conditions) {
      if (each.isSatisfiedBy(screening)) {
        return getDiscountAmount(screening);
      }
    }
    return Money.ZERO;
  }
  
  abstract protected Money getDiscountAmount(Screening screening);
}
```

```java
public class AmountDiscountPolicy extends DiscountPolicy {
  private Money discountAmount;
  
  public AmountDiscountPolicy(Money discountAmount, DiscountCondition ... conditions) {
    super(conditions);
    this.discountAmount = discountAmount;
  }
  
  @Override
  protected Money getDiscountAmount(Screening screening) {
    return discountAmount;
  }
}
```

```java
public class PercentDiscountPolicy extends DiscountPolicy {
  private double percent;
  
  public PercentDiscountPolicy(double percent, DiscountCondition ... conditions) {
    super(conditions);
    this.percent = percent;
  }
  
  @Override
  protected Money getDiscountAmount(Screening screening) {
    return screening.getMovieFee().times(percent);
  }
}
```

이처럼 부모 클래스에 기본적인 기능 구현을 하고 중간에 필요한 처리를 자식 클래스에게 위임하는 디자인 패턴을 **템플릿 메서드 패턴**이라 한다.

## 인터페이스
영화 예매 시스템의 할인 조건은 순번 조건 SequenceCondition, 기간 조건 PeriodCondition으로 구분된다. DiscountCondition은 **인터페이스**(interface)를 이용해 선언되었다.  

> **인터페이스**
> 1. 모든 필드가 public static final로 해석되며, 모든 메서드는 추상 메서드로 간주된다.
> 2. 클래스에 의해 구현되거나 다른 인터페이스에 의해 상속될 수 있다. 다중 상속이 가능하다.
> 3. 인스턴스를 생성할 수 없다.
> 4. 자바 8부터 default를 통해 일반 메서드 구현이 가능해졌다.
> 5. 예시 : 자동차 표준 기술이 담긴 OperateCar 인터페이스를 구현하는 OperateBMW760i, OperateToyota86, OperateHyundaii30 

```java
public interface DiscountCondition {
  boolean isSatisfiedBy(Screening screening);
}
```

```java
public class SequenceCondition implements DiscountCondition {
  private int sequence;
  
  public SequenceCondition(int sequence) {
    this.sequence = sequence;
  }
  
  public boolean isSatisfiedBy(Screening screening) {
    return screening.isSequence(sequence);
  }
}
```

```java
public class PeriodCondition implements DiscountCondition {
  private DayOfWeek dayOfWeek;
  private LocalTime startTime;
  private LocalTime endTime;
  
  public PeriodCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
    this.dayOfWeek = dayOfWeek;
    this.startTime = startTime;
    this.endTime = endTime;
  }
  
  public boolean isSatisfiedBy(Screening screening) {
    return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
      startTime.conpareTo(screening.getStartTime().toLocalTime()) <= 0 &&
      endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
  }
}
```

## 추상 클래스 vs 인터페이스
### 추상 클래스 적용하기
1. 연관된 클래스들 사이에 구현 코드를 공유하는 경우
2. protected나 private 필드, 메서드를 선언하려는 경우
3. non-static이나 non-final 필드를 선언하려는 경우

### 인터페이스 적용하기
1. 인터페이스를 구현할 클래스들이 서로 관련이 없는 경우
   - 예시 : Comparable, Cloneable 인터페이스는 관련 없는 수많은 클래스에서 구현된다.
2. 다중 상속을 사용하려는 경우

## 컴파일 시간 의존성과 실행 시간 의존성
다시 돌아가서 Movie 내부에 구체적인 할인 정책을 결정하는 코드가 없는데도 어떻게 적절한 정책을 선택할 수 있을까? 이는 컴파일 시간 의존성과 실행 시간 의존성이 다르기 때문이다.  

```java
//...
private DiscountPolicy discountPolicy;

public Money calculateMovieFee(Screening screening) {
  return fee.minus(discountPolicy.calculateDiscountAmount(screening));
}
```

코드 수준에서 Movie 클래스는 오직 추상 클래스인 DiscountPolicy에만 의존하고 있다. 이제 Movie의 인스턴스를 생성하는 코드를 살펴보자.

```java
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000), new AmountDiscountPolicy(Money.wons(800)));
```

Movie의 인스턴스를 생성할 때 금액 할인 정책을 적용하고 싶다면 인자로 AmountDiscountPolicy의 인스턴스를 전달하면 된다. 이제 실행 시에 Movie의 인스턴스는 AmountDiscountPolicy의 인스턴스에 의존하게 된다. 마찬가지로 비율 할인 정책을 적용하고 싶다면 PercentDiscountPolicy의 인스턴스를 전달하면 된다. 그러면 실행 시에 Movie의 인스턴스는 PercentDiscountPolicy의 인스턴스에 의존하게 된다. **다시 말해 클래스 사이의 의존성과 객체 사이의 의존성은 동일하지 않을 수 있다.**

Movie는 협력하는 객체가 calculateDiscountAmount 메시지를 이해할 수 있다면 그 객체가 어떤 클래스의 인스턴스인지는 상관하지 않는다. 따라서 해당 메시지를 수신할 수 있는 AmountDiscountPolicy, PercentDiscountPolicy가 부모 클래스인 DiscountPolicy를 대신해서 Movie와 협력할 수 있다. 이처럼 자식 클래스가 부모 클래스를 대신하는 것을 **업캐스팅**(upcasting)이라고 부른다.

### 주의!
컴파일 시간 의존성과 실행 시간 의존성이 다르면, 설계가 유연해지고 확장 가능해진다. 설계가 유연해질수록 코드를 이해하고 디버깅하기 점점 더 어려워진다. 설계의 유연성을 억제하면 코드를 이해하고 디버깅하기는 쉬워지지만, 재사용성과 확장 가능성이 낮아진다. **무조건 유연한 설계도, 무조건 읽기 쉬운 코드도 정답이 아니다.** 

## 다형성
Movie는 calculateDiscountAmount라는 동일한 메시지를 전송하지만 실제로 어떤 메서드가 실행될 것인지는 메시지를 수신하는 객체의 클래스가 무엇이냐에 따라 달라진다. 이를 **다형성**이라고 부른다. 다형성은 컴파일 시간 의존성과 실행 시간 의존성을 다르게 만들 수 있는 객체지향의 특성을 이용해 서로 다른 메서드를 실행할 수 있게 한다.  

메시지와 메서드를 실행 시점에 바인딩하는 것을 **지연 바인딩**(lazy binding) 또는 **동적 바인딩**(dynamic binding)이라고 부른다. 반대로 컴파일할 때 어떤 함수를 실행할지 결정하는 것을 **초기 바인딩**(early binding) 또는 **정적 바인딩**(static binding)이라고 부른다. 객체지향은 지연 바인딩 메커니즘을 사용한다.

## 추상화
지금까지 할인 정책은 추상 클래스로, 할인 조건은 인터페이스로 각각 추상화 작업을 했다. 이렇게 추상화를 사용하면 두 가지 장점이 있다.  

1. 요구사항의 정책을 높은 수준에서 서술할 수 있다. 즉, 협력의 흐름을 나타낼 수 있다. (그림 2.13)
2. 기존 구조를 수정하지 않고도 새로운 기능을 쉽게 추가할 수 있다. 따라서 설계가 유연해진다. (그림 2.14)

## 상속 vs 합성
합성은 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법을 말한다.

```java
public class Movie {
  private DiscountPolicy discountPolicy;
  //...
```

Movie가 DiscountPolicy의 코드를 재사용하는 방법이 합성이다. 이 설계를 상속을 사용하도록 변경할 수도 있다. 

```java
public class Movie {...}
public class AmountDiscountMovie extends Movie {...}
public class PercentDiscountMovie extends Movie {...}
```

Movie를 상속받는 두 개의 자식 클래스를 추가하면 합성을 사용한 기존 방법과 기능적인 관점에서 완벽히 동일하다. 그럼에도 많은 사람들이 상속 대신 합성을 선호하는 이유는 무엇일까?

### 상속
상속은 두 가지 관점에서 설계에 안 좋은 영향을 미친다.

1. 상속이 캡슐화를 위반한다.
   - 부모 클래스의 구현이 자식 클래스에게 노출되어 캡슐화가 약화된다. 상속은 결합도를 높이기 때문에 부모 클래스를 변경할 때 자식 클래스도 함께 변경될 확률을 높인다.
2. 설계를 유연하지 못하게 만든다.
   - 상속은 부모 클래스와 자식 클래스 사이의 관계를 컴파일 시점에 결정한다. 실행 시점에 객체의 종류를 변경하는 것이 불가능하다.

### 합성
합성이 상속과 다른 점은 Movie가 DiscountPolicy의 인터페이스를 통해 약하게 결합된다는 것이다. Movie는 calculateDiscountAmount 메시지만 알고 내부 구현에 대해서는 전혀 알지 못한다. 합성은 상속이 가지는 두 가지 문제점을 모두 해결한다.

1. 인터페이스에 정의된 메시지를 통해서만 재사용이 가능하기 때문에 구현을 효과적으로 캡슐화한다.
2. 의존하는 인스턴스를 교체하는 것이 비교적 쉽기 때문에 설계를 유연하게 만든다.

## 결론
1. 객체지향에서 가장 중요한 것은 객체다. 클래스는 객체지향 프로그래밍을 구성하는 하나의 요소일 뿐이다.
2. 클래스, 추상 클래스, 인터페이스를 조합해서 객체지향 프로그램을 구조화할 수 있다. 그 과정에서 상속과 합성을 적절히 사용하고 설계의 유연성과 코드의 가독성 사이에서 항상 고민해야 한다.

## 참고
https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html  
https://docs.oracle.com/javase/tutorial/java/concepts/interface.html  
https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html
