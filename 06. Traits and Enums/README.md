# Chapter 6. Traits and Enums


## 챕터 개요

`trait`과 `enum`은 대규모 Scala 애플리케이션을 구성하는 기본 빌딩 블록이기 때문에, 이 두 번째 도메인 모델링 챕터에서 함께 다룹니다.

trait은 동작(behavior)을 세분화된 단위로 정의하는 데 사용할 수 있으며, 이렇게 잘게 나눈 단위들을 결합하여 더 큰 컴포넌트를 만들 수 있습니다. Recipe 6.1에서 보듯이, 가장 기본적인 형태로 사용할 경우 trait은 Java 8 이전의 `interface`처럼 쓸 수 있습니다. 이때 trait의 주된 용도는, 이를 확장하는 클래스가 반드시 구현해야 하는 abstract method의 시그니처를 선언하는 것입니다.

그러나 Scala의 trait은 이보다 훨씬 강력하고 유연합니다. abstract 멤버뿐만 아니라 concrete method와 concrete field까지 정의할 수 있습니다. 그리고 클래스와 object는 여러 개의 trait을 mix in(혼합)할 수 있습니다. 이러한 특징들은 Recipe 6.2, 6.3, 6.4에서 다룹니다.

이 접근 방식을 간단히 보여주는 예로, 개(dog)가 할 수 있는 모든 것을 하나의 `Dog` 클래스에 전부 정의하려고 시도하는 대신, Scala에서는 꼬리(tail), 다리(legs), 눈(eyes), 귀(ears), 코(nose) 같은 더 작은 기능 단위를 trait으로 정의할 수 있습니다. 이런 작은 단위들은 생각하기 쉽고, 만들기 쉽고, 테스트하기 쉽고, 사용하기 쉬우며, 나중에 결합하여 완전한 개를 만들 수 있습니다.

```scala
class Dog extends Tail, Legs, Ears, Mouth, RubberyNose
```

이것은 Scala trait이 할 수 있는 것에 대한 아주 제한적인 소개에 불과합니다. 추가적인 기능들은 다음과 같습니다.

- abstract field와 concrete field (Recipe 6.2)
- abstract method와 concrete method (Recipe 6.3)
- 여러 개의 trait을 mix in할 수 있는 클래스 (Recipe 6.4, 6.5에서 다룸)
- trait이 mix in될 수 있는 클래스를 제한하는 기능 (Recipe 6.6, 6.7, 6.8에서 다룸)
- 어떤 클래스와 함께 사용될 수 있는지 제한하기 위해 trait을 매개변수화(parameterize)할 수 있는 기능 (Recipe 6.9)
- 생성자 매개변수(constructor parameters)를 가질 수 있는 trait (Recipe 6.10)
- trait을 사용해 모듈(modules)을 만드는 기능 (Recipe 6.11). 이는 대규모 애플리케이션을 조직화하고 단순화하는 훌륭한 방법입니다.

이 모든 기능들은 이 챕터의 recipe들에서 다룹니다.

### A Brief Introduction to Traits (trait 간단 소개)

trait이 어떻게 사용되는지 보여주는 간단한 예로, `Pet`이라는 trait의 소스 코드를 살펴봅니다. 이 trait은 하나의 concrete method와 하나의 abstract method를 가지고 있습니다.

```scala
trait Pet:
    def speak() = println("Yo")   // concrete implementation
    def comeToMaster(): Unit      // abstract method
```

여기서 보듯이, concrete method는 구현(body)을 가진 메서드이고, abstract method는 body가 없는 메서드입니다.

다음으로, `HasLegs`라는 trait이 있는데, 이 trait은 concrete한 `run` 메서드를 내장하고 있습니다.

```scala
trait HasLegs:
    def run() = println("I'm running!")
```

마지막으로, `Pet`과 `HasLegs` 두 trait을 모두 mix in하면서, abstract method인 `comeToMaster`에 대한 concrete 구현을 제공하는 `Dog` 클래스가 있습니다.

```scala
class Dog extends Pet, HasLegs:
    def comeToMaster() = println("I'm coming!")
```

이제 새로운 `Dog` 인스턴스를 생성하고 그 메서드들을 호출하면, 다음과 같은 출력을 볼 수 있습니다.

```scala
val d = Dog()
d.speak()        // yo
d.comeToMaster() // I'm coming!
d.run()          // I'm running
```

이것이 기본적인 *trait as an interface*(인터페이스로서의 trait) 기능을 살짝 엿본 것입니다. 이는 여러 trait을 mix in하여 클래스를 만드는 하나의 방법입니다.

### Trait Construction Order (trait 생성 순서)

이어지는 recipe들에서 다루지 않는 한 가지 포인트는, 클래스가 여러 trait을 mix in할 때 trait들이 생성되는 순서입니다. 예를 들어, 다음과 같은 trait들이 있다고 합시다.

```scala
trait First:
    println("First is constructed")
trait Second:
    println("Second is constructed")
trait Third:
    println("Third is constructed")
```

그리고 이 trait들을 mix in하는 다음 클래스가 있을 때,

```scala
class MyClass extends First, Second, Third:
    println("MyClass is constructed")
```

`MyClass`의 새 인스턴스가 생성되면,

```scala
val c = MyClass()
```

다음과 같은 출력이 나옵니다.

```
First is constructed
Second is constructed
Third is constructed
MyClass is constructed
```

이는 trait들이 클래스 자체가 생성되기 전에, 왼쪽에서 오른쪽 순서로 생성된다는 것을 보여줍니다.

### enum 소개

trait을 다룬 후, 이 챕터의 마지막 두 강의에서는 Scala 3에서 새로 도입된 `enum` 구문을 다룹니다. `enum`(enumeration의 약자)은 (a) sealed class 또는 trait과 (b) 그 클래스의 컴패니언 객체(companion object)의 멤버로 정의된 값들을, 함께 정의할 수 있는 단축 표기(shortcut)입니다.

`enum`은 단축 표기이지만, 강력하고 간결한 단축 표기입니다. 상수 이름값(constant named values)들의 집합을 만드는 데 사용할 수 있고, 대수적 데이터 타입(algebraic data types, ADTs)을 구현하는 데도 사용할 수 있습니다. 상수 집합을 정의하는 데 사용하는 방법은 Recipe 6.12에서, ADT를 정의하는 데 사용하는 방법은 Recipe 6.13에서 다룹니다.

---

## 6.1 Using a Trait as an Interface

### Problem

다른 언어에서 순수 인터페이스(pure interface) — 구현 없이 메서드 시그니처만 선언하는 것 — 를 만드는 데 익숙해서, Scala에서도 그와 비슷한 것을 만들고 그 인터페이스를 concrete class와 함께 사용하고 싶습니다.

### Solution

가장 기본적인 수준에서, Scala trait은 Java 8 이전의 인터페이스처럼 사용할 수 있습니다. 즉, 메서드 시그니처는 정의하되 그에 대한 구현은 제공하지 않는 방식입니다.

예를 들어, 개나 고양이처럼 꼬리를 가진 동물을 모델링하는 코드를 작성한다고 상상해 봅시다. 가장 먼저 떠오르는 생각은, 꼬리는 흔들 수 있다는 것입니다. 그래서 다음과 같이 두 개의 메서드 시그니처를 가지고 메서드 body는 없는 trait을 정의합니다.

```scala
trait HasTail:
    def startTail(): Unit
    def stopTail(): Unit
```

이 두 메서드는 매개변수를 받지 않습니다. 정의하려는 메서드가 매개변수를 받는 경우에는, 평소처럼 매개변수를 선언하면 됩니다.

```scala
trait HasLegs:
    def startRunning(speed: Double): Unit
    def runForNSeconds(speed: Double, numSeconds: Int): Unit
```

#### Extending traits (trait 확장하기)

반대 측면에서, trait을 확장하는 클래스를 만들고 싶을 때는 `extends` 키워드를 사용합니다.

```scala
class Dog extends HasTail
```

클래스가 여러 개의 trait을 확장할 때는, 첫 번째 trait에 `extends`를 사용하고 그 뒤에 이어지는 trait들은 쉼표(comma)로 구분합니다.

```scala
class Dog extends HasTail, HasLegs, HasRubberyNose
```

클래스가 trait을 확장하지만 그 trait의 abstract method를 모두 구현하지 않는 경우, 그 클래스는 반드시 `abstract`로 선언되어야 합니다.

```scala
abstract class Dog extends HasTail, HasLegs:
    // does not implement methods from HasTail or HasLegs so
    // it must be declared abstract
```

그러나 클래스가 확장하는 trait들의 모든 abstract method에 대한 구현을 제공한다면, 그 클래스는 일반 클래스로 선언될 수 있습니다.

```scala
class Dog extends HasTail, HasLegs:
    def startTail(): Unit = println("Tail is wagging")
    def stopTail(): Unit = println("Tail is stopped")
    def startRunning(speed: Double): Unit =
        println(s"Running at $speed miles/hour")
    def runForNSeconds(speed: Double, numSeconds: Int): Unit =
        println(s"Running at $speed miles/hour for $numSeconds seconds")
```

### Discussion

위 예제들에서 보듯이, 가장 기본적인 수준에서 trait은 단순한 인터페이스로 사용할 수 있습니다. 그러면 클래스는 다음 규칙에 따라 `extends` 키워드를 사용하여 trait을 확장합니다.

- 클래스가 하나의 trait을 확장할 때는 `extends` 키워드를 사용합니다.
- 클래스가 여러 개의 trait을 확장할 때는 첫 번째 trait에 `extends`를 사용하고 나머지는 쉼표로 구분합니다.
- 클래스가 (일반 클래스 또는 abstract class인) 클래스 하나와 trait을 확장할 때는, 항상 클래스 이름을 먼저 나열하고 — 클래스 이름 앞에 `extends`를 사용하고 — 그다음 추가되는 trait 이름들 앞에 쉼표를 사용합니다.

