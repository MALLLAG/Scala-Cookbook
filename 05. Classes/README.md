# Chapter 5. Classes

## 챕터 개요

이번 챕터는 Scala 3에서 **도메인 모델링(domain modeling)** 개념을 다루는 네 개 챕터 시리즈의 첫 번째입니다. **도메인 모델링**이란 프로그래밍 언어를 사용해 우리 주변 세계를 모델링하는 방법, 즉 사람, 자동차, 금융 거래 등의 개념을 어떻게 모델링하는지를 의미합니다. 함수형 프로그래밍(FP) 스타일로 코드를 작성하든 객체 지향 프로그래밍(OOP) 스타일로 작성하든, 이는 곧 이러한 대상들의 **속성(attributes)** 과 **동작(behaviors)** 을 모델링하는 것을 뜻합니다.

세계를 모델링하는 유연성을 제공하기 위해, Scala 3는 다음과 같은 언어 구성 요소를 제공합니다.

- 클래스(Classes)
- 케이스 클래스(Case classes)
- 트레이트(Traits)
- 이넘(Enums)
- 오브젝트와 케이스 오브젝트(Objects and case objects)
- 추상 클래스(Abstract classes)
- 메서드(Methods) — 위의 모든 구성 요소 내부에 정의될 수 있습니다

다룰 내용이 많기 때문에, 이 복잡성을 관리하기 위해 Recipe 5.1에서 FP 스타일과 OOP 스타일로 프로그래밍할 때 이러한 구성 요소들을 어떻게 사용하는지 보여 줍니다. 그 후 클래스와 케이스 클래스는 이번 챕터에서, 트레이트와 이넘은 Chapter 6에서, 오브젝트는 Chapter 7에서, 메서드 관련 레시피는 Chapter 8에서 다룹니다. 추상 클래스는 자주 사용되지 않으므로 Recipe 5.1에서 간략히만 언급됩니다.

### 클래스와 케이스 클래스 (Classes and Case Classes)

Scala와 Java는 많은 유사점을 공유하지만, **클래스(classes)** 와 **생성자(constructors)** 관련 문법은 두 언어 사이의 가장 큰 차이점 중 하나를 보여 줍니다. Java는 다소 장황(verbose)한 경향이 있는 반면, Scala는 더 간결합니다. 그리고 우리가 작성한 코드는 결국 다른 코드를 생성하게 됩니다. 예를 들어, 다음의 한 줄짜리 Scala 클래스는 최소 29줄의 Java 코드로 컴파일되며, 그중 대부분은 접근자(accessor)/변경자(mutator) 메서드라는 보일러플레이트(boilerplate) 코드입니다.

```scala
class Employee(var name: String, var age: Int, var role: String)
```

클래스와 생성자는 매우 중요하기 때문에, 이번 챕터의 초반 레시피들에서 상세히 다룹니다.

다음으로, **equals**가 무엇을 의미하는지에 대한 개념은 매우 중요한 프로그래밍 주제이므로, Recipe 5.9에서 Scala에서 `equals` 메서드를 구현하는 방법을 자세히 설명하는 데 많은 분량을 할애합니다.

> **match 표현식에서 클래스 사용하기**
>
> 클래스를 `match` 표현식에서 사용하고 싶을 때는, 클래스의 컴패니언 오브젝트(companion object) 내부에 `unapply` 메서드를 구현합니다. 이는 오브젝트에서 수행하는 작업이므로, 해당 주제는 Recipe 7.8("unapply로 패턴 매칭 구현하기")에서 다룹니다.

클래스 필드(class fields)에 접근하는 개념은 중요하므로, Recipe 5.10에서 접근자와 변경자 메서드가 생성되지 않도록 막는 방법을 보여 줍니다. 그 후 Recipe 5.11에서는 접근자와 변경자 메서드의 기본 동작을 오버라이드하는 방법을 보여 줍니다.

> **접근자(Accessors)와 변경자(Mutators)**
>
> Java에서는 접근자와 변경자 메서드를 **getter**와 **setter** 메서드라고 부르는 것이 적절해 보이는데, 이는 주로 JavaBeans의 `get`/`set` 표준 때문입니다. 이번 챕터에서 저자는 이 용어들을 혼용해서 사용하지만, 명확히 하자면 Scala는 접근자와 변경자 메서드에 대해 JavaBeans 네이밍 컨벤션을 따르지 않습니다.

다음으로, 두 개의 레시피가 파라미터와 필드 관련해서 알아야 할 다른 기법들을 보여 줍니다. 먼저 Recipe 5.12에서는 클래스 내의 **lazy** 필드에 코드 블록을 할당하는 방법을 보여 주고, Recipe 5.13에서는 `Option` 타입을 사용해 초기화되지 않은 `var` 필드를 다루는 방법을 보여 줍니다.

마지막으로, 앞서 보았듯이 OOP 스타일의 Scala `Employee` 클래스는 29줄의 Java 코드와 동등합니다. 이에 비해, 다음의 FP 스타일 `case` 클래스는 100줄이 훨씬 넘는 Java 코드와 동등합니다.

```scala
case class Employee(name: String, age: Int, role: String)
```

`case` 클래스는 많은 보일러플레이트 코드를 생성해 주기 때문에, 그 사용법과 이점은 Recipe 5.14에서 다룹니다. 또한 `case` 클래스는 기본 Scala `class`와 다르기 때문에 — `case` 클래스의 생성자는 사실 팩토리 메서드(factory method)입니다 — 이에 대해서는 Recipe 5.15에서 다룹니다.

---

## 5.1 도메인 모델링 옵션 선택하기 (Choosing from Domain Modeling Options)

### 문제 (Problem)

Scala는 트레이트, 이넘, 클래스, 케이스 클래스, 오브젝트, 추상 클래스를 제공하기 때문에, 자신만의 코드를 설계할 때 이러한 도메인 모델링 옵션 중에서 어떻게 선택해야 할지 이해하고 싶습니다.

### 해결책 (Solution)

해결책은 함수형 프로그래밍 스타일을 사용하는지 객체 지향 프로그래밍 스타일을 사용하는지에 따라 달라집니다. 따라서 이 두 가지 해결책을 다음 섹션에서 나누어 설명합니다. 예시는 Discussion에서도 제공되며, 그 뒤에 추상 클래스를 언제 사용해야 하는지에 대한 간략한 설명이 이어집니다.

#### 함수형 프로그래밍 모델링 옵션 (Functional programming modeling options)

FP 스타일로 프로그래밍할 때는 주로 다음 구성 요소들을 사용합니다.

- 트레이트(Traits)
- 이넘(Enums)
- 케이스 클래스(Case classes)
- 오브젝트(Objects)

FP 스타일에서 이 구성 요소들은 다음과 같이 사용됩니다.

**Traits (트레이트)**
: 트레이트는 작고 논리적으로 그룹화된 동작 단위를 만드는 데 사용됩니다. 일반적으로 `def` 메서드로 작성되지만, 원한다면 `val` 함수로도 작성할 수 있습니다. 어느 쪽이든 순수 함수(pure functions)로 작성됩니다("Pure Functions" 272페이지 참고). 이 트레이트들은 나중에 구체적인 오브젝트로 결합됩니다.

**Enums (이넘)**
: 이넘을 사용해 대수적 데이터 타입(ADTs, Recipe 6.13 "이넘으로 대수적 데이터 타입 모델링하기" 참고)뿐 아니라 일반화된 ADT(GADTs)를 만듭니다.

**Case classes (케이스 클래스)**
: 케이스 클래스를 사용해 불변(immutable) 필드를 가진 오브젝트를 만듭니다. 이는 일부 언어에서 **불변 레코드(immutable records)** 로 알려져 있습니다(예: Java 14의 `record` 타입). 케이스 클래스는 FP 스타일을 위해 만들어졌으며, 이 스타일을 돕는 여러 특화된 메서드를 가지고 있습니다. 여기에는 다음이 포함됩니다: 기본적으로 `val` 필드인 파라미터, 값을 변경한 것처럼 시뮬레이션하고 싶을 때 사용하는 `copy` 메서드, 패턴 매칭을 위한 내장 `unapply` 메서드, 좋은 기본 `equals`와 `hashCode` 메서드 등.

**Objects (오브젝트)**
: FP에서는 일반적으로 오브젝트를 하나 이상의 트레이트를 "실체화(real)"하는 방법으로 사용하며, 이 과정은 기술적으로 **reification(구체화)** 으로 알려져 있습니다.

FP에서 케이스 클래스의 모든 기능이 필요하지 않을 때는, (`case class` 구성 요소 대신) 일반 `class` 구성 요소를 사용할 수도 있습니다. 이렇게 할 때는 파라미터를 `val` 필드로 정의한 다음, 클래스에 대해 `unapply` 추출자(extractor) 메서드를 정의하고 싶은 경우처럼 다른 동작들을 수동으로 구현합니다(Recipe 7.8 "unapply로 패턴 매칭 구현하기" 참고).

#### 객체 지향 프로그래밍 모델링 옵션 (Object-oriented programming modeling options)

OOP 스타일로 프로그래밍할 때는 주로 다음 구성 요소들을 사용합니다.

- 트레이트(Traits)
- 이넘(Enums)
- 클래스(Classes)
- 오브젝트(Objects)

이 구성 요소들은 다음과 같은 방식으로 사용됩니다.

**Traits (트레이트)**
: 트레이트는 주로 인터페이스(interface)로 사용됩니다. Java를 사용해 본 적이 있다면, Scala 트레이트를 Java 8 이상의 인터페이스처럼 추상 멤버와 구체 멤버를 모두 갖춘 형태로 사용할 수 있습니다. 클래스는 나중에 이 트레이트들을 구현하는 데 사용됩니다.

**Enums (이넘)**
: 주로 디스플레이의 위치(상, 하, 좌, 우)처럼 단순한 상수 집합을 만드는 데 이넘을 사용합니다.

**Classes (클래스)**
: OOP에서는 주로 케이스 클래스가 아닌 일반 클래스를 사용합니다. 또한 생성자 파라미터를 `var` 필드로 정의해서 변경(mutate)할 수 있도록 합니다. 이 클래스들은 이 변경 가능한 필드들에 기반한 메서드를 포함합니다. 필요에 따라 기본 접근자/변경자 메서드(getter와 setter)를 오버라이드합니다.

**Object (오브젝트)**
: 주로 `object` 구성 요소를 Java의 정적 메서드(static method)와 동등한 것을 만드는 방법으로 사용합니다. 예를 들어 문자열을 다루는 정적 메서드를 담은 `StringUtils` 오브젝트처럼 사용합니다(Recipe 7.4 "컴패니언 오브젝트로 정적 멤버 만들기" 참고).

케이스 클래스가 제공하는 기능의 많은 부분 또는 전부를 원할 때는(Recipe 5.14 참고), 일반 클래스 대신 케이스 클래스를 사용할 수 있습니다(다만 케이스 클래스는 주로 FP 스타일 코딩을 위해 의도된 것입니다).

### 논의 (Discussion)

이 해결책을 설명하기 위해 FP 예시와 OOP 예시를 따로 보여 줍니다. 다만 개별 예시로 들어가기 전에, 두 스타일 모두에서 사용되는 다음 이넘들을 먼저 보여 줍니다.

```scala
enum Topping:
    case Cheese, Pepperoni, Sausage, Mushrooms, Onions

enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Regular, Thin, Thick
```

이처럼 이넘을 사용하는 것은 — 기술적으로는 ADT입니다(Recipe 6.13 "이넘으로 대수적 데이터 타입 모델링하기" 참고) — FP와 OOP 도메인 모델링 사이의 공통점을 보여 줍니다.

#### FP 스타일 예시 (An FP-style example)

피자 가게(pizza store) 예시는 Recipe 10.10 "실전 예제: 함수형 도메인 모델링"에서 FP 도메인 모델링 접근법을 상세히 보여 주므로, 여기서는 빠르게 살펴보겠습니다.

먼저 위의 이넘들을 사용해 `case class` 구성 요소로 `Pizza` 클래스를 정의합니다.

```scala
case class Pizza(
    crustSize: CrustSize,
    crustType: CrustType,
    toppings: Seq[Topping]
)
```

그 후, 추가적인 클래스들도 케이스 클래스로 모델링합니다.

```scala
case class Customer(
    name: String,
    phone: String,
    address: Address
)

case class Address(
    street1: String,
    street2: Option[String],
    city: String,
    state: String,
    postalCode: String
)

case class Order(
    pizzas: Seq[Pizza],
    customer: Customer
)
```

FP에서는 모든 파라미터가 불변이고, 케이스 클래스가 FP를 더 쉽게 만드는 내장 메서드를 제공하기 때문에 케이스 클래스가 선호됩니다(Recipe 5.14에서 보여 줍니다). 또한 이 클래스들은 메서드를 전혀 포함하지 않는다는 점에 주목하세요. 이들은 단순한 데이터 구조일 뿐입니다.

다음으로, 이 데이터 구조들을 다루는 메서드들을 순수 함수로 작성하고, 이 메서드들을 작고 논리적으로 정리된 트레이트들로 그룹화합니다. 이 경우에는 하나의 트레이트로 묶습니다.

```scala
trait PizzaServiceInterface:
    def addTopping(p: Pizza, t: Topping): Pizza
    def removeTopping(p: Pizza, t: Topping): Pizza
    def removeAllToppings(p: Pizza): Pizza
    def updateCrustSize(p: Pizza, cs: CrustSize): Pizza
    def updateCrustType(p: Pizza, ct: CrustType): Pizza
```

