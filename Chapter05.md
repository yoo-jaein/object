# Chapter 05 책임 할당하기

## 책임 주도 설계
### 1. 데이터보다 행동을 먼저 결정하라.
데이터 중심 설계에서는 "이 객체가 포함해야 하는 데이터가 무엇인가"를 결정한 후에 "데이터를 처리하는 데 필요한 오퍼레이션은 무엇인가"를 결정한다. 반면 책임 중심 설계에서는 "이 객체가 수행하야 하는 책임은 무엇인가"를 결정한 후에 "이 책임을 수행하는 데 필요한 데이터는 무엇인가"를 결정한다.

### 2. 협력이라는 문맥 안에서 책임을 결정하라.
객체의 입장에서 책임이 조금 어색해 보이더라도 협력에 적합하다면 그 책임은 좋은 것이다. 객체를 결정한 후에 메시지를 선택하는 것이 아니라 메시지를 결정한 후에 객체를 선택해야 한다.

## GRASP 패턴
General Responsibility Assignment Software Pattern, GRASP은 객체지향 소프트웨어 개발 프로젝트에서 공통적으로 나타나는 문제를 해결하기 위한 패턴이다. 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 9가지 원칙들을 안내한다.

- ```Information expert``` : 정보 전문가 패턴   
  > Problem: 객체에게 책임을 할당하는 기본 원칙은 무엇인가?  
  > Solution: 책임을 수행하는 데 필요한 정보를 가진 객체에게 할당하라.  
- ```Creator``` : 창조자 패턴
  > Problem: 누가 객체 A를 생성해야 하는가?  
  > Solution: 다음 조건을 최대한 많이 만족하는 객체 B에게 그 책임을 할당하라.  
  > 'B가 A를 포함하거나 참조한다.' 'B가 A를 기록한다.' 'B가 A를 긴밀하게 사용한다.' 'B가 A를 초기화하는 데 필요한 데이터를 가진다.'  
- ```Controller``` : 컨트롤러 패턴
  > Problem: 누가 시스템의 입력 이벤트를 핸들링하기 위한 책임을 가져야 하는가?  
  > Solution: 유즈 케이스 컨트롤러가 유즈 케이스의 모든 시스템 이벤트를 다뤄야 한다. 혹은, 하나 이상의 유즈 케이스를 위해 사용되어야 한다. 예를 들어, 'Create User', 'Delete User'라는 유즈 케이스는 두 개의 분리된 유즈 케이스 컨트롤러 대신 하나의 UserController를 공통적으로 가진다.  
- ```Indirection``` : 간접 패턴
  > Problem: 직접적인 결합을 피하기 위해 어디에 책임을 할당해야 하는가?  
  > Solution: 그들이 직접적으로 결합되지 않도록 컴포넌트 사이에 중재하는 객체를 만들고 그 객체에 책임을 할당하라.  

  Adapter, Facade, Observer과 같은 디자인 패턴이 Indirection 패턴에 해당한다.

- ```Low coupling``` : 낮은 결합도 패턴
  > Problem: 낮은 의존성, 변경 가능성, 높은 재사용성을 유지하는 방법을 무엇인가?  
  > Solution: 낮은 결합도를 유지할 수 있도록 책임을 할당하라.
- ```High cohesion``` : 높은 응집도 패턴
  > Problem: 객체를 관리하기 쉽고 이해하기 쉽게 유지하는 방법은 무엇인가?  
  > Solution: 높은 응집도를 유지할 수 있도록 책임을 할당하라.
- ```Polymorphism``` : 다형성 패턴
  > Problem: 객체의 타입에 따라 변하는 로직이 있을 때 변하는 로직을 담당할 책임을 어떻게 할당해야 하는가?  
  > Solution: 추상 클래스나 인터페이스와 같은 다형성 연산을 통해 행동별 책임을 할당하라.
- ```Protected variations``` : 변경 보호 패턴
  > Problem: 어떻게 객체, 서브시스템, 시스템을 설계할까? 의도하지 않은 변화를 최소화할 수 있도록  
  > Solution: 예측 가능한 변화를 확인하라. 
