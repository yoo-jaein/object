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
응집도는 모듈에 포함된 내부 요소들이 연관돼 있는 정도를 나타낸다. 응집도가 높은 설계에서는 하나의 요구사항 변경을 반영하기 위해 오직 하나의 모듈만 수정하면 된다. 반면 응집도가 낮은 설계에서는 하나의 원인에 의해 변경해야 하는 부분이 다수의 모듈에 분산돼 있기 때문에 여러 모듈을 동시에 수정해야 한다.  

### 결합도
결합도는 의존성의 정도를 나타낸다. 다시 말해 결합도는 하나의 모듈을 수정할 때 얼마나 많은 모듈을 함께 수정해야 하는지를 나타낸다. 결합도가 낮은 설계에서는 하나의 모듈을 변경했을 때 오직 하나의 모듈만 영향을 받는다. 반면 결합도가 높은 설계에서는 하나의 모듈을 변경했을 때 여러 모듈을 동시에 변경해야 한다.

## 데이터 중심의 영화 예매 시스템의 문제점

### 캡슐화 위반
데이터 중심으로 설계한 Movie 클래스를 보면 메서드를 통해서만 객체의 내부 상태에 접근할 수 있다는 것을 알 수 있다. 

```java
private Money fee;

public Money getFee() {
    return fee;
}

public void setFee(Money fee) {
    this.fee = fee;
}
```

이 코드는 객체 내부에 직접 접근할 수 없기 때문에 캡슐화의 원칙을 지키고 있는 것처럼 보인다. 그러나 안타깝게도 getFee, setFee 메서드는 Money 타입의 fee라는 이름의 인스턴스 변수가 Movie 내부에 존재한다는 사실을 드러낸다. 따라서 이 설계는 캡슐화의 원칙을 위반했다. 

### 높은 결합도
다음으로 ReservationAgency의 코드를 살펴보자.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        //...
        Money fee;  // (2)
        if (discountable) {
            //...
            fee = movie.getFee().minus(discountedAmount).times(audienceCount);  // (1), (2)
        } else {
            fee = movie.getFee();
        }
        //...
    }
}
```

(1) : 한 사람의 예매 요금을 계산하기 위해 Movie의 getFee 메서드를 호출한다. 계산된 결과를 Money 타입의 fee에 저장한다.  
(2) : 만약 fee의 타입을 변경한다고 가정해보자. 이를 위해 getFee 메서드의 반환 타입, ReservationAgency 내부의 구현도 변경된 타입에 맞게 함께 수정해야 할 것이다. fee의 타입 변경으로 인해 협력하는 클래스도 변경되기 때문에 getFee 메서드는 fee를 정상적으로 캡슐화하지 못한다. 이처럼 getFee 메서드를 사용하는 것은 인스턴스 변수 fee의 가시성을 private에서 public으로 변경하는 것과 거의 동일하다.  

지금의 ReservationAgency는 Screening, Reservation, Movie, DiscountCondition, 모든 데이터 객체에 의존한다. DiscountCondition의 데이터가 변경되면 ReservationAgency도 함께 수정해야 하고, Screening의 데이터가 변경되면 ReservationAgency도 함께 수정해야 한다. 너무 많은 대상에 의존하고 있기 때문에 변경에 취약하다.  

### 낮은 응집도
ReservationAgency 안에 할인 정책을 선택하는 코드와 할인 조건을 판단하는 코드가 함께 들어간다. 따라서 새로운 할인 정책을 추가하는 작업이 할인 조건 코드에도 영향을 미칠 수 있다. 어떤 코드를 수정한 후에 아무 상관도 없던 코드에 문제가 발생하는 것은 모듈의 응집도가 낮을 때 발생하는 대표적인 증상이다. 또한, 하나의 요구사항 변경을 반영하기 위해 동시에 여러 모듈을 수정해야 한다. 새로운 할인 정책을 추가하는 작업에서 MovieType의 값, ReservationAgency의 reserve 메서드 조건식 코드를 수정해야 한다.  

## 개선: 자율적인 객체 만들기
객체가 자신의 데이터를 책임질 수 있도록 개선한다.

```java
public class DiscountCondition {
    //...
    public DiscountConditionType getType() {
        return type;
    }
    
