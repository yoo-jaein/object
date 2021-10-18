# Chapter 14 일관성 있는 협력

## 일관성 없는 코드

## 일관성 없는 코드의 문제점
### 1. 점점 더 어긋나는 구현 방식
- 공통점이 있는데도 구현 방식은 완전히 다른 경우 
- 프로젝트의 전체적인 일관성이 점점 더 떨어지게 된다.

### 2. 이해하기 어려운 코드
- 서로 다른 구현 방식은 코드를 이해하기 더욱 어렵게 만든다.
- 대부분의 사람들은 유사한 요구사항을 구현하는 코드는 유사한 방식으로 구현될 것 이라고 예상한다. 만약 유사한 요구사항이 서로 다른 방식으로 구현돼 있다면 코드를 이해하는데 심리적인 장벽을 만든다. 

## 일관성 있는 설계를 하려면
1. 다양한 설계 경험을 익혀야 한다.
2. 널리 알려진 디자인 패턴을 학습하고 이를 적용해봐야 한다.

## 조건 로직 vs 객체 이동
### 절차지향
```java
if (...) {

} else if (...) {

} else {

}
```

조건 로직 설계가 나쁜 이유는 변경의 주기가 서로 다른 코드가 한 클래스 안에 뭉쳐있기 때문이다. 새로운 조건을 추가하는 경우 이 코드의 내부를 수정해야 하기 때문에 오류가 발생할 확률도 높다. 이런 조건문의 분기 로직은 절차지향 프로그램에서 변경을 처리하는 방법이다.

### 객체지향
```java
fee.minus(discountPolicy.calculateDiscountAmount(screening));
```

객체지향에서 변경을 다루는 전통적인 방법은 객체 사이의 이동이다. 정책의 종류를 체크하는 조건문이 discountPolicy로의 객체 이동으로 대체된 것이다.

다형성은 객체 이동을 위해 객체지향이 제공하는 설계 기법이다. 

## 일관성 있는 협력을 위한 지침
### 1. 변하는 개념을 변하지 않는 개념으로부터 분리하라.

### 2. 변하는 개념을 캡슐화하라.

## 캡슐화
1. 데이터 캡슐화
2. 메서드 캡슐화
3. 객체 캡슐화
4. 서브타입 캡슐화

## 지속적으로 개선하라
설계의 일관성에 금이 가는 것은 자연스러운 현상이다. 협력은 고정된 것이 아니다. 요구사항의 변경에 따라 협력도 지속적으로 개선해야 한다.