- ```Pure fabrication``` : 순수한 가공물 패턴
  > Problem: 책임을 할당할만한 확실한 객체가 없을 때 어떤 객체가 책임을 가져야 하는가?  
  > Solution: 그 책임을 가지는 새로운 객체를 만들어라.  
  
  은행 시스템의 도메인 개념을 생각해보자. Account, Branch, Cash, Check, Transaction 등이 있을 것이다. 도메인 클래스들은 고객에 대한 정보를 저장한다. 이를 수행하기 위해 도메인 클래스에는 데이터 저장을 위한 책임도 할당된다. 도메인 클래스의 응집도가 낮아지고 SRP 원칙을 위반한다.  

  다른 옵션은, 다른 클래스를 만드는 것이다. 어떤 도메인 개념도 나타내지 않는. 예를 들어, 은행 시스템에서 PersistenceProvider라는 클래스를 만든다. 이 클래스는 어떤 도메인 엔티티도 나타내지 않는다. 이 클래스의 목적은 데이터 저장 함수를 다루기 위한 것이다. 그러므로 이 클래스는 pure fabrication이다.

  
## 설계 0단계: 도메인 그리기
설계를 시작하기 전에 도메인에 대한 개략적인 모습을 그려 보는 것이 유용하다. 도메인 개념들을 책임 할당의 대상으로 사용하면 코드에 도메인의 모습을 투영하기가 좀 더 수월해지기 때문이다. 이 때 주의해야 할 점은, 도메인 개념들을 한 번에 완벽하게 정리할 필요가 없다는 것이다. 책임을 할당받을 객체들의 종류와 관계에 대한 정보를 제공할 수 있다면 충분하다. 이 단계에서 너무 많은 시간을 들이지 말고 빠르게 설계와 구현을 진행하자.

## 설계 1단계: 정보 전문가에게 책임 할당하기
책임 주도 설계의 첫 단계는 애플리케이션이 제공해야 하는 기능을 애플리케이션의 책임으로 생각하는 것이다. 이에 따라 애플리케이션은 '영화를 예매할 책임이 있다'고 말할 수 있다. '영화를 예매하라'는 메시지를 책임질 첫 번째 객체를 선택하는 것으로 설계를 시작한다.  

### 메시지를 전송할 객체는 무엇을 원하는가?
'영화를 예매하라'

### 메시지를 수신할 적합한 객체는 누구인가?
'영화를 예매하라' 메시지를 처리하는데 필요한 정보를 알고 있는 객체

객체에게 책임을 할당하는 첫 번째 원칙은 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것이다. GRASP에서는 이를 ```Information expert``` 패턴이라고 부른다. 여기서 이야기하는 '정보'란 데이터와 다르다. 전문가는 정보를 알고 있다고 해서 반드시 그 정보를 저장하고 있을 필요는 없다. 그 정보를 제공할 수 있는 다른 객체를 알고 있거나 필요한 정보를 계산해서 제공할 수 있다.  

## 설계 2단계: 높은 응집도와 낮은 결합도를 고려하기
설계는 트레이드오프 활동이다. 동일한 기능을 구현할 수 있는 무수히 많은 설계가 존재한다. 따라서 실제로 설계를 진행하다 보면 몇 가지 설계 중에서 한 가지를 선택해야 하는 일이 빈번하게 발생한다. 이 경우 ```Information expert``` 패턴과 ```Low coupling```, ```High cohesion``` 패턴을 함께 고려할 필요가 있다.  

## 설계 3단계: 창조자 결정하기
영화 예매 협력의 최종 결과물은 Reservation 인스턴스를 생성하는 것이다. 이것은 협력하는 어떤 객체에게 Reservation 인스턴스를 생성할 책임을 할당해야 한다는 것을 의미한다. 여기서 ```Creator``` 패턴을 사용하여 설계해보자. 

Reservation을 잘 알고 있거나, 긴밀하게 사용하거나, 초기화에 필요한 데이터를 가지고 있는 객체는 무엇인가? 바로 Screening이다. 

## 설계 4단계: 코드 작성하기
Screening부터 구현하자. Screening은 '예매하라' 메시지에 응답할 수 있어야 한다. 이 메시지를 처리할 메서드를 만들고, 책임을 수행하는 데 필요한 인스턴스 변수를 결정하자. 

