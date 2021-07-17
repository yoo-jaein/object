# Chapter 02 객체지향 프로그래밍
## 객체지향
객체지향 프로그램을 작성할 때 대부분의 사람들은 먼저 어떤 클래스가 필요한지 고민한다. 안타깝게도 클래스 자체는 객체지향의 본질과는 거리가 멀다. 진정한 객체지향 패러다임으로의 전환은 클래스가 아닌 객체에 초점을 맞출 때에만 얻을 수 있다.  

**1. 어떤 객체들이 필요한지 고민하라.**  
**2. 객체를 협력하는 공동체의 일원으로 봐라.**

## 도메인
객체지향 패러다임이 강력한 이유는 요구사항을 분석하는 초기 단계부터 프로그램을 구현하는 마지막 단계까지 객체라는 동일한 추상화 기법을 사용할 수 있기 때문이다. 일반적으로 클래스 이름은 대응되는 도메인 개념 이름과 동일하거나 유사하게 지어야 한다. 클래스 사이의 관계도 최대한 도메인 개념 사이에 맺어진 관계와 유사하게 만들어서 프로그램의 구조를 이해하고 예상하기 쉽게 만들어야 한다.  

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
```java
public interface DiscountCondition {
  boolean isSatisfiedBy(Screening screening);
}
```

영화 예매 시스템의 할인 조건은 순번 조건 SequenceCondition, 기간 조건 PeriodCondition으로 구분된다. DiscountCondition은 인터페이스를 이용해 선언되었다.  

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

## 생성자로 객체 보장하기

## 컴파일 시점 의존성과 런타임 의존성

## 다형성

## 결론
