---
layout: post
title: "헤드퍼스트 디자인패턴 정리"
image: '/assets/img/'
description: 'Design Pattern Summary'
tags:
- Design Pattern
categories:
- Programming
---

블로그 포스팅이 너무 오랜만이네요. 사내 디자인 패턴 스터디를 진행하면서 어렴풋이 알고 있던 디자인 패턴에 대해서 다시 한번 정리할 기회가 생겼습니다.
[헤드 퍼스트 디자인 패턴](https://search.shopping.naver.com/book/catalog/32473420628?cat_id=50010881&frm=PBOKMOD&query=%ED%97%A4%EB%93%9C+%ED%8D%BC%EC%8A%A4%ED%8A%B8+%EB%94%94%EC%9E%90%EC%9D%B8+%ED%8C%A8%ED%84%B4&NaPm=ct%3Dlr8h8jw8%7Cci%3D8c7de3f506cb8782ca01ec92e52605761d6ab849%7Ctr%3Dboknx%7Csn%3D95694%7Chk%3D461a009dbdfcd348d68ff2c2a63f1acbe9f58563) 를 책을 읽고 공부하면서 알게 된 부분에 대해서 정리해 보고자 합니다.

---

## 전략패턴

알고리즘군을 정의하고 캡슐화해서 각각의 알고리즘군을 `교체`해서 쓸 수 있게 해 줍니다. 전략 패턴을 사용하면 클라이언트로부터 알고리즘을 분리해서 독립적으로 변경할 수 있습니다.

전략 패턴은 여러 상황에서 유용하게 적용됩니다. 예를 들어, 동일한 문제를 해결하는 여러 알고리즘이 존재하고,
이 알고리즘들을 동적으로 교환하여 사용하고자 할 때 전략 패턴이 효과적입니다. 전략 패턴은 코드의 재사용성과 확장성을 높여주며, 알고리즘을 캡슐화함으로써 코드의 유지보수성을 향상시킵니다.

1. 전략(Strategy): 알고리즘을 나타내는 인터페이스 혹은 추상 클래스를 정의합니다. 각각의 구체적인 전략은 이 인터페이스를 구현하게 됩니다.
2. 구체적인 전략(Concrete Strategy): 전략 인터페이스를 실제로 구현한 클래스들로, 클라이언트는 이 중 하나를 선택하여 사용합니다.
3. 컨텍스트(Context): 전략을 사용하는 클라이언트를 나타냅니다. 컨텍스트는 전략 객체를 가지고 있고, 필요에 따라 전략을 변경할 수 있습니다.

```kotlin
package strategy

// 전략(Strategy)
interface TradingMethod {
    fun trading()
}

// 구체적인 전략(Concrete Strategy)
class MovingAverage: TradingMethod {
    override fun trading() {
        println("이동평균선 매매")
    }
}

// 구체적인 전략(Concrete Strategy)
class RSI: TradingMethod {
    override fun trading() {
        println("상대강도지수 매매")
    }
}

// 구체적인 전략(Concrete Strategy)
class BollingerBands: TradingMethod {
    override fun trading() {
        println("볼린저밴드 매매")
    }
}

// 컨텍스트(Context)
class Trading(private val tradingMethod: TradingMethod) {
    fun trading() {
        tradingMethod.trading()
    }
}

fun main() {
    val movingAverage: TradingMethod = MovingAverage()
    val rsi: TradingMethod = RSI()
    val bollingerBands: TradingMethod = MovingAverage()
    val trading = Trading(rsi)

    trading.trading()
}
```
---

## 옵저버 패턴

 한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체에게 연락이 가고 자동으로 내용이 갱신되는 방식으로 일대다의 의존성을 정의합니다.

옵저버 패턴은 주로 분산 이벤트 핸들링 시스템이나 MVC(Model-View-Controller) 패턴에서 사용됩니다.
이 패턴을 사용하면 주체와 옵저버들 간의 결합도를 낮추고, 확장성을 높이며, 객체 간의 상호 작용을 느슨하게 만듭니다.

1. 주체(Subject): 상태를 갖고 있는 객체로, 옵저버들을 등록하고 알림을 보내는 주체입니다.
2. 옵저버(Observer): 주체의 상태 변경을 감시하고 이에 반응하는 객체입니다. 여러 옵저버가 등록될 수 있습니다.
3. 구체적인 주체(Concrete Subject): 주체를 구체화한 클래스로, 상태를 저장하고 변경시 옵저버들에게 알림을 보내는 역할을 합니다.
4. 구체적인 옵저버(Concrete Observer): 옵저버를 구체화한 클래스로, 주체의 상태 변경에 대한 처리를 구현합니다.

```kotlin
import java.util.*

// 1. 주체(Subject) 인터페이스
interface Subject {
    fun addObserver(observer: Observer)
    fun removeObserver(observer: Observer)
    fun notifyObservers()
}

// 2. 옵저버(Observer) 인터페이스
interface Observer {
    fun update(price: Double)
}

// 3. 구체적인 주체(Concrete Subject) 클래스
class StockPriceTracker : Subject {
    private val observers = mutableListOf<Observer>()
    private var currentPrice: Double = 0.0

    fun setPrice(price: Double) {
        currentPrice = price
        notifyObservers()
    }

    override fun addObserver(observer: Observer) {
        observers.add(observer)
    }

    override fun removeObserver(observer: Observer) {
        observers.remove(observer)
    }

    override fun notifyObservers() {
        for (observer in observers) {
            observer.update(currentPrice)
        }
    }
}

// 4. 구체적인 옵저버(Concrete Observer) 클래스
class StockDisplay(val stockName: String) : Observer {
    override fun update(price: Double) {
        println("$stockName Stock Price Update: $price")
    }
}

fun main() {
    // 주체 생성
    val stockPriceTracker = StockPriceTracker()

    // 옵저버 생성 및 등록
    val observer1 = StockDisplay("ABC")
    val observer2 = StockDisplay("XYZ")

    stockPriceTracker.addObserver(observer1)
    stockPriceTracker.addObserver(observer2)

    // 주체의 상태 변경 및 옵저버에게 알림
    stockPriceTracker.setPrice(150.0)
    stockPriceTracker.setPrice(160.0)

    // 옵저버 제거
    stockPriceTracker.removeObserver(observer1)

    // 다시 상태 변경 및 옵저버에게 알림
    stockPriceTracker.setPrice(170.0)
}

```
---

## 데코레이터 패턴

객체에 추가 요소를 동적으로 더할 수 있습니다. 데코레이터를 사용하면 서브클래스를 만들 때보다 훨씬 유연하게 기능을 확장할 수 있습니다.

데코레이터 패턴의 핵심 아이디어는 기본 기능을 제공하는 구체적인 컴포넌트를 데코레이터로 감싸고, 필요한 경우 데코레이터를 중첩함으로써 여러 기능을 조합하는 것입니다.

1. 컴포넌트(Component): 인터페이스나 추상 클래스로서, 데코레이터와 구체적인 컴포넌트들이 공통으로 구현해야 하는 메서드를 정의합니다.
2. 구체적인 컴포넌트(Concrete Component): 컴포넌트의 구현 클래스로, 기본 기능을 제공합니다.
3. 데코레이터(Decorator): 컴포넌트와 같은 인터페이스를 구현하고, 컴포넌트를 감싸서 새로운 행동을 추가하는 역할을 합니다.
4. 구체적인 데코레이터(Concrete Decorator): 데코레이터의 구현 클래스로, 실제로 새로운 행동을 추가하거나 기존 행동을 수정합니다.

```kotlin
// 1. 컴포넌트
interface Coffee {
    fun cost(): Double
    fun description(): String
}

// 2. 구체적인 컴포넌트
class SimpleCoffee : Coffee {
    override fun cost(): Double = 5.0

    override fun description(): String = "Simple Coffee"
}

// 3. 데코레이터
abstract class CoffeeDecorator(private val decoratedCoffee: Coffee) : Coffee {
    override fun cost(): Double = decoratedCoffee.cost()
    override fun description(): String = decoratedCoffee.description()
}

// 4. 구체적인 데코레이터
class MilkDecorator(decoratedCoffee: Coffee) : CoffeeDecorator(decoratedCoffee) {
    override fun cost(): Double = super.cost() + 2.0
    override fun description(): String = super.description() + ", Milk"
}

class SugarDecorator(decoratedCoffee: Coffee) : CoffeeDecorator(decoratedCoffee) {
    override fun cost(): Double = super.cost() + 1.0
    override fun description(): String = super.description() + ", Sugar"
}

fun main() {
    // 기본 컴포넌트
    val simpleCoffee: Coffee = SimpleCoffee()
    println("Cost: ${simpleCoffee.cost()}, Description: ${simpleCoffee.description()}")

    // 데코레이터 추가
    val milkCoffee: Coffee = MilkDecorator(simpleCoffee)
    println("Cost: ${milkCoffee.cost()}, Description: ${milkCoffee.description()}")

    val sugarMilkCoffee: Coffee = SugarDecorator(milkCoffee)
    println("Cost: ${sugarMilkCoffee.cost()}, Description: ${sugarMilkCoffee.description()}")
}
```
---

## 팩토리 메소드 패턴

객체를 생성할 때 필요한 인터페이스를 만듭니다. 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정합니다. 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을
서브클래스에게 맡기게 됩니다.

팩토리 메서드 패턴은 객체 생성에 대한 확장이 필요한 경우에 유용하며, 코드의 재사용성과 확장성을 높이는 데 기여합니다.

1. 생성자(Creator): 객체 생성을 위한 인터페이스를 정의하며, 팩토리 메서드를 선언합니다. 팩토리 메서드를 통해 객체를 생성하고 반환합니다. Creator는 주로 추상 클래스나 인터페이스로 정의됩니다.
2. 구체적인 생성자(Concrete Creator): Creator를 상속받아 구현한 클래스로, 팩토리 메서드를 구체화하여 실제 객체를 생성합니다.
3. 제품(Product): 팩토리 메서드를 통해 생성되는 객체의 공통 인터페이스를 정의합니다.
4. 구체적인 제품(Concrete Product): Product를 구현한 클래스로, 실제 객체의 구현을 나타냅니다.

```kotlin
// 1. 제품(Product) 인터페이스
interface Product {
    fun getName(): String
}

// 2. 구체적인 제품(Concrete Product)
class ConcreteProductA : Product {
    override fun getName(): String = "Product A"
}

class ConcreteProductB : Product {
    override fun getName(): String = "Product B"
}

// 3. 생성자(Creator) 인터페이스
interface Creator {
    fun createProduct(): Product
}

// 4. 구체적인 생성자(Concrete Creator)
class ConcreteCreatorA : Creator {
    override fun createProduct(): Product {
        println("Creating Product A")
        return ConcreteProductA()
    }
}

class ConcreteCreatorB : Creator {
    override fun createProduct(): Product {
        println("Creating Product B")
        return ConcreteProductB()
    }
}

fun main() {
    // 사용 예제
    val creatorA: Creator = ConcreteCreatorA()
    val productA: Product = creatorA.createProduct()
    println("Product A Name: ${productA.getName()}")

    val creatorB: Creator = ConcreteCreatorB()
    val productB: Product = creatorB.createProduct()
    println("Product B Name: ${productB.getName()}")
}
```

---

## 추상 팩토리 패턴

구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생상하는 인터페이스를 제공합니다. 구상 클래스는 서브클래스에서 만듭니다.

추상 팩토리 패턴은 클라이언트 코드가 구체적인 클래스를 명시하지 않고도 객체의 생성을 요청할 수 있도록 하며, 객체들 간의 관련성을 강화합니다.
이 패턴은 특히 서로 연관된 객체들의 생성과 조합이 필요한 경우에 유용하게 사용됩니다.

1. 추상 팩토리(Abstract Factory): 관련된 객체들을 생성하는 메서드의 집합을 정의하는 인터페이스입니다. 이는 하나 이상의 추상 메서드로 이루어져 있습니다.
2. 구체적인 팩토리(Concrete Factory): 추상 팩토리를 구현한 클래스로, 실제 객체를 생성하는 책임이 있습니다.
3. 제품(Product): 추상 팩토리가 생성하는 객체의 공통 인터페이스를 정의합니다.
4. 구체적인 제품(Concrete Product): 제품을 구현한 클래스로, 실제 객체의 구현을 나타냅니다.

```kotlin
// 1. 제품(Product) 인터페이스
interface Button {
    fun display()
}

// 2. 구체적인 제품(Concrete Product)
class WindowsButton : Button {
    override fun display() {
        println("Displaying a Windows Button")
    }
}

class MacOSButton : Button {
    override fun display() {
        println("Displaying a MacOS Button")
    }
}

// 3. 추상 팩토리(Abstract Factory) 인터페이스
interface GUIFactory {
    fun createButton(): Button
}

// 4. 구체적인 팩토리(Concrete Factory)
class WindowsFactory : GUIFactory {
    override fun createButton(): Button = WindowsButton()
}

class MacOSFactory : GUIFactory {
    override fun createButton(): Button = MacOSButton()
}

// 클라이언트 코드
fun createUI(factory: GUIFactory) {
    val button: Button = factory.createButton()
    button.display()
}

fun main() {
    // Windows UI 생성
    val windowsFactory: GUIFactory = WindowsFactory()
    createUI(windowsFactory)

    // MacOS UI 생성
    val macOSFactory: GUIFactory = MacOSFactory()
    createUI(macOSFactory)
}
```

---

## 팩토리 메서드 패턴과 추상 팩토리 패턴의 차이

#### 팩토리 메서드 패턴 (Factory Method Pattern):

- 용도:
  - 단일 `메서드를 통해 객체 생성을 처리`하는 패턴입니다. 
  - 하위 클래스에서 객체 생성의 구체적인 방법을 결정합니다. 
- 구조:
  - Creator 클래스가 팩토리 메서드를 정의하고, 이를 하위 클래스에서 구현합니다. 
  - Product는 생성되는 객체의 인터페이스를 정의합니다. 
- 확장성:
  - 새로운 제품을 추가하려면 새로운 ConcreteCreator를 추가하면 됩니다. 
  - 기존 코드를 변경하지 않고 확장이 가능합니다.
- 예시:
  - 자바의 java.util.Calendar 클래스의 getInstance 메서드가 팩토리 메서드 패턴을 사용한 예시입니다.
 

#### 추상 팩토리 패턴 (Abstract Factory Pattern):

- 용도:
  - 관련된 객체들의 집합을 생성하거나 구성하는 패턴입니다. 
  - 여러 제품군을 생성하고 이들을 조합하여 사용합니다. 
- 구조:
  - AbstractFactory가 관련된 제품군의 팩토리 메서드를 정의합니다. 
  - ConcreteFactory는 이를 구체화하고, Product는 생성되는 객체의 인터페이스를 정의합니다. 
- 확장성:
  - 새로운 제품군을 추가하려면 새로운 ConcreteFactory와 그에 해당하는 Product 클래스들을 추가해야 합니다. 
  - 기존 코드를 변경하지 않고도 새로운 제품군을 도입할 수 있습니다. 
- 예시:
  - GUI 라이브러리에서 여러 플랫폼에 따라 다른 스타일의 UI를 생성하는 경우가 추상 팩토리 패턴을 사용한 예시입니다. 

#### 차이점:
- 목적:
  - 팩토리 메서드는 단일 객체를 생성하는데 중점을 둡니다. 
  - 추상 팩토리는 연관된 객체들의 집합을 생성하거나 구성하는 데 중점을 둡니다. 
- 구현:
  - 팩토리 메서드는 단일 메서드를 통해 객체를 생성합니다. 
  - 추상 팩토리는 여러 제품에 대한 팩토리 메서드들의 집합을 정의합니다. 
- 확장성:
  - 팩토리 메서드는 새로운 제품을 추가하려면 하위 클래스에서 메서드를 구현하면 됩니다. 
  - 추상 팩토리는 새로운 제품군을 추가하려면 새로운 팩토리와 제품 클래스들을 추가해야 합니다. 
- 적합한 상황:
  - 팩토리 메서드는 단일 객체 생성이 주된 관심사일 때 사용됩니다. 
  - 추상 팩토리는 여러 제품군을 생성하고 이들을 조합해야 할 때 사용됩니다.


---

## 싱글턴 패턴

클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역접근을 제공합니다.

1. 유일성: 싱글톤 클래스의 인스턴스는 하나만 존재합니다. 이는 모든 클라이언트가 동일한 인스턴스에 접근할 수 있도록 합니다.
2. 전역 접근 지점: 싱글톤 인스턴스에 대한 접근은 전역적으로 가능하며, 주로 정적 메서드나 메서드를 통해 이루어집니다. 
3. 지연 초기화: 인스턴스가 필요한 시점에 생성되도록 하는 지연 초기화 방법을 사용할 수 있습니다. 
4. 쓰레드 안전성: 멀티쓰레드 환경에서 안전하게 인스턴스를 생성하고 접근할 수 있도록 하는 방법이 있어야 합니다. 
5. 프라이빗 생성자: 외부에서 싱글톤 클래스의 생성자에 접근할 수 없도록 생성자를 프라이빗으로 선언합니다.

```kotlin
class SynchronizedSingleton private constructor() {
    private var uniqueInstance: SynchronizedSingleton? = null

    // 메서드 동기화시 성능이 100배 정도 저하될 수 있습니다
    @Synchronized
    fun getInstance(): SynchronizedSingleton {
        if (uniqueInstance == null) {
            uniqueInstance = SynchronizedSingleton()
        }

        return uniqueInstance!!
    }
}

class PreGeneratedSingleton private constructor() {
    // 인스턴스를 수시로 만들고 관리하기가 성가시다면 처음부터 만든다
    private var uniqueInstance: PreGeneratedSingleton = PreGeneratedSingleton()

    fun getInstance(): PreGeneratedSingleton {
        return uniqueInstance
    }
}

class DoubleCheckLockingSingleton private constructor() {

    @Volatile
    private var instance: DoubleCheckLockingSingleton? = null

    fun getInstance(): DoubleCheckLockingSingleton {
        if (instance == null) {
            // 인스턴스가 생성되지 않았을때에만 동기화
            synchronized(this) {
                if (instance == null) {
                    instance = DoubleCheckLockingSingleton()
                }
            }
        }

        return instance!!
    }
}
```

---

## 커맨드 패턴

요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화할 수 있습니다.
이러면 요청을 큐에 저장하거나 로그로 기록하거나 작업 취소 기능을 사용할 수 있습니다.

커맨드 패턴은 객체 간의 결합도를 낮추고, 요청을 객체로 캡슐화함으로써 다양한 명령을 지원하며, 새로운 명령을 추가하기 쉽게 합니다. 또한, 명령을 큐에 저장하거나 로깅하는 등의 기능도 쉽게 추가할 수 있습니다.

1. 커맨드(Command): 요청을 나타내는 인터페이스로, 실행을 위한 execute 메서드를 정의합니다.
2. 구체적인 커맨드(Concrete Command): 실제로 실행되는 명령을 구체화한 클래스입니다. Command 인터페이스를 구현하며, 수신자 객체와 실행 메서드를 함께 포함합니다. 
3. 수신자(Receiver): 실제 동작을 수행하는 객체입니다. 명령을 실행하는 메서드를 정의합니다. 
4. 호출자(Invoker): 커맨드 객체를 저장하고, 이를 통해 실제 명령을 실행합니다. 
5. 클라이언트(Client): 명령을 생성하고, 이를 특정 수신자에 연결한 후, 호출자를 사용하여 명령을 실행합니다.

```kotlin
// 1. 커맨드(Command) 인터페이스
interface Command {
    fun execute()
}

// 2. 구체적인 커맨드(Concrete Command)
class LightOnCommand(private val light: Light) : Command {
    override fun execute() {
        light.turnOn()
    }
}

class LightOffCommand(private val light: Light) : Command {
    override fun execute() {
        light.turnOff()
    }
}

// 3. 수신자(Receiver)
class Light {
    fun turnOn() {
        println("Light is ON")
    }

    fun turnOff() {
        println("Light is OFF")
    }
}

// 4. 호출자(Invoker)
class RemoteControl {
    private var command: Command? = null

    fun setCommand(command: Command) {
        this.command = command
    }

    fun pressButton() {
        command?.execute()
    }
}

fun main() {
    // 클라이언트 코드
    val light = Light()
    val lightOnCommand = LightOnCommand(light)
    val lightOffCommand = LightOffCommand(light)

    val remoteControl = RemoteControl()

    // 버튼에 켜기 명령 설정 후 실행
    remoteControl.setCommand(lightOnCommand)
    remoteControl.pressButton() // Light is ON

    // 버튼에 끄기 명령 설정 후 실행
    remoteControl.setCommand(lightOffCommand)
    remoteControl.pressButton() // Light is OFF
}

```

---

## 어댑터 패턴

특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환합니다. 인터페이스가 호환되지 않아 같이 쓸 수 없어던 클래스를 사용할 수 있게 도와줍니다.

어댑터 패턴은 주로 두 클래스 간의 호환성이 없는 인터페이스를 가진 경우에 사용됩니다.

1. 타겟 인터페이스(Target Interface): 클라이언트가 사용하는 인터페이스입니다. 클라이언트는 이 인터페이스를 통해 어댑터와 상호 작용합니다. 
2. 어댑터(Adapter): 어댑터 패턴의 핵심이 되는 클래스로, 타겟 인터페이스를 구현하면서, 어댑티(Adaptee)의 인터페이스를 사용합니다. 즉, 어댑터는 클라이언트와 어댑티 간의 인터페이스 차이를 해결하는 역할을 합니다. 
3. 어댑티(Adaptee): 어댑터 패턴을 적용하고자 하는 클래스 또는 인터페이스입니다. 어댑터는 이 어댑티의 인터페이스를 사용하여 클라이언트와 상호 작용합니다. 
4. 클라이언트(Client): 어댑터를 사용하여 어댑티의 인터페이스를 호출하는 클래스 또는 모듈입니다.

```kotlin
// 1. 타겟 인터페이스(Target Interface)
interface Target {
    fun request(): String
}

// 2. 어댑티(Adaptee)
class Adaptee {
    fun specificRequest(): String {
        return "Specific request"
    }
}

// 3. 어댑터(Adapter)
class Adapter(private val adaptee: Adaptee) : Target {
    override fun request(): String {
        // 어댑티의 메서드를 호출하고 그 결과를 타겟 인터페이스에 맞게 변환
        return "Adapter: ${adaptee.specificRequest()}"
    }
}

// 4. 클라이언트(Client)
fun clientCode(target: Target) {
    println(target.request())
}

fun main() {
    // 어댑티를 생성
    val adaptee = Adaptee()

    // 어댑터를 이용하여 클라이언트 코드에서 호출
    val adapter = Adapter(adaptee)
    clientCode(adapter)
}
```

---

## 퍼사드 패턴

서브시스템에 있는 일련의 인터페이스를 통합 인터페이스로 묶어 줍니다. 또한 고수준 인터페이스도 정의하므로 서브시스템을 더 편리하게 사용할 수 있습니다.

1. 간단한 인터페이스 제공: 퍼사드는 서브시스템의 복잡성을 감추고, 클라이언트에게 단순한 인터페이스를 제공합니다.  
2. 서브시스템의 일관된 인터페이스: 퍼사드는 여러 서브시스템의 복잡한 인터페이스들을 통합하여 클라이언트에게 일관된 인터페이스를 제공합니다. 
3. 클라이언트와 서브시스템 간의 결합도 감소: 퍼사드를 사용하면 클라이언트는 퍼사드와만 상호 작용하므로, 서브시스템의 변경이나 교체에 대한 영향이 최소화됩니다. 
4. 단일 지점 제어: 퍼사드는 서브시스템의 상태와 동작을 통합하고, 클라이언트가 서브시스템에 대한 모든 요청을 단일 지점에서 처리합니다.

```kotlin
class Projector {
    fun on() {
        println("Projector On")
    }

    fun off() {
        println("Projector Off")
    }

    fun tvMode() {
        println("Projector tvMode")
    }

    fun wideScreenMode() {
        println("Projector wideScreenMode")
    }
}

class Screen {
    fun up() {
        println("Screen Up")
    }

    fun down() {
        println("Screen Down")
    }
}

class StreamingPlayer {
    fun on() {
        println("Player On")
    }

    fun off() {
        println("Player Off")
    }

    fun pause() {
        println("Player Pause")
    }

    fun play() {
        println("Player Play")
    }

    fun stop() {
        println("Player Stop")
    }
}

class HomeTheaterFacade(
    private val player: StreamingPlayer,
    private val screen: Screen,
    private val projector: Projector,
) {

    fun watchMovie() {
        println("Ready to watch movie")
        screen.down()
        projector.on()
        projector.wideScreenMode()
        player.on()
        player.play()
    }

    fun endMovie() {
        println("Turn off movie")
        player.stop()
        player.off()
        projector.off()
        screen.up()
    }

}

fun main() {
    val homeTheater = HomeTheaterFacade(
        player = StreamingPlayer(),
        screen = Screen(),
        projector = Projector(),
    )

    homeTheater.watchMovie()
    homeTheater.endMovie()
}
```

---

## 템플릿 매소드 패턴

알고리즘의 골격을 정의합니다. 템플릿 메소드를 사용하면 알고리즘의 일부 단계를 서븤르래스에서 구현할 수 있으며,
알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브클래스에서 재정의할 수도 있습니다.

템플릿 메소드 패턴의 장점은 다음과 같습니다:
- 코드 재사용성: 알고리즘의 공통된 부분을 부모 클래스에 두기 때문에 코드의 재사용성이 높아집니다.
- 확장성: 템플릿 메소드를 가진 클래스를 상속받아 새로운 알고리즘을 추가하거나 기존 알고리즘을 수정할 수 있습니다.

1. 템플릿 메소드(Template Method): 알고리즘의 구조를 정의하는 메소드입니다. 이 메소드는 일련의 단계를 나타내며, 일부 단계는 구현이 되어 있고 일부 단계는 추상 메소드로 선언되어 하위 클래스에서 구현됩니다.
2. 기본 메소드(Concrete Method): 템플릿 메소드에서 호출되는 구현이 이미 제공되어 있는 메소드입니다. 이 메소드들은 필수는 아니지만, 기본적인 알고리즘에 필요한 보조적인 동작을 수행합니다.
3. 훅 메소드(Hook Method): 기본 메소드와 마찬가지로 템플릿 메소드에서 호출되는데, 이 메소드들은 비어있거나 기본 구현이 제공되어 하위 클래스에서 선택적으로 오버라이드할 수 있는 메소드입니다.

```kotlin
abstract class CaffeineBeverage {

    fun prepareRecipe() {
        boilWater()
        brew()
        pourInCup()
        addCondiments()
    }

    private fun boilWater() {
        println("Boiling")
    }

    private fun pourInCup() {
        println("Pouring")
    }

    abstract fun brew()

    abstract fun addCondiments()

}

class Coffee : CaffeineBeverage() {

    override fun addCondiments() {
        println("Add Sugar and Milk")
    }

    override fun brew() {
        println("Brewing")
    }

}

class Tea : CaffeineBeverage() {

    override fun brew() {
        println("Steep Tea")
    }

    override fun addCondiments() {
        println("Add Lemon")
    }
}

fun main() {
    val tea = Tea()
    val coffee = Coffee()

    tea.prepareRecipe()
    coffee.prepareRecipe()
}
```

---


## 반복자 패턴

구현 방법을 노출하지 않으면서 집합체 내의 모든 항목에 접근하는 방법을 제공합니다.

반복자 패턴을 이용하면 클라이언트 코드는 컬렉션 객체의 구현에 독립적으로 원소에 접근할 수 있기 때문에,
컬렉션 객체의 내부 구조가 변경되어도 클라이언트 코드는 수정 없이 그대로 사용할 수 있습니다.

1. 반복자(Iterator): 컬렉션 객체의 원소에 접근하고 순회하기 위한 인터페이스를 정의합니다. 일반적으로 next(), hasNext() 등의 메소드가 있습니다.
2. 구체적인 반복자(Concrete Iterator): 반복자 인터페이스를 실제로 구현한 클래스입니다. 컬렉션 객체의 내부 구조에 대한 정보를 가지고 있어야 합니다.
3. 집합 객체(Iterable/Aggregate): 반복자를 생성하는 인터페이스를 정의합니다. 이는 컬렉션 객체에 대한 인터페이스를 의미하며, 반복자를 생성하는 iterator() 메소드를 제공합니다.
4. 구체적인 집합 객체(Concrete Iterable/Aggregate): 집합 객체 인터페이스를 실제로 구현한 클래스로, 실제 컬렉션 객체를 나타냅니다.

```kotlin
interface Iterator {

    fun hasNext(): Boolean

    fun next(): MenuItem?
}

interface Menu {

    fun createIterator(): Iterator

}

data class MenuItem(
    val name: String,
    val description: String,
    val vegetarian: Boolean,
    val price: Double,
)

class PancakeHouseMenu : Menu {

    private val menuItems = mutableListOf<MenuItem>()

    init {
        addItem(
            "K&B 팬케이크 세트",
            "스크램블 에그와 토스트가 곁들여진 팬케이크",
            true,
            2.99,
        )

        addItem(
            "레귤러 팬케이크 세트",
            "달걀 프라이와 소시지가 곁들여진 팬케이크",
            false,
            2.99,
        )

        addItem(
            "블루베리 팬케이크",
            "신선한 블루베리와 블루베리 시럽으로 만든 팬케이크",
            true,
            3.49,
        )

        addItem(
            "와플",
            "취향에 따라 블루베리나 딸기를 얹을 수 있는 와플",
            true,
            3.59,
        )

    }

    private fun addItem(
        name: String,
        description: String,
        vegetarian: Boolean,
        price: Double,
    ) {
        val menuItem = MenuItem(
            name = name,
            description = description,
            vegetarian = vegetarian,
            price = price
        )
        menuItems.add(menuItem)
    }

    override fun createIterator() = PancakeHouseIterator(menuItems)

}

class PancakeHouseIterator(
    private val items: List<MenuItem>
) : Iterator {

    private var position = 0

    override fun hasNext(): Boolean {
        return position < items.size
    }

    override fun next(): MenuItem? {
        val menuItem = items[position]
        position++
        return menuItem
    }
}

class DinnerMenu : Menu {

    companion object {
        const val MAX_ITEMS = 6
    }

    private var numberOfItems = 0

    private val menuItems = arrayOfNulls<MenuItem>(MAX_ITEMS)

    init {
        addItem(
            "채식주의자용 BLT",
            "통밀 위에 콩고기 베이컨, 상추, 토마토를 얹은 메뉴",
            true,
            2.99,
        )

        addItem(
            "BLT",
            "통밀 위에 베이컨, 상추, 토마토를 얹은 메뉴",
            false,
            2.99,
        )

        addItem(
            "오늘의 스프",
            "감자 샐러드를 곁들인 오늘의 스프",
            false,
            3.29,
        )

        addItem(
            "핫도그",
            "사워크라우트, 갖은 양념, 양파, 치즈가 곁들여진 핫도그",
            false,
            3.05,
        )
    }

    private fun addItem(
        name: String,
        description: String,
        vegetarian: Boolean,
        price: Double
    ) {
        val menuItem = MenuItem(
            name = name,
            description = description,
            vegetarian = vegetarian,
            price = price
        )

        if (numberOfItems >= MAX_ITEMS) {
            println("죄송합니다, 메뉴가 꽉 찼습니다. 더 이상 추가할 수 없습니다")
        } else {
            menuItems[numberOfItems] = menuItem
            numberOfItems++
        }
    }

    override fun createIterator(): Iterator = DinnerMenuIterator(menuItems)

}

class DinnerMenuIterator(
    private val items: Array<MenuItem?>
) : Iterator {

    private var position = 0

    override fun next(): MenuItem? {
        val menuItem = items[position]
        position++
        return menuItem
    }

    override fun hasNext(): Boolean {
        return !(position >= items.size || items[position] == null)
    }

}

class Waitress(
    private val pancakeHouseMenu: Menu,
    private val dinnerMenu: Menu,
) {

    fun printMenu() {
        val pancakeIterator = pancakeHouseMenu.createIterator()
        val dinnerMenuIterator = dinnerMenu.createIterator()
        println("메뉴\n---\n아침 메뉴")
        printMenu(pancakeIterator)
        println("\n점심 메뉴")
        printMenu(dinnerMenuIterator)
    }

    private fun printMenu(iterator: Iterator) {
        while (iterator.hasNext()) {
            val menuItem = iterator.next()
            println("${menuItem?.name}, ${menuItem?.price} -- ${menuItem?.description}")
        }
    }

}

fun main() {

    val pancakeHouseMenu = PancakeHouseMenu()
    val dinnerMenu = DinnerMenu()

    val waitress = Waitress(pancakeHouseMenu, dinnerMenu)
    waitress.printMenu()

}
```

---

## 컴포지트 패턴

객체를 트리구조로 구성해서 부분-전체 계층구조를 구현합니다. 컴포지트 패턴을 사용하면 클라이언트에서 개별 객체와 복합 객체를 똑같은 방법으로 다룰 수 있습니다.

컴포지트 패턴을 사용하면 클라이언트 코드에서는 단일 객체든 복합 객체든 동일한 방식으로 다룰 수 있습니다.
이는 코드의 일관성을 유지하고, 새로운 구성 요소를 추가하거나 수정할 때 유연성을 제공합니다.

1. Component (구성 요소): 모든 객체가 가져야 하는 인터페이스를 정의합니다. 보통은 구체적인 구현이 아닌 추상적인 형태의 인터페이스로 정의됩니다. 
2. Leaf (단일 객체): Component 인터페이스를 구현하면서 자식 객체를 가지지 않는 기본 단위 객체입니다. 
3. Composite (복합 객체): Component 인터페이스를 구현하면서 여러 개의 자식 객체를 가질 수 있는 복합 객체입니다. 즉, Leaf와 Composite를 모두 자식으로 갖을 수 있습니다.

```kotlin
abstract class MenuComponent {

    open fun add(menuComponent: MenuComponent) {
        throw UnsupportedOperationException()
    }

    open fun remove(menuComponent: MenuComponent) {
        throw UnsupportedOperationException()
    }

    open fun getChild(index: Int): MenuComponent {
        throw UnsupportedOperationException()
    }

    open fun getName(): String {
        throw UnsupportedOperationException()
    }

    open fun getDescription(): String {
        throw UnsupportedOperationException()
    }

    open fun getPrice(): Double {
        throw UnsupportedOperationException()
    }

    open fun isVegetarian(): Boolean {
        throw UnsupportedOperationException()
    }

    open fun print() {
        throw UnsupportedOperationException()
    }

}

class Menu(
    private val name: String,
    private val description: String,
) : MenuComponent() {

    private val menuComponents = mutableListOf<MenuComponent>()

    override fun add(menuComponent: MenuComponent) {
        menuComponents.add(menuComponent)
    }

    override fun remove(menuComponent: MenuComponent) {
        menuComponents.remove(menuComponent)
    }

    override fun getChild(index: Int): MenuComponent {
        return menuComponents[index]
    }

    override fun getName(): String {
        return name
    }

    override fun getDescription(): String {
        return description
    }

    override fun print() {
        print("\n ${getName()}")
        println(", ${getDescription()}")
        println("---------------------")

        for (menuComponent in menuComponents) {
            menuComponent.print()
        }
    }

}

class MenuItem(
    private val name: String,
    private val description: String,
    private val vegetarian: Boolean,
    private val price: Double,
) : MenuComponent() {

    override fun getName(): String {
        return name
    }

    override fun getDescription(): String {
        return description
    }

    override fun isVegetarian(): Boolean {
        return vegetarian
    }

    override fun getPrice(): Double {
        return price
    }

    override fun print() {
        print(" ${getName()}")
        if (isVegetarian()) {
            print("(v)")
        }
        println(", ${getPrice()}")
        println("    -- ${getDescription()}")
    }
}

class Waitress(private val allMenus: MenuComponent) {

    fun printMenu() {
        allMenus.print()
    }

}

fun main() {

    val pancakeHouseMenu = Menu("팬케이크 하우스 메뉴", "아침 메뉴")
    val dinnerMenu = Menu("객체마을 식당 메뉴", "점심 메뉴")
    val cafeMenu = Menu("카페 메뉴", "저녁 메뉴")
    val dessertMenu = Menu("디저트 메뉴", "디저트를 즐겨 보세요")

    val allMenus = Menu("전체 메뉴", "전체 메뉴").apply {
        add(pancakeHouseMenu)
        add(dinnerMenu)
        add(cafeMenu)
    }

    dinnerMenu.add(MenuItem("파스타", "마리나라 소스 스파게티, 효모빵도 드립니다", true, 3.89))
    dinnerMenu.add(dessertMenu)

    dessertMenu.add(MenuItem("애플 파이", "바삭바삭한 크러스트에 바닐라 아이스크림이 얹혀 잇는 애플 파이", true, 1.59))

    val waitress = Waitress(allMenus)
    waitress.printMenu()

}
```

## 상태 패턴

객체의 내부 상태가 바뀜에 따라서 객체의 행동을 바꿀 수 있습니다. 마치 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있습니다.

상태 패턴은 다양한 상태를 가진 객체를 만들 때 유용하며, 특히 상태 전환 로직이 복잡하고 상태에 따라 행동이 크게 달라질 때 유용합니다. 이 패턴을 사용하면 코드의 가독성이 향상되고 유지 보수가 용이해집니다.

1. Context (콘텍스트): 상태를 가지고 있는 객체로, 상태에 따라 다른 행동을 수행할 수 있도록 합니다. 콘텍스트는 상태 객체와 현재 상태를 가지고 있습니다.
2. State (상태): 콘텍스트의 상태에 따라 정의되는 인터페이스 또는 추상 클래스입니다. 각 상태에 대한 구체적인 동작을 정의하고, 콘텍스트에게 이 상태에서의 행동을 수행하도록 합니다.
3. ConcreteState (구체적인 상태): State를 구현한 구체적인 클래스로, 특정 상태에서 어떻게 행동할지를 정의합니다.

```kotlin
// 1. State: 라이트의 상태를 나타내는 인터페이스
interface State {
    fun turnOn()
    fun turnOff()
}

// 2. ConcreteState: 라이트가 켜진 상태
class LightOnState : State {
    override fun turnOn() {
        println("The light is already on.")
    }

    override fun turnOff() {
        println("Turning off the light.")
        // 상태 전환
        context.transitionTo(LightOffState())
    }
}

// 3. ConcreteState: 라이트가 꺼진 상태
class LightOffState : State {
    override fun turnOn() {
        println("Turning on the light.")
        // 상태 전환
        context.transitionTo(LightOnState())
    }

    override fun turnOff() {
        println("The light is already off.")
    }
}

// 4. Context: 라이트를 나타내는 클래스
class Light {
    private var currentState: State = LightOffState()

    fun turnOn() {
        currentState.turnOn()
    }

    fun turnOff() {
        currentState.turnOff()
    }

    // 상태 전환 메서드
    fun transitionTo(state: State) {
        currentState = state
        currentState.context = this
    }
}

fun main() {
    val light = Light()

    light.turnOn()  // Turning on the light.
    light.turnOn()  // The light is already on.

    light.turnOff() // Turning off the light.
    light.turnOff() // The light is already off.
}
```

---

## 프록시 패턴

객체로의 접근을 제어하는 대리인(특정 객체를 대변하는 객체)을 제공합니다.

프록시 패턴은 여러 상황에서 사용될 수 있습니다. 일부 일반적인 사용 사례에는 다음과 같은 것들이 있습니다:

- 보안 제어: 실제 객체에 직접 접근하지 않고 프록시를 통해 접근하여 보안을 강화할 수 있습니다. 프록시는 인증, 권한 부여 등의 작업을 수행할 수 있습니다.
- 지연 로딩(Lazy Loading): 대용량의 자원을 로딩하는 경우, 프록시를 사용하여 실제 필요한 시점에 로딩할 수 있습니다. 이는 성능을 향상시킬 수 있습니다.
- 캐싱: 결과를 캐싱하여 동일한 요청에 대해 매번 계산을 수행하지 않도록 합니다. 이는 성능 향상에 도움이 됩니다.
- 원격 프록시 (Remote Proxy): 다른 서버나 프로세스에서 객체를 다룰 수 있도록 하는 프록시입니다. 원격 서비스에 대한 로컬 인터페이스를 제공합니다.

1. Subject (주체): 실제 객체와 프록시가 구현할 공통 인터페이스입니다.
2. RealSubject (실제 주체): 프록시가 대리하는 실제 객체입니다.
3. Proxy (프록시): RealSubject에 대한 참조를 갖고 있으며, 클라이언트의 요청을 수행하거나 조절하는 역할을 합니다.

```kotlin
// 1. Subject
interface Image {
    fun display()
}

// 2. RealSubject
class RealImage(private val filename: String) : Image {
    init {
        loadFromDisk()
    }

    private fun loadFromDisk() {
        println("Loading image: $filename")
    }

    override fun display() {
        println("Displaying image: $filename")
    }
}

// 3. Proxy
class ProxyImage(private val filename: String) : Image {
    private var realImage: RealImage? = null

    override fun display() {
        if (realImage == null) {
            realImage = RealImage(filename)
        }
        realImage?.display()
    }
}

fun main() {
    // 클라이언트는 ProxyImage를 사용하며 실제 객체를 직접 접근하지 않음
    val image: Image = ProxyImage("example.jpg")

    // 이미지를 처음 표시할 때 실제 로딩이 발생
    image.display()

    // 이미지를 두 번째 표시할 때는 이미 로딩된 이미지를 사용
    image.display()
}

```