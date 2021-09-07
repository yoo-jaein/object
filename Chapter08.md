# Chapter 08 의존성 관리하기

## 협력과 의존성
다른 객체와 협력하기 위해서 그 객체가 존재한다는 사실과 그 객체가 수신할 수 있는 메시지에 대해 알고 있어야 한다. 이런 지식이 객체 사이의 ```의존성```을 낳는다. 협력을 위해서는 의존성이 필요하지만 과도한 의존성은 애플리케이션을 수정하기 어렵게 만든다. 8장에서는 협력적이면서도 유연한 객체를 만들기 위해 의존성을 관리하는 방법을 알아본다.

## 의존성

다음 코드에서 PeriodCondition 클래스가 어떤 요소에 의존성을 가지는지 찾아보자.

```java
public class PeriodCondition implements DiscountCondition { // (3)
    private DayOfWeek dayOfWeek; // (2)
    private LocalTime startTime;
    private LocalTime endTime;

    public boolean isSatisfiedBy(Screening screening) { // (1)
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
            startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 && 
            endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
}
```

(1) : 실행 시점에 PeriodCondition의 인스턴스가 정상적으로 동작하기 위해서는 Screening의 인스턴스가 존재해야 한다. 이처럼 **어떤 객체가 작업을 정상 수행하기 위해 다른 객체를 필요로 하는 경우 '두 객체 사이에 의존성이 존재한다'고 말한다.** 의존성은 단방향이다. Screening이 변경될 때 PeriodCondition이 영향을 받게 되지만 그 역은 성립하지 않는다. 이 경우 PeriodCondition은 Screening에 의존한다.

(2) : PeriodCondition은 DayOfWeek, LocalTime의 인스턴스를 속성으로 포함한다. 만약 DayOfWeek, LocalTime의 클래스 이름을 변경하면 PeriodCondition에 정의된 인스턴스 변수의 타입 선언도 함께 수정해야 한다. PeriodCondition은 DayOfWeek, LocalTime에 의존하고 있다.

(3) : DiscountCondition 인터페이스의 이름이 변경되면 어떻게 될까? DiscountCondition에 선언된 isSatisfiedBy 오퍼레이션의 시그니처가 변경되면 어떻게 될까? DiscountCondition을 구현하는 PeriodCondition 클래스도 함께 수정해야 할 것이다. PeriodCondition은 DiscountCondition에도 의존하고 있다.

> **UML과 의존성**  
> 여기서 말하는 의존성은 UML(Unified Modeling Language)에서 정의하는 의존 관계와 조금 다르다. UML에서는 두 요소 사이의 관계로 실체화 관계, 연관 관계, 의존 관계, 일반화/특수화 관계, 합성 관계, 집합 관계 등을 정의한다. 의존성은 두 요소 사이에 변경에 의해 영향을 주고 받는 관계가 존재한다는 것에 초점을 맞춘다. UML을 기준으로 PeriodCondition과 DiscountCondition을 봤을 때 실체화 관계라고 말할 수 있지만, 두 요소 사이에는 의존성이 존재한다. **의존성은 UML에서 정의하는 모든 관계가 가지는 공통적인 특성으로 봐야 한다.**

## 의존성 전이
**의존성은 전이될 수 있다.** PeriodCondition이 Screening에 의존할 경우 PeriodCondition은 Screening이 의존하는 대상에 대해서도 자동적으로 의존하게 된다는 것이다. 따라서 Screening이 Movie, LocalDateTime, Customer에 의존하기 때문에 PeriodCondition 역시 간접적으로 Movie, LocalDateTime, Customer에 의존하게 된다.  

그러나 모든 경우에 의존성이 전이되는 것은 아니다. 의존성 전이 여부는 1) 변경의 방향과 2) 캡슐화의 정도에 따라 달라진다. 

- 직접 의존성(direct dependency)
- 간접 의존성(indirect dependency)

지금까지 클래스를 예로 들어 설명했지만 의존성의 대상은 객체일 수도 있고 모듈이나 더 큰 규모의 시스템이 될 수도 있다. 