이어지는 일부 recipe에서 보게 되겠지만, trait은 다른 trait을 확장할 수도 있습니다.

```scala
trait SentientBeing:
    def imAlive_!(): Unit = println("I'm alive!")
trait Furry
trait Dog extends SentientBeing, Furry
```

---

## 6.2 Defining Abstract Fields in Traits

### Problem

trait이 어떤 field를 가져야 한다고 선언하고 싶지만, 그 field에 초깃값을 주고 싶지는 않습니다. 즉, 그 field를 abstract하게 만들고 싶습니다.

### Solution

시간이 지나면서 Scala 개발자들은, trait에서 abstract field를 정의하는 가장 단순하면서도 가장 유연한 방법이 `def`를 사용하는 것임을 알게 되었습니다.

```scala
trait PizzaTrait:
    def maxNumToppings: Int
```

이렇게 하면 trait을 확장하는 클래스(및 trait)에서 그 field를 다양한 방식으로 오버라이드할 수 있습니다. `val`로 구현할 수도 있고,

```scala
class SmallPizza extends PizzaTrait:
    val maxNumToppings = 4
```

`lazy val`로 구현할 수도 있으며,

```scala
class SmallPizza extends PizzaTrait:
    lazy val maxNumToppings =
        // some long-running operation
        Thread.sleep(1_000)
        4
```

`var`로 구현할 수도 있고,

```scala
class MediumPizza extends PizzaTrait:
    var maxNumToppings = 6
```

`def`로 구현할 수도 있습니다.

```scala
class LargePizza extends PizzaTrait:
    def maxNumToppings: Int =
        // some algorithm here
        42
```

### Discussion

trait의 field는 concrete일 수도 있고 abstract일 수도 있습니다.

- 값을 할당하면 concrete입니다.
- 값을 할당하지 않으면 abstract입니다.

구현 관점에서 보면, 이것은 다음처럼 단순합니다.

```scala
trait Foo:
    def bar: Int   // abstract
    val a = 1      // concrete val
    var b = 2      // concrete var
```

이러한 선택지들이 모두 가능하지만, 시간이 지나면서 Scala 개발자들은 trait에서 field를 정의하는 가장 유연한 방법 — 그리고 가장 abstract한 방법 — 이 그 field를 `def`로 선언하는 것임을 알게 되었습니다. Solution에서 보았듯이, `def`로 선언하면 trait을 확장하는 클래스에서 그 field를 구현할 수 있는 다양한 방법이 제공됩니다. 다르게 말하면, abstract field를 `var`나 `val`로 정의하면 확장 클래스에서의 선택지를 크게 제한하게 됩니다.

저자가 배운 바로는, 중요한 고려사항은 스스로에게 이렇게 물어보는 것입니다. "기반 trait(base trait)이 어떤 field를 가져야 한다고 말할 때, 그 구현에 대해 나는 얼마나 구체적으로 정하고 싶은가?" 정의상, 다른 클래스들이 구현하도록 의도한 trait을 정의할 때 그 trait은 abstract하게 의도된 것이고, Scala에서 그 field를 가장 abstract한 방식으로 선언하는 방법은 `def`로 선언하는 것입니다. 이는 구현을 못 박지(tie down) 않겠다는 의미이며, 확장하는 클래스들이 자신의 필요에 가장 알맞은 방식으로 구현하기를 원한다는 뜻입니다.

#### Concrete fields in traits (trait의 concrete field)

trait에서 정말로 concrete한 `val` 또는 `var` field를 정의하고 싶은 상황이라면, IntelliJ IDEA나 VS Code 같은 IDE가 그 trait을 확장하는 클래스에서 무엇을 할 수 있고 무엇을 할 수 없는지 판단하는 데 도움을 줄 수 있습니다. 예를 들어, trait에 concrete한 `var` field를 지정하면, 다음처럼 확장하는 클래스에서 그 값을 오버라이드할 수 있음을 알 수 있습니다.

```scala
trait SentientBeing:
    var uuid = 0   // concrete

class Person extends SentientBeing:
    uuid = 1
```

이와 유사하게, trait field를 concrete한 `val`로 정의하면, 확장하는 클래스에서 그 값을 변경하기 위해 `override` 한정자(modifier)를 사용해야 합니다.

```scala
trait Cat:
    val numLives = 9   // concrete

class BetterCat extends Cat:
    override val numLives = 10
```

두 경우 모두, 이 field들을 클래스에서 `def`나 `lazy val` 값으로는 구현할 수 *없습니다*.

---

## 6.3 Using a Trait Like an Abstract Class

### Problem

Java의 abstract class처럼 trait을 사용하여, abstract method와 concrete method를 모두 정의하고 싶습니다.

### Solution

원하는 대로 trait에 concrete method와 abstract method를 모두 정의합니다. trait을 확장하는 클래스에서는 두 종류의 메서드를 모두 오버라이드할 수 있고, 또는 concrete method의 경우 trait에 정의된 기본 동작(default behavior)을 그대로 상속받을 수 있습니다.

다음 예제에서는, `Pet` trait의 `speak` 메서드에 기본 concrete 구현이 제공되어 있어서, 이를 구현하는 클래스들이 반드시 오버라이드할 필요는 없습니다. `Dog` 클래스는 이를 오버라이드하지 않기로 선택했고, 반면 `Cat` 클래스는 오버라이드합니다. 두 클래스 모두 `comeToMaster` 메서드는 반드시 구현해야 하는데, 이 메서드는 `Pet` trait에 구현이 없기 때문입니다.

```scala
trait Pet:
    def speak() = println("Yo")   // concrete implementation
    def comeToMaster(): Unit      // abstract method

class Dog extends Pet:
    // no need to implement `speak` if you don't want to
    def comeToMaster() = println("I'm coming!")

class Cat extends Pet:
    override def speak() = println("meow")
    def comeToMaster() = println("That's not gonna happen.")
```

클래스가 trait을 확장하면서 그 abstract method를 구현하지 않으면, 그 클래스는 반드시 `abstract`로 선언되어야 합니다. `FlyingPet`은 `comeToMaster`를 구현하지 않기 때문에, `abstract`로 선언되어야 합니다.

```scala
abstract class FlyingPet extends Pet:
    def fly() = println("Woo-hoo, I'm flying!")
```

### Discussion

Scala에 abstract class가 있긴 하지만, 기반 동작(base behavior)을 구현할 때는 abstract class보다 trait을 사용하는 것이 *훨씬* 더 흔합니다. 클래스는 오직 하나의 abstract class만 확장할 수 있지만, 여러 개의 trait은 구현(mix in)할 수 있기 때문에 trait을 사용하는 것이 더 유연합니다. 또한 Scala 3에서는 trait이 생성자 매개변수(constructor parameters)를 가질 수 있게 되었으므로, trait은 더욱 다양한 상황에서 사용될 것입니다.


---

## 6.4 Using Traits as Mixins

### Problem

하나 이상의 trait을 클래스에 mixin(섞어 넣기)하여 견고한(robust) 설계를 만들어 내는 방법을 찾고자 합니다.

### Solution

trait을 mixin으로 사용하려면, 평소처럼 trait 안에 메서드를 abstract 또는 concrete 메서드로 정의한 다음, `extends`를 사용해 해당 trait들을 클래스에 섞어 넣으면 됩니다. 이 작업은 최소한 두 가지 서로 다른 방식으로 수행할 수 있습니다.

- 클래스를 생성하면서 trait을 함께 섞는 방식 (Constructing a class with traits)
- 변수를 생성하는 시점에 trait을 섞는 방식 (Mix in traits during variable construction)

다음 절들에서 이 두 가지 접근 방식을 설명합니다.

#### Constructing a class with traits

첫 번째 접근 방식은 하나 이상의 trait을 extends하면서 클래스를 생성하는 것입니다. 예를 들어 다음과 같은 두 개의 trait이 있다고 가정해 봅니다.

```scala
trait HasTail:
    def wagTail() = println("Tail is wagging")
    def stopTail() = println("Tail is stopped")

trait Pet:
    def speak() = println("Yo")
    def comeToMaster(): Unit   // abstract
```

`HasTail`의 메서드들은 모두 concrete(구현이 있는) 메서드인 반면, `Pet`의 `comeToMaster` 메서드는 본문(body)이 없으므로 abstract입니다. 이제 이 trait들을 섞어 넣고 `comeToMaster`를 구현함으로써 concrete한 `Dog` 클래스를 만들 수 있습니다.

```scala
class Dog(val name: String) extends Pet, HasTail:
    def comeToMaster() = println("Woo-hoo, I'm coming!")

val d = Dog("Zeus")
```

같은 접근 방식으로, `comeToMaster`를 다르게 구현하면서 동시에 `speak`를 override하는 `Cat` 클래스를 만들 수 있습니다.

```scala
class Cat(val name: String) extends Pet, HasTail:
    def comeToMaster() = println("That's not gonna happen.")
    override def speak() = println("meow")

val c = Cat("Morris")
```

#### Mix in traits during variable construction

또 다른 mixin 접근 방식은 변수를 생성하는 바로 그 시점에 클래스에 trait을 추가하는 것입니다. 이제 다음과 같이 (메서드가 없는) 세 개의 trait과 하나의 `Pet` 클래스가 있다고 가정해 봅니다.

```scala
trait HasLegs
trait HasTail
trait MansBestFriend
class Pet(val name: String)
```

이제 특정 변수를 위해 원하는 trait들을 섞어 넣으면서 새로운 `Pet` 인스턴스를 생성할 수 있습니다.

```scala
val zeus = new Pet("Zeus") with MansBestFriend with HasTail with HasLegs
```

그런 다음 의미가 통하는 trait들을 섞어 넣어 다른 변수들도 생성할 수 있습니다.

```scala
val cat = new Pet("Morris") with HasTail with HasLegs
```

### Discussion

저자는 두 가지 접근 방식을 모두 보여 주는데, 이는 사람마다 mixin이 의미하는 바에 대한 정의가 다르기 때문입니다. 저자가 처음 mixin을 배웠을 때의 주된 사용 사례는 두 번째 예시, 즉 변수를 생성하는 시점에 trait을 섞어 넣는 방법이었습니다.

그러나 요즘에는 mixin이라는 용어가 여러 trait이 하나의 클래스를 구성(compose)하기 위해 사용될 때라면 언제든지 사용될 수 있습니다. 이는 그러한 trait들이 클래스의 유일한 부모(sole parent)가 아니라, 클래스에 섞여 들어가는(mixed into) 것이기 때문입니다. 예를 들어 위키피디아의 mixin 페이지는 이를 잘 설명하고 있는데, mixin은 '상속된(inherited)'다기보다 '포함된(included)' 것으로 묘사된다고 말합니다.

이것이 trait의 핵심 이점입니다. trait은 큰 문제를 더 작은 문제들로 분해함으로써 동작(behavior)의 모듈식 단위(modular units)를 구축할 수 있게 해 줍니다. 예를 들어 거대한 `Dog` 클래스를 설계하려 애쓰는 대신, 개를 구성하는 더 작은 구성 요소(components)들을 이해하고 그 문제를 꼬리, 다리, 털, 귀 등과 관련된 trait들로 분해하는 것이 훨씬 쉽습니다. 그런 다음 그 trait들을 함께 섞어 개를 만듭니다. 이렇게 함으로써 이해하고 테스트하기 더 쉬운 작고 세분화된(granular) 모듈을 만들게 되며, 그 모듈들은 고양이나 말 같은 다른 것을 만드는 데에도 사용될 수 있습니다.

trait을 mixin으로 사용하는 데 있어 몇 가지 핵심은 다음과 같습니다.

- 초점이 좁혀진 범위(focused scope)와 기능을 가진 작은 단위를 만드십시오.
- 구현할 수 있는 메서드는 구현하고, 나머지는 abstract로 선언하십시오.
- trait은 책임 영역이 집중되어 있기 때문에, 일반적으로 서로 관련 없는 동작(unrelated behavior, 직교적 동작(orthogonal behavior)이라고도 함)을 구현합니다.

> **Stackable Trait Pattern**
>
> mixin의 강력함에 대한 훌륭한 시연을 보려면 Bill Venners의 짧은 Artima 기사(stackable trait pattern에 관한 글)를 읽어 보십시오. 이 기사는 trait과 클래스를 base, core, stackable 구성 요소로 정의함으로써, 세 개의 trait을 함께 stacking(쌓기)하여 16가지의 서로 다른 클래스를 어떻게 파생시킬 수 있는지 보여 줍니다.

mixin에 관한 마지막 참고 사항으로, Cay S. Horstmann의 책 *Scala for the Impatient*(Addison-Wesley Professional)는 철학적으로 다음 코드가

```scala
class Pet(val name: String) extends HasLegs, HasTail, MansBestFriend
```

"`class Pet extends HasLegs 'with HasTail and MansBestFriend'`"으로 읽히는 것이 아니라, "`class Pet extends 'HasLegs, HasTail, and MansBestFriend'`"로 읽힌다는 점을 지적합니다. 이는 클래스가 첫 번째 trait을 특별히 우대하는 것이 아니라, 그 모든 trait을 동등하게(equally) 섞어 넣는다는 미묘한(subtle) 요점입니다.


---

## 6.5 Resolving Method Name Conflicts and Understanding super

### Problem

여러 trait을 섞어 넣는 클래스를 만들려고 하는데, 그 trait들이 동일한 메서드 이름과 매개변수 목록(parameter list)을 가지고 있어 컴파일러 오류가 발생합니다.

### Solution

두 개 이상의 mixin된 trait이 동일한 메서드 이름을 공유할 때, 해결책은 그 충돌을 수동으로 해결하는 것입니다. 이를 위해서는 mixin된 trait을 참조할 때 `super`가 의미하는 바를 이해해야 할 수 있습니다.

예를 들어 둘 다 `greet` 메서드를 가진 두 개의 trait이 있다고 가정해 봅니다.

```scala
trait Hello:
    def greet = "hello"

trait Hi:
    def greet = "hi"
```

이제 이 두 trait을 모두 섞어 넣는 `Greeter` 클래스를 만들려고 하면

```scala
class Greeter extends Hello, Hi
```

다음과 같은 오류를 보게 됩니다.

```
   class Greeter extends Hello, Hi
                 ^
class Greeter inherits conflicting members:
      |method greet in trait Hello of type   |=> String  and
      |method greet in trait Hi of type      |=> String
   (Note: this can be resolved by declaring an override in class Greeter.)
```

이 오류 메시지는 `Greeter` 클래스에서 `greet`를 override할 수 있다고 해결책을 알려 주지만, 그 방법에 대한 구체적인 내용은 제공하지 않습니다.

세 가지 주요 해결책이 있으며, 모두 `Greeter`에서 `greet`를 override해야 합니다.

- 커스텀 동작으로 `greet`를 override한다.
- `Greeter`의 `greet`가 `super`의 `greet` 메서드를 호출하도록 한다. 이는 "여러 trait을 섞어 넣을 때 `super`가 무엇을 가리키는가?"라는 질문을 제기합니다.
- `Greeter`의 `greet`가 섞여 들어간 특정 trait의 `greet` 메서드를 사용하도록 한다.

다음 절들에서 각 해결책을 자세히 다룹니다.

#### Override greet with custom behavior

첫 번째 해결책은 trait들에 정의된 메서드를 무시하고, 메서드를 override하여 어떤 커스텀 동작을 구현하는 것입니다.

```scala
// resolve the conflict by overriding 'greet' in the class
class Greeter extends Hello, Hi:
    override def greet = "I greet thee!"

// the 'greet' method override works as expected
val g = Greeter()
g.greet == "I greet thee!"   // true
```

이는 trait들이 이 메서드를 어떻게 구현했는지 신경 쓰지 않는 상황에 적합한, 간단하고 직관적인 해결책입니다.

#### Invoke greet using super

두 번째 해결책은 직속 부모(immediate parent), 즉 `super` 인스턴스에 정의된 대로 메서드를 호출하는 것입니다. 다음 코드에서 `Speaker` 클래스의 `speak` 메서드는 `super.speak`를 호출합니다.

```scala
trait Parent:
    def speak = "make your bed"
trait Granddad:
    def speak = "get off my lawn"

// resolve the conflict by calling 'super.speak'
class Speaker extends Parent, Granddad:
    override def speak = super.speak

@main def callSuperSpeak =
    println(Speaker().speak)
```

여기서 질문은, `super.speak`가 무엇을 출력하느냐는 것입니다.

답은 `super.speak`가 `"get off my lawn"`을 출력한다는 것입니다. 이 예시처럼 클래스가 여러 trait을 섞어 넣고 그 trait들 사이에 mixin이나 상속 관계가 없는 경우, `super`는 항상 *가장 마지막에 섞여 들어간 trait(the last trait that is mixed in)*을 가리킵니다. 이것을 *back to front* linearization 순서라고 부릅니다.

#### Control which super you call

세 번째 해결책에서는 `super[classname].methodName` 구문을 사용하여 호출하고자 하는 mixin된 trait의 메서드를 명시적으로 지정합니다. 예를 들어 다음 세 개의 trait이 있다고 합니다.

```scala
trait Hello:
    def greet = "hello"
trait Hi:
    def greet = "hi"
trait Yo:
    def greet = "yo"
```

이 trait들을 섞어 넣은 `Greeter` 클래스를 만든 다음, 그 trait들의 `greet` 메서드를 호출하는 일련의 `greet` 메서드들을 정의할 수 있습니다.

```scala
class Greeter extends Hello, Hi, Yo:
    override def greet = super.greet
    def greetHello = super[Hello].greet
    def greetHi    = super[Hi].greet
    def greetYo    = super[Yo].greet
end Greeter
```

REPL에서 다음 코드로 이 구성을 테스트할 수 있습니다.

```scala
val g = Greeter()
g.greet        // yo
g.greetHello   // hello
g.greetHi      // hi
g.greetYo      // yo
```

이 해결책의 핵심은 `super[Hello].greet` 구문이 `Hello` trait의 `hello` 메서드를 참조할 수 있는 방법을 제공한다는 것이며, `Hi`와 `Yo` trait에 대해서도 마찬가지입니다. `g.greet` 예시에서 `super`가 다시금 가장 마지막에 섞여 들어간 trait을 가리킨다는 점에 유의하십시오.

### Discussion

이름 충돌(naming conflict)은 메서드 이름이 같고 메서드 매개변수 목록이 동일할 때에만 발생합니다. 메서드의 반환 타입(return type)은 충돌 발생 여부에 영향을 주지 않습니다. 예를 들어, 다음 코드는 두 버전의 `f`가 모두 `(Int, Int)` 타입을 가지므로 이름 충돌을 일으킵니다.

```scala
trait A:
    def f(a: Int, b: Int): Int = 1
trait B:
    def f(a: Int, b: Int): Long = 2

// won't compile. error: "class C inherits conflicting members."
class C extends A, B
```

그러나 다음 코드는 매개변수 목록의 타입이 서로 다르기 때문에 충돌을 일으키지 *않습니다(does not)*.

```scala
trait A:
    def f(a: Int, b: Int): Int = 1    // (Int, Int)
trait B:
    def f(a: Int, b: Long): Int = 2   // (Int, Long)

// this code compiles because 'A.f' and 'B.f' have different
// parameter lists
class C extends A, B
```


---

## 6.6 Marking Traits So They Can Only Be Used by Subclasses of a Certain Type

### Problem

trait을 표시(mark)하여, 특정 기반 타입(base type)을 extends하는 타입에서만 사용될 수 있도록 하고자 합니다.

### Solution

`MyTrait`라는 이름의 trait이 `BaseType`이라는 타입의 서브클래스인 클래스에만 섞여 들어갈 수 있도록 하려면, trait을 다음 구문으로 시작하면 됩니다.

```scala
trait MyTrait:
    this: BaseType =>
```

예를 들어, `StarfleetWarpCore`가 `FederationStarship`도 함께 섞어 넣은 클래스에만 섞여 들어갈 수 있도록 하려면, `StarfleetWarpCore` trait을 다음과 같이 시작합니다.

```scala
trait StarfleetWarpCore:
    this: FederationStarship =>
    // the rest of the trait here ...
```

이 선언이 주어졌을 때, 다음 코드는 의도한 대로 컴파일됩니다.

```scala
// this compiles, as desired
trait FederationStarship
class Enterprise extends FederationStarship, StarfleetWarpCore
```

그러나 다음과 같은 다른 시도는 실패합니다.

```scala
class RomulanShip

// this won't compile
class Warbird extends RomulanShip, StarfleetWarpCore
        ^
illegal inheritance: self type
Warbird of class Warbird does not conform to self type
FederationStarship of parent trait StarfleetWarpCore

Explanation: You mixed in trait StarfleetWarpCore which requires self
type FederationStarship
```

Discussion에서는 이 기법을 사용하여 *여러 개의(multiple)* 다른 타입의 존재를 요구하는 방법을 보여 줍니다.

### Discussion

오류 메시지에서 나타나듯이, 이 접근 방식은 *self type*(또는 *self-type*)이라고 불립니다. Scala 용어집(glossary)은 self type에 대한 설명의 일부로 다음과 같은 문장을 포함합니다.

> trait의 *self type*은 trait 내부에서 사용될, 수신자(receiver)인 `this`의 가정된(assumed) 타입입니다. 그 trait을 섞어 넣는 어떤 concrete 클래스든 자신의 타입이 그 trait의 self type을 따르도록(conform) 보장해야 합니다.

그 문장을 이해하는 한 가지 방법은, mixin을 사용해 클래스를 구성할 때 `this`가 무엇을 의미하는지 평가해 보는 것입니다. 예를 들어 `HasLegs`라는 trait이 주어졌을 때

```scala
trait HasLegs
```

`CanRun`이 concrete 클래스에 섞여 들어갈 때마다 `HasLegs`의 존재를 요구하는 `CanRun`이라는 trait을 정의할 수 있습니다.

```scala
trait CanRun:
    this: HasLegs =>
```

그래서 `HasLegs`와 `CanRun`을 섞어 넣어 `Dog` 클래스를 만들면, 그 클래스 내부에서 `this`가 무엇을 의미하는지 테스트할 수 있습니다.

```scala
class Dog extends HasLegs, CanRun:
    def whatAmI(): Unit =
        if this.isInstanceOf[Dog] then println("Dog")
        if this.isInstanceOf[HasLegs] then println("HasLegs")
        if this.isInstanceOf[CanRun] then println("CanRun")
```

이제 `Dog` 인스턴스를 만들고 `whatAmI`를 실행하면

```scala
val d = Dog()
d.whatAmI()
```

`this`는 `Dog` 내부에서 그 모든 타입의 인스턴스이기 때문에 다음 결과가 출력되는 것을 보게 됩니다.

```
Dog
HasLegs
CanRun
```

기억해야 할 중요한 부분은, 다음과 같이 self-type을 정의할 때

```scala
trait CanRun:
    this: HasLegs =>
```

핵심은 `CanRun`이 결국 자신의 concrete 인스턴스가 생성될 때, 그 concrete 인스턴스 안의 `this`가 "그래, 나는 `HasLegs`의 인스턴스이기도 해."라고 응답할 수 있다는 사실을 안다는 점입니다.

#### A trait can call methods on the required type

이 접근 방식의 훌륭한 기능은, trait이 다른 타입이 반드시 존재해야 한다는 것을 알기 때문에 그 다른 타입에 정의된 메서드를 호출할 수 있다는 것입니다. 예를 들어, `numLegs`라는 메서드를 가진 `HasLegs`라는 타입이 있다면

```scala
trait HasLegs:
    def numLegs = 0
```

`CanRun`이라는 새로운 trait을 만들고 싶을 수 있습니다. `CanRun`은 `HasLegs`의 존재를 요구하므로, self-type으로 그것을 계약적 요구사항(contractual requirement)으로 만듭니다.

```scala
trait CanRun:
    this: HasLegs =>
```

이제 이것을 한 걸음 더 나아가게 할 수 있습니다. `CanRun`은 자신이 섞여 들어갈 때 `HasLegs`가 반드시 존재해야 한다는 것을 알기 때문에, `numLegs` 메서드를 안전하게 호출할 수 있습니다.

```scala
trait CanRun:
    this: HasLegs =>
    def run() = println(s"I have $numLegs legs and I'm running!")
```

이제 `HasLegs`와 `CanRun`을 가진 `Dog` 클래스를 만들면

```scala
class Dog extends HasLegs, CanRun:
    override val numLegs = 4

@main def selfTypes =
    val d = Dog()
    d.run()
```

다음 출력을 보게 됩니다.

```
I have 4 legs and I'm running!
```

이는 강력하면서도 안전한(컴파일러가 강제하는, compiler-enforced) 기법입니다.

#### Requiring multiple other types be present

trait은 또한 그것을 섞어 넣고자 하는 어떤 타입이든 *여러 개의(multiple)* 다른 타입을 extends해야 한다고 요구할 수도 있습니다. 다음 `WarpCore` 정의는 그것을 섞어 넣고자 하는 어떤 타입이든 `WarpCoreEjector`, `FireExtinguisher`, 그리고 `FederationStarship`을 extends해야 한다고 요구합니다.

```scala
trait WarpCore:
    this: FederationStarship & WarpCoreEjector & FireExtinguisher =>
    // more trait code here ...
```

다음 `Enterprise` 정의는 그 시그니처와 일치하므로, 이 코드는 컴파일됩니다.

```scala
class FederationStarship
trait WarpCoreEjector
trait FireExtinguisher

// this works
class Enterprise extends FederationStarship, WarpCore, WarpCoreEjector,
    FireExtinguisher
```


---

## 6.7 Ensuring a Trait Can Only Be Added to a Type That Has a Specific Method

### Problem

특정 시그니처를 가진 메서드를 이미 보유한 타입(class, abstract class, 또는 trait)에만 trait이 mixin될 수 있도록 제한하고 싶은 경우입니다. 즉, trait을 섞어 넣으려는 대상이 미리 약속된 메서드를 반드시 구현하고 있어야만 mixin을 허용하려는 상황입니다.

### Solution

self-type 문법의 변형을 사용하면, 해당 trait을 mixin하려는 모든 클래스가 우리가 명시한 메서드를 반드시 구현하도록 강제할 수 있습니다.

다음 예제에서 `WarpCore` trait은, 이 trait을 mixin하려는 어떤 클래스든 `String` 파라미터를 받아 `Boolean`을 반환하는 `ejectWarpCore` 메서드를 반드시 가지고 있어야 한다고 요구합니다.

```scala
trait WarpCore:
    this: { def ejectWarpCore(password: String): Boolean } =>
    // more trait code here ...
```

다음 `Enterprise` 클래스 정의는 이 요구 조건을 충족하므로 정상적으로 컴파일됩니다.

```scala
class Starship:
    // code here ...

class Enterprise extends Starship, WarpCore:
    def ejectWarpCore(password: String): Boolean =
        if password == "password" then
            println("ejecting core!")
            true
        else
            false
        end if
```

### Discussion

이 접근 방식은 structural type이라고 불립니다. 왜냐하면 trait이 mixin될 수 있는 클래스를, "그 클래스가 특정한 구조를 가져야 한다", 즉 우리가 지정한 메서드 시그니처들을 갖추고 있어야 한다고 명시함으로써 제한하기 때문입니다.

trait은 구현 클래스가 여러 개의 메서드를 갖추도록 요구할 수도 있습니다. 두 개 이상의 메서드를 요구하려면, 추가적인 메서드 시그니처를 블록 안에 넣어주면 됩니다. 다음은 완전한 예제입니다.

```scala
trait WarpCore:
    this: {
        // an implementing class must have methods with
        // these names and input parameters
        def ejectWarpCore(password: String): Boolean
        def startWarpCore(): Unit
    } =>
    // more trait code here ...

class Starship

class Enterprise extends Starship, WarpCore:
    def ejectWarpCore(password: String): Boolean =
        if password == "password" then
            println("core ejected")
            true
        else
            false
        end if
    end ejectWarpCore
    def startWarpCore() = println("core started")
```

이 예제에서는 `Enterprise`가 `WarpCore` trait이 요구하는 `ejectWarpCore` 메서드와 `startWarpCore` 메서드를 모두 포함하고 있기 때문에, `Enterprise`는 `WarpCore` trait을 mixin할 수 있습니다.


---

## 6.8 Limiting Which Classes Can Use a Trait by Inheritance

### Problem

trait이 특정 superclass를 상속하는 클래스에만 추가될 수 있도록 제한하고 싶은 경우입니다.

### Solution

다음 문법을 사용해 `TraitName`이라는 trait을 선언하면, 이 trait은 `SuperClass`라는 타입을 상속하는 클래스에만 mixin될 수 있습니다. 여기서 `SuperClass`는 클래스(class)일 수도 있고 추상 클래스(abstract class)일 수도 있습니다.

```scala
trait TraitName extends SuperClass
```

예를 들어, 본사(corporate office)와 다수의 소매점(retail store)을 보유한 대형 피자 체인점을 모델링한다고 가정해 보겠습니다. 법무팀(legal department)은 "고객에게 피자를 배달하는 사람은 반드시 `StoreEmployee`의 하위 클래스여야 하며, `CorporateEmployee`의 하위 클래스여서는 안 된다"는 규칙을 만들었습니다. 이 규칙을 강제하기 위해, 우선 기반 클래스들을 다음과 같이 정의합니다.

```scala
trait Employee
class CorporateEmployee extends Employee
class StoreEmployee extends Employee
```

음식을 배달하는 사람은 오직 `StoreEmployee`만 될 수 있으므로, 이 요구 사항을 `DeliversFood` trait에 강제합니다.

```scala
trait DeliversFood extends StoreEmployee
                   ---------------------
```

이제 다음과 같이 `DeliveryPerson` 클래스를 성공적으로 정의할 수 있습니다.

```scala
// this is allowed
class DeliveryPerson extends StoreEmployee, DeliversFood
```

하지만 `DeliversFood` trait은 `StoreEmployee`를 상속하는 클래스에만 mixin될 수 있기 때문에, 다음 코드는 컴파일되지 않습니다.

```scala
// won’t compile
class Receptionist extends CorporateEmployee, DeliversFood
```

컴파일러 오류 메시지는 다음과 같습니다.

```
illegal trait inheritance: superclass CorporateEmployee
does not derive from trait DeliversFood’s super class StoreEmployee
```

이로써 법무팀 사람들이 만족하게 됩니다.

### Discussion

저자는 이 기법을 자주 사용하지는 않지만, trait이 mixin될 수 있는 클래스를 특정 superclass를 요구함으로써 제한해야 할 필요가 있을 때 이는 효과적인 방법입니다.

주의할 점은, `CorporateEmployee`와 `StoreEmployee`가 클래스가 아니라 trait인 경우에는 이 방식이 동작하지 않는다는 것입니다. trait들에 대해 이러한 제한 방식을 적용해야 할 때는 Recipe 6.6을 참고하시기 바랍니다.


---

## 6.9 Working with Parameterized Traits

### Problem

타입을 다루는 데 점점 숙련되면서, 메서드가 제네릭 타입에 적용되거나 또는 특정 다른 타입들로 제한될 수 있는 trait을 작성하고 싶은 경우입니다.

### Solution

필요에 따라 trait에서 type parameter 또는 type member를 사용할 수 있습니다. 다음 예제는 제네릭 trait의 type parameter가 어떤 모습인지 보여줍니다.

```scala
trait Stringify[A]:
    def string(a: A): String
```

다음 예제는 type member가 어떤 모습인지 보여줍니다.

```scala
trait Stringify:
    type A
    def string(a: A): String
```

다음은 완전한 type parameter 예제입니다.

```scala
trait Stringify[A]:
    def string(a: A): String = s"value: ${a.toString}"

@main def typeParameter =
    object StringifyInt extends Stringify[Int]
    println(StringifyInt.string(100))
```

그리고 다음은 동일한 예제를 type member를 사용해 작성한 것입니다.

```scala
trait Stringify:
    type A
    def string(a: A): String

object StringifyInt extends Stringify:
    type A = Int
    def string(i: Int): String = s"value: ${i.toString}"

@main def typeMember =
    println(StringifyInt.string(42))
```

> **Dependent Types**
>
> Dave Gurnell(Underscore)이 쓴 무료 책 *The Type Astronaut’s Guide to Shapeless*는 type parameter와 type member를 조합하여 dependent type이라고 알려진 것을 만들어 내는 예제를 보여줍니다.

### Discussion

type parameter 방식을 사용하면 여러 개의 타입을 지정할 수 있습니다. 예를 들어, 다음은 Java 문서 페이지에 나오는 Java `Pair` 인터페이스를 Scala로 구현한 것입니다.

```scala
trait Pair[A, B]:
    def getKey: A
    def getValue: B
```

이는 작은 trait 예제에서 두 개의 제네릭 파라미터를 사용하는 것을 보여줍니다.

두 가지 기법 중 어느 쪽이든 trait을 파라미터화하는 것의 장점은, 결코 일어나서는 안 되는 일이 발생하는 것을 막을 수 있다는 점입니다. 예를 들어, 다음과 같은 trait과 클래스 계층 구조가 주어졌다고 합시다.

```scala
sealed trait Dog
class LittleDog extends Dog
class BigDog extends Dog
```

여기서 다음과 같이 type member를 사용하는 또 다른 trait을 정의할 수 있습니다.

```scala
trait Barker:
    type D <: Dog   //type member
    def bark(d: D): Unit
```

이제 작은 개(little dog)를 위한 `bark` 메서드를 가진 object를 정의할 수 있습니다.

```scala
object LittleBarker extends Barker:
    type D = LittleDog
    def bark(d: D) = println("wuf")
```

그리고 큰 개(big dog)를 위한 `bark` 메서드를 가진 또 다른 object를 정의할 수 있습니다.

```scala
object BigBarker extends Barker:
    type D = BigDog
    def bark(d: D) = println("WOOF!")
```

이제 다음과 같은 인스턴스들을 생성하면

```scala
val terrier = LittleDog()
val husky = BigDog()
```

다음 코드는 컴파일됩니다.

```scala
LittleBarker.bark(terrier)
BigBarker.bark(husky)
```

그리고 다음 코드는 의도한 대로 컴파일되지 않습니다.

```scala
// won’t work, compiler error
// BigBarker.bark(terrier)
```

이는 type member가 초기 trait에서 기반 타입(base type)을 선언할 수 있고, 그 기반 타입을 상속하는 trait, 클래스, object에서 더 구체적인 타입을 적용할 수 있음을 보여줍니다.


---

## 6.10 Using Trait Parameters

### Problem

Scala 3에서, 클래스나 추상 클래스가 생성자 파라미터를 받는 것과 동일한 방식으로, 하나 이상의 파라미터를 받는 trait을 만들고 싶은 경우입니다.

### Solution

Scala 3에서 trait은 클래스나 추상 클래스처럼 파라미터를 가질 수 있습니다. 예를 들어, 다음은 파라미터를 받는 trait의 예입니다.

```scala
trait Pet(val name: String)
```

하지만 Scala 3 trait parameters 명세에 따르면, 이 기능을 사용하는 방식에는 다음과 같은 제한이 있습니다.

- trait `T`는 하나 이상의 파라미터를 가질 수 있습니다.
- trait `T1`은, `T`에 파라미터를 전달하지 않는 한, `T`를 extend할 수 있습니다.
- 클래스 `C`가 `T`를 extend하고 그 superclass는 그렇지 않다면, `C`는 반드시 `T`에 파라미터를 전달해야 합니다.
- 클래스 `C`가 `T`를 extend하고 그 superclass도 `T`를 extend한다면, `C`는 `T`에 인자를 전달해서는 안 됩니다.

예제로 돌아가서, 파라미터를 받는 trait이 있다면, 클래스는 다음과 같이 그것을 extend할 수 있습니다.

```scala
trait Pet(val name: String)

// a class can extend a trait with a parameter
class Dog(override val name: String) extends Pet(name):
    override def toString = s"dog name: $name"

// use the Dog class
val d = Dog("Fido")
```

이후 코드에서, 또 다른 클래스가 `Dog` 클래스를 extend할 수도 있습니다.

```scala
class SiberianHusky(override val name: String) extends Dog(name)
```

모든 고양이의 이름이 "Morris"인 세계에서는, 클래스가 다음과 같이 파라미터를 가진 trait을 extend할 수 있습니다.

```scala
class Cat extends Pet("Morris"):
    override def toString = s"Cat: $name"

// use the Cat class
val c = Cat()
```

이 예제들은 앞서 나열한 첫 번째, 세 번째, 네 번째 항목에서 trait이 어떻게 사용되는지 보여줍니다.

#### One trait can extend another, with limits

다음으로, 앞서 언급한 것처럼, trait은 하나 이상의 파라미터를 받는 또 다른 trait을 extend할 수 있되, 그것에 파라미터를 전달하지 않는 한에서만 가능합니다. 따라서 다음 시도는 실패합니다.

```scala
// won’t compile
trait Echidna(override val name: String) extends Pet(name)
                                              ^^^^^^^^^

                trait Echidna may not call constructor of trait Pet
```

그리고 `Pet`에 파라미터를 전달하려고 시도하지 않는 다음 시도는 성공합니다.

```scala
// works
trait FeatheredPet extends Pet
```

그런 다음, 어떤 클래스가 나중에 `FeatheredPet`을 extend할 때, 올바른 접근 방식은 다음과 같이 코드를 작성하는 것입니다.

```scala
class Bird(override val name: String) extends Pet(name), FeatheredPet:
    override def toString = s"bird name: $name"

// create a new Bird
val b = Bird("Tweety")
```

### Discussion

이 해법에는 다음 두 접근 방식 사이에 미묘한 차이가 있습니다.

```scala
trait Pet(val name: String)   // shown in the Solution
trait Pet(name: String)
```

`val`이 사용되지 않으면 `name`은 단순한 파라미터이며, getter 메서드를 제공하지 않습니다. `val`이 사용되면 `name`에 대한 getter를 제공하며, Solution에서 보인 모든 것이 그대로 동작합니다.

`Pet`의 `name` 필드에서 `val`을 빼면, `Cat` 클래스를 제외한 다음의 모든 코드가 이전과 동일하게 동작합니다. `Cat` 클래스는 컴파일되지 않습니다.

```scala
trait Pet(name: String):
    override def toString = s"Pet: $name"
trait FeatheredPet extends Pet

// `override` is not needed on these parameters
class Bird(val name: String) extends Pet(name), FeatheredPet:
    override def toString = s"Bird: $name"
class Dog(val name: String) extends Pet(name):
    override def toString = s"Dog: $name"
class SiberianHusky(override val name: String) extends Dog(name)

// this will not compile
class Cat extends Pet("Morris"):
    override def toString = s"Cat: $name"
```

`Cat` 방식이 컴파일되지 않는 이유는, `Pet` 클래스의 `name` 파라미터가 `val`로 정의되어 있지 않아서 그것에 대한 getter 메서드가 없기 때문입니다. 다시 말하지만, 이는 미묘한 지점이며, 초기 필드를 어떻게 정의할지는 앞으로 `name`에 어떻게 접근하고 싶은지에 따라 달라집니다.

trait parameter는 적어도 부분적으로는, Scala 2에서 early initializers 또는 early definitions라고 알려진 기능을 없애는 데 도움을 주기 위해 Scala 3에 추가되었습니다. Scala 2 역사의 어느 시점에, 누군가는 다음과 같이 코드를 작성할 수 있다는 것을 알아냈습니다.

```scala
// this is Scala 2 code. start with a normal trait.
trait Pet {
    def name: String
    val nameLength = name.length   // note: this is based on `name`
}

// notice the unusual approach of initializing a variable after ‘extends’ and
// before ‘with’. this is a Scala 2 “early initializer” technique:
class Dog extends {
    val name = "Xena, the Princess Warrior"
} with Pet

val d = new Dog
d.name           // Xena, the Princess Warrior
d.nameLength     // 26
```

이 접근의 목적은 `name`이 일찍(early) 초기화되도록 보장하여, `nameLength` 표현식이 `NullPointerException`을 던지지 않게 하는 것이었습니다. 반대로, 만약 다음과 같이 코드를 작성하면, 새로운 `Dog`를 생성하려고 할 때 `NullPointerException`을 던지게 됩니다.

```scala
// this is also Scala 2 code
trait Pet {
    def name: String
    val nameLength = name.length
}

class Dog extends Pet {
    val name = "Xena, the Princess Warrior"
}

val d = new Dog  //java.lang.NullPointerException
```

저자는 Scala 2에서 이 early initializer 기능을 한 번도 사용하지 않았지만, 이 기능은 제대로 구현하기 어려운 것으로 알려져 있어서 Scala 3에서는 제거되고 trait parameter로 대체되었습니다.

또한 주의할 점은, trait parameter는 trait이 초기화되는 방식에는 아무런 영향을 주지 않는다는 것입니다. 다음 trait들이 주어졌을 때

```scala
trait A(val a: String):
    println(s"A: $a")
trait B extends A:
    println(s"B: $a")
trait C:
    println(s"C")
```

다음 클래스 `D`와 `E`는, trait들이 mixin될 때 어떤 순서로든 지정될 수 있음을 보여줍니다.

```scala
class D(override val a: String) extends A(a), B, C
class E(override val a: String) extends C, B, A(a)
```

`D`와 `E`의 새 인스턴스를 생성한 결과는 REPL에서 다음과 같이 나타납니다.

```scala
scala> D("d")
A: d
B: d
C

scala> E("e")
C
A: e
B: e
```

위에서 보듯, trait들은 어떤 순서로든 나열될 수 있습니다.


---

## 6.11 Using Traits to Create Modules

### Problem

Scala에서 module을 구현하는 정석적인 방법이 trait이라는 이야기를 들어보셨고, trait을 이런 용도로 어떻게 사용하는지 이해하고 싶으십니다.

### Solution

세부적으로 보면 이 문제를 해결하는 방법은 여러 가지가 있지만, 그 해법들에 공통적으로 흐르는 주제는 Scala에서 module을 만들 때 object를 사용한다는 점입니다.

이 레시피에서 보여드리는 기법은 일반적으로 큰 규모의 시스템을 조합(compose)할 때 사용되므로, 우선 작은 예제로 시작해 기법을 시연해 보겠습니다. 정수 두 개를 더하는 메서드를 구현하는 trait을 정의했다고 상상해 보십시오.

```scala
trait AddService:
    def add(a: Int, b: Int) = a + b
```

module을 만드는 기본 기법은 이 trait으로부터 singleton object를 생성하는 것입니다. 이를 위한 문법은 다음과 같습니다.

```scala
object AddService extends AddService
```

이 경우 `AddService`라는 trait으로부터 `AddService`라는 singleton object를 만듭니다. trait 안의 `add` 메서드가 구체(concrete) 메서드이기 때문에, `object` 안에서 메서드를 따로 구현하지 않고도 이렇게 만들 수 있습니다.

> **Reifying a Trait**
>
> 어떤 사람들은 이것을 trait을 reify(구체화)한다고 부릅니다. 여기서 reify라는 단어는 "추상적인 개념을 가져다 구체적으로 만든다"는 의미입니다. 저는 이 의미를 기억하는 한 가지 방법으로, reify를 real-ify, 즉 "현실로 만든다(make real)"로 생각하면 좋다고 봅니다.

코드의 나머지 부분에서 `AddService` module(singleton object)을 사용하는 방식은 다음과 같습니다.

```scala
import AddService.*
println(add(1,1))   // prints 2
```

가능한 한 단순하게 유지하기 위해, 이번에는 trait 두 개를 mixin해서 또 다른 module을 만드는 두 번째 예제를 보여드리겠습니다.

```scala
trait AddService:
    def add(a: Int, b: Int) = a + b

trait MultiplyService:
    def multiply(a: Int, b: Int) = a * b

object MathService extends AddService, MultiplyService
```

애플리케이션의 나머지 부분에서는 이 module도 동일한 방식으로 사용합니다.

```scala
import MathService.*
println(add(1,1))       // 2
println(multiply(2,2))  // 4
```

이 예제들은 단순하지만, 이 기법의 핵심을 잘 보여줍니다.

- 비즈니스 도메인에서 논리적으로 묶이는 작은 영역들을 모델링하기 위해 trait을 만듭니다.
- 그 trait들의 public 인터페이스는 오직 순수 함수(pure functions)만 포함합니다.
- 의미가 있는 경우 그 trait들을 함께 mixin해 `MathService`처럼 더 큰 논리적 그룹으로 묶습니다.
- 그 trait들로부터 singleton object를 만듭니다(reify합니다).
- 그 object들의 순수 함수를 사용해 문제를 해결합니다.

이것이 두 개의 작은 예제로 본 해법의 핵심입니다. 하지만 trait은 이 장에서 보여드린 다른 모든 기능을 가지고 있으므로, 실제 현실에서는 필요에 따라 구현이 얼마든지 복잡해질 수 있습니다.

### Discussion

Scala라는 이름은 scalable이라는 단어에서 왔고, Scala는 확장(scale)되도록 의도되었습니다. 즉, 작은 문제는 쉽게 풀고, 동시에 세상에서 가장 큰 컴퓨팅 과제도 풀 수 있도록 확장된다는 뜻입니다. module과 모듈성(modularity)의 개념은 Scala가 이런 큰 문제를 풀 수 있게 해주는 방식입니다.

Scala 언어의 창시자인 Martin Odersky가 공동 저술한 *Programming in Scala*에서는, 프로그래밍 언어에서 모듈성을 구현하는 모든 기법이 다음 몇 가지를 반드시 제공해야 한다고 말합니다.

- 첫째, 언어에는 인터페이스와 구현 사이를 분리해 주는 module 구성 요소가 필요합니다. Scala에서는 trait이 그 기능을 제공합니다.
- 둘째, 어떤 module을, 그 module에 의존하는 다른 module들을 변경하거나 재컴파일하지 않고도, 동일한 인터페이스를 가진 다른 module로 교체할 수 있는 방법이 있어야 합니다.
- 셋째, module들을 서로 연결(wire)할 수 있는 방법이 있어야 합니다. 이 연결 작업은 시스템을 구성(configuring)하는 것으로 생각할 수 있습니다.

*Programming in Scala*은 특히 프로그램을 singleton object들로 나눌 것을 권장하는데, 이 또한 module로 생각할 수 있습니다.

#### A larger example: An order-entry system

이 기법이 어떻게 동작하는지 더 큰 규모로 시연하면서 동시에 이 장의 다른 기능들도 함께 녹여보기 위해, 피자 가게의 주문 입력(order-entry) 시스템을 개발하는 예제를 살펴보겠습니다.

옛말에 때로는 결말을 먼저 염두에 두고 시작하는 것이 도움이 된다고 하는데, 그 조언을 따라서 이 절에서 만들 `@main` 메서드의 소스 코드를 먼저 보겠습니다.

```scala
@main def pizzaModuleDemo =
    import CrustSize.*
    import CrustType.*
    import Topping.*

    // create some mock objects for testing
    object MockOrderDao extends MockOrderDao
    object MockOrderController extends OrderController, ConsoleLogger:
        // specify a concrete instance of an OrderDao, in this case a
        // MockOrderDao for this MockOrderController
        val orderDao = MockOrderDao

    val smallThinCheesePizza = Pizza(
        Small, Thin, Seq(Cheese)
    )

    val largeThickWorks = Pizza(
        Large, Thick, Seq(Cheese, Pepperoni, Olives)
    )

    MockOrderController.addItemToOrder(smallThinCheesePizza)
    MockOrderController.addItemToOrder(largeThickWorks)
    MockOrderController.printReceipt()
```

이 코드를 실행하면 STDOUT에 다음 출력이 찍히는 것을 볼 수 있습니다.

```
YOUR ORDER
----------
Pizza(Small,Thin,List(Cheese))
Pizza(Large,Thick,List(Cheese, Pepperoni, Olives))

LOG:
YOUR ORDER
----------
Pizza(Small,Thin,List(Cheese))
Pizza(Large,Thick,List(Cheese, Pepperoni, Olives))
```

이 코드가 어떻게 동작하는지 보기 위해, 코드를 구성하는 부분을 파고들어 봅시다. 먼저 Scala 3의 `enum` 구문을 사용해 피자와 관련된 ADT 몇 개를 만드는 것으로 시작합니다.

```scala
enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Thin, Thick, Regular

enum Topping:
    case Cheese, Pepperoni, Olives
```

다음으로, 함수형 스타일로 `Pizza` 클래스를 만듭니다. 즉, 불변(immutable) 필드를 가진 case class라는 뜻입니다.

```scala
case class Pizza(
    crustSize: CrustSize,
    crustType: CrustType,
    toppings: Seq[Topping]
)
```

이 접근법은 C, Rust, Go 같은 다른 언어에서 `struct`를 사용하는 것과 유사합니다.

다음으로, 주문(order)이라는 개념은 단순하게 유지하겠습니다. 현실 세계에서 주문은 피자, 브레드스틱, 치즈스틱, 음료수 등 여러 라인 아이템을 가질 수 있지만, 이 예제에서는 피자 목록만 담도록 하겠습니다.

```scala
case class Order(items: Seq[Pizza])
```

이 예제는 데이터베이스라는 개념도 다루므로, 다음과 같이 데이터베이스 인터페이스(interface)를 만듭니다.

```scala
trait OrderDao:
    def addItem(p: Pizza): Unit
    def getItems: Seq[Pizza]
```

인터페이스의 좋은 점은, 그것에 대한 여러 구현체를 만들 수 있고, 그 구현체들로 module을 구성할 수 있다는 점입니다. 예를 들어, 테스트에 사용할 mock 데이터베이스를 만들고, 프로덕션에서는 실제 데이터베이스 서버에 연결되는 다른 코드를 만드는 것이 흔합니다. 다음은 테스트 목적으로 사용하는 mock DAO(data access object)로, 단순히 아이템들을 메모리 안의 `ArrayBuffer`에 저장합니다.

```scala
trait MockOrderDao extends OrderDao:
    import scala.collection.mutable.ArrayBuffer
    private val items = ArrayBuffer[Pizza]()

    def addItem(p: Pizza) = items += p
    def getItems: Seq[Pizza] = items.toSeq
```

조금 더 복잡하게 만들기 위해, 우리 피자 가게의 법무팀이 영수증을 만들 때마다 별도의 로그에 기록할 것을 요구한다고 가정해 보겠습니다. 이 요구사항을 지원하기 위해 같은 패턴을 따라, 먼저 인터페이스를 만듭니다.

```scala
trait Logger:
    def log(s: String): Unit
```

그런 다음 그 인터페이스의 구현체를 만듭니다.

```scala
trait ConsoleLogger extends Logger:
    def log(s: String) = println(s"LOG: $s")
```

다른 구현체로는 `FileLogger`, `DatabaseLogger` 등이 있을 수 있지만, 이 예제에서는 `ConsoleLogger`만 사용하겠습니다.

이 시점에서 남은 일은 `OrderController`를 만드는 것뿐입니다. 다음 코드에서 `Logger`가 self-type으로 선언되어 있고, `orderDao`가 abstract 필드라는 점에 주목하십시오.

```scala
trait OrderController:
    this: Logger =>          // declares a self-type
    def orderDao: OrderDao   // abstract

    def addItemToOrder(p: Pizza) = orderDao.addItem(p)
    def printReceipt(): Unit =
        val receipt = generateReceipt
        println(receipt)
        log(receipt)   // from Logger

    // this is an example of a private method in a trait
    private def generateReceipt: String =
        val items: Seq[Pizza] = for p <- orderDao.getItems yield p
        s"""
        |YOUR ORDER
        |----------
        |${items.mkString("\n")}""".stripMargin
```

이 controller가 mixin하는 어떤 `Logger` 인스턴스이든 그것의 `log` 메서드가 `printReceipt` 메서드 안에서 호출된다는 점에 주목하십시오. 또한 이 코드는 `OrderDao` 인스턴스의 `addItem` 메서드를 호출하는데, 그 인스턴스는 `MockOrderDao`일 수도 있고 `OrderDao` 인터페이스의 다른 어떤 구현체일 수도 있습니다.

소스 코드를 다시 돌아보면, 이 예제가 다음과 같은 여러 trait 기법을 시연하고 있음을 알 수 있습니다.

- trait을 object(module)로 reify하는 방법
- 인터페이스(`OrderDao`와 같은)와 abstract 필드(`orderDao`)를 사용해 일종의 dependency injection(의존성 주입)을 만드는 방법
- self-type을 사용해 `OrderController`가 반드시 `Logger`를 mixin해야 한다고 선언하는 방법

이 예제를 확장할 방법은 많이 있으며, 저는 제 책 *Functional Programming, Simplified*에서 더 큰 버전을 설명합니다. 예를 들어 `OrderDao`는 다음과 같이 커질 수 있습니다.

```scala
trait OrderDao:
    def addItem(p: Pizza): Unit
    def removeItem(p: Pizza): Unit
    def removeAllItems: Unit
    def getItems: Seq[Pizza]
```

그러면 `PizzaService`는 `Pizza`를 갱신하는 데 필요한 모든 순수 함수를 제공합니다.

```scala
trait PizzaService:
    def addTopping(p: Pizza, t: Topping): Pizza
    def removeTopping(p: Pizza, t: Topping): Pizza
    def removeAllToppings(p: Pizza): Pizza
    def setCrustSize(p: Pizza, cs: CrustSize): Pizza
    def setCrustType(p: Pizza, ct: CrustType): Pizza
```

또한 피자의 가격을 계산하는 함수도 필요할 것입니다. 설계 아이디어에 따라 그 코드를 `PizzaService`에 둘 수도 있고, `PizzaPricingService`라는 가격 책정 관련 별도 trait을 두기를 원할 수도 있습니다.

```scala
trait PizzaPricingService:
    def pizzaDao: PizzaDao
    def toppingDao: ToppingDao

    def calculatePizzaPrice(
        p: Pizza,
        toppingsPrices: Map[Topping, Money],
        crustSizePrices: Map[CrustSize, Money],
        crustTypePrices: Map[CrustType, Money]
    ): Money
```

처음 두 줄에서 보이듯이, `PizzaPricingService`는 데이터베이스에서 가격을 가져오기 위해 다른 DAO 인스턴스들에 대한 참조가 필요합니다.

이 모든 예제에서 저는 trait 이름의 일부로 "Service"라는 단어를 사용합니다. 저는 이것이 좋은 이름이라고 생각하는데, 그 trait들을, 웹 서비스나 마이크로서비스에서 볼 수 있는 것처럼, 관련된 순수 함수나 서비스의 모음을 제공하는 것으로 생각할 수 있기 때문입니다. 사용하기 좋은 또 다른 단어는 *Module*이며, 이 경우 `PizzaModule`과 `PizzaPricingModule`을 갖게 됩니다. (의미가 통하는 이름이라면 무엇이든 자유롭게 사용하십시오.)


---

## 6.12 How to Create Sets of Named Values with Enums

### Problem

세상의 무언가를 모델링하기 위해 상수들의 집합을 만들고 싶으십니다. 예를 들어 방향(north, south, east, west), 디스플레이 상의 위치(top, bottom, left, right), 피자의 토핑, 그 밖의 유한한 값 집합 같은 것들입니다.

### Solution

Scala 3의 `enum` 구문을 사용해 이름 붙은 상수 값들의 집합을 정의합니다. 다음 예제는 피자 가게 애플리케이션을 모델링할 때 crust 크기, crust 종류, 토핑에 대한 값을 정의하는 방법을 보여줍니다.

```scala
enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Thin, Thick, Regular

enum Topping:
    case Cheese, Pepperoni, Mushrooms, GreenPeppers, Olives
```

`enum`을 만든 뒤에는, 먼저 그 인스턴스들을 import하고, 그런 다음 클래스, trait, 또는 다른 타입과 마찬가지로 표현식과 매개변수에서 사용하면 됩니다.

```scala
import CrustSize.*

if currentCrustSize == Small then ...

currentCrustSize match
    case Small => ...
    case Medium => ...
    case Large => ...

case class Pizza(
    crustSize: CrustSize,
    crustType: CrustType,
    toppings: ArrayBuffer[Topping]
)
```

클래스나 trait과 마찬가지로, enum도 매개변수를 받을 수 있고 필드나 메서드 같은 멤버를 가질 수 있습니다. 다음 예제는 `code`라는 매개변수가 enum에서 어떻게 사용되는지를 보여줍니다.

```scala
enum HttpResponse(val code: Int):
    case Ok extends HttpResponse(200)
    case MovedPermanently extends HttpResponse(301)
    case InternalServerError extends HttpResponse(500)
```

Discussion에서 설명하듯이, enum의 인스턴스는 case object와 유사합니다. 따라서 다른 어떤 object와 마찬가지로(Java의 `static` 멤버처럼) object에서 직접 `code` 필드에 접근할 수 있습니다.

```scala
import HttpResponse.*
Ok.code                  // 200
MovedPermanently.code    // 301
InternalServerError.code // 500
```

멤버에 대해서는 이어지는 Discussion에서 보여드립니다.

> **Enums Contain Sets of Values**
>
> 이 레시피에서 *set*이라는 단어는 enum을 묘사하기 위해 의도적으로 사용되었습니다. `Set` 클래스와 마찬가지로, enum 안의 모든 값은 반드시 고유(unique)해야 합니다.

### Discussion

`enum`은 (a) sealed class 또는 trait과, (b) 그 클래스의 companion object의 멤버로 정의된 값들을, 함께 정의하는 단축 표기입니다. 예를 들어 다음 enum은

```scala
enum CrustSize:
    case Small, Medium, Large
```

다음과 같은 더 장황한 코드를 작성하는 것의 단축 표기입니다.

```scala
sealed class CrustSize
object CrustSize:
    case object Small extends CrustSize
    case object Medium extends CrustSize
    case object Large extends CrustSize
```

이 더 긴 코드에서, enum 인스턴스들이 companion object 안에서 case object로 열거되는 방식에 주목하십시오. 이것은 Scala 2에서 열거형(enumerations)을 만드는 흔한 방법이었습니다.

#### Enums can have members

[Scala 3 enum 페이지의] `Planet` 예제에서 보여주듯이, enum도 멤버, 즉 필드와 메서드를 가질 수 있습니다.

```scala
enum Planet(mass: Double, radius: Double):
    private final val G = 6.67300E-11
    def surfaceGravity = G * mass / (radius * radius)
    def surfaceWeight(otherMass: Double) = otherMass * surfaceGravity

    case Mercury extends Planet(3.303e+23, 2.4397e6)
    case Earth   extends Planet(5.976e+24, 6.37814e6)
    // more planets here ...
```