    public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime time) {
        if (type != DiscountConditionType.PERIOD) {
            throw new IllegalArgumentException();
        }
        
        return this.dayOfWeek.equals(dayOfWeek)
                && this.startTime.compareTo(time) <= 0
                && this.endTime.compareTo(time) >= 0;
    }
    
    public boolean isDiscountable(int sequence) {
        if (type != DiscountConditionType.SEQUENCE) {
            throw new IllegalArgumentException();
        }
        
        return this.sequence == sequence;
    }
}
```

```java
public class Movie {
    //...
    public MovieType getMovieType() {
        return movieType;
    }
    
    public Money calculateAmountDiscountedFee() {
        if (movieType != MovieType.AMOUNT_DISCOUNT) {
            throw new IllegalArgumentException();
        }
        
        return fee.minus(discountAmount);
    }
    
    public Money calculatePercentDiscountedFee() {
        if (movieType != MovieType.PERCENT_DISCOUNT) {
            throw new IllegalArgumentException();
        }
        
        return fee.minus(fee.times(discountPercent));
    }
    
    public Money calculateNoneDiscountedFee() {
        if (movieType != MovieType.NONE_DISCOUNT) {
            throw new IllegalArgumentException();
        }
        
        return fee;
    }
    
    public boolean isDiscountable(LocalDateTime whenScreened, int sequence) {
        for (DiscountCondition condition : discountConditions) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                if (condition.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
                    return true;
                }
            } else {
                if (condition.isDiscountable(sequence)) {
                    return true;
                }
            }
        }
        
        return false;
    }
}
```

```java
public class Screening {
    //...
    public Money calculateFee(int audienceCount) {
        switch (movie.getMovieType()) {
            case AMOUNT_DISCOUNT:
                if (movie.isDiscountable(whenScreened, sequence)) {
                    return movie.calculateAmountDiscountedFee().times(audienceCount);
                }
                break;
            case PERCENT_DISCOUNT:
                if (movie.isDiscountable(whenScreened, sequence)) {
                    return movie.calculatePercentDiscountedFee().times(audienceCount);
                }
                break;
            case NONE_DISCOUNT:
                return movie.calculateNoneDiscountedFee().times(audienceCount);
        }
        
        return movie.calculateNoneDiscountedFee().times(audienceCount);
    }
}
```

```java
public class ReservationAgency {
    //...
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Money fee = screening.calculateFee(audienceCount);
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

## 여전한 문제점
```java
public class DiscountCondition {
    //...
    public DiscountConditionType getType() {...}
    public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime time) {...} // (1)
    public boolean isDiscountable(int sequence) {...} // (1)
}
```

```java
public class Movie {
    //...
    public MovieType getMovieType() {...}
    public Money calculateAmountDiscountedFee() {...}   // (2)
    public Money calculatePercentDiscountedFee() {...}  // (2)
    public Money calculateNoneDiscountedFee() {...}     // (2)
}
```

(1) : 할인 조건을 판단하는 isDiscountable 메서드를 통해 DiscountCondition는 자신의 상태를 관리할 수 있게 되었다. 그런데 자세히 살펴보면 isDiscountable 메서드는 파라미터로 DayOfWeek, LocalTime, int 정보를 갖고 있다. 여전히 내부 구현을 캡슐화하는 데는 실패했다.  
(2) : Movie 역시 내부 구현을 인터페이스의 이름에 그대로 노출시키고 있다. 만약 새로운 할인 정책을 추가하면 이 메서드들에 의존하는 모든 클라이언트가 영향을 받을 것이다. 여기도 내부 구현을 캡슐화하는데 실패했다.  

> **캡슐화의 진정한 의미**  
> 내부 속성을 외부로부터 감추는 것은 '데이터 캡슐화'라고 불리는 캡슐화의 한 종류일 뿐이다. 캡슐화는 변할 수 있는 어떤 것이라도 감추는 것이다. 데이터 캡슐화에만 집중하지 말고 더 큰 개념을 바라봐야 한다.  

## 데이터 중심 설계의 문제점
1. 데이터 중심 설계는 객체의 행동보다는 상태에 초점을 맞춘다.
2. 데이터 중심 설계는 객체를 고립시킨 채 오퍼레이션을 정의하도록 만든다.

## 결론
1. 훌륭한 객체지향 설계는 데이터가 아니라 책임에 초점을 맞춰야 한다.

## 참고
https://en.wikipedia.org/wiki/Mutator_method  