## 의존성의 종류
의존성은 실행 시점과 구현 시점에 서로 다른 의미를 가진다.

- 실행 시점 (런타임) : 의존하는 객체가 정상적으로 동작하기 위해서는 실행 시점에 의존 대상 객체가 반드시 존재해야 한다.
- 구현 시점 (컴파일타임) : 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다.

## 런타임 의존성과 컴파일타임 의존성
런타임이란 애플리케이션이 실행되는 시점을 가리킨다. 컴파일 타임은 작성된 코드를 컴파일하는 시점을 가리키지만 문맥에 따라서는 코드 그 자체를 가리키기도 한다. 컴파일타임 의존성이 바로 이런 경우에 해당한다. 컴파일타임 의존성이라는 용어가 중요하게 생각하는 것은 컴파일 시간이 아니라 코드의 구조이다. 

**런타임 의존성이 다루는 주제는 객체 사이의 의존성이다.** 반면, 코드 관점에서 **컴파일타임 의존성이 다루는 주제는 클래스 사이의 의존성이다.** 여기서 중요한 것은 [런타임 의존성과 컴파일타임 의존성이 다를 수 있다는 것이다.](https://github.com/yoo-jaein/object/blob/main/Chapter02.md#%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EC%8B%9C%EA%B0%84-%EC%9D%98%EC%A1%B4%EC%84%B1%EA%B3%BC-%EC%8B%A4%ED%96%89-%EC%8B%9C%EA%B0%84-%EC%9D%98%EC%A1%B4%EC%84%B1)

## 의존성 해결하기
컴파일타임 의존성은 런타임 의존성으로 대체되어야 한다. 다시 말해서 **클래스 사이의 의존성을 객체 사이의 의존성으로 수정해야 한다.**

컴파일타임 의존성을 실행 컨텍스트에 맞는 적절한 런타임 의존성으로 교체하는 것을 **의존성 해결**이라고 부른다. 의존성을 해결하기 위해서는 일반적으로 다음과 같은 세 가지 방법을 사용한다.

1. 생성자
2. setter 메서드
3. 메서드 파라미터

## 의존성과 결합도
모든 의존성이 나쁜 것은 아니다. 의존성은 협력을 가능하게 만드는 매개체라는 관점에서는 바람직한 것이다. 의존성의 정도가 과할 때 문제가 되는 것이다.  

```java
public class Movie {
    private PercentDiscountPolicy percentDiscountPolicy; // (1)

    public Movie(String title, Duration runningTime, Money fee, PercentDiscountPolicy percentDiscountPolicy) {
        //...
        this.percentDiscountPolicy = percentDiscountPolicy;
    }
    
    public Money calculateMovieFee(Screening screening) {
        return fee.minus(percentDiscountPolicy.calculateDiscountAmount(screening));
    }
}
```

(1) : Movie가 비율 할인 정책을 구현하는 PercentDiscountPolicy에 직접 의존한다고 가정해보자. Movie와 PercentDiscountPolicy 사이에 의존성이 존재하는 것은 문제가 아니다. 문제는 의존성의 정도다. 이 코드는 Movie를 PercentDiscountPolicy라는 구체적인 클래스에 의존하게 만들기 때문에 다른 종류의 할인 정책이 필요한 문맥에서 Movie를 재사용할 수 없게 만들었다.  

Movie가 PercentDiscountPolicy뿐만 아니라 AmountDiscountPolicy와도 협력해야 한다면 어떻게 해야 할까? 해결 방법은 의존성을 바람직하게 만드는 것이다. Movie의 입장에서 협력할 객체의 클래스를 고정할 필요가 없다. 자신이 전송하는 calculateDiscountAmount 메시지를 이해할 수 있다면 어떤 타입의 객체와 협력하더라도 상관이 없다. 추상 클래스 DiscountPolicy를 만듬으로써 이 문제를 해결한다. **정리하면 PercentDiscountPolicy에 대한 의존성은 바람직하지 않다. DiscountPolicy에 대한 의존성은 바람직하다.**

### 의존성과 결합도
어떤 두 요소 사이에 존재하는 의존성이 바람직할 때 두 요소가 **느슨한 결합도(loose coupling)** 또는 약한 결합도(weak coupling) 를 가진다고 말한다. 반대로 두 요소 사이의 의존성이 바람직하지 못할 때 **단단한 결합도(tight coupling)** 또는 강한 결합도(strong coupling) 를 가진다고 말한다.  

서로에 대해 알고 있는 지식의 양이 결합도를 결정한다. 더 많이 알수록 더 많이 결합된다. 결합도를 느슨하게 유지하려면 협력하는 대상에 대해 더 적게 알아야 한다. 그러기 위해서 협력하는 대상에 대해 필요한 정보 외에는 최대한 감추는 것이 중요하다. 여기서 ```추상화```를 사용한다.

## 추상화에 의존하라
아래쪽으로 갈수록 결합도가 느슨해진다.

- 구체 클래스 의존성(concrete class dependency)
- 추상 클래스 의존성(abstract class dependency)
- 인터페이스 의존성(interface dependency)

결합도를 느슨하게 만들기 위해서는 ```구체적인 클래스```보다 ```추상 클래스```에, ```추상 클래스```보다 ```인터페이스```에 의존하도록 만드는 것이 더 효과적이다.  

## 명시적인 의존성
```java
public class Movie {
    private DiscountPolicy discountPolicy; // (1)
    
    public Movie(String title, Duration runningTime, Money fee) {
        //...
        this.discountPolicy = new AmountDiscountPolicy(...); // (2)
    }
}
```

위의 코드는 한 가지 실수로 인해 결합도가 불필요하게 높아졌다. 

(1) : Movie의 인스턴스 변수인 discountPolicy는 추상 클래스인 DiscountPolicy 타입으로 선언돼 있다.  

(2) : 하지만 생성자에서 구체 클래스인 AmountDiscountPolicy의 인스턴스를 직접 생성해서 대입하고 있다. 이 코드 때문에 Movie는 DiscountPolicy뿐만 아니라 AmountDiscountPolicy에도 의존하게 된다.  

여기서 알 수 있는 것처럼 인스턴스 변수의 타입을 추상 클래스나 인터페이스로 변경하는 것만으로는 의존성을 개선할 수 없다. 클래스 안에서 구체적인 클래스에 대한 모든 의존성을 제거해야만 한다.  

의존성을 해결하는 방법에는 ```생성자```, ```setter 메서드```, ```메서드 인자```를 사용하는 세 가지 방식이 존재한다. 여기서의 트릭은 **인스턴스 변수의 타입은 추상 클래스나 인터페이스로 정의하고 생성자, setter 메서드, 메서드 인자로 의존성을 해결할 때는 추상 클래스를 상속받거나 인터페이스를 실체화한 구체적인 클래스를 전달하는 것이다.** 

### 방법 1: 생성자
```java
public class Movie {
    private DiscountPolicy discountPolicy;
    
    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        //...
        this.discountPolicy = discountPolicy;
    }
}
```

```java
Movie avatar = new Movie(..., new AmountDiscountPolicy(...));
Movie starWars = new Movie(..., new PercentDiscountPolicy(...));
```

생성자가 호출되는 시점에 1회 호출된다. 전달하는 객체가 변하지 않거나, 반드시 객체가 필요한 경우에 강제하기 위해 사용한다. 

### 방법 2: setter 메서드
```java
public class Movie {
    private DiscountPolicy discountPolicy;
    
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

```java
Movie avatar = new Movie(...);
avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
avatar.setDiscountPolicy(new PercentDiscountPolicy(...));
```

의존 대상 객체가 변경될 가능성이 있는 경우에 사용한다. 객체가 생성된 후에 협력 대상을 설정하기 때문에 의존 대상을 설정하기 전까지는 객체의 상태가 불완전하여 NPE를 발생시킬 수 있다. 

### 방법 3: 메서드 인자
```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Money calculateMovieFee(Screening screening, DiscountPolicy discountPolicy) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

```java
Movie avatar = new Movie(...);
avatar.calculateMovieFee(..., new AmountDiscountPolicy(...));
```

일반 메서드를 통해 의존성을 주입한다.

## new는 해롭다
의존할 대상인 구체적인 클래스의 생성을 클라이언트로 위임했다. 만약 Movie 클래스 내부에서 구체적인 클래스를 만들면 어떻게 될까?  

```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        //...
        this.discountPolicy = new AmountDiscountPolicy(
            Money.wons(800),
            new SequenceCondition(1),
            new SequenceCondition(10),
            new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime.of(11, 59)),
            new PeriodCondition(DayOfWeek.THURSDAY, LocalTime.of(10, 0), LocalTime.of(20, 59))
        );
    }
}
```

Movie 클래스가 AmountDiscountPolicy의 인스턴스를 생성하기 위해서는 생성자에 전달되는 모든 인자를 알고 있어야 한다. 이는 Movie 클래스가 알아야 하는 지식의 양을 늘리고 Movie와 AmountDiscountPolicy의 결합도가 더욱 강해진다.  

따라서, **인스턴스를 생성하는 로직과 생성된 인스턴스를 사용하는 로직을 분리**해야 한다. AmountDiscountPolicy를 사용하는 Movie는 인스턴스를 생성해서는 안 된다. Movie는 외부로부터 이미 생성된 AmountDiscountPolicy의 인스턴스를 전달받아서 그 인스턴스를 사용하기만 해야 한다.

> **의존성 주입(Dependency Injection, DI)**  
> **의존성 주입의 의도는 객체의 생성과 사용의 관심을 분리하는 것이다.** 이는 가독성과 코드 재사용을 높여준다. 의존성 주입 프레임워크에는 Spring, Guice 등이 있다. (중략) 의존성 주입의 이점은 다음과 같다. 의존 관계 설정이 컴파일시가 아닌 실행시에 이루어져 모듈들간의 결합도를 낮출 수 있다. 코드 재사용을 높여서 작성된 모듈을 여러 곳에서 소스 코드의 수정 없이 사용할 수 있다. 모의 객체 등을 이용한 단위 테스트의 편의성을 높여준다.  
> 출처 : [위키피디아 - 의존성 주입](https://ko.wikipedia.org/wiki/%EC%9D%98%EC%A1%B4%EC%84%B1_%EC%A3%BC%EC%9E%85)  

```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

```java
Movie avatar = new Movie(
    "아바타",
    Duration.ofMinutes(120),
    Money.wons(10000),
    new AmountDiscountPolicy(
        Money.wons(800),
        new SequenceCondition(1),
        new SequenceCondition(10),
        new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime(11, 59)),
        new PeriodCondition(DayOfWeek.THURSDAY, LocalTime.of(10, 0), LocalTime(20, 59))
    ));
```

사용과 생성의 책임을 분리하면 Movie의 결합도가 낮아지고 설계를 유연하게 만들 수 있다.  

가끔은 클래스 안에서 객체의 인스턴스를 직접 생성하는 방식이 유용한 경우도 있다. 협력하는 기본 객체를 설정하고 싶은 경우가 여기에 속한다.  

```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        this(title, runningTime, fee, new AmountDiscountPolicy(...));
    }
    
    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        //...
        this.discountPolicy = discountPolicy;
    }
}
```

만약 Movie가 대부분의 경우 AmountDiscountPolicy의 인스턴스와 협력하고 아주 가끔 PercentDiscountPolicy의 인스턴스와 협력한다면, 객체의 생성자 안에서 AmountDiscountPolicy의 인스턴스를 생성하도록 구성할 수 있다. 이 방법은 메서드 오버로딩에도 사용할 수 있다.

```java
public class Moive {
    public Movie calculateMovieFee(Screening screening) {
        return calculateMovieFee(screening, new AmountDiscountPolicy(...));
    }
    
