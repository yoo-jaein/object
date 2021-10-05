# Chapter 12 다형성

## 다형성
다형성(Polymorphism)이란 여러 타입을 대상으로 동작할 수 있는 코드를 작성할 수 있는 방법이다.

- 유니버설 다형성
  - 매개변수 다형성 : 타입을 임의의 타입으로 선언한 후 사용하는 시점에 구체적인 타입으로 지정하는 경우
  - 포함 다형성 : 메시지가 동일하더라도 수신한 객체의 타입에 따라 실제 수행되는 행동이 다른 경우
- 임시 다형성
  - 오버로딩 다형성 : 하나의 클래스 안에 동일한 이름의 메서드가 존재하는 경우
  - 강제 다형성 : 동일한 연산자를 다양한 타입에 사용할 수 있는 경우

## 상속
### 업캐스팅

```java
Lecture lecture = new GradeLecture(...); // 업캐스팅
```

- 부모 클래스(Lecture) 타입으로 선언된 변수에 자식 클래스(GradeLecture)의 인스턴스를 할당하는 것

### 다운캐스팅

```java
Lecture lecture = new GradeLecture(...);
GradeLecture gradeLecture = (GradeLecture) lecture; // 다운캐스팅
```

- 부모 클래스(Lecture)의 인스턴스를 자식 클래스(GradeLecture)으로 변환하기 위해 명시적인 타입 캐스팅을 하는 것

## 바인딩
### 정적 바인딩
- 컴파일타임에 호출할 함수를 결정하는 방식
- 정적 바인딩(static binding), 초기 바인딩(early binding), 컴파일타임 바인딩(compile-time binding)

### 동적 바인딩
- 런타임에 호출할 함수를 결정하는 방식
- 동적 바인딩(dynamic binding), 지연 바인딩(late binding), 런타임 바인딩(runtime binding)
