# Chapter 04 설계 품질과 트레이드오프

## 데이터 중심 설계
3장에서 [데이터 주도 설계](https://github.com/yoo-jaein/object/blob/main/Chapter03.md#데이터-주도-설계)에 대해 간략하게 알아봤다. 4장에서는 영화 예매 시스템을 데이터 중심의 관점에서 다시 설계해보자.  

## 데이터가 무엇인가
책임 중심의 관점은 객체의 행동에 초점을 맞추지만, 데이터 중심의 관점은 객체의 상태(데이터)에 초점을 맞춘다. 따라서, 데이터 중심의 설계는 먼저 객체 내부에 어떤 데이터가 저장될 것인지 결정하는 것으로 시작한다. 데이터 중심의 Movie 클래스 역시 책임 중심의 Movie 클래스와 마찬가지로 영화를 표현하는 가장 기본적인 정보인 title, runningTime, fee를 인스턴스 변수로 포함한다.   

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions; // (1)
    
    private MovieType movieType; // (3)
    private Money discountAmount; // (2)
    private double discountPercent; // (2)
}
```

```java
public enum MovieType { // (3)
    AMOUNT_DISCOUNT,    // 금액 할인 정책
    PERCENT_DISCOUNT,   // 비율 할인 정책
    NONE_DISCOUNT       // 미적용
}
```

이전 설계와 비교하여 달라진 점을 찾아보자.  
(1) : 할인 조건의 목록 discountConditions을 인스턴스 변수로 직접 포함하고 있다.  
(2) : 할인 정책을 별도의 클래스로 분리했던 이전 예제와 달리 할인 정책에 사용되는 할인 금액 discountAmount, 할인 비율 discountPercent을 인스턴스 변수로 직접 포함하고 있다.  
(3) : 영화에 사용될 할인 정책의 종류를 결정하기 위해 열거형 타입인 movieType을 포함하고 있다. 이렇게 객체의 종류를 저장하는 인스턴스 변수와 인스턴스 종류에 따라 배타적으로 사용될 인스턴스 변수들을 하나의 클래스 안에 함께 포함시키는 방식은 데이터 중심 설계에서 자주 보이는 패턴이다.  


필요한 데이터를 준비했다. 이제 객체지향의 가장 중요한 원칙인 캡슐화를 적용시키자. 가장 간단한 방법은 내부의 데이터를 반환하는 접근자(accessor)와 데이터를 변경하는 수정자(mutator)를 추가하는 것이다. 접근자는 getter, 수정자는 setter라고도 한다.

```java
public class Movie {
    //...
    public MovieType getMovieType() {
        return movieType;
    }
    
    public void setMovieType(MovieType movieType) {
        this.movieType = movieType;
    }
    
    public Money getFee() {
        return fee;
    }
    
    public void setFee(Money fee) {
        this.fee = fee;
    }
    
    public List<DiscountCondition> getDiscountConditions() {
        return Collections.unmodifiableList(discountConditions);
    }
    
    public void setDiscountConditions(List<DiscountCondition> discountConditions) {
        this.discountConditions = discountConditions;
    }
    
    public Money getDiscountAmount() {
        return discountAmount;
    }
    
    public void setDiscountAmount(Money discountAmount) {
        this.discountAmount = discountAmount;
    }
    
    public double getDiscountPercent() {
        return discountPercent;
    }
    
    public void setDiscountPercent(double discountPercent) {
        this.discountPercent = discountPercent;
    }
}
```

이제 할인 조건을 구현해보자. movieType과 마찬가지로 할인 조건의 타입을 저장할 DiscountConditionType을 만들고 할인 조건 DiscountCondition에 순번 조건에서 사용되는 데이터와 기간 조건에서 사용되는 데이터를 포함시킨다. 그리고 접근자와 수정자를 추가한다.  

```java
public enum DiscountConditionType {
    SEQUENCE,   // 순번 조건
    PERIOD      // 기간 조건
}
```

```java
public class DiscountCondition {
    private DiscountConditionType type;

    private int sequence;

    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public DiscountConditionType getType() {
        return type;
    }
    
    public void setType(DiscountConditionType type) {
        this.type = type;
    }
    //...
}
```

동일한 방법으로 Screening, Reservation, Customer를 구현한다. 마지막으로 클래스들을 조합해서 영화 예매 절차를 구현한 ReservationAgency를 만든다.  

## 비교 기준
지금까지 영화 예매 시스템을 데이터 중심으로 설계해봤다. 이제 이 설계를 책임 중심 설계와 비교해보자. 이를 위해 먼저 비교를 위한 기준을 이해해야 한다.

### 캡슐화
객체지향에서 가장 중요한 원리는 캡슐화다. 캡슐화가 중요한 이유는 불안정한 부분(구현)과 안정적인 부분(인터페이스)을 분리해서 변경의 영향을 통제할 수 있기 때문이다. 지금까지는 데이터, 상태를 캡슐화한다고 했지만 사실 데이터뿐만 아니라 변경될 수 있는 어떤 것이라도 캡슐화해야 한다.  

### 응집도
응집도는 모듈에 포함된 내부 요소들이 연관돼 있는 정도를 나타낸다.

### 결합도
결합도는 의존성의 정도를 나타낸다.

## 데이터 중심의 영화 예매 시스템의 문제점

### 캡슐화 위반

### 높은 결합도

### 낮은 응집도

## 개선 1: 자율적인 객체 만들기

## 개선 2: 진정한 캡슐화

## 데이터 중심 설계의 문제점

## 결론

## 참고
https://en.wikipedia.org/wiki/Mutator_method  