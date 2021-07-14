# Chapter 01 객체, 설계

## 소프트웨어 모듈의 세 가지 목적
로버트 마틴 <클린 소프트웨어: 애자일 원칙과 패턴, 그리고 실천 방법>, 모든 소프트웨어 모듈에는 세 가지 목적이 있다.
1. 제대로 동작한다.
2. 변경이 용이해야 한다.
3. 의사소통하기 쉬워야 한다.

## 의존성
객체 사이에는 의존성(dependency) 문제가 있다. 의존성은 변경에 대한 영향을 암시한다. 즉, 어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함께 변경될 수 있다는 뜻이다.  

객체 사이의 의존성을 완전히 없애는 것이 정답은 아니다. 객체지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것이다. 따라서 우리의 목표는 애플리케이션의 기능을 구현하는 데 필요한 최소한의 의존성만 유지하고 불필요한 의존성을 제거하는 것이다.  

## 결합도
객체 사이의 의존성이 과한 경우를 가리켜 결합도(coupling)가 높다고 말한다. 결합도는 의존성과 관련돼 있기 때문에 결합도 역시 변경과 관련이 있다. 두 객체 사이의 결합도가 높으면 높을수록 함께 변경될 확률이 높다. 따라서 객체 사이의 결합도를 낮춰야 한다.  

## 자율성을 높이자
결합도가 높은 코드를 개선하기 위해, 각각의 객체를 자율적인 존재로 만들어 보자.

### Before
```java
public class Theater {
  private TicketSeller ticketSeller;
  
  public Theater(TicketSeller ticketSeller) {
    this.ticketSeller = ticketSeller;
  }
  
  public void enter(Audience audience) {
    if (audience.getBag().hasInvitation()) {
      Ticket ticket = ticketSeller.getTicketOffice().getTicket();
      audience.getBag().setTicket(ticket);
    } else {
      Ticket ticket = ticketSeller.getTicketOffice().getTicket();
      audience.getBag().minusAmount(ticket.getFee());
      ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
      audience.getBag().setTicket(ticket);
    }
  }
}
```   

```java
public class TicketSeller {
  private TicketOffice ticketOffice;
  
  public TicketSeller(TicketOffice ticketOffice) {
    this.ticketOffice = ticketOffice;
  }
  
  public TicketOffice getTicketOffice() {
    return ticketOffice;
  }
}
```

### After
```java
public class Theater {
  private TicketSeller ticketSeller;
  
  public Theater(TicketSeller ticketSeller) {
    this.ticketSeller = ticketSeller;
  }
  
  public void enter(Audience audience) {
    ticketSeller.sellTo(audience);
  }
}
```   

```java
public class TicketSeller {
  private TicketOffice ticketOffice;
  
  public TicketSeller(TicketOffice ticketOffice) {
    this.ticketOffice = ticketOffice;
  }
  
  public void sellTo(Audience audience) {
    if (audience.getBag().hasInvitation()) {
      Ticket ticket = ticketOffice.getTicket();
      audience.getBag().setTicket(ticket);
    } else {
      Ticket ticket = ticketOffice.getTicket();
      audience.getBag().minusAmount(ticket.getFee());
      ticketOffice.plusAmount(ticket.getFee());
      audience.getBag().setTicket(ticket);
    }
  }
}
```

이제 ticketOffice에 대한 접근은 오직 TicketSeller 안에서만 존재한다. Theater에서는 더이상 ticketOffice에 접근하지 못한다. Theater는 단지 ticketSeller가 sellTo 메시지를 이해하고 응답할 수 있다는 사실만 알고 있다. 이렇게 객체 내부의 세부적인 사항을 감추는 것을 캡슐화(encapsulation)라한다. 캡슐화를 통해 접근을 제한하면 객체 사이의 결합도를 낮출 수 있다.  

## 인터페이스와 구현
Theater는 오직 TicketSeller의 인터페이스(interface)에만 의존한다. TicketSeller가 내부에 TicketOffice 인스턴스를 포함하고 있다는 사실은 구현(implementation)의 영역에 속한다. 객체를 인터페이스와 구현으로 나누고 인터페이스만을 공개하는 것은 가장 기본적인 설계 원칙이다.  

## 응집도
밀접하게 연관된 작업만 수행하고 그렇지 않은 작업은 다른 객체에 위임하는 객체를 가리켜 응집도(cohesion)가 높다고 말한다. 자율성이 높은 객체가 곧 결합도가 낮고 응집도가 높은 객체이다.

## 책임의 이동
절차지향과 객체지향의 근본적인 차이는 책임의 이동(shift of responsibility)이다. 절차지향은 책임이 Theater 하나에 집중되어 있지만, 객체지향은 각 객체에 책임이 적절하게 분배된다. 각 객체는 자신을 스스로 책임져야 한다. 

## 트레이드오프
때때로 자율성을 챙기기 위해 기존에 없던 의존성이 추가되어 버린다. 이 때는 의존성을 낮추는 것이 자율성을 보장하는 것보다 중요하다. 훌륭한 설계는 적절한 트레이드오프의 결과물이다.  

## 결론
훌륭한 객체지향 설계란 협력하는 객체 사이의 의존성을 적절하게 관리하는 설계다.