그런 다음 이 메서드들을 다른 트레이트에서 구현합니다.

```scala
trait PizzaService extends PizzaServiceInterface:
    def addTopping(p: Pizza, t: Topping): Pizza =
        // 'copy' 메서드는 케이스 클래스와 함께 제공됩니다
        val newToppings = p.toppings :+ t
        p.copy(toppings = newToppings)

    // 이 메서드들 각각에는 약 두 줄의 코드가 있으므로,
    // 모든 코드를 여기서 반복하지는 않습니다:
    def removeTopping(p: Pizza, t: Topping): Pizza = ???
    def removeAllToppings(p: Pizza): Pizza = ???
    def updateCrustSize(p: Pizza, cs: CrustSize): Pizza = ???
    def updateCrustType(p: Pizza, ct: CrustType): Pizza = ???
end PizzaService
```

이 트레이트에서 모든 것이 불변(immutable)이라는 점에 주목하세요. 피자, 토핑, 크러스트 세부 정보가 메서드로 전달되고, 메서드는 그 값들을 변경하지 않습니다. 대신 전달받은 값들에 기반한 새로운 값들을 반환합니다.

마지막으로, 서비스를 오브젝트로 구체화(reify)하여 "실체화"합니다.

```scala
object PizzaService extends PizzaService
```

이 예시에서는 트레이트를 하나만 사용하지만, 실제 세계에서는 종종 여러 트레이트를 하나의 오브젝트로 결합합니다.

```scala
object DogServices extends TailService, RubberyNoseService, PawService ...
```

이처럼, 여러 개의 세분화된 단일 목적 서비스를 하나의 더 큰 완전한 서비스로 결합하는 방법입니다.

피자 가게 예시에 대해 여기서 보여 줄 내용은 이것이 전부이지만, 더 자세한 내용은 Recipe 10.10 "실전 예제: 함수형 도메인 모델링"을 참고하세요.

#### OOP 스타일 예시 (An OOP-style example)

다음으로, 같은 문제에 대해 OOP 스타일 해결책을 만들어 봅니다. 먼저 `class` 구성 요소와 변경 가능한 파라미터를 사용해 OOP 스타일의 피자 클래스를 만듭니다.

```scala
class Pizza (
    var crustSize: CrustSize,
    var crustType: CrustType,
    val toppings: ArrayBuffer[Topping]
):
    def addTopping(t: Topping): Unit =
        toppings += t
    def removeTopping(t: Topping): Unit =
        toppings -= t
    def removeAllToppings(): Unit =
        toppings.clear()
```

처음 두 생성자 파라미터는 변경할 수 있도록 `var` 필드로 정의되었고, `toppings`는 그 값들도 변경할 수 있도록 `ArrayBuffer`로 정의되었습니다.

FP 스타일 케이스 클래스가 속성만 포함하고 동작은 포함하지 않는 반면, OOP 접근법에서는 피자 클래스가 속성과 동작 둘 다를 포함합니다. 여기에는 변경 가능한 파라미터를 다루는 메서드들이 포함됩니다. 각 메서드는 한 줄로 정의될 수 있지만, 저자는 읽기 쉽도록 모든 메서드의 본문을 별도의 줄에 두었습니다. 하지만 원한다면 다음과 같이 더 간결하게 작성할 수도 있습니다.

```scala
def addTopping(t: Topping): Unit = toppings += t
def removeTopping(t: Topping): Unit = toppings -= t
def removeAllToppings(): Unit = toppings.clear()
```

이 길을 계속 따라간다면, 속성과 동작을 모두 캡슐화하는 추가적인 OOP 스타일 클래스들을 만들게 됩니다. 예를 들어, `Order` 클래스는 여러 라인 아이템(line items)으로 구성된 주문 개념을 완전히 캡슐화할 수 있습니다.

```scala
class Order:
    private val lineItems = ArrayBuffer[Product]()

    def addItem(p: Product): Unit = ???
    def removeItem(p: Product): Unit = ???
    def getItems(): Seq[Product] = ???

    def getPrintableReceipt(): String = ???
    def getTotalPrice(): Money = ???
end Order

// 사용법:
val o = Order()
o.addItem(Pizza(Small, Thin, ArrayBuffer(Cheese, Pepperoni)))
o.addItem(Cheesesticks)
```

이 예시는 다음과 같은 `Product` 클래스 계층 구조가 있다고 가정합니다.

```scala
// Product는 비용, 판매 가격, 기타 세부 정보를
// 결정하는 메서드를 가질 수 있습니다
sealed trait Product

// 각 클래스는 추가적인 속성과 메서드를 가질 수 있습니다
class Pizza extends Product
class Beverage extends Product
class Cheesesticks extends Product
```

대부분의 개발자가 속성과 동작을 캡슐화하고 다형성(polymorphic) 메서드를 사용하는 OOP 스타일에 익숙하다고 가정하기 때문에, 이 예시는 여기서 더 진행하지 않습니다.

#### 한 가지 더: 추상 클래스를 언제 사용할 것인가 (One more thing: When to use abstract classes)

Scala 3에서는 이제 트레이트도 파라미터를 받을 수 있고, 클래스는 하나의 추상 클래스만 확장(extend)할 수 있는 반면 여러 트레이트를 믹스인(mix in)할 수 있기 때문에, "추상 클래스는 언제 사용해야 하는가?"라는 질문이 생깁니다.

일반적인 답은 "거의 사용하지 않는다(rarely)"입니다. 좀 더 구체적인 답은 다음과 같습니다.

- Java에서 Scala 코드를 사용할 때는, 트레이트보다 클래스를 확장하는 것이 더 쉽습니다.
- 저자가 Scala Center에서 이 질문을 했을 때, Scala.js의 제작자인 Sébastien Doeraene는 "Scala.js에서는 클래스를 JavaScript로 import하거나 export할 수 있다"라고 답했습니다.
- 같은 논의에서 Scala Center의 교육 책임자인 Julien Richard-Foy는, 추상 클래스가 트레이트보다 약간 더 효율적인 인코딩을 가질 수 있다고 언급했습니다. 부모(parent)로서 트레이트는 동적(dynamic)인 반면, 추상 클래스는 정적으로(statically) 알려지기 때문입니다.

따라서 저자의 경험 법칙(rule of thumb)은, 항상 트레이트를 먼저 사용하고, 위의 조건 중 하나(또는 우리가 미처 생각하지 못한 다른 조건)에 해당하여 필요한 경우에만 추상 클래스로 물러나 사용하는 것입니다.

---

## 5.2 주 생성자 만들기 (Creating a Primary Constructor)

### 문제 (Problem)

Scala 클래스를 위한 주 생성자(primary constructor)를 만들고 싶은데, 그 접근법이 Java(및 다른 언어들)와 다르다는 것을 곧 알게 됩니다.

### 해결책 (Solution)

Scala 클래스의 주 생성자는 다음 요소들의 조합입니다.

- 생성자 파라미터(constructor parameters)
- 클래스 본문 내의 필드(변수 할당)
- 클래스 본문 내에서 실행되는 문(statements)과 표현식(expressions)

다음 클래스는 생성자 파라미터, 클래스 필드, 그리고 클래스 본문 내의 문들을 보여 줍니다.

```scala
class Employee(var firstName: String, var lastName: String):
    // 문(statement)
    println("the constructor begins ...")

    // 클래스 필드 (변수 할당)
    var age = 0
    private var salary = 0d

    // 메서드 호출
    printEmployeeInfo()

    // 클래스 내에 정의된 메서드들
    override def toString = s"$firstName $lastName is $age years old"
    def printEmployeeInfo() = println(this)  // toString을 사용함

    // 클래스 정의가 끝나기 전의 모든 문이나 필드는
    // 클래스 생성자의 일부입니다
    println("the constructor ends")

// 선택적인 'end' 문
end Employee
```

생성자 파라미터, 문, 필드는 모두 클래스 생성자의 일부입니다. 메서드들도 클래스 본문 안에 있지만, 이들은 생성자의 일부가 아니라는 점에 주목하세요.

클래스 본문 내의 **메서드 호출**도 생성자의 일부이므로, `Employee` 클래스의 인스턴스가 생성되면, 클래스 선언의 시작과 끝에 있는 `println` 문들의 출력과 함께 `printEmployeeInfo` 메서드 호출 결과도 볼 수 있습니다.

```scala
scala> val e = Employee("Kim", "Carnes")
the constructor begins ...
Kim Carnes is 0 years old
the constructor ends
val e: Employee = Kim Carnes is 0 years old
```

### 논의 (Discussion)

Java에서 Scala로 넘어오면, Scala에서 주 생성자를 선언하는 과정이 상당히 다르다는 것을 알게 됩니다. Java에서는 메인 생성자 안에 있을 때와 그렇지 않을 때가 꽤 명확하지만, Scala는 이 구분을 흐릿하게 만듭니다. 하지만 일단 이 접근법을 이해하고 나면, 클래스 선언을 더 간결하게 만드는 데 도움이 됩니다.

위 예시에서, 두 생성자 인자 `firstName`과 `lastName`은 `var` 필드로 정의되었으며, 이는 이들이 변수(variable)임을, 즉 **변경 가능(mutable)** 함을 의미합니다. 처음 설정된 후에도 값을 바꿀 수 있습니다. 필드가 변경 가능하고 — 또한 기본적으로 public 접근 권한을 가지므로 — Scala는 이들에 대한 접근자와 변경자 메서드를 모두 생성합니다. 그 결과, `Employee` 타입의 인스턴스 `e`가 주어졌을 때, 다음과 같이 값을 변경할 수 있습니다.

```scala
e.firstName = "Xena"
e.lastName = "Princess Warrior"
```

그리고 다음과 같이 접근할 수 있습니다.

```scala
println(e.firstName)   // Xena
println(e.lastName)    // Princess Warrior
```

`age` 필드가 `var`로 선언되었고 — 생성자 파라미터처럼 클래스 멤버도 기본적으로 public이므로 — 이 또한 보이고(visible) 변경 및 접근이 가능합니다.

```scala
e.age = 30
println(e.age)
```

반대로, `salary` 필드는 `private`로 선언되었기 때문에 클래스 외부에서 접근할 수 없습니다.

```scala
scala> e.salary
1 |e.salary
  |^^^^
  |variable salary cannot be accessed as a member of (e: Employee)
```

`printEmployeeInfo` 메서드 호출처럼 클래스 본문 내에서 메서드를 호출하면, 그것은 **문(statement)** 이며 역시 생성자의 일부입니다. 궁금하다면, `scalac`으로 코드를 *Employee.class* 파일로 컴파일한 후 JAD 같은 디컴파일러로 Java 소스 코드로 디컴파일하여 이를 확인할 수 있습니다. 그렇게 하면, `Employee` 생성자가 Java 코드로 디컴파일되었을 때 다음과 같이 보입니다.

```java
public Employee(String firstName, String lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
    super();
    Predef$.MODULE$.println("the constructor begins ...");
    age = 0;
    double salary = 0.0D;
    printEmployeeInfo();
    Predef$.MODULE$.println("the constructor ends");
}
```

이는 `Employee` 생성자 안에 두 개의 `println` 문과 `printEmployeeInfo` 메서드 호출이 있다는 것, 그리고 초기 `age`와 `salary`가 설정된다는 것을 명확하게 보여 줍니다.

> **주 생성자 내용 (Primary Constructor Contents)**
>
> Scala에서, 클래스 본문 내의 모든 문, 표현식, 또는 변수 할당은 주 클래스 생성자의 일부입니다.

비교의 마지막 포인트로, JAD로 클래스 파일을 디컴파일한 후 Scala와 Java 파일의 소스 코드 줄 수를 세어 보면 — 각 파일에 동일한 포맷팅 스타일을 사용했을 때 — Scala 소스 코드는 9줄이고 Java 소스 코드는 38줄임을 알 수 있습니다. 개발자가 코드를 작성하는 것보다 읽는 데 10배의 시간을 더 쓴다고들 하는데, 간결하면서도 여전히 읽기 쉬운 코드를 만들 수 있는 이 능력 — 우리는 이를 **표현력 있다(expressive)** 고 부릅니다 — 이 바로 저자가 처음 Scala에 끌리게 된 이유 중 하나입니다.

---

## 5.3 생성자 필드의 가시성 제어하기 (Controlling the Visibility of Constructor Fields)

### 문제 (Problem)

Scala 클래스에서 생성자 파라미터로 사용되는 필드의 가시성(visibility)을 제어하고 싶습니다.

### 해결책 (Solution)

다음 예시들에서 볼 수 있듯이, Scala 클래스에서 생성자 필드의 가시성은 (a) 필드가 `val`로 선언되었는지 `var`로 선언되었는지, 또는 `val`/`var` 둘 다 없는지, 그리고 (b) 필드에 `private`가 추가되었는지에 따라 제어됩니다.

해결책의 짧은 버전은 다음과 같습니다.

- 필드가 `var`로 선언되면, Scala는 해당 필드에 대해 getter와 setter 메서드를 모두 생성합니다.
- 필드가 `val`이면, Scala는 getter 메서드만 생성합니다.
- 필드에 `var`나 `val` 수식어가 없으면, Scala는 getter나 setter 메서드를 생성하지 않으며, 그 필드는 클래스에 대해 private이 됩니다.
- 추가로, `var`와 `val` 필드는 `private` 키워드로 수정할 수 있는데, 이는 public getter와 setter가 생성되는 것을 막습니다.

