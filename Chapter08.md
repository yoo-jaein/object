# Chapter 08 의존성 관리하기

## 협력과 의존성
협력은 필수적이지만 과도한 협력은 설계를 어지럽힌다. 다른 객체와 협력하기 위해서는 그 객체가 존재한다는 사실을 알고 있어야 한다. 그 객체가 수신할 수 있는 메시지에 대해서도 알고 있어야 한다. 이런 지식이 객체 사이의 '의존성'을 낳는다. 협력을 위해서는 의존성이 필요하지만 과도한 의존성은 애플리케이션을 수정하기 어렵게 만든다. 8장에서는 협력적이면서도 유연한 객체를 만들기 위해 의존성을 관리하는 방법을 살펴 보자.

## 의존성
의존성은 실행 시점과 구현 시점에 서로 다른 의미를 가진다.  

- 실행 시점 : 의존하는 객체가 정상적으로 동작하기 위해서는 실행 시점에 의존 대상 객체가 반드시 존재해야 한다.
- 구현 시점 : 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다.

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

(1) : 실행 시점에 PeriodCondition의 인스턴스가 정상적으로 동작하기 위해서는 Screening의 인스턴스가 존재해야 한다. 이처럼 어떤 객체가 작업을 정상 수행하기 위해 다른 객체를 필요로 하는 경우 '두 객체 사이에 의존성이 존재한다'고 말한다. 의존성은 단방향이다. Screening이 변경될 때 PeriodCondition이 영향을 받게 되지만 그 역은 성립하지 않는다. 이 경우 PeriodCondition은 Screening에 의존한다.

(2) : PeriodCondition은 DayOfWeek, LocalTime의 인스턴스를 속성으로 포함한다. 만약 DayOfWeek, LocalTime의 클래스 이름을 변경하면 PeriodCondition에 정의된 인스턴스 변수의 타입 선언도 함께 수정해야 한다. PeriodCondition은 DayOfWeek, LocalTime에 의존하고 있다.

(3) : DiscountCondition 인터페이스의 이름이 변경되면 어떻게 될까? DiscountCondition에 선언된 isSatisfiedBy 오퍼레이션의 시그니처가 변경되면 어떻게 될까? DiscountCondition을 구현하는 PeriodCondition 클래스도 함께 수정해야 할 것이다. PeriodCondition은 DiscountCondition에도 의존하고 있다.

> **UML과 의존성**
> 여기서 말하는 의존성은 UML(Unified Modeling Language)에서 정의하는 의존 관계와 조금 다르다. UML에서는 두 요소 사이의 관계로 실체화 관계, 연관 관계, 의존 관계, 일반화/특수화 관계, 합성 관계, 집합 관계 등을 정의한다. 의존성은 두 요소 사이에 변경에 의해 영향을 주고 받는 관계가 존재한다는 것에 초점을 맞춘다. UML을 기준으로 PeriodCondition과 DiscountCondition을 봤을 때 실체화 관계라고 말할 수 있지만, 두 요소 사이에는 의존성이 존재한다. 의존성은 UML에서 정의하는 모든 관계가 가지는 공통적인 특성으로 봐야 한다.

## 의존성 전이

## 런타임 의존성과 컴파일타임 의존성

## 컨텍스트 독립성

## 의존성 해결하기

## 의존성과 결합도

## 추상화에 의존하라

## 명시적인 의존성

## new는 해롭다

## 컨텍스트 확장하기

## 결론