```java
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;
    
    public Reservation reserve(Customer customer, int audienceCount) {
        return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
    }
    
    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}
```

Screening을 구현하는 과정에서 Movie에 전송하는 메시지의 시그니처를 calculateMovieFee(Screening screening)으로 선언했다. 이 메시지는 Movie가 아니라 메시지의 송신자인 Screening의 의도를 표현한다. 이처럼 Movie의 구현을 고려하지 않고 필요한 메시지를 결정하면 Movie의 내부 구현을 깔끔하게 캡슐화할 수 있다. 또한, 메시지를 기반으로 협력을 구성했기 때문에 Screening과 Movie 사이의 결합도를 느슨하게 유지할 수 있다. 

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;
    
    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;
    
    public Money calculateMovieFee(Screening screening) {
        if (isDiscountable(screening)) {
            return fee.minus(calculateDiscountAmount());
        }
        
        return fee;
    }
    
    private boolean isDiscountable(Screening screening) {
        return discountConditions
                .stream()
                .anyMatch(condition -> condition.isSatisfiedBy(screening));
    }
    private Money calculateDiscountAmount() {
        switch(movieType) {
          case AMOUNT_DISCOUNT:
              return calculateAmountDiscountAmount(); 
          case PERCENT_DISCOUNT:
              return calculatePercentDiscountAmount();
          case NONE_DISCOUNT:
              return calculateNoneDiscountAmount();
        }
        throw new IllegalStateException();
    }
    
    private Money calculateAmountDiscountAmount() {
        return discountAmount;
    }
    
    private Money calculatePercentDiscountAmount() {
        return fee.times(discountAmount);
    }
    
    private Money calculateNoneDiscountAmount() {
        return Money.ZERO;
    }
}
```

```java
public enum MovieType {
    AMOUNT_DISCOUNT,    // 금액 할인 정책 
    PERCENT_DISCOUNT,   // 비율 할인 정책
    NONE_DISCOUNT       // 미적용
}
```


```java
public class DiscountCondition {
    private DiscountConditionType type;
    private int sequence;
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;
    
    public boolean isSatisfiedBy(Screening screening) {
        if (type == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(screening);
        }
        
        return isSatisfiedBySequence(screening);
    }
    
    private boolean isSatisfiedByPeriod(Screening screening) {
        return dayOfWeek.equals(screening.getWhenScreened().getDayOfWeek()) &&
                startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                endTime.isAfter(screening.getWhenScreened().toLocalTime()) >= 0;
    }
    
