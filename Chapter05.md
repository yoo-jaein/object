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
#### 새로운 할인 조건 추가
#### 순번 조건을 판단하는 로직 변경
#### 기간 조건을 판단하는 로직 변경

### 클래스 응집도 판단하기

### 타입 분리하기

### 다형성을 통해 분리하기


## 개선 2단계: 객체를 자율적으로 만들기

## 결론

## 참고
https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)  
https://www.youtube.com/watch?v=zf2A_xSNhvM  
https://dzone.com/articles/solid-grasp-and-other-basic-principles-of-object-o  
https://stackoverflow.com/questions/14352106/difference-between-pure-fabrication-and-indirection  