자세한 내용은 다음 예시들을 참고하세요.

#### var 필드 (var fields)

생성자 파라미터가 `var`로 선언되면, 필드의 값을 **변경할 수 있으므로** Scala는 해당 필드에 대해 getter와 setter 메서드를 모두 생성합니다. 다음 예시에서 생성자 파라미터 `name`이 `var`로 선언되었으므로, 필드에 접근하고 변경할 수 있습니다.

```scala
scala> class Person(var name: String)
scala> val p = Person("Mark Sinclair Vincent")

// getter
scala> p.name
val res0: String = Mark Sinclair Vincent

// setter
scala> p.name = "Vin Diesel"

scala> p.name
val res1: String = Vin Diesel
```

Java에 익숙하다면, Scala가 접근자와 변경자 메서드를 생성할 때 JavaBean의 `getName`/`setName` 네이밍 컨벤션을 따르지 않는다는 것도 볼 수 있습니다. 대신, 단순히 필드 이름으로 접근합니다.

#### val 필드 (val fields)

생성자 필드가 `val`로 정의되면, Java의 `final`처럼 일단 설정된 값을 **변경할 수 없습니다**. 따라서 이는 접근자 메서드는 가지되 변경자 메서드는 **가지지 않는** 것이 합리적입니다.

```scala
scala> class Person(val name: String)
defined class Person

scala> val p = Person("Jane Doe")
p: Person = Person@3f9f332b

// getter
scala> p.name
res0: String = Jane Doe

// setter 사용 시도
scala> p.name = "Wilma Flintstone"
1 |p.name = "Wilma Flintstone"
  |^^^^^^^^^
  |Reassignment to val name
```

마지막 예시는 `val` 필드에 대해서는 변경자 메서드가 생성되지 않기 때문에 실패합니다.

#### val이나 var가 없는 필드 (Fields without val or var)

생성자 파라미터에 `val`도 `var`도 지정되지 않으면, 그 필드는 클래스에 대해 private이 되며, Scala는 접근자나 변경자 메서드를 생성하지 않습니다. 다음과 같은 클래스를 만들면 이를 확인할 수 있습니다.

```scala
class SuperEncryptor(password: String):
    // String의 각 Char를 1씩 증가시켜 암호화
    private def encrypt(s: String) = s.map(c => (c + 1).toChar)
    def getEncryptedPassword = encrypt(password)
```

그런 다음 `val`이나 `var` 없이 선언된 `password` 필드에 접근하려고 시도하면:

```scala
val e = SuperEncryptor("1234")
e.password              // error: value password cannot be accessed
e.getEncryptedPassword  // 2345
```

보다시피, `password` 필드에 직접 접근할 수는 없지만, `getEncryptedPassword` 메서드는 클래스 멤버이므로 `password`에 접근할 수 있습니다. 이 코드로 계속 실험해 보면, `password`를 `val`이나 `var` 없이 선언하는 것은 그것을 `private val`로 만드는 것과 동등하다는 것을 알게 됩니다.

대부분의 경우 저자는 이 문법을 우연히만 사용합니다 — 필드에 대해 `val`이나 `var`를 지정하는 것을 잊었을 때 말이죠 — 하지만 생성자 파라미터를 받아서 클래스 내부에서 그 파라미터를 사용하되 클래스 외부에서는 직접 사용할 수 없게 하고 싶을 때는 의미가 있을 수 있습니다.

#### val이나 var에 private 추가하기 (Adding private to val or var)

이 세 가지 기본 구성에 더해, `val`이나 `var` 필드에 `private` 키워드를 추가할 수 있습니다. 이는 getter와 setter 메서드가 생성되는 것을 막으므로, 그 필드는 클래스의 멤버 내에서만 접근할 수 있게 됩니다. 다음 예시의 `salary` 필드처럼 말이죠.

```scala
enum Role:
    case HumanResources, WorkerBee

import Role.*

class Employee(var name: String, private var salary: Double):
    def getSalary(r: Role): Option[Double] = r match
        case HumanResources => Some(salary)
        case _ => None
```

이 코드에서 `getSalary`는 클래스 내부에 정의되어 있기 때문에 `salary` 필드에 접근할 수 있지만, `salary` 필드는 클래스 외부에서 직접 접근할 수 없습니다. 다음 예시에서 이를 보여 줍니다.

```scala
val e = Employee("Steve Jobs", 1)

// salary 필드에 접근하려면 getSalary를 사용해야 합니다
e.name                  // Steve Jobs
e.getSalary(WorkerBee)        // None
e.getSalary(HumanResources)   // Some(1.0)

e.salary  // error: variable salary in class Employee cannot be accessed
```

### 논의 (Discussion)

이 모든 게 헷갈린다면, 컴파일러가 코드를 생성할 때 어떤 선택을 하는지 생각해 보면 도움이 됩니다. 필드가 `val`로 정의되면, 정의상 그 값을 변경할 수 없으므로, getter는 생성하되 setter는 생성하지 않는 것이 합리적입니다. 마찬가지로, 정의상 `var` 필드의 값은 변경할 수 있으므로, getter와 setter를 모두 생성하는 것이 합리적입니다.

생성자 파라미터의 `private` 설정은 추가적인 유연성을 제공합니다. `val`이나 `var` 필드에 추가되면, getter와 setter 메서드가 이전처럼 생성되지만 `private`로 표시됩니다. 생성자 파라미터에 `val`이나 `var`를 지정하지 않으면, getter나 setter 메서드가 전혀 생성되지 않습니다.

이 설정들에 기반하여 생성되는 접근자와 변경자는 다음 표(Table 5-1)에 정리되어 있습니다.

**Table 5-1. 생성자 파라미터 설정의 효과**

| 가시성(Visibility) | 접근자(Accessor)? | 변경자(Mutator)? |
| --- | --- | --- |
| `var` | 예(Yes) | 예(Yes) |
| `val` | 예(Yes) | 아니오(No) |
| 기본 가시성 (var나 val 없음) | 아니오(No) | 아니오(No) |
| `var`나 `val`에 `private` 키워드 추가 | 아니오(No) | 아니오(No) |

#### 케이스 클래스 (Case classes)

**케이스 클래스(case class)** 의 생성자 파라미터는 이 규칙들과 한 가지 점에서 다릅니다. 케이스 클래스 생성자 파라미터는 기본적으로 `val`입니다. 따라서 다음과 같이 `val`이나 `var`를 추가하지 않고 케이스 클래스 필드를 정의해도:

```scala
case class Person(name: String)
```

마치 `val`로 정의된 것처럼 필드에 접근할 수 있습니다.

```scala
scala> val p = Person("Dale Cooper")
p: Person = Person(Dale Cooper)

scala> p.name
res0: String = Dale Cooper
```

이는 일반 클래스와는 다르지만, 멋진 편의 기능이며 케이스 클래스가 함수형 프로그래밍에서 의도된 사용 방식, 즉 **불변 레코드(immutable records)** 로서의 사용과 관련이 있습니다.

---

## 5.4 클래스의 보조 생성자 정의하기 (Defining Auxiliary Constructors for Classes)

### 문제 (Problem)

클래스의 사용자(consumers)가 오브젝트 인스턴스를 만드는 여러 가지 방법을 가질 수 있도록, 클래스에 하나 이상의 보조 생성자(auxiliary constructors)를 정의하고 싶습니다.

### 해결책 (Solution)

보조 생성자를 `this`라는 이름과 적절한 시그니처(signature)를 가진 메서드로 클래스 내에 정의합니다. 여러 개의 보조 생성자를 정의할 수 있지만, 각각은 서로 다른 시그니처(파라미터 목록)를 가져야 합니다. 또한, 각 생성자는 이전에 정의된 생성자 중 하나를 호출해야 합니다.

예시를 위해, 다음 `Pizza` 클래스에서 사용될 두 개의 이넘 정의를 보여 줍니다.

```scala
enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Thin, Regular, Thick
```

이 정의들이 주어졌을 때, 주 생성자와 세 개의 보조 생성자를 가진 `Pizza` 클래스는 다음과 같습니다.

```scala
import CrustSize.*, CrustType.*

// 주 생성자
class Pizza (var crustSize: CrustSize, var crustType: CrustType):

    // 인자 1개짜리 보조 생성자
    def this(crustSize: CrustSize) =
        this(crustSize, Pizza.DefaultCrustType)

    // 인자 1개짜리 보조 생성자
    def this(crustType: CrustType) =
        this(Pizza.DefaultCrustSize, crustType)

    // 인자 0개짜리 보조 생성자
    def this() =
        this(Pizza.DefaultCrustSize, Pizza.DefaultCrustType)

    override def toString = s"A $crustSize pizza with a $crustType crust"

object Pizza:
    val DefaultCrustSize = Medium
    val DefaultCrustType = Regular
```

이 생성자들이 주어지면, 같은 피자를 이제 다음과 같은 방법들로 만들 수 있습니다.

```scala
import Pizza.{DefaultCrustSize, DefaultCrustType}

// 다른 생성자들을 사용
val p1 = Pizza(DefaultCrustSize, DefaultCrustType)
val p2 = Pizza(DefaultCrustSize)
val p3 = Pizza(DefaultCrustType)
val p4 = Pizza
```

이 모든 정의는 동일한 출력을 만들어 냅니다.

```
A Medium pizza with a Regular crust
```

### 논의 (Discussion)

이 레시피에는 몇 가지 중요한 포인트가 있습니다.

- 보조 생성자는 `this`라는 이름의 메서드를 만들어서 정의합니다.
- 각 보조 생성자는 이전에 정의된 생성자 중 하나를 호출하는 것으로 시작해야 합니다.
- 각 생성자는 서로 다른 파라미터 목록을 가져야 합니다.
- 한 생성자는 메서드 이름 `this`를 사용해 다른 생성자를 호출하고, 원하는 파라미터를 지정합니다.

위 예시에서는 모든 보조 생성자가 주 생성자를 호출하지만, 이것이 반드시 필요한 것은 아닙니다. 보조 생성자는 이전에 정의된 생성자 중 하나만 호출하면 됩니다. 예를 들어, `crustType` 파라미터를 받는 보조 생성자는 `CrustSize` 생성자를 호출하도록 작성될 수도 있었습니다.

```scala
def this(crustType: CrustType) =
    this(Pizza.DefaultCrustSize)
    this.crustType = Pizza.DefaultCrustType
```

> **기본 파라미터 값을 잊지 마세요 (Don't Forget About Default Parameter Values)**
>
> 해결책에서 보여 준 접근법은 완전히 유효하지만, 이처럼 여러 클래스 생성자를 만들기 전에 잠시 Recipe 5.6을 읽어 보세요. 그 레시피에서 보여 주는 것처럼 기본 파라미터 값(default parameter values)을 사용하면 여러 생성자가 필요 없어지는 경우가 많습니다. 예를 들어, 다음 접근법은 해결책에서 보여 준 클래스와 거의 동일한 기능을 가집니다.
>
> ```scala
> class Pizza(
>     var crustSize: CrustSize = Pizza.DefaultCrustSize,
>     var crustType: CrustType = Pizza.DefaultCrustType
> ):
>     override def toString =
>         s"A $crustSize pizza with a $crustType crust"
> ```

---

## 5.5 private 주 생성자 정의하기 (Defining a Private Primary Constructor)

### 문제 (Problem)

싱글톤(Singleton) 패턴을 강제하는 등의 이유로, 클래스의 주 생성자를 private으로 만들고 싶습니다.

### 해결책 (Solution)

주 생성자를 private으로 만들려면, 클래스 이름과 생성자가 받는 파라미터 사이에 `private` 키워드를 삽입합니다.

```scala
// 인자 1개짜리 private 주 생성자
class Person private (var name: String)
```

REPL에서 보듯이, 이렇게 하면 클래스의 인스턴스를 만들 수 없게 됩니다.

```scala
scala> class Person private(name: String)
defined class Person

scala> val p = Person("Mercedes")
1 |val p = Person("Mercedes")
  |        ^^
  |method apply cannot be accessed as a member of Person.type
```

저자는 이 문법을 처음 봤을 때 조금 특이하다고 생각했지만, 코드를 훑으면서 소리 내어 읽어 보면 "이것은 **private 생성자**를 가진 `Person` 클래스다…"라고 읽히게 됩니다. 그 문장에서 "private 생성자(private constructor)"라는 단어가 `private` 키워드를 생성자 파라미터 바로 앞에 두는 것을 기억하는 데 도움이 된다고 합니다.

### 논의 (Discussion)

Scala에서 싱글톤 패턴을 강제하려면, 주 생성자를 `private`으로 만든 다음, 클래스의 **컴패니언 오브젝트(companion object)** 에 `getInstance` 메서드를 만듭니다.

```scala
// 파라미터를 받지 않는 private 생성자
class Brain private:
    override def toString = "This is the brain."

object Brain:
    val brain = Brain()
    def getInstance = brain

@main def singletonTest =
    // 생성자가 private이므로 컴파일되지 않습니다:
    // val brain = Brain()

    // 이것은 작동합니다:
    val brain = Brain.getInstance
    println(brain)
```

접근자 메서드의 이름을 반드시 `getInstance`로 지을 필요는 없습니다. 여기서는 Java 컨벤션 때문에 그렇게 했을 뿐입니다. 가장 적절해 보이는 이름으로 지으면 됩니다.

> **컴패니언 오브젝트 (Companion Objects)**
>
> **컴패니언 오브젝트**는 단순히 클래스와 같은 파일에 정의되고 그 클래스와 같은 이름을 가진 `object`입니다. *Foo.scala*라는 파일에 `Foo`라는 클래스를 선언하고, 같은 파일에 `Foo`라는 오브젝트를 선언하면, 그 `Foo` 오브젝트는 `Foo` 클래스의 컴패니언 오브젝트입니다.
>
> 컴패니언 오브젝트는 여러 목적으로 사용될 수 있으며, 그중 하나는 컴패니언 오브젝트에 선언된 모든 메서드가 오브젝트의 정적 메서드(static method)처럼 보이게 된다는 점입니다. Java의 정적 메서드와 동등한 것을 만드는 방법은 Recipe 7.4 "컴패니언 오브젝트로 정적 멤버 만들기"를, 컴패니언 오브젝트에 `apply` 메서드를 정의하는 방법과 이유에 대한 예시는 Recipe 7.6 "apply로 정적 팩토리 구현하기"를 참고하세요.

#### 유틸리티 클래스 (Utility classes)

무엇을 하려고 하느냐에 따라, private 클래스 생성자를 만드는 것이 전혀 필요하지 않을 수도 있습니다. 예를 들어, Java에서는 Java 클래스에 정적 메서드를 정의하여 **파일 유틸리티(file utilities)** 클래스를 만들지만, Scala에서는 Scala `object`에 메서드를 넣어 같은 작업을 할 수 있습니다.

```scala
object FileUtils:
    def readFile(filename: String): String = ???
    def writeFile(filename: String, contents: String): Unit = ???
```

이렇게 하면 코드 사용자가 `FileUtils` 클래스의 인스턴스를 만들 필요 없이 이 메서드들을 호출할 수 있습니다.

```scala
val contents = FileUtils.readFile("input.txt")
FileUtils.writeFile("output.txt", contents)
```

이런 경우에는 private 클래스 생성자가 필요 없습니다. 단지 클래스를 정의하지 않으면 됩니다.

---

## 5.6 생성자 파라미터에 기본값 제공하기 (Providing Default Values for Constructor Parameters)

### 문제 (Problem)

생성자 파라미터에 기본값(default value)을 제공하고 싶습니다. 이는 클래스 사용자에게 생성자를 호출할 때 해당 파라미터를 지정하거나 하지 않을 수 있는 선택권을 줍니다.

### 해결책 (Solution)

생성자 선언에서 파라미터에 기본값을 지정합니다. 다음은 `timeout`이라는 하나의 생성자 파라미터를 가지며 기본값이 `10_000`인 `Socket` 클래스의 선언입니다.

```scala
class Socket(val timeout: Int = 10_000)
```

파라미터가 기본값과 함께 정의되었으므로, `timeout` 값을 지정하지 않고도 생성자를 호출할 수 있으며, 이 경우 기본값을 얻게 됩니다.

```scala
val s = Socket()
s.timeout   // Int = 10000
```

새로운 `Socket`을 만들 때 원하는 `timeout` 값을 지정할 수도 있습니다.

```scala
val s = Socket(5_000)
s.timeout   // Int = 5000
```

### 논의 (Discussion)

이 레시피는 보조 생성자가 필요 없게 만들 수 있는 강력한 기능을 보여 줍니다. 해결책에서 보여 준 것처럼, 다음 단일 생성자는 두 생성자와 동등합니다.

```scala
class Socket(val timeout: Int = 10_000)
val s = Socket()
val s = Socket(5_000)
```

이 기능이 없다면, 같은 기능을 얻기 위해 두 개의 생성자가 필요할 것입니다 — 인자 1개짜리 주 생성자와 인자 0개짜리 보조 생성자.

```scala
class Socket(val timeout: Int):
    def this() = this(10_000)
```

#### 여러 파라미터 (Multiple parameters)

여러 생성자 파라미터에 기본값을 제공할 수도 있습니다.

```scala
class Socket(val timeout: Int = 1_000, val linger: Int = 2_000):
    override def toString = s"timeout: $timeout, linger: $linger"
```

생성자를 하나만 정의했음에도, 이제 클래스는 세 개의 생성자를 가진 것처럼 보입니다.

```scala
println(Socket())               // timeout: 1000, linger: 2000
println(Socket(3_000))          // timeout: 3000, linger: 2000
println(Socket(3_000, 4_000))   // timeout: 3000, linger: 4000
```

Recipe 8.3 "메서드 호출 시 파라미터 이름 사용하기"에서 보여 주듯이, 원한다면 클래스 인스턴스를 만들 때 생성자 파라미터의 이름을 제공할 수도 있습니다.

```scala
Socket(timeout=3_000, linger=4_000)
Socket(linger=4_000, timeout=3_000)
Socket(timeout=3_000)
Socket(linger=4_000)
```

---

## 5.7 클래스를 확장할 때 생성자 파라미터 다루기 (Handling Constructor Parameters When Extending a Class)

### 문제 (Problem)

생성자 파라미터를 가진 베이스 클래스(base class)를 확장하고 싶고, 새로운 서브클래스(subclass)는 추가적인 파라미터를 받을 수도 있습니다.

### 해결책 (Solution)

이 섹션에서는 하나 이상의 `val` 생성자 파라미터를 가진 클래스를 확장하는 경우를 다룹니다. `var`로 정의된 생성자 파라미터를 다루는 해결책은 더 복잡하며 Discussion에서 다룹니다.

#### val 생성자 파라미터를 다루기 (Working with val constructor parameters)

베이스 클래스가 `val` 생성자 파라미터만 가진다고 가정할 때, 서브클래스 생성자를 정의할 때는 두 클래스에 공통인 필드에서 `val` 선언을 생략합니다. 그런 다음 서브클래스에서 새로운 생성자 파라미터들을 `val`(또는 `var`) 필드로 정의합니다.

이를 보여 주기 위해, 먼저 `name`이라는 `val` 파라미터를 가진 `Person` 베이스 클래스를 정의합니다.

```scala
class Person(val name: String)
```

다음으로, `Person`의 서브클래스로 `Employee`를 정의하여, 생성자 파라미터 `name`과 새로운 파라미터 `age`를 받도록 합니다. `name` 파라미터는 부모 `Person` 클래스와 공통이므로 그 필드에서는 `val` 선언을 생략하고, `age`는 새로운 것이므로 `val`로 선언합니다.

```scala
class Employee(name: String, val age: Int) extends Person(name):
    override def toString = s"$name is $age years old"
```

이제 새로운 `Employee`를 만들 수 있습니다.

```scala
scala> val joe = Employee("Joe", 33)
val joe: Employee = Joe is 33 years old
```

이는 원하는 대로 작동하며, 필드가 불변(immutable)이기 때문에 다른 문제가 없습니다.

### 논의 (Discussion)

베이스 클래스의 생성자 파라미터가 `var` 필드로 정의되면, 상황이 더 복잡해집니다. 두 가지 가능한 해결책이 있습니다.

- 서브클래스에서 필드에 다른 이름을 사용한다.
- 서브클래스 생성자를 컴패니언 오브젝트의 `apply` 메서드로 구현한다.

#### 서브클래스에서 필드에 다른 이름 사용하기 (Use a different name for the field in the subclass)

첫 번째 접근법은 서브클래스 생성자에서 공통 필드에 다른 이름을 사용하는 것입니다. 예를 들어 이 예시에서는 `Employee` 생성자에서 `name` 대신 `_name`이라는 이름을 사용합니다.

```scala
class Person(var name: String)

// 여기서 '_name'을 사용한다는 점에 주목하세요
class Employee(_name: String, var age: Int) extends Person(_name):
    override def toString = s"$name is $age"
```

이렇게 하는 이유는, `Employee` 클래스의 이 생성자 파라미터(`_name`)가 `Employee` 클래스 내부의 필드로 생성되기 때문입니다. *Employee.class* 파일을 디스어셈블(disassemble)해 보면 이를 확인할 수 있습니다.

```
$ javap -private Employee
public class Employee extends Person {
    private final java.lang.String _name;   // name 필드
    private final int age;                   // age 필드
    public Employee(java.lang.String, int);
    public int age();
    public java.lang.String toString();
}
```

이 필드의 이름을 `name`으로 지었다면, `Employee` 클래스의 이 필드가 본질적으로 `Person` 클래스의 `name` 필드를 가려 버리게(cover up) 됩니다. 이는 다음 예시의 끝부분에서 보이는 것 같은 일관성 없는 결과 같은 문제를 야기합니다.

```scala
class Person(var name: String)

// '_name' 대신 'name'을 잘못 사용
class Employee(name: String, var age: Int) extends Person(name):
    override def toString = s"$name is $age years old"

// 처음에는 모든 게 괜찮아 보입니다
val e = Employee("Joe", 33)
e   // Joe is 33 years old

// 하지만 'name' 필드를 업데이트하면 문제가 드러납니다
e.name = "Fred"
e.age = 34
e        // "Joe is 34 years old"  <-- 오류: "Fred"여야 함
e.name   // "Fred"                 <-- 이것은 "Fred"임
```

이는 이 예시에서 `Employee`의 필드 이름을 (잘못) `name`으로 지었기 때문에 발생하며, 이 `name`이 `Person`의 `name`과 충돌합니다. 따라서 `var` 생성자 파라미터를 가진 클래스를 확장할 때는, 서브클래스에서 그 필드에 다른 이름을 사용하세요.

> **이는 private val 필드를 만듭니다 (This Creates a private val Field)**
>
> 이 예시에서, 보여 준 접근법은 `Employee` 클래스에 `_name`이라는 `private val` 필드를 만듭니다. 하지만 그 필드는 이 클래스 외부에서 접근할 수 없으므로, 그 필드를 사용하지 않는 한 비교적 사소한 문제입니다. 다른 누구도 그것을 사용할 수 없습니다.

#### 컴패니언 오브젝트에서 apply 메서드 사용하기 (Use an apply method in a companion object)

위 해결책이 `Employee` 클래스에 `_name`이라는 `private val` 필드를 만들기 때문에, 다른 해결책을 선호할 수도 있습니다.

문제를 해결하는 또 다른 방법은 다음과 같습니다.

- `Employee` 생성자를 private으로 만든다.
- `Employee` 컴패니언 오브젝트에 생성자 역할을 할 `apply` 메서드를 만든다.

예를 들어, `name`이라는 `var` 파라미터를 가진 이 `Person` 클래스가 주어졌을 때:

```scala
class Person(var name: String):
    override def toString = s"$name"
```

다음과 같이 private 생성자와 컴패니언 오브젝트의 `apply` 메서드를 가진 `Employee` 클래스를 만들 수 있습니다.

```scala
class Employee private extends Person(""):
    var age = 0
    println("Employee constructor called")
    override def toString = s"$name is $age"

object Employee:
    def apply(_name: String, _age: Int) =
        val e = new Employee()
        e.name = _name
        e.age = _age
        e
```

이제 이 단계들을 실행하면, 모든 것이 원하는 대로 작동합니다.

```scala
val e = Employee("Joe", 33)
e        // Joe is 33 years old

// name과 age 필드를 업데이트하고 검증
e.name = "Fred"
e.age = 34
e        // "Fred is 34 years old"
e.name   // "Fred"
```

이 접근법은 `Employee` 클래스가 이전 해결책에서처럼 `_name` 필드를 사용하지 않고도 `Person` 클래스로부터 `name` 필드를 상속받을 수 있게 합니다. 트레이드오프는 이 접근법이 코드가 조금 더 필요하다는 점이지만, 더 깔끔한 접근법입니다.

요약하면, `val` 생성자 파라미터만 가진 클래스를 확장한다면 해결책에서 보여 준 접근법을 사용하세요. 하지만 `var` 생성자 파라미터를 가진 클래스를 확장한다면, Discussion에서 보여 준 두 해결책 중 하나를 사용하세요.

---

## 5.8 슈퍼클래스 생성자 호출하기 (Calling a Superclass Constructor)

### 문제 (Problem)

서브클래스에서 생성자를 정의할 때 호출되는 슈퍼클래스(superclass) 생성자를 제어하고 싶습니다.

### 해결책 (Solution)

이것은 약간 함정이 있는 질문입니다. 서브클래스의 주 생성자가 호출하는 슈퍼클래스 생성자는 **제어할 수 있지만**, 서브클래스의 보조 생성자가 호출하는 슈퍼클래스 생성자는 **제어할 수 없기** 때문입니다.

Scala에서 서브클래스를 정의할 때, 서브클래스 선언의 `extends` 부분을 지정함으로써 주 생성자가 호출하는 슈퍼클래스 생성자를 제어합니다. 예를 들어, 다음 코드에서 `Dog` 클래스의 주 생성자는 `Pet` 클래스의 주 생성자를 호출하는데, 이는 `name`을 파라미터로 받는 인자 1개짜리 생성자입니다.

```scala
class Pet(var name: String)
class Dog(name: String) extends Pet(name)
```

더 나아가, `Pet` 클래스가 여러 생성자를 가지고 있다면, `Dog` 클래스의 주 생성자는 그 생성자들 중 어느 것이든 호출할 수 있습니다. 다음 예시에서 `Dog` 클래스의 주 생성자는 `extends` 절에서 그 생성자를 지정함으로써 `Pet` 클래스의 인자 1개짜리 보조 생성자를 호출합니다.

```scala
// (1) 인자 2개짜리 주 생성자
class Pet(var name: String, var age: Int):
    // (2) 인자 1개짜리 보조 생성자
    def this(name: String) = this(name, 0)
    override def toString = s"$name is $age years old"

// Pet의 인자 1개짜리 생성자를 호출
class Dog(name: String) extends Pet(name)
```

또는, `Pet` 클래스의 인자 2개짜리 주 생성자를 호출할 수도 있습니다.

```scala
// 인자 2개짜리 생성자를 호출
class Dog(name: String) extends Pet(name, 0)
```

하지만, 보조 생성자에 관해서는, 보조 생성자의 첫 줄은 **현재(current)** 클래스의 다른 생성자를 호출해야 하므로, 보조 생성자가 슈퍼클래스 생성자를 호출할 방법은 없습니다.

### 논의 (Discussion)

다음 코드에서 보듯이, `Employee` 클래스의 주 생성자는 `Person` 클래스의 어떤 생성자든 호출할 수 있지만, `Employee` 클래스의 보조 생성자는 그 첫 줄로 자기 클래스의 이전에 정의된 생성자를 `this` 메서드로 호출해야 합니다.

```scala
case class Address(city: String, state: String)
case class Role(role: String)

class Person(var name: String, var address: Address):
    // Employee 보조 생성자가 이 생성자를 호출할 방법이 없음
    def this(name: String) =
        this(name, null)
        address = null  // 실제 세계에서는 null을 사용하지 마세요

class Employee(name: String, role: Role, address: Address)
extends Person(name, address):

    def this(name: String) =
        this(name, null, null)

    def this(name: String, role: Role) =
        this(name, role, null)

    def this(name: String, address: Address) =
        this(name, null, address)
```

따라서, 서브클래스의 보조 생성자에서 어떤 슈퍼클래스 생성자가 호출될지 직접 제어할 방법은 없습니다. 실제로, 각 보조 생성자는 같은 클래스 내의 이전에 정의된 생성자를 호출해야 하므로, 모든 보조 생성자는 결국 서브클래스의 주 생성자에서 호출되는 동일한 슈퍼클래스 생성자를 호출하게 됩니다.

---

## 5.9 equals 메서드 정의하기 (객체 동등성) (Defining an equals Method (Object Equality))

### 문제 (Problem)

오브젝트 인스턴스들을 서로 비교할 수 있도록 클래스에 `equals` 메서드를 정의하고 싶습니다.

### 해결책 (Solution)

이 해결책은 약간의 배경 지식을 다루면 이해하기 더 쉬우므로, 먼저 알아야 할 세 가지를 공유합니다.

첫 번째는 오브젝트 인스턴스가 `==` 기호로 비교된다는 것입니다.

```scala
"foo" == "foo"   // true
"foo" == "bar"   // false
"foo" == null    // false
null == "foo"    // false
1 == 1           // true
1 == 2           // false

case class Person(name: String)
Person("Alex") == Person("Alvin")   // false
```

이는 원시 값(primitive values)에는 `==`를, 오브젝트 비교에는 `equals`를 사용하는 Java와 다릅니다.

알아야 할 두 번째는 `==`가 `Any` 클래스에 정의되어 있다는 것입니다. 따라서 (a) 다른 모든 클래스에 상속되고, (b) 클래스에 정의된 `equals` 메서드를 호출합니다. 무슨 일이 일어나냐면, `1 == 2`라고 쓰면 그 코드는 `1.==(2)`라고 쓰는 것과 같고, 그러면 그 `==` 메서드가 `1` 오브젝트의 `equals` 메서드를 호출합니다. 이 예시에서 `1`은 `Int`의 인스턴스입니다.

알아야 할 세 번째는, `equals` 메서드를 제대로 작성하는 것이 어려운 문제라는 것입니다. 너무 어려워서 *Programming in Scala*는 이를 논의하는 데 23페이지를, *Effective Java*는 오브젝트 동등성을 다루는 데 17페이지를 할애합니다. *Effective Java*는 그 설명을 다음과 같은 문장으로 시작합니다: "`equals` 메서드를 오버라이드하는 것은 간단해 보이지만, 잘못할 수 있는 방법이 많고, 그 결과는 끔찍할 수 있다." 이런 복잡성에도 불구하고, 저자는 이 문제에 대한 견고한 해결책을 보여 주고, 추가 읽을거리에 대한 참고 자료도 공유합니다.

#### 필요할 때만 equals를 구현하세요 (Don't implement equals unless necessary)

`equals` 메서드를 구현하는 방법으로 들어가기 전에, *Effective Java*가 다음과 같은 상황에서는 `equals` 메서드를 구현하지 **않는** 것이 올바른 해결책이라고 말한다는 점을 언급할 가치가 있습니다.

- 클래스의 각 인스턴스가 본질적으로 고유한(inherently unique) 경우. `Thread` 클래스의 인스턴스가 예시로 제시됩니다.
- 클래스가 논리적 동등성 테스트를 제공할 필요가 없는 경우. Java `Pattern` 클래스가 예시로 제시됩니다. 설계자들은 사람들이 이 기능을 원하거나 필요로 할 것이라고 생각하지 않았기 때문에, 단순히 Java `Object` 클래스로부터 동작을 상속받게 했습니다.
- 슈퍼클래스가 이미 `equals`를 오버라이드했고, 그 동작이 이 클래스에 적절한 경우.
- 클래스가 private이거나 (Java에서) package-private이고, 그 `equals` 메서드가 절대 호출되지 않을 것이 확실한 경우.

이것들은 Java 클래스에 대해 커스텀 `equals` 메서드를 작성하고 싶지 않은 네 가지 상황이며, 이 규칙들은 Scala에도 적용됩니다. 이 레시피의 나머지 부분은 `equals` 메서드를 제대로 구현하는 방법에 집중합니다.

#### 일곱 단계 프로세스 (A seven-step process)

*Programming in Scala* 4판은 final이 아닌 클래스에 대해 `equals` 메서드를 구현하는 일곱 단계 프로세스를 권장합니다.

1. `Any` 파라미터를 받고 `Boolean`을 반환하는 적절한 시그니처의 `canEqual` 메서드를 만든다.
2. `canEqual`은 자신에게 전달된 인자가 현재 클래스의 인스턴스이면 `true`를, 그렇지 않으면 `false`를 반환해야 한다. (상속에서는 **현재(current)** 클래스가 특히 중요합니다.)
3. `Any` 파라미터를 받고 `Boolean`을 반환하는 적절한 시그니처의 `equals` 메서드를 구현한다.
4. `equals`의 본문을 단일 `match` 표현식으로 작성한다.
5. `match` 표현식은 두 개의 case를 가져야 한다. 다음 코드에서 볼 수 있듯이, 첫 번째 case는 현재 클래스에 대한 타입 패턴(typed pattern)이어야 한다.
6. 이 첫 번째 case의 본문에서, 이 클래스에서 참이어야 하는 모든 테스트에 대해 일련의 논리적 "and(그리고)" 테스트를 구현한다. 이 클래스가 `AnyRef` 외의 무언가를 확장한다면, 이 테스트들의 일부로 슈퍼클래스의 `equals` 메서드를 호출하고 싶을 것이다. "and" 테스트 중 하나는 `canEqual` 호출이어야 한다.
7. 두 번째 case에는, `false`를 yield하는 와일드카드 패턴(wildcard pattern)을 지정한다.

실용적인 관점에서, `equals` 메서드를 구현할 때마다 `hashCode` 메서드도 구현해야 합니다. 이는 다음 예시에서 선택적인 여덟 번째 단계로 표시됩니다.

이 레시피에서는 두 가지 예시를 보여 줍니다. 하나는 여기서, 다른 하나는 Discussion에서 보여 줍니다.

#### 예시 1: 단일 클래스에 대한 equals 구현 (Implementing equals for a single class)

다음은 작은 Scala 클래스에 대해 `equals` 메서드를 제대로 작성하는 방법을 보여 주는 예시입니다. 이 예시에서는 두 개의 `var` 필드를 가진 `Person` 클래스를 만듭니다.

```scala
class Person(var name: String, var age: Int)
```

이 두 생성자 파라미터가 주어졌을 때, 다음은 `equals` 메서드와 그에 대응하는 `hashCode` 메서드를 구현한 `Person` 클래스의 완전한 소스 코드입니다. 주석은 코드가 해결책의 어느 단계를 가리키는지 보여 줍니다.

```scala
class Person(var name: String, var age: Int):

    // Step 1 - `canEqual`에 대한 적절한 시그니처
    // Step 2 - `a`를 현재 클래스와 비교
    // (isInstanceOf는 true나 false를 반환)
    def canEqual(a: Any) = a.isInstanceOf[Person]

    // Step 3 - `equals`에 대한 적절한 시그니처
    // Steps 4 thru 7 - `match` 표현식 구현
    override def equals(that: Any): Boolean =
        that match
            case that: Person =>
                that.canEqual(this) &&
                this.name == that.name &&
                this.age == that.age
            case _ =>
                false

    // Step 8 (선택) - 대응하는 hashCode 메서드 구현
    override def hashCode: Int =
        val prime = 31
        var result = 1
        result = prime * result + age
        result = prime * result + (if name == null then 0 else name.hashCode)
        result

end Person
```

그 코드를 앞서 설명한 일곱 단계와 비교해 보면, 그것들이 일치한다는 것을 알 수 있습니다. 해결책의 핵심은 첫 번째 `case` 문 내부의 다음 코드입니다.

```scala
case that: Person =>
    that.canEqual(this) &&
    this.name == that.name &&
    this.age == that.age
```

이 코드는 `that`이 `Person`의 인스턴스인지 테스트합니다.

```scala
case that: Person =>
```

`that`이 `Person`이 **아니면**, 다른 `case` 문이 실행됩니다.

다음으로, 이 코드 줄은 반대 상황을 테스트합니다. 현재 인스턴스(`this`)가 `that`의 클래스의 인스턴스인지 말입니다.

```scala
that.canEqual(this) ...
```

이는 상속이 관련될 때 특히 중요합니다. 예를 들어 `Employee`가 `Person`의 인스턴스이지만 `Person`은 `Employee`의 인스턴스가 아닌 경우처럼 말입니다.

그 후, `canEqual` 다음의 나머지 코드는 `Person` 클래스의 개별 필드들의 동등성을 테스트합니다.

`equals` 메서드가 정의되면, 다음 ScalaTest 단위 테스트에서 보듯이 `Person`의 인스턴스들을 `==`로 비교할 수 있습니다.

```scala
import org.scalatest.funsuite.AnyFunSuite

class PersonTests extends AnyFunSuite:

    // 처음 두 인스턴스는 동등해야 합니다
    val nimoy    = Person("Leonard Nimoy", 82)
    val nimoy2   = Person("Leonard Nimoy", 82)
    val nimoy83  = Person("Leonard Nimoy", 83)
    val shatner  = Person("William Shatner", 82)

    // [1] 기본 테스트로 시작
    test("nimoy    != null")     { assert(nimoy != null) }

    // [2] 이 reflexive(반사적)와 symmetric(대칭적) 테스트는 모두 true여야 합니다
    // [2a] reflexive
    test("nimoy    == nimoy")    { assert(nimoy == nimoy) }
    // [2b] symmetric
    test("nimoy    == nimoy2")   { assert(nimoy == nimoy2) }
    test("nimoy2   == nimoy")    { assert(nimoy2 == nimoy) }

    // [3] 이것들은 동등하지 않아야 합니다
    test("nimoy    != nimoy83")  { assert(nimoy != nimoy83) }
    test("nimoy    != shatner")  { assert(nimoy != shatner) }
    test("shatner != nimoy")     { assert(shatner != nimoy) }
```

이 모든 테스트는 통과합니다. Discussion에서 reflexive와 symmetric 주석이 설명되며, 두 번째 예시는 `Employee` 클래스가 `Person`을 확장할 때 이 공식이 어떻게 작동하는지 보여 줍니다.

> **IntelliJ IDEA**
>
> 이 글을 쓰는 시점에, `name`과 `age` 필드를 가진 `Person` 클래스가 주어졌을 때, IntelliJ IDEA 버전 2021.1.4는 이 해결책에서 보여 준 코드와 거의 동일한 `equals` 메서드를 생성합니다.

### 논의 (Discussion)

Scala에서 `==`가 작동하는 방식은, `nimoy == shatner`처럼 클래스 인스턴스에서 호출되면 `nimoy`의 `equals` 메서드가 호출된다는 것입니다. 짧게 말하면, 이 코드:

```scala
nimoy == shatner
```

는 이 코드와 같고:

```scala
nimoy.==(shatner)
```

이는 또 이 코드와 같습니다:

```scala
nimoy.equals(shatner)
```

보다시피, `==` 메서드는 `equals`를 호출하기 위한 일종의 문법적 설탕(syntactic sugar)과 같습니다. `nimoy.equals(shatner)`라고 쓸 수도 있지만, `==`가 사람이 읽기에 훨씬 쉽기 때문에 아무도 그렇게 하지 않습니다.

> **equals 계약 (The equals Contract)**
>
> `Any` 클래스의 `equals` 메서드에 대한 Scaladoc은 본질적으로 `equals` 메서드가 어떻게 구현되어야 하는지에 대한 계약(contract)을 명시합니다. 그것은 "이 메서드의 모든 구현은 **동치 관계(equivalence relation)** 여야 한다"라고 명시하는 것으로 시작합니다. 더 나아가 동치 관계는 다음 세 가지 속성을 가져야 한다고 명시합니다.
>
> - **reflexive(반사적)** 이다: `Any` 타입의 임의의 인스턴스 `x`에 대해, `x.equals(x)`는 `true`를 반환해야 한다.
> - **symmetric(대칭적)** 이다: `Any` 타입의 임의의 인스턴스 `x`와 `y`에 대해, `x.equals(y)`는 `y.equals(x)`가 `true`를 반환할 때 그리고 오직 그때만 `true`를 반환해야 한다.
> - **transitive(추이적)** 이다: `AnyRef` 타입의 임의의 인스턴스 `x`, `y`, `z`에 대해, `x.equals(y)`가 `true`를 반환하고 `y.equals(z)`가 `true`를 반환하면, `x.equals(z)`는 `true`를 반환해야 한다.
>
> 마지막으로 "`equals` 메서드를 오버라이드한다면, 그 구현이 동치 관계를 유지하는지 검증해야 한다"라고 명시합니다.
>
> `Person` 예시는 이 기준을 충족합니다.

이제 상속이 관련될 때 이것을 어떻게 다루는지 살펴보겠습니다.

#### 예시 2: 상속 (Inheritance)

이 접근법의 중요한 이점은 클래스에서 상속을 사용할 때도 계속 사용할 수 있다는 것입니다. 예를 들어, 다음 코드에서 `Employee` 클래스는 해결책에서 보여 준 `Person` 클래스를 확장합니다. 첫 번째 예시에서 보여 준 것과 같은 공식을 사용하되, (a) `Employee`의 새로운 `role` 필드를 테스트하고, (b) `Person`의 `equals`도 참인지 검증하기 위해 `super.equals(that)`를 호출하는 추가 테스트가 있습니다.

```scala
class Employee(name: String, age: Int, var role: String)
extends Person(name, age):
    override def canEqual(a: Any) = a.isInstanceOf[Employee]

    override def equals(that: Any): Boolean =
        that match
            case that: Employee =>
                that.canEqual(this) &&
                this.role == that.role &&
                super.equals(that)
            case _ =>
                false

    override def hashCode: Int =
        val prime = 31
        var result = 1
        result = prime * result + (if role == null then 0 else role.hashCode)
        result + super.hashCode

end Employee
```

이 코드에서 주목할 점:

- `canEqual`은 (`Person`이 아니라) `Employee`의 인스턴스인지 확인합니다.
- 첫 번째 `case` 표현식도 (`Person`이 아니라) `Employee`를 테스트합니다.
- `Employee` case는 `canEqual`을 호출하고, 자기 클래스의 필드를 테스트하며, `Person`의 동등성 테스트를 사용하기 위해 `super.equals(that)`도 호출합니다. 이는 `Person`의 필드들과 `Employee`의 새로운 `role` 필드가 모두 동등한지 보장합니다.

다음 ScalaTest 단위 테스트는 `Employee`의 `equals` 메서드가 올바르게 구현되었는지 검증합니다.

```scala
import org.scalatest.funsuite.AnyFunSuite

class EmployeeTests extends AnyFunSuite:

    // 처음 두 인스턴스는 동등해야 합니다
    val eNimoy1 = Employee("Leonard Nimoy", 82, "Actor")
    val eNimoy2 = Employee("Leonard Nimoy", 82, "Actor")
    val pNimoy = Person("Leonard Nimoy", 82)
    val eShatner = Employee("William Shatner", 82, "Actor")

    // 동등성 테스트 (reflexive와 symmetric)
    test("eNimoy1 == eNimoy1") { assert(eNimoy1 == eNimoy1) }
    test("eNimoy1 == eNimoy2") { assert(eNimoy1 == eNimoy2) }
    test("eNimoy2 == eNimoy1") { assert(eNimoy2 == eNimoy1) }

    // 비동등성 테스트
    test("eNimoy1 != pNimoy")   { assert(eNimoy1 != pNimoy) }
    test("pNimoy   != eNimoy1") { assert(pNimoy != eNimoy1) }
    test("eNimoy1 != eShatner") { assert(eNimoy1 != eShatner) }
    test("eShatner != eNimoy1") { assert(eShatner != eNimoy1) }
```

각각 `Employee`와 `Person` 클래스의 인스턴스인 `eNimoy`와 `pNimoy` 오브젝트의 비교를 포함하여, 모든 테스트가 통과합니다.

#### var 필드와 변경 가능한 컬렉션이 있는 equals 메서드를 조심하세요 (Beware equals methods with var fields and mutable collections)

경고로서, 이 예시들이 `equals`와 `hashCode` 메서드를 구현하는 견고한 공식을 보여 주지만, Artima 블로그 글 "How to Write an Equality Method in Java"는 `equals`와 `hashCode` 알고리즘이 **변경 가능한 상태(mutable state)** — 즉 `name`, `age`, `role` 같은 `var` 필드 — 에 의존할 때, 이것이 컬렉션 사용자에게 문제가 될 수 있다고 설명합니다.

기본적인 문제는, 만약 클래스 사용자가 변경 가능한 필드를 컬렉션에 넣으면, 필드가 컬렉션에 들어간 후에 바뀔 수 있다는 것입니다. 다음은 이 문제를 보여 주는 예시입니다. 먼저, 다음과 같이 `Employee` 인스턴스를 만듭니다.

```scala
val eNimoy = Employee("Leonard Nimoy", 81, "Actor")
```

그런 다음 그 인스턴스를 `Set`에 추가합니다.

```scala
val set = scala.collection.mutable.Set[Employee]()
set += eNimoy
```

이 코드를 실행하면, 예상대로 `true`를 반환하는 것을 볼 수 있습니다.

```scala
set.contains(eNimoy)   // true
```

하지만 이제 `eNimoy` 인스턴스를 수정한 다음 같은 테스트를 실행하면, (아마도) `false`를 반환하는 것을 보게 됩니다.

```scala
eNimoy.age = 82
set.contains(eNimoy)   // false
```

이 문제를 다루는 것에 관해, Artima 블로그 글은 이런 상황에서는 `hashCode`를 오버라이드하지 말고 동등성 메서드의 이름을 `equals`가 아닌 다른 것으로 지어야 한다고 제안합니다. 이렇게 하면, 클래스가 `hashCode`와 `equals`의 기본 구현을 상속받게 됩니다.

#### hashCode 구현하기 (Implementing hashCode)

`hashCode` 알고리즘을 깊이 다루지는 않겠지만, *Effective Java*에서 Joshua Bloch는 다음 문장들이 `hashCode` 알고리즘에 대한 계약을 구성한다고 씁니다(Java `Object` 문서에서 각색한 것).

- `hashCode`가 애플리케이션 내에서 한 오브젝트에 대해 반복적으로 호출될 때, `equals` 메서드 비교에서 사용되는 정보가 변경되지 않았다면 일관되게 같은 값을 반환해야 한다.
- 두 오브젝트가 그들의 `equals` 메서드에 따라 동등하다면, 그들의 `hashCode` 값은 같아야 한다.
- 두 오브젝트가 그들의 `equals` 메서드에 따라 **동등하지 않다면(unequal)**, 그들의 `hashCode` 값이 달라야 할 필요는 없다. 하지만 동등하지 않은 오브젝트에 대해 서로 다른 결과를 만들어 내면 해시 테이블의 성능을 향상시킬 수 있다.

`hashCode` 알고리즘에 대한 간략한 개요로서, `Person` 클래스에서 사용한 알고리즘은 *Effective Java*의 제안과 일관됩니다.

```scala
// 참고: `if name == null then 0` 테스트가 필요한 이유는
// `null.hashCode`가 NullPointerException을 던지기 때문입니다
override def hashCode: Int =
    val prime = 31
    var result = 1
    result = prime * result + age
    result = prime * result + (if name == null then 0 else name.hashCode)
    result
```

다음으로, 이것은 `Person`을 `case` 클래스로 만든 다음 Scala 3 `scalac` 명령으로 컴파일하고 JAD로 디컴파일하여 만들어진 `hashCode` 메서드입니다.

```java
public int hashCode() {
    int i = 0xcafebabe;
    i = Statics.mix(i, productPrefix().hashCode());
    i = Statics.mix(i, Statics.anyHash(name()));
    i = Statics.mix(i, age());
    return Statics.finalizeHash(i, 2);
}
```

IntelliJ IDEA의 **generate code** 옵션은 Scala 2.x 버전의 `Person` 클래스에 대해 이 코드를 생성합니다.

```scala
// scala 2 문법
override def hashCode(): Int = {
    val state = Seq(super.hashCode(), name, age)
    state.map(_.hashCode()).foldLeft(0)((a, b) => 31 * a + b)
}
```

---

## 5.10 접근자와 변경자 메서드 생성 막기 (Preventing Accessor and Mutator Methods from Being Generated)

### 문제 (Problem)

클래스 필드를 `var`로 정의하면 Scala가 자동으로 접근자(getter)와 변경자(setter) 메서드를 생성하고, 필드를 `val`로 정의하면 자동으로 접근자 메서드를 생성하는데, 접근자도 변경자도 원하지 않습니다.

### 해결책 (Solution)

해결책은 다음 중 하나를 하는 것입니다.

- `val`이나 `var` 선언에 `private` 접근 수식어를 추가하여 현재 클래스의 인스턴스에서만 접근할 수 있도록 한다.
- `protected` 접근 수식어를 추가하여 현재 클래스를 확장하는 클래스들이 접근할 수 있도록 한다.

#### private 수식어 (The private modifier)

`private` 접근 수식어가 어떻게 작동하는지에 대한 예시로, 이 `Animal` 클래스는 `_numLegs`를 private 필드로 선언합니다. 그 결과, 다른 비-`Animal` 인스턴스는 `_numLegs`에 접근할 수 없지만, `iHaveMoreLegs` 메서드는 다른 `Animal` 인스턴스(`that`)의 `_numLegs` 필드에 접근할 수 있다는 점에 주목하세요(`that._numLegs`처럼).

```scala
class Animal:
    private var _numLegs = 2
    def numLegs = _numLegs                  // getter
    def numLegs_=(numLegs: Int): Unit =     // setter
        _numLegs = numLegs

    // `_numLegs` 필드에 다른 Animal 인스턴스(`that`)에서
    // 접근할 수 있다는 점에 주목하세요
    def iHaveMoreLegs(that: Animal): Boolean =
        this._numLegs > that._numLegs
```

그 코드가 주어지면, 다음 ScalaTest `assert` 테스트들이 모두 통과합니다.

```scala
val a = Animal()
assert(a.numLegs == 2)   // getter 테스트

a.numLegs = 4
assert(a.numLegs == 4)   // setter 테스트

// 기본 다리 수는 2이므로, 이것은 true입니다
val b = Animal()
assert(a.iHaveMoreLegs(b))
```

또한, 클래스 외부에서 `_numLegs`에 접근하려고 시도하면, 이 코드가 컴파일되지 않는 것을 볼 수 있습니다.

```scala
//a._numLegs   // error, cannot be accessed (다른 곳에서 _numLegs에 접근 불가)
```

#### protected 수식어 (The protected modifier)

`Animal`의 `_numLegs` 필드를 `private`에서 `protected`로 변경하면, `Animal`을 확장하면서 `_numLegs` 값을 오버라이드하는 새로운 클래스를 만들 수 있습니다.

```scala
class Dog extends Animal:
    _numLegs = 4
```

이렇게 하고 두 개의 `Dog` 인스턴스를 만들면, 이전 테스트들과 마찬가지로 이 모든 테스트가 통과하는 것을 볼 수 있습니다.

```scala
val a = Dog()
assert(a.numLegs == 4)

a.numLegs = 3
assert(a.numLegs == 3)

// 기본 다리 수는 4이므로, 이것은 true입니다
val b = Dog()
assert(b.iHaveMoreLegs(a))
```

마찬가지로, `_numLegs`는 여전히 클래스 외부에서 접근할 수 없으므로, 이 코드 줄은 컴파일되지 않습니다.

```scala
a._numLegs   // 컴파일러 오류, 접근 불가
```

### 논의 (Discussion)

Scala 생성자 파라미터와 필드는 기본적으로 public하게 접근 가능하므로, 그 필드들이 접근자나 변경자를 갖지 않기를 원할 때는, 필드를 `private`나 `protected`로 정의하면 보여 준 수준의 제어를 얻을 수 있습니다.

> **클래스를 디스어셈블하여 JVM이 보는 것을 확인하세요 (Disassemble Classes to See What the JVM Sees)**
>
> 상기시켜 드리자면, Scala가 어떻게 작동하는지 더 알고 싶을 때마다, 작은 테스트를 만든 후 `javap`로 디컴파일하는 것이 도움이 될 수 있습니다. 예를 들어, 여기 두 개의 `val` 필드를 가진 클래스가 있는데, `i`는 public이고 `d`는 private입니다.
>
> ```scala
> class Foo(val i: Int, private val d: Double)
> ```
>
> 그 클래스를 `scalac`으로 컴파일한 다음 `javap`로 디스어셈블하면, JVM이 보는 것을 볼 수 있습니다.
>
> ```
> $ javap -private Foo
> Compiled from "Foo.scala"
> public class Foo {
>     public Foo(int, double);
>
>     private final int i;     // i는 private이고
>     public int i();          // public 접근자 메서드를 가짐
>
>     private final double d;  // d는 private이고
>     private double d();      // private 접근자 메서드를 가짐
> }
> ```
>
> 이 코드의 출력을 설명하기 위해 주석을 추가했지만, 나머지는 `javap` 명령이 생성한 것입니다. 명령줄에서 `javap -help`를 입력하면 클래스 파일 디스어셈블에 사용할 수 있는 다른 옵션들을 볼 수 있습니다.

---

## 5.11 기본 접근자와 변경자 오버라이드하기 (Overriding Default Accessors and Mutators)

### 문제 (Problem)

Scala가 생성해 주는 getter나 setter 메서드를 오버라이드하고 싶습니다.

### 해결책 (Solution)

이것은 약간 함정이 있는 문제입니다. 왜냐하면 — 적어도 Scala 네이밍 컨벤션을 고수하고자 한다면 — Scala가 생성하는 getter와 setter 메서드를 직접 오버라이드할 수 없기 때문입니다. 예를 들어, `name`이라는 생성자 파라미터를 가진 `Person` 클래스가 있고, Scala 컨벤션에 따라 getter와 setter 메서드를 만들려고 하면, 코드가 컴파일되지 않습니다.

```scala
// error: 이것은 작동하지 않습니다
class Person(private var name: String):
    def name = name
    def name_=(aName: String): Unit =
        name = aName
```

이 코드를 컴파일하려고 하면 다음 오류가 생성됩니다.

```
2 |    def name = name
  |                  ^
  |               Overloaded or recursive method name needs return type
```

이 문제들은 Discussion에서 더 자세히 살펴보지만, 짧은 답은 생성자 파라미터와 getter 메서드가 둘 다 `name`으로 이름 지어졌고, Scala는 이를 허용하지 않는다는 것입니다.

이 문제를 해결하려면, 클래스 생성자에서 사용하는 필드의 이름을 변경하여 사용하고 싶은 getter 메서드의 이름과 충돌하지 않도록 합니다. 한 가지 접근법은 파라미터 이름에 앞쪽 밑줄(leading underscore)을 추가하는 것입니다. 따라서 `name`이라는 getter 메서드를 만들고 싶다면, 생성자에서 파라미터 이름을 `_name`으로 사용하고, 그런 다음 Scala 컨벤션에 따라 getter와 setter 메서드를 선언합니다.

```scala
class Person(private var _name: String):
    def name = _name                            // accessor
    def name_=(aName: String): Unit = _name = aName   // mutator
```

생성자 파라미터가 `private`와 `var`로 선언되었다는 점에 주목하세요. `private` 키워드는 Scala가 그 필드를 다른 클래스에 노출하는 것을 막고, `var`는 `_name`의 값을 변경할 수 있게 합니다.

Discussion에서 보듯이, `name`이라는 getter 메서드와 `name_=`라는 setter 메서드를 만드는 것은 `name`이라는 필드에 대한 Scala 컨벤션을 따르며, 클래스 사용자가 다음과 같은 코드를 작성할 수 있게 합니다.

```scala
val p = Person("Winston Bishop")

// setter
p.name = "Winnie the Bish"

// getter
println(p.name)   // "Winnie the Bish"를 출력
```

getter와 setter에 대한 이 Scala 네이밍 컨벤션을 따르고 싶지 않다면, 원하는 다른 어떤 접근법이든 사용할 수 있습니다. 예를 들어, JavaBeans 스타일을 따라 메서드 이름을 `getName`과 `setName`으로 지을 수 있습니다.

이를 요약하면, 기본 getter와 setter 메서드를 오버라이드하는 레시피는 다음과 같습니다.

1. 클래스 내부에서 참조하고 싶은 이름으로 `private var` 생성자 파라미터를 만든다. 이 예시에서 필드 이름은 `_name`입니다.
2. 다른 클래스들이 사용할 getter와 setter 메서드 이름을 정의한다. 이 예시에서 getter 메서드 이름은 `name`이고, setter 메서드 이름은 `name_=`입니다(이는 Scala의 문법적 설탕과 결합되어 사용자가 `p.name = "Winnie the Bish"`라고 쓸 수 있게 합니다).
3. getter와 setter 메서드의 본문을 원하는 대로 수정한다.

> **클래스 필드도 같은 방식으로 작동합니다 (Class Fields Work the Same Way)**
>
> 이 예시들은 클래스 생성자의 필드를 사용하지만, 클래스 내부에 정의된 필드에 대해서도 같은 원칙이 적용됩니다.

### 논의 (Discussion)

생성자 파라미터를 `var` 필드로 정의하고 코드를 컴파일하면, Scala는 그 필드를 클래스에 private으로 만들고, 다른 클래스가 그 필드에 접근할 수 있도록 getter와 setter 메서드를 자동으로 생성합니다. 예를 들어, 다음 `Stock` 클래스가 주어졌을 때:

```scala
class Stock(var symbol: String)
```

클래스를 `scalac`으로 클래스 파일로 컴파일한 다음 JAD 같은 도구로 디컴파일하면, 다음과 같은 Java 코드를 보게 됩니다.

```java
public class Stock {
    public Stock(String symbol) {
        this.symbol = symbol;
        super();
    }
    public String symbol() {
        return symbol;
    }
    public void symbol_$eq(String x$1) {
        symbol = x$1;
    }
    private String symbol;
}
```

Scala 컴파일러가 두 개의 메서드를 생성하는 것을 볼 수 있습니다. `symbol`이라는 getter와 `symbol_$eq`라는 setter입니다. 이 두 번째 메서드는 Scala 코드에서 `symbol_=`라고 이름 지을 메서드와 같지만, 내부적으로 Scala는 JVM과 작동하기 위해 `=` 기호를 `$eq`로 변환해야 합니다.

그 두 번째 메서드 이름은 조금 특이하지만, Scala 컨벤션을 따르며, 약간의 문법적 설탕과 섞이면 다음과 같이 `Stock` 인스턴스의 `symbol` 필드를 설정할 수 있게 합니다.

```scala
stock.symbol = "GOOG"
```

이것이 작동하는 방식은, 무대 뒤에서 Scala가 그 코드 줄을 다음 코드 줄로 변환하는 것입니다.

```scala
stock.symbol_$eq("GOOG")
```

이것은 변경자 메서드를 오버라이드하고 싶지 않은 한, 일반적으로 전혀 생각할 필요가 없는 것입니다.

---

## 5.12 (lazy) 필드에 블록이나 함수 할당하기 (Assigning a Block or Function to a (lazy) Field)

### 문제 (Problem)

코드 블록을 사용하거나, 메서드 또는 함수를 호출하여 클래스의 필드를 초기화하고 싶습니다.

### 해결책 (Solution)

원하는 코드 블록이나 함수를 클래스 본문 내의 필드에 할당합니다. 선택적으로, 알고리즘이 실행하는 데 오랜 시간이 걸린다면 필드를 `lazy`로 정의합니다.

다음 예시 클래스에서, `text` 필드는 코드 블록 — `try`/`catch` 블록 — 과 같게 설정되는데, 이는 파일이 존재하고 읽을 수 있는지에 따라 (a) 파일에 담긴 텍스트 또는 (b) 오류 메시지를 반환합니다.

```scala
import scala.io.Source

class FileReader(filename: String):
    // 이 코드 블록을 'text' 필드에 할당
    val text =
        // 'fileContents'는 파일 내용 또는
        // 예외 메시지를 문자열로 담게 됩니다
        val fileContents =
            try
                Source.fromFile(filename).getLines.mkString
            catch
                case e: Exception => e.getMessage
        println(fileContents)   // 내용 출력
        fileContents            // 블록에서 내용 반환

@main def classFieldTest =
    val reader = FileReader("/etc/passwd")
```

코드 블록을 `text` 필드에 할당하는 것이 `FileReader` 클래스의 본문 안에 있기 때문에, 이 코드는 클래스의 생성자 안에 있으며 새로운 클래스 인스턴스가 생성될 때 실행됩니다. 따라서 이 예시를 컴파일하고 실행하면, 파일의 내용이나 파일을 읽으려다 발생한 예외 메시지를 출력합니다. 어느 쪽이든, 코드 블록이 — `println` 문을 포함하여 — 실행되고, 그 결과가 `text` 필드에 할당됩니다.

### 논의 (Discussion)

의미가 있다면, 이런 필드를 `lazy`로 정의할 수 있는데, 이는 그 필드가 접근될 때까지 평가되지 않음을 의미합니다. 이를 보여 주기 위해, 이전 예시를 `text`를 `lazy val` 필드로 만들어 업데이트합니다.

```scala
import scala.io.Source

class FileReader(filename: String):
    // 이전 예시와의 유일한 차이는
    // 이 필드가 'lazy'로 정의되었다는 것입니다
    lazy val text =
        val fileContents =
            try
                Source.fromFile(filename).getLines.mkString
            catch
                case e: Exception => e.getMessage
        println(fileContents)
        fileContents

@main def classFieldTest =
    val reader = FileReader("/etc/passwd")
```

이제 이 예시를 실행하면, 아무 일도 일어나지 않습니다. 출력이 보이지 않는데, 이는 `text` 필드가 접근될 때까지 평가되지 않기 때문입니다. 코드 블록은 `reader.text`를 호출하기 전까지 실행되지 않으며, 그 시점에서 `println` 문의 출력을 보게 됩니다.

이것이 `lazy` 필드가 작동하는 방식입니다. 클래스의 필드로 정의되었음에도 — 즉 클래스 생성자의 일부임에도 — 그 코드는 명시적으로 요청하기 전까지 실행되지 않습니다.

필드를 `lazy`로 정의하는 것은, 알고리즘의 일반적인 처리 과정에서 그 필드에 접근하지 않을 수도 있을 때, 또는 알고리즘 실행에 오랜 시간이 걸려서 그것을 나중으로 미루고 싶을 때 유용한 접근법입니다.

---

## 5.13 초기화되지 않은 var 필드 타입 설정하기 (Setting Uninitialized var Field Types)

### 문제 (Problem)

클래스 내의 초기화되지 않은(uninitialized) `var` 필드에 타입을 설정하고 싶어서, 다음과 같이 코드를 작성하기 시작합니다.

```scala
var x =
```

그러고는 이 표현식을 어떻게 끝맺을지 고민하게 됩니다.

### 해결책 (Solution)

일반적으로, 가장 좋은 접근법은 필드를 `Option`으로 정의하는 것입니다. `String`이나 숫자 필드 같은 특정 타입에 대해서는 기본 초기값을 지정할 수 있습니다.

예를 들어, 다음번 대박 소셜 네트워크를 시작하면서 사람들의 가입을 유도하기 위해, 등록 과정 중에는 username과 password만 묻는다고 상상해 봅시다. 따라서 `username`과 `password`를 클래스 생성자의 필드로 정의합니다.

```scala
case class Person(var username: String, var password: String) ...
```

하지만 나중에는 사용자로부터 나이, 이름(first name), 성(last name), 주소 같은 다른 정보도 얻고 싶을 것입니다. 처음 세 개의 `var` 필드를 기본값으로 설정하는 것은 간단합니다.

```scala
var age = 0
var firstName = ""
var lastName = ""
```

하지만 `address`에 이르면 어떻게 해야 할까요? 해결책은 `address` 필드를 `Option`으로 정의하는 것입니다. 여기 보이는 것처럼 말이죠.

```scala
case class Person(var username: String, var password: String):
    var age = 0
    var firstName = ""
    var lastName = ""
    var address: Option[Address] = None

case class Address(city: String, state: String, zip: String)
```

나중에, 사용자가 주소를 제공하면, 다음과 같이 `Some`을 사용해 할당합니다.

```scala
val p = Person("alvinalexander", "secret")
p.address = Some(Address("Talkeetna", "AK", "99676"))
```

`address` 필드에 접근해야 할 때는, 사용할 수 있는 다양한 접근법이 있으며, 이들은 Recipe 24.6 "Scala의 오류 처리 타입 사용하기 (Option, Try, Either)"에서 자세히 다룹니다. 한 예로, `foreach`를 사용해 `Address`의 필드들을 출력할 수 있습니다.

```scala
p.address.foreach { a =>
    println(s"${a.city}, ${a.state}, ${a.zip}")
}
```

`address` 필드가 할당되지 않았다면, `address`는 `None` 값을 가지며, 그것에 `foreach`를 호출해도 아무 일도 일어나지 않습니다. `address` 필드가 할당되었다면, 그것은 `Address`를 담은 `Some`이 되고, `Some`의 `foreach` 메서드가 `Some`에서 값을 추출하여 `foreach`로 데이터를 출력합니다.

### 논의 (Discussion)

`None`의 `foreach` 메서드의 본문을 다음과 같이 정의된 것으로 생각할 수 있습니다.

```scala
def foreach[A,U](f: A => U): Unit = {}
```

`None`은 항상 비어 있도록 보장되기 때문에 — 그것은 빈 컨테이너입니다 — 그것의 `foreach` 메서드는 본질적으로 아무 일도 하지 않는 메서드입니다. (실제로는 이와 다르게 구현되어 있지만, 이렇게 생각하는 것이 편리합니다.)

마찬가지로, `Some`에 `foreach`를 호출하면, 그것은 자신이 하나의 요소 — 예를 들어 `Address`의 인스턴스 — 를 담고 있음을 알기 때문에, 그 요소에 알고리즘을 적용합니다.

---

## 5.14 케이스 클래스로 보일러플레이트 코드 생성하기 (Generating Boilerplate Code with Case Classes)

### 문제 (Problem)

`match` 표현식, Akka actors, 또는 접근자와 변경자 메서드를 비롯해 `apply`, `unapply`, `toString`, `equals`, `hashCode` 메서드 등 보일러플레이트 코드를 생성하기 위해 **케이스 클래스(case class)** 문법을 사용하고 싶은 다른 상황들을 다루고 있습니다.

### 해결책 (Solution)

함수형 프로그래밍에서 클래스를 만들 때처럼 — 클래스가 많은 추가 내장 기능을 갖기를 원할 때 — 클래스를 **케이스 클래스**로 정의하고, 생성자에 필요한 파라미터들을 선언합니다.

```scala
// name과 relation은 기본적으로 'val'입니다
case class Person(name: String, relation: String)
```

클래스를 케이스 클래스로 정의하면 많은 유용한 보일러플레이트 코드가 생성되며, 다음과 같은 이점이 있습니다.

- 케이스 클래스 생성자 파라미터는 기본적으로 `val`이므로, 생성자 파라미터에 대해 접근자 메서드가 생성됩니다. `var`로 선언된 파라미터에 대해서는 변경자 메서드도 생성됩니다.
- 좋은 기본 `toString` 메서드가 생성됩니다.
- `unapply` 메서드가 생성되어, 케이스 클래스를 `match` 표현식에서 쉽게 사용할 수 있게 합니다.
- `equals`와 `hashCode` 메서드가 생성되므로, 인스턴스들을 쉽게 비교하고 컬렉션에서 사용할 수 있습니다.
- `copy` 메서드가 생성되어, 기존 인스턴스로부터 새로운 인스턴스를 쉽게 만들 수 있게 합니다(함수형 프로그래밍에서 사용되는 기법).

다음은 이 기능들을 보여 주는 예시입니다. 먼저, 케이스 클래스와 그 인스턴스를 정의합니다.

```scala
case class Person(name: String, relation: String)
val emily = Person("Emily", "niece")   // Person(Emily,niece)
```

케이스 클래스 생성자 파라미터는 기본적으로 `val`이므로, 파라미터에 대해 접근자 메서드가 생성되지만, 변경자 메서드는 생성되지 않습니다.

```scala
scala> emily.name
res0: String = Emily

// `name`을 변경할 수 없습니다
scala> emily.name = "Miley"
1 |emily.name = "Miley"
  |^^^^^^^^^^
  |Reassignment to val name
```

비-FP 스타일로 코드를 작성한다면, 생성자 파라미터를 `var` 필드로 선언할 수 있으며, 그러면 접근자와 변경자 메서드가 모두 생성됩니다.

```scala
scala> case class Company(var name: String)
defined class Company

scala> val c = Company("Mat-Su Valley Programming")
c: Company = Company(Mat-Su Valley Programming)

scala> c.name
res0: String = Mat-Su Valley Programming

scala> c.name = "Valley Programming"
c.name: String = Valley Programming
```

케이스 클래스는 좋은 기본 `toString` 메서드 구현도 가집니다.

```scala
scala> emily
res0: Person = Person(Emily,niece)
```

케이스 클래스에 대해 `unapply` 메서드가 자동으로 생성되기 때문에, `match` 표현식에서 정보를 추출해야 할 때 잘 작동합니다.

```scala
scala> emily match { case Person(n, r) => println(s"$n, $r") }
(Emily,niece)
```

케이스 클래스에 대해 `equals`와 `hashCode` 메서드가 그 생성자 파라미터에 기반하여 생성되므로, 인스턴스들을 map과 set에서 사용할 수 있고, `if` 표현식에서 쉽게 비교할 수 있습니다.

```scala
scala> val hannah = Person("Hannah", "niece")
hannah: Person = Person(Hannah,niece)

scala> emily == hannah
res0: Boolean = false
```

케이스 클래스는 또한 `copy` 메서드를 생성하는데, 이는 오브젝트를 복제(clone)하면서 복제 과정에서 일부 필드를 변경해야 할 때 유용합니다.

```scala
scala> case class Person(firstName: String, lastName: String)
// defined case class Person

scala> val fred = Person("Fred", "Flintstone")
val fred: Person = Person(Fred,Flintstone)

scala> val wilma = fred.copy(firstName = "Wilma")
val wilma: Person = Person(Wilma,Flintstone)
```

이 기법은 FP에서 흔히 사용되며, 저자는 이를 **update as you copy(복사하면서 업데이트하기)** 라고 부릅니다.

### 논의 (Discussion)

케이스 클래스는 주로 Scala 코드를 FP 스타일로 작성할 때 불변 레코드(immutable records)를 만들기 위해 의도되었습니다. 실제로, 순수 FP 개발자들은 케이스 클래스를 ML, Haskell, 그리고 다른 FP 언어들에서 볼 수 있는 불변 레코드와 유사한 것으로 봅니다. 케이스 클래스는 — 모든 것이 불변인 — FP와 함께 사용하도록 의도되었기 때문에, 케이스 클래스 생성자 파라미터는 기본적으로 `val`입니다.

#### 생성된 코드 (Generated code)

해결책에서 보여 준 것처럼, 케이스 클래스를 만들면 Scala는 클래스를 위한 풍부한 코드를 생성합니다. 생성되는 코드를 보려면, 먼저 간단한 케이스 클래스를 컴파일한 다음 `javap`로 디스어셈블합니다. 예를 들어, 이 코드를 *Person.scala*라는 파일에 넣습니다.

```scala
case class Person(var name: String, var age: Int)
```

그런 다음 파일을 컴파일합니다.

```
$ scalac Person.scala
```

이는 두 개의 클래스 파일, *Person.class*와 *Person$.class*를 만듭니다. *Person.class* 파일은 `Person` 클래스의 바이트코드를 담고 있으며, 이 명령으로 그 코드를 디스어셈블할 수 있습니다.

```
$ javap -public Person
```

이는 `Person` 클래스의 public 시그니처인 다음 출력을 만들어 냅니다.

```java
Compiled from "Person.scala"
public class Person implements scala.Product,java.io.Serializable {
    public static Person apply(java.lang.String, int);
    public static Person fromProduct(scala.Product);
    public static Person unapply(Person);
    public Person(java.lang.String, int);
    public scala.collection.Iterator productIterator();
    public scala.collection.Iterator productElementNames();
    public int hashCode();
    public boolean equals(java.lang.Object);
    public java.lang.String toString();
    public boolean canEqual(java.lang.Object);
    public int productArity();
    public java.lang.String productPrefix();
    public java.lang.Object productElement(int);
    public java.lang.String productElementName(int);
    public java.lang.String name();
    public void name_$eq(java.lang.String);
    public int age();
    public void age_$eq(int);
    public Person copy(java.lang.String, int);
    public java.lang.String copy$default$1();
    public int copy$default$2();
    public java.lang.String _1();
    public int _2();
}
```

다음으로, 컴패니언 오브젝트의 바이트코드를 담고 있는 *Person$.class*를 디스어셈블합니다.

```
$ javap -public Person$

Compiled from "Person.scala"
public final class Person$ implements
scala.deriving.Mirror$Product,java.io.Serializable {
    public static final Person$ MODULE$;
    public static {};
    public Person apply(java.lang.String, int);
    public Person unapply(Person);
    public java.lang.String toString();
    public Person fromProduct(scala.Product);
    public java.lang.Object fromProduct(scala.Product);
}
```

보다시피, 클래스를 케이스 클래스로 선언하면 Scala는 **많은(lot)** 소스 코드를 생성합니다.

비교 포인트로, 그 코드에서 `case` 키워드를 제거하여 — 일반 클래스로 만들어서 — 컴파일하면, *Person.class* 파일만 만들어집니다. 그것을 디스어셈블하면, Scala가 다음 코드만 생성하는 것을 볼 수 있습니다.

```java
Compiled from "Person.scala"
public class Person {
    public Person(java.lang.String, int);
    public java.lang.String name();
    public void name_$eq(java.lang.String);
    public int age();
    public void age_$eq(int);
}
```

이는 큰 차이입니다. 케이스 클래스는 총 30개의 메서드를 만들어 내는 반면, 일반 클래스는 단 5개만 만들어 냅니다. 이 기능이 필요하다면 이것은 좋은 일이며, 실제로 FP에서는 이 모든 메서드가 사용됩니다. 하지만 이 추가 기능 전부가 필요하지 않다면, 대신 일반 `class` 선언을 사용하고 원하는 대로 추가하는 것을 고려하세요.

> **케이스 클래스는 단지 편의 기능일 뿐입니다 (Case Classes Are Just a Convenience)**
>
> 케이스 클래스가 매우 편리하지만, 그 안에 우리가 직접 코딩할 수 없는 것은 아무것도 없다는 점을 기억하는 것이 중요합니다.

#### 케이스 오브젝트 (Case objects)

Scala에는 **케이스 오브젝트(case objects)** 도 있는데, 이는 많은 유사한 추가 메서드가 생성된다는 점에서 케이스 클래스와 비슷합니다. 케이스 오브젝트는 Akka actors를 위한 불변 메시지를 만들 때처럼 특정 상황에서 유용합니다.

```scala
sealed trait Message
case class Speak(text: String) extends Message
case object StopSpeaking extends Message
```

이 예시에서, `Speak`는 파라미터를 필요로 하므로 케이스 클래스로 선언되었지만, `StopSpeaking`은 파라미터를 필요로 하지 않으므로 케이스 오브젝트로 선언되었습니다.

하지만, Scala 3에서는 케이스 오브젝트 대신 이넘을 사용할 수 있는 경우가 많다는 점에 주목하세요.

```scala
enum Message:
    case Speak(text: String)
    case StopSpeaking
```

---

## 5.15 케이스 클래스의 보조 생성자 정의하기 (Defining Auxiliary Constructors for Case Classes)

### 문제 (Problem)

이전 레시피와 유사하게, 일반 클래스가 아니라 **케이스 클래스(case class)** 에 하나 이상의 보조 생성자를 정의하고 싶습니다.

### 해결책 (Solution)

케이스 클래스는 많은(lot) 보일러플레이트 코드를 생성해 주는 특별한 종류의 클래스입니다. 케이스 클래스가 작동하는 방식 때문에, 케이스 클래스에 보조 생성자처럼 **보이는** 것을 추가하는 것은 일반 클래스에 보조 생성자를 추가하는 것과 다릅니다. 이는 그것들이 실제로는 생성자가 아니기 때문입니다. 그것들은 클래스의 컴패니언 오브젝트에 있는 `apply` 메서드입니다.

이를 보여 주기 위해, *Person.scala*라는 파일에 이 케이스 클래스로 시작합니다.

```scala
// 초기 케이스 클래스
case class Person(var name: String, var age: Int)
```

이는 새로운 `Person` 인스턴스를 만들 수 있게 합니다.

```scala
val p = Person("John Smith", 30)
```

이 코드는 일반 `class`와 같아 보이지만, 실제로는 다르게 구현됩니다. 그 마지막 코드 줄을 작성하면, 무대 뒤에서 Scala 컴파일러는 그것을 다음과 같이 변환합니다.

```scala
val p = Person.apply("John Smith", 30)
```

이는 `Person` 클래스의 **컴패니언 오브젝트**에 있는 `apply` 메서드에 대한 호출입니다. 그 줄을 작성하는 것은 보이지 않지만 — 단지 우리가 작성한 줄만 보지만 — 이것이 컴파일러가 코드를 변환하는 방식입니다. 그 결과, 케이스 클래스에 새로운 생성자를 추가하고 싶다면, 새로운 `apply` 메서드를 작성합니다. (명확히 하자면, 여기서 **생성자(constructor)** 라는 단어는 느슨하게 사용됩니다. `apply` 메서드를 작성하는 것은 팩토리 메서드를 작성하는 것에 더 가깝습니다.)

예를 들어, 파라미터를 전혀 지정하지 않고 만드는 것과 `name`만 지정해서 만드는, 두 개의 보조 생성자를 추가하여 새로운 `Person` 인스턴스를 만들 수 있게 하고 싶다면, 해결책은 *Person.scala* 파일의 `Person` 케이스 클래스의 컴패니언 오브젝트에 `apply` 메서드를 추가하는 것입니다.

```scala
// 케이스 클래스
case class Person(var name: String, var age: Int)

// 컴패니언 오브젝트
object Person:
    def apply() = new Person("<no name>", 0)         // 인자 0개짜리 생성자
    def apply(name: String) = new Person(name, 0)    // 인자 1개짜리 생성자
```

다음 코드는 이것이 원하는 대로 작동함을 보여 줍니다.

```scala
val a = Person()                       // Person(<no name>,0)
val b = Person("Sarah Bracknell")      // Person(Sarah Bracknell,0)
val c = Person("Sarah Bracknell", 32)  // Person(Sarah Bracknell,32)

// setter 메서드가 작동하는지 검증
a.name = "Sarah Bannerman"
a.age = 38
println(a)                             // Person(Sarah Bannerman,38)
```

마지막으로, 컴패니언 오브젝트의 `apply` 메서드에서 새로운 `Person` 인스턴스를 만들기 위해 `new` 키워드가 사용된다는 점에 주목하세요.

```scala
object Person:
    def apply() = new Person("<no name>", 0)
                  ---
```

이것은 `new`가 필요한 드문 상황 중 하나입니다. 이 상황에서, 그것은 컴파일러에게 클래스 생성자를 사용하라고 알려 줍니다. `new`를 생략하면, 컴파일러는 우리가 컴패니언 오브젝트의 `apply` 메서드를 참조하는 것으로 가정하는데, 이는 순환(circular) 또는 재귀(recursive) 참조를 만들게 됩니다.