    private boolean isSatisfiedBySequence(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```

```java
public class Screening {
    //...
    public LocalDateTime getWhenScreened() {
        return whenScreened;
    }
    
    public int getSequence() {
        return sequence;
    }
}
```

```java
public enum DiscountConditionType {
    SEQUENCE,       // 순번 조건
    PERIOD          // 기간 조건
}
```

## 개선 1단계: 낮은 응집도를 위한 클래스 분리하기
### 현재 문제점
#### 1. 새로운 할인 조건 추가
새로운 할인 조건이 추가되면 isSatisfiedBy 메서드 안의 if ~ else 구문을 수정해야 한다. 새로운 데이터가 필요한 경우 DiscountCondition에 속성을 추가해야 한다.

#### 2. 순번 조건을 판단하는 로직 변경
isSatisfiedBySequence 메서드 안의 내부 구현을 수정해야 한다. 로직에 필요한 데이터가 변경되면 sequence 속성도 변경해야 한다.

#### 3. 기간 조건을 판단하는 로직 변경
isSatisfiedByPeriod 메서드 안의 내부 구현을 수정해야 한다. 로직에 필요한 데이터가 변경되면 dayOfWeek, startTime, endTime 속성도 변경해야 한다.

### 클래스 응집도 판단하기
클래스의 응집도를 판단할 수 있는 세 가지 방법
1. 클래스가 하나 이상의 이유로 변경돼야 한다면 응집도가 낮은 것이다.
2. 클래스의 인스턴스를 초기화하는 시점에 경우에 따라 서로 다른 속성들을 초기화하고 있다면 응집도가 낮은 것이다.
3. 메서드 그룹이 속성 그룹을 사용하는지 여부로 나뉜다면 응집도가 낮은 것이다.

### 타입 분리하기
DiscountCondition의 가장 큰 문제는 순번 조건과 기간 조건이라는 두 개의 독립적인 타입이 하나의 클래스 안에 공존하고 있다는 점이다. 가장 먼저, 두 타입을 SequenceCondition과 PeriodCondition이라는 두 개의 클래스로 분리해보자.

```java
public class SequenceCondition {
    private int sequence;
    
    public SequenceCondition(int sequence) {
        this.sequence = sequence;
    }
    
    public boolean isSatisfiedBy(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```

```java
public class PeriodCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;
    
    public PeriodCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
        this.dayOfWeek = dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }
    
    public boolean isSatisfiedBy(Screening screening) {
        return dayOfWeek.equals(screening.getWhenScreened().getDayOfWeek()) &&
            startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
            endTime.compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
    }
}
```

클래스를 분리함으로써 개별 클래스들의 응집도가 향상됐다. 하지만 안타깝게도 클래스를 분리한 후에 새로운 문제가 나타났다. 수정 후에 Movie의 인스턴스는 SequenceCondition과 PeriodCondition이라는 두 개의 서로 다른 클래스의 인스턴스 모두와 협력할 수 있어야 한다. 이를 위해 Movie 클래스 안에는 SequenceCondition List와 PeriodCondition List를 따로 유지하게 되어 결국 Movie 클래스가 양쪽 클래스 모두에 결합된다. 설계의 관점에서 전체적인 결합도가 높아졌다.

또 다른 문제는 수정 후에 새로운 할인 조건을 추가하기가 더 어려워졌다는 것이다. 새로운 할인 조건 클래스를 담기 위한 List를 추가하고, 이 List를 이용해 할인 조건을 만족하는지 확인하는 메서드를 추가하고, 이 메서드를 호출하도록 isDiscountable 메서드를 수정해야 한다. 변경과 캡슐화라는 관점에서 전체적으로 설계의 품질이 나빠졌다.

### 다형성을 통해 분리하기
사실 Movie의 입장에서 할인 가능 여부를 반환해주기만 하면 그 객체가 SequenceCondition의 인스턴스인지, PeriodCondition의 인스턴스인지 상관하지 않는다. 여기서 **역할**의 개념이 등장한다.

역할은 협력 안에서 대체 가능성을 의미한다. SequenceCondition과 PeriodCondition에 역할의 개념을 적용하면 Movie가 구체적인 클래스에 의존하지 않도록 만들 수 있다. 자바에서는 일반적으로 역할을 구현하기 위해 추상 클래스나 인터페이스를 사용한다. **역할을 대체할 클래스들 사이에서 구현을 공유해야 할 필요가 있다면 추상 클래스를 사용하면 된다. 구현을 공유할 필요 없이 역할을 대체하는 객체들의 책임만 정의하고 싶다면 인터페이스를 사용하면 된다.**

```java
public interface DiscountCondition {
    boolean isSatisfiedBy(Screening screening);
}
```

```java
public class PeriodCondition implements DiscountCondition {...}
```

```java
public class SequenceCondition implements DiscountCondition {...}
```

```java
public class Movie {
    private List<DiscountCondition> discountConditions;
    
    public Money calculateMovieFee(Screening screening) {
        if (isDiscountable(screening)) {
            return fee.minus(calculateDiscountAmount());
        }
    
        return fee;
    }
    
    private boolean isDiscountable(Screening screening) {
        return discountConditions.stream()
            .anyMatch(condition -> condition.isSatisfiedBy(screening));
    }
}
```

Movie와 DiscountCondition 사이의 협력은 다형적이다. 위 코드에서 알 수 있듯이 **객체의 타입에 따라 행동을 분기해야 한다면 객체의 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당함**으로써 문제를 해결할 수 있다. GRASP에서는 이를 ```Polymorphism``` 패턴이라고 부른다.

새로운 조건이 추가되는 경우 DiscountCondition 인터페이스를 실체화하는 클래스를 추가하는 것만으로 확장할 수 있게 되었다. 이처럼 **변경을 캡슐화하도록 책임을 할당하는 것**을 GRASP에서는 ```Protected variations``` 패턴이라고 부른다. ```Polymorphism``` 패턴으로 역할을 분리하고, ```Protected variations``` 패턴으로 인터페이스 뒤로 변경을 캡슐화했다. 그 결과 변경과 확장에 유연하게 대처할 수 있는 설계를 얻게 되었다.

Movie 클래스 역시 DiscountCondition과 동일한 문제를 겪고 있으므로 해결 방법도 동일하게 적용한다.

### 변경과 유연성
개발자로서 변경에 대비할 수 있는 두 가지 방법이 있다. 하나는 코드를 이해하고 수정하기 쉽도록 최대한 단순하게 설계하는 것이다. 다른 하나는 코드를 수정하지 않고도 변경을 수용할 수 있도록 최대한 단순하게 설계하는 것이다. **대부분의 경우에 전자가 더 좋은 방법이지만 유사한 변경이 반복적으로 발생하고 있다면 복잡성이 상승하더라도 유연성을 추가하는 두 번째 방법이 더 좋다.**

## 개선 2단계: 몬스터 메서드 해체하기
영화 예매를 처리하는 ReservationAgency 코드를 살펴보자.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {  // (1)
        Movie movie = screening.getMovie();
        
        boolean discountable = false;
        for (DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                    condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                    condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }
        }
        
        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;
            switch (movie.getMovieType()) {
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
                case NONE_DISCOUNT:
                    discountAmount = Money.ZERO;
                    break;
            }
            
            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

(1) : reserve 메서드는 길이가 너무 길고 이해하기도 어렵다. 긴 메서드는 응집도가 낮기 때문에 재사용하기도 어렵고 변경하기도 어렵다. 이런 메서드를 몬스터 메서드(monster method)라고 부른다.

긴 메서드를 작고 응집도 높은 메서드로 분리해보자.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) { // (1)
        boolean discountable = checkDiscountable(screening);
        Money fee = calculateFee(screening, discountable, audienceCount);
        return createReservation(screening, customer, audienceCount, fee);
    }
    
    private boolean checkDiscountable(Screening screening) {
        return screening.getMovie().getDiscountConditions().stream()
            .anyMatch(condition -> isDiscountable(condition, screening));
    }
    
    private boolean isDiscountable(DiscountCondition condition, Screening screening) {
        if (condition.getType() == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(condition, screening);
        }
        
        return isSatisfiedBySequence(condition, screening);
    }
    
    private boolean isSatisfiedByPeriod(DiscountCondition condition, Screening screening) {
        return screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
            condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
            condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
    }
    
    private boolean isSatisfiedBySequence(DiscountCondition condition, Screening screening) {
        return condition.getSequence() == screening.getSequence();
    }
    //...
```

(1) : 수정 후에는 reserve 메서드가 무슨 일을 하는지 한 눈에 알아볼 수 있다. 코드를 작은 메서드들로 분해하면 전체적인 흐름을 이해하기가 쉬워진다. 세부적인 정보가 필요하다면 그때 각 메서드의 세부 구현을 확인하면 된다.

메서드의 응집도 자체는 높아졌지만 이 메서드들을 담고 있는 ReservationAgency 클래스의 응집도는 여전히 낮다. ReservationAgency의 응집도를 높이기 위해 변경의 이유가 다른 메서드들을 적절한 위치로 분배해야 한다.

## 개선 3단계: 자율적인 객체 만들기
어떤 메서드를 어떤 클래스로 이동시켜야 할까? 객체가 자율적인 존재여야 한다는 사실을 잊지 말자. 따라서 메서드가 사용하는 데이터를 가지고 있는 클래스로 메서드를 이동시키면 된다. 어떤 데이터를 사용하는지를 가장 쉽게 알 수 있는 방법은 메서드 안에서 어떤 클래스의 접근자(getter) 메서드를 사용하는지 파악하는 것이다.  

## 결론

## 참고
https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)  
https://www.youtube.com/watch?v=zf2A_xSNhvM  
https://dzone.com/articles/solid-grasp-and-other-basic-principles-of-object-o  
https://stackoverflow.com/questions/14352106/difference-between-pure-fabrication-and-indirection  