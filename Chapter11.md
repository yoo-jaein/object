# Chapter 11 합성과 유연한 설계

## 상속과 합성

### 상속
- 부모 클래스를 재사용한다.  
  -> 제대로 활용하려면 부모 클래스의 내부 구현에 대해 상세하게 알아야 한다.  
  -> **자식 클래스와 부모 클래스의 결합도가 높아진다.**  
- ```컴파일 타임 의존성```, 정적인 관계
- ```is-a``` 관계

### 합성
- 합성되는 객체의 구현이 아닌 퍼블릭 인터페이스에 의존한다.  
  -> **포함된 객체의 내부 구현이 변경되더라도 영향을 최소화한다.**  
- ```런타임 의존성```, 동적인 관계
- ```has-a``` 관계

## 상속을 합성으로 변경하기
### 1. 불필요한 인터페이스 상속 문제

#### java.util.Properties
```java
public class Properties {
    private Hashtable<String, String> properties = new Hashtable<>();

    public String setProperty(String key, String value) {
        return properties.put(key, value);
    }

    public String getProperty(String key) {
        return properties.get(key);
    }
}
```
- 이제 Properties의 클라이언트는 Hashtable의 오퍼레이션을 사용할 수 없기 때문에 Properties의 규칙을 어길 위험성이 사라졌다.

### 2. 메서드 오버라이딩의 오작용 문제
#### InstrumentedHashSet
```java
public class InstrumentedHashSet<E> implements Set<E> {
    private int addCount = 0;
    private Set<E> set;
    
    public InstrumentedHashSet(Set<E> set) {
        this.set = set;
    }
    
    @Override
    public boolean add(E e) {
        addCount++;
        return set.add(e); 
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }
    
    public int getAddCount() {
        return addCount; 
    }
    
    @Override
    public boolean remove(Object o) {
        return set.remove(o); 
    }
    
    @Override
    public void clear() {
        set.clear();
    }
    //...
}
```

- ```포워딩``` : 기존 클래스의 인터페이스를 그대로 외부에 제공하면서 구현에 대한 결합 없이 일부 작동 방식을 변경하고 싶은 경우에 사용할 수 있는 기법

### 3. 부모 클래스와 자식 클래스의 동시 수정 문제
#### PersonalPlaylist
```java
public class PersonalPlaylist {
    private Playlist playlist = new Playlist();
    
    public void append(Song song) {
        playlist.append(song); 
    }
    
    public void remove(Song song) {
        playlist.getTracks().remove(song);
        playlist.getSingers().remove(song.getSinger()); 
    }
}
```

- 합성으로 변경하더라도 Playlist와 PersonalPlaylist를 모두 수정해야한다. 그렇다고 하더라도 여전히 상속보다 합성을 사용하는 것이 더 좋다. 향후에 Playlist의 내부 구현을 변경하더라도 파급효과를 최대한 PersonalPlaylist 내부로 캡슐화할 수 있기 때문이다.

## 클래스 폭발 / 조합의 폭발
- Class explosion / Combinational explosion
- 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스를 추가해야 하는 문제.
- 부모 클래스와 자식 클래스 간 결합도가 높은 **상속의 근본적인 한계 때문에 발생**한다. 
- 컴파일타임에 이미 결정된 관계는 변경될 수 없다. 부모 클래스와 자식 클래스의 다양한 조합이 필요한 상황에서 유일한 해결 방법은 조합의 수만큼 새로운 클래스를 추가하는 것이다.
- **이를 해결하려면 상속을 포기해야 한다.**

## 합성 관계로 변경하기
```java
Phone phone1 = new Phone(new TaxablePolicy(0.05, new RegularPolicy(...));
Phone phone2 = new Phone(new TaxablePolicy(0.04, new RateDiscountablePolicy(Money.wons(1000)), new RegularPolicy(...)));
Phone phone3 = new Phone(new RateDiscountablePolicy(Money.wons(1000), new TaxablePolicy(0.05, new RegularPolicy(...)));
```

- 합성은 구성 요소들을 개별 클래스로 구현한 후 **실행 시점에 인스턴스를 조립하는 방법을 사용한다.**
- 따라서 컴파일 의존성에 제한되지 않고 **다양한 방식의 런타임 의존성**을 구성할 수 있다는 것이 장점이다.
- 또한 ```단일 책임 원칙```을 준수하기 때문에 요구사항을 변경할 때 하나의 클래스만 수정해도 된다.

## 믹스인
- Mix-in
- 이 용어는 미국의 한 아이스크림 가게에서 영감을 받았다. 아이스크림 가게의 주인은 기본적인 맛(바닐라, 초콜릿 등)과 추가적인 재료(땅콩, 쿠키, 사탕 등)의 조합으로 버무려진 아이스크림을 판매했다. "믹스인 (mix-in)"이라고 불린 이 아이스크림은 재료의 조합에 따라 아이스크림 결과가 달라진다.
- 컴파일시점에 코드 조각을 조합하여 코드를 재사용하는 기법이다.

### 상속과 믹스인의 차이점
- 상속은 자식 클래스를 부모 클래스와 **동일한 범주로 묶어 is-a 관계를 만들기 위한 것이다.** 
- 믹스인은 말 그대로 코드를 다른 코드에 섞어 넣기 위한 방법이다. 일반적으로 **믹스인은 is-a 관계를 만들지 않고 원하는 기능만 하위 클래스로 전달한다.**