이 예제에서 매개변수 `mass`와 `radius`가 `val`이나 `var` 필드로 정의되어 있지 않다는 점에 주목하십시오. 이 때문에 이들은 `Planet` enum에 대해 private입니다. 즉, `surfaceGravity`나 `surfaceWeight` 같은 내부 메서드에서는 접근할 수 있지만, enum 외부에서는 접근할 수 없습니다. 이것은 클래스와 trait에서 private 매개변수를 사용할 때 얻는 동작과 동일합니다.

#### When to use enums

trait, 클래스, enum을 언제 사용해야 하는지의 경계가 모호해 보일 수 있지만, enum에 대해 기억할 점은 보통 작고 유한한 가능 값의 집합을 모델링할 때 사용된다는 것입니다. 예를 들어 `Planet` 예제에서, 우리 태양계에는 (세는 사람에 따라) 행성이 여덟 개 또는 아홉 개밖에 없습니다. 이것은 작고 유한한 상수 값의 집합이므로, enum을 사용해 행성을 모델링하는 것은 좋은 선택입니다.

#### Compatibility with Java

Scala enum을 Java enum으로 정의하고 싶다면 `java.lang.Enum`을 extend하면 되며, 이것은 기본적으로 import되어 있습니다.

```scala
enum CrustSize extends Enum[CrustSize]:
    case Small, Medium, Large
```

보이는 것처럼, `java.lang.Enum`을 여러분의 Scala enum 타입으로 매개변수화(parameterize)해 주어야 합니다.


---

## 6.13 Modeling Algebraic Data Types with Enums

### Problem

함수형 프로그래밍 스타일로 프로그래밍할 때, Scala 3를 사용해 algebraic data type(ADT)을 모델링하고 싶으십니다.

### Solution

ADT에는 두 가지 주요 유형이 있습니다.

- Sum types
- Product types

이어지는 예제에서 둘 다 시연합니다.

#### Sum types

*Sum type*은 *enumerated type*이라고도 불리는데, 그 타입의 가능한 모든 인스턴스를 단순히 열거(enumerate)하기 때문입니다. Scala 3에서는 이것을 `enum` 구문으로 합니다. 예를 들어, 여러분만의 boolean 데이터 타입을 만들려면 다음과 같이 Sum type을 정의하는 것으로 시작합니다.

```scala
enum Bool:
    case True, False
```

이것은 "`Bool`은 `True`와 `False`라는 두 가지 가능한 값을 갖는 타입이다"라고 읽을 수 있습니다. 마찬가지로, `Position`은 네 가지 가능한 값을 갖는 타입입니다.

```scala
enum Position:
    case Top, Right, Bottom, Left
```

#### Product types

*Product type*은 클래스 생성자(class constructor)로 만들어집니다. *Product*라는 이름은, 그 클래스의 가능한 구체 인스턴스의 개수가 모든 생성자 필드의 가능성 개수를 곱한 값(multiplying)으로 결정된다는 사실에서 비롯됩니다.

예를 들어, `DoubleBoo`라는 이 클래스는 두 개의 `Bool` 생성자 매개변수를 가집니다.

```scala
case class DoubleBoo(b1: Bool, b2: Bool)
```

이처럼 작은 예제에서는 이 생성자로부터 만들 수 있는 가능한 값들을 직접 열거할 수 있습니다.

```scala
DoubleBoo(True, True)
DoubleBoo(True, False)
DoubleBoo(False, True)
DoubleBoo(False, False)
```

보이듯이 가능한 값은 네 가지입니다. *Product*라는 이름이 암시하듯이, 이 답을 수학적으로 도출할 수도 있습니다. 이는 Discussion에서 다룹니다.

### Discussion

비공식적으로 *algebra*는 두 가지로 구성된다고 생각할 수 있습니다.

- 객체들의 집합(a set of objects)
- 새로운 객체를 만들기 위해 그 객체들에 적용할 수 있는 연산들(operations)

기술적으로 algebra에는 세 번째 항목, 즉 그 algebra를 지배하는 법칙(laws)도 포함되지만, 이는 FP를 다루는 더 큰 책에서 다룰 주제입니다.

`Bool` 예제에서 객체들의 집합은 `True`와 `False`입니다. 연산은 여러분이 그 객체들을 위해 정의하는 메서드들로 구성됩니다. 예를 들어, `Bool`을 다루기 위해 `and`와 `or` 연산을 다음과 같이 정의할 수 있습니다.

```scala
enum Bool:
    case True, False

import Bool.*

def and(a: Bool, b: Bool): Bool = (a,b) match
    case (True, True)   => True
    case (False, False) => False
    case (True, False)  => False
    case (False, True)  => False

def or(a: Bool, b: Bool): Bool = (a,b) match
    case (True, _) => True
    case (_, True) => True
    case (_, _)    => False
```

다음 예제는 그 연산들이 어떻게 동작하는지를 보여줍니다.

```scala
and(True,True)    // True
and(True,False)   // False
or(True,False)    // True
or(False,False)   // False
```

#### The Sum type

Sum type에 대한 몇 가지 중요한 점입니다.

- Scala 3에서는 `enum` 구문의 case들로 만들어집니다.
- 여러분이 나열한 열거된 타입(enumerated types)들만이 그 타입의 유일하게 가능한 인스턴스입니다. 앞의 예제에서 `Bool`이 타입이고, 그것은 `True`와 `False`라는 두 가지 가능한 값을 가집니다.
- Sum type에 대해 이야기할 때 *is a*와 *or a*라는 표현이 사용됩니다. 예를 들어, `True` *is a* `Bool`이고, `Bool`은 `True` *or a* `False`입니다.

> **Alternate Names for Sum Type Instances**
>
> 사람들은 Sum type의 구체 인스턴스에 대해 서로 다른 이름을 사용하는데, 여기에는 value constructors, alternates, cases 등이 있습니다.

#### The Product type

앞서 언급했듯이, *Product type*이라는 이름은 한 타입의 가능한 인스턴스 개수를, 모든 생성자 필드의 가능성 개수를 곱해서(multiplying) 결정할 수 있다는 사실에서 비롯됩니다. Solution에서는 가능한 `Bool` 값 네 가지를 열거했지만, 다음과 같이 수학적으로도 가능한 인스턴스 개수를 결정할 수 있습니다.

1. `b1`은 두 가지 가능성을 가집니다.
2. `b2`는 두 가지 가능성을 가집니다.
3. 매개변수가 두 개이고 각각 두 가지 가능성을 가지므로, `DoubleBoo`의 가능한 인스턴스 개수는 2 곱하기 2, 즉 4입니다.

마찬가지로, 다음 예제에서 `TripleBoo`는 여덟 가지 가능한 값을 가지는데, 이는 2 곱하기 2 곱하기 2가 8이기 때문입니다.

```scala
case class TripleBoo(b1: Bool, b2: Bool, b3: Bool)
```

이 논리를 사용하면, 이 `Pair` 클래스는 몇 개의 값을 가질 수 있을까요?

```scala
case class Pair(a: Int, b: Int)
```

"아주 많이"라고 답하셨다면 거의 맞습니다. `Int`는 2의 32제곱 개의 가능한 값을 가지므로, 가능한 `Int` 값의 개수를 자기 자신과 곱하면 매우 큰 숫자가 나옵니다.

#### Much more different than Scala 2

`enum` 타입은 Scala 3에서 도입되었고, Scala 2에서는 Sum type을 정의하기 위해 다음과 같은 더 긴 문법을 사용해야 했습니다.

```scala
sealed trait Bool
case object True extends Bool
case object False extends Bool
```

다행히 새 문법은 훨씬 간결하며, 더 큰 Sum type을 열거할 때 그 장점을 실감할 수 있습니다.

```scala
enum Topping:
    case Cheese, BlackOlives, GreenOlives, GreenPeppers, Onions, Pepperoni,
        Mushrooms, Sausage
```

> **Why Programmers Don't Use String or Int for Constants**
>
> 이 레시피의 Product types 부분은 프로그래머가 상수에 문자열이나 숫자를 사용하기를 꺼리는 이유를 설명하는 데 도움이 됩니다. 이를 시연하기 위해, 피자와 관련된 모든 값 집합을 문자열로 정의한다고 가정해 봅시다. 다음과 같이 시작할 수 있습니다.
>
> ```scala
> val Small = "SMALL"
> val Medium = "MEDIUM"
> val Large = "LARGE"
> ```
>
> 이런 식으로 계속하다 보면 결국 이런 `Pizza` 클래스 생성자에 도달하게 됩니다.
>
> ```scala
> case class Pizza(
>     crustSize: String,
>     crustType: String,
>     toppings: ArrayBuffer[String]
> )
> ```
>
> 이 접근법에는 두 가지 문제가 있습니다.
>
> - Scala는 정적 타입 언어이므로, 여기서 강한 타이핑(strong typing)을 사용해 타입 안전성(type safety)의 모든 이점을 얻어야 합니다. 매개변수는 `CrustSize`, `CrustType`, `Topping` 같은 타입을 사용해야 하는데, 대신 문자열이 사용되고 있습니다.
> - 이 생성자가 Product type을 만든다는 사실을 알면, 가능한 값의 개수가 무한(infinite)이 될 수 있다는 것도 알 수 있습니다. 즉, 각각 무한한 개수의 가능성을 가진 세 개의 생성자 매개변수를 가지므로("무한의 세제곱"은 무한입니다).
>
> 반대로, enum을 사용해 `CrustSize`, `CrustType`, `Topping`을 모델링하면, 가능한 crust 크기 세 개, 가능한 crust 종류 세 개, 가능한 토핑 열 개를 가진 `Pizza`는 단 90가지 가능성만 갖습니다(3 × 3 × 10).