    public Movie calculateMovieFee(Screening screening, DiscountPolicy discountPolicy) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

## 컨텍스트 확장하기
이제 Movie가 유연하고 재사용 가능한 설계라는 사실을 입증하기 위해 Movie를 확장하는 두 가지 예를 살펴 보자.

### 확장 1: 할인 혜택을 제공하지 않는 영화
가장 쉽게 생각할 수 있는 방법은 discountPolicy에 null 값을 할당하는 것이다.

```java
public class Movie {
    public Movie(String title, Duration runningTime, Money fee) {
        this(title, runningTime, fee, null); // (1)
    }
    
    public Moive(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        //...
        this.discountPolicy = discountPolicy;
    }
    
    public Money calculateMovieFee(Screening screening) {
        if (discountPolicy == null) { // (2)
            return fee;
        }

        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

(1) : 기본값으로 null을 할당하고 있다.  
(2) : calculateMovieFee() 내부에서 discountPolicy의 값이 null인지 체크하고 null인 경우 할인 정책을 적용하지 않는다.  

이 코드는 제대로 동작하지만 한 가지 문제가 있다. 지금까지의 Movie와 DiscountPolicy 사이의 협력 방식에 어긋나는 예외 케이스가 추가된 것이다. 이를 위해 Movie의 내부 코드를 직접 수정해야 했다. **어떤 경우든 코드 내부를 직접 수정하는 것은 버그의 발생 가능성을 높이는 행동이다.** 더 나은 해결책은 예외 케이스로 처리하지 않고 기존 협력 방식을 따르도록 만드는 것이다. 다시 말해 '할인 정책이 존재하지 않음'을 할인 정책의 한 종류로 간주하는 것이다.  

```java
public class NoneDiscountPolicy extends DiscountPolicy {
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return Money.ZERO;
    }
}
```

```java
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000), new NoneDiscountPolicy());
```

이제 Movie에서 예외 케이스를 위한 if 문을 추가하지 않고도 요구사항을 구현할 수 있게 되었다. 

### 확장 2: 중복 적용이 가능한 할인 정책
중복 할인이란 금액 할인 정책과 비율 할인 정책을 혼합해서 적용하는 정책을 의미한다. 가장 간단하게 구현할 수 있는 방법은 Movie가 DiscountPolicy의 인스턴스들로 구성된 List를 인스턴스 변수로 갖게 하는 것이다. 하지만 이 방법은 기존의 할인 정책의 협력방식과는 다른 예외 케이스를 추가하게 만든다. 따라서 확장 1과 같은 방법을 사용해서 해결할 수 있다. 중복 할인 정책을 할인 정책의 한 종류로 간주하는 것이다. OverlappedDiscountPolicy를 DiscountPolicy의 자식 클래스로 만들어 기존의 Movie와 DiscountPolicy 사이의 협력 방식을 수정하지 않고도 요구사항을 구현할 수 있다. 

```java
public class OverlappedDiscountPolicy extends DiscountPolicy {
    private List<DiscountPolicy> discountPolicies = new ArrayList<>();

    public OverlappedDiscountPolicy(DiscountPolicy ... discountPolicies) {
        this.discountPolicies = Arrays.asList(discountPolicies);
    }
    
    @Override 
    protected Money getDiscountAmount(Screening screening) {
        Money result = Money.ZERO;
        for (DiscountPolicy each : discountPolicies) {
            result = result.plus(each.calculateDiscountAmount(screening));
        }
        return result;
    }
}
```

```java
Movie avatar = new Movie("아바타", Money.wons(10000), new OverlappedDiscountPolicy(new AmountDiscountPolicy(...), new PercentDiscountPolicy(...)));
```

## 결론
1. 구체 클래스보다 추상 클래스에, 추상 클래스보다 인터페이스에 의존하도록 만들자.
2. 생성자, setter 메서드, 메서드 인자를 통해 구체 클래스에 대한 의존성을 제거하고 클래스 생성 책임을 클라이언트로 위임할 수 있다.

## 참고
https://mangkyu.tistory.com/150  
https://mangkyu.tistory.com/125  