### 스칼라
> 믹스인(코드를 섞는) 방법은 언어마다 다르다. **그 방법이 무엇이건 코드를 다른 코드 안에 유연하게 섞어 넣을 수 있다면 믹스인이라고 부를 수 있다.**

```scala
abstract class BasicRatePolicy {

    def calculateFee(phone: Phone): Money =
        phone.calls.map(calculateCallFee(_)).reduce(_+_)

    protected def calculateCallFee(call: Call): Money;
}
```

```scala
class RegularPolicy(val amount: Money, val seconds: Duration) extends BasicRatePolicy {

    override protected def calculateCallFee(call: Call): Money =
        amount * (call.duration.getSeconds / seconds.getSeconds)
}
```

```scala
class NightlyDiscountPolicy(
    val nightlyAmount: Money,
    val regularAmount: Money,
    val seconds: Duration) extends BasicRatePolicy {

    override protected def calculateCallFee(call: Cal): Money =

    if (call.from.getHour >= NightlyDiscountPolicy.LateNightHour) {
        nightlyAmount * (call.duration.getSeconds / seconds.getSeconds)
    } else {
        regularAmount * (call.duration.getSeconds / seconds.getSeconds)
    }
}

object NightlyDiscountPolicy {
    val LateNightHour: Integer = 22
}
```

- 스칼라에서는 다른 코드와 조합해서 확장할 수 있는 기능을 ```트레이트```(trait)로 구현한다.

```scala
trait TaxablePolicy extends BasicRatePolicy { // (1)

    def taxRate: Double
    
    override def calculateFee(phone: Phone): Money = {
        val fee = super.calculateFee(phone)
        return fee + fee * taxRate
    }
}
```

(1) : TaxablePolicy 트레이트는 BasicRatePolicy를 확장하고 있다. **여기서의 ```extends```는** 상속의 개념이 아니라 TaxablePolicy가 BasicRatePolicy나 그의 자손에 해당하는 경우에만 믹스인될 수 있다는 것을 의미한다. **단지 TaxablePolicy가 사용될 수 있는 문맥을 제한할 뿐이지 TaxablePolicy가 BasicRatePolicy의 자식 트레이트가 되는 것은 아니다.**

```scala
trait RateDiscountablePolicy extends BasicRatePolicy {

    val discountAmount: Money
    
    override def calculateFee(phone: Phone): Money = {
        val fee = super.calculateFee(phone)
        fee - discountAmount
    }
}
```

- 스칼라에서는 트레이트를 클래스나 다른 트레이트에 믹스인할 수 있도록 ```extends```와 ```with``` 키워드를 제공한다. 믹스인하려는 대상 클래스의 부모 클래스가 존재하는 경우 부모 클래스는 ```extends```를 이용해 상속받고 트레이트는 ```with```를 이용해 믹스인한다.

```scala
class TaxableRegularPolicy(
    amount: Money, 
    seconds: Duration, 
    val taxRate: Double)
  extends RegularPolicy(amount, seconds)
  with TaxablePolicy
```

- RegularPolicy 클래스를 상속받고 TaxablePolicy 트레이트를 믹스인한 새로운 클래스 TaxableRegularPolicy를 만들었다.
- TaxableRegularPolicy 인스턴스에 calculateFee 메시지를 전송하면 스칼라는 클래스와 트레이트를 선형화해서 어떤 메서드를 호출할지 결정한다. 

! calculateFee 메시지 ! ```TaxableRegularPolicy``` -> **```TaxablePolicy```** -> ```RegularPolicy``` -> **```BasicRatePolicy```**

```scala
class RateDiscountableAndTaxableRegularPolicy(
    amount: Money,
    seconds: Duration,
    val discountAmount: Money,
    val taxRate: Double)
  extends RegularPolicy(amount, seconds)
  with TaxablePolicy
  with RateDiscountablePolicy
```

```scala
class TaxableAndRateDiscountableReqularPolicy(
    amount: Money,
    seconds: Duration,
    val discountAmount: Money,
    val taxRate: Double)
  extends RegularPolicy(amount, seconds)
  with RateDiscountablePolicy
  with TaxablePolicy
```

- 세금 정책(TaxablePolicy)과 비율 할인 정책(RateDiscountablePolicy)는 임의의 순서에 따라 조합될 수 있어야 한다. 이 경우에도 선형화를 통해 간단히 해결할 수 있다. 

### 자바
- Effective Java : 믹스인은 다른 클래스에서 이용할 기능(메서드)들을 포함하는 타입이다. 자바에서는 다중 상속의 문제가 없는 ```인터페이스```가 믹스인에 해당한다.

```java
public interface Singer {
    AudioClip sing(Song s);
}
```

```java
public interface Songwriter {
    Song compose();
}
```

```java
public interface SingerSongwriter extends Singer, Songwriter {
    AudioClip strum();
}
```

## 결론
1. 객체 합성이 클래스 (구현) 상속보다 더 좋은 방법이다.

## 참고
https://en.wikipedia.org/wiki/Mixin  
