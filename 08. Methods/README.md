# Chapter 8. Methods

## 들어가며

도메인 모델링을 다루는 이 마지막 장에서는 **메서드(methods)** 를 다룹니다. 메서드는 클래스, 케이스 클래스(case class), 트레이트(trait), 열거형(enum), 그리고 오브젝트(object) 안에 정의될 수 있습니다. 중요한 점이자 Scala 3에서 크게 바뀐 부분은, 메서드를 이러한 구조체들의 **바깥에서도** 정의할 수 있다는 것입니다. 그 결과로, 완전한 Scala 3 애플리케이션은 다음과 같은 모습이 될 수 있습니다.

```scala
def printHello(name: String) = println(s"Hello, $name")
def printString(s: String) = println(s)

@main def hiMom =
    printHello("mom")
    printString("Look mom, no classes or objects required!")
```

Scala의 메서드는 다른 타입 기반 언어들의 메서드와 유사합니다. 메서드는 `def` 키워드로 정의되며, 보통 하나 이상의 파라미터를 받고, 수행할 알고리즘을 가지며, 어떤 종류의 결과를 반환합니다. 제네릭 타입이나 `using` 절을 갖지 않는 기본적인 메서드는 다음과 같이 정의됩니다.

```scala
def methodName(paramName1: type1, paramName2: type2, ...): ReturnType =
    // the method body
    // goes here
```

메서드의 반환 타입을 선언하는 것은 선택 사항입니다. 하지만 애플리케이션을 유지보수하는 입장에서 볼 때, 몇 달 혹은 몇 년 동안 들여다보지 않은 코드라 하더라도, 지금 잠깐 시간을 들여 타입을 선언해 두면 며칠 뒤, 몇 달 뒤, 그리고 몇 년 뒤에 코드를 이해하기가 더 쉬워집니다. 타입을 추가하지 않을 때, 혹은 다른 동적 타입 언어를 사용할 때는, 나중에 메서드 본문을 살펴보면서 반환 타입이 무엇인지 알아내는 데 상당한 시간을 들여야 한다는 것을 알게 됩니다. 따라서 대부분의 개발자들은 기억이 생생할 때, 즉 지금 바로 반환 타입을 선언해 두는 것이 가장 좋다는 데 동의합니다. 그리고 함수형 프로그래밍(functional programming)에 발을 들이게 되면, 순수 함수의 시그니처(pure function signatures)가 대단히 의미 있다는 것을 알게 됩니다.

`def` 앞에 붙일 수 있는 키워드가 여러 가지 있습니다. 예를 들어, 상속받는 클래스가 메서드를 오버라이드하지 못하도록 하려면 메서드를 `final`로 선언할 수 있습니다.

```scala
class Foo:
    final def foo = "foo"            // FINAL

class FooFoo extends Foo:
    override def foo = "foo foo"     // ERROR, won't compile
```

`protected`와 `private` 같은 다른 키워드들은 이 장에서 메서드의 범위(scope)를 제어하는 방법을 보여주기 위해 설명됩니다.

Scala는 **표현식 지향(expression-oriented)** 프로그래밍 언어로 간주됩니다. 이는 모든 코드 줄이 **표현식(expression)** 이라는 의미로, 표현식은 값을 반환하고 일반적으로 부수 효과(side effect)를 갖지 않습니다. 그 결과, 메서드는 매우 간결해질 수 있는데, `if`, `for/yield`, `match`, `try`와 같은 언어 구조들이 모두 값을 반환하는 표현식이기 때문입니다. 간결하면서도 읽기 쉬운 코드를 **표현력 있는(expressive)** 코드라고 부르며, 이를 보여주기 위해 이러한 구조들을 사용하는 표현력 있는 메서드의 작은 모음을 소개하겠습니다.

먼저, 메서드 본문으로 동등성 테스트(equality test)나 `if` 표현식만을 사용하는 몇 가지 예시입니다.

```scala
// with return type
def isBetween(a: Int, x: Int, y: Int): Boolean = a >= x && a <= y
def max(a: Int, b: Int): Int = if a > b then a else b

// without return type
def isBetween(a: Int, x: Int, y: Int) = a >= x && a <= y
def max(a: Int, b: Int) = if a > b then a else b
```

더 읽기 편하다고 느낀다면, 메서드 본문을 별도의 줄에 배치할 수도 있습니다.

```scala
def isBetween(a: Int, x: Int, y: Int): Boolean =
    a >= x && a <= y

def max(a: Int, b: Int): Int =
    if a > b then a else b
```

다음은 메서드 본문으로 `match` 표현식을 사용한 예시입니다.

```scala
def sum(xs: List[Int]): Int = xs match
    case Nil => 0
    case x :: tail => x + sum(tail)
```

`for`와 `yield`의 조합인 `for` 표현식도 메서드 본문으로 사용할 수 있습니다.

```scala
def allThoseBetween3and10(xs: List[Int]): List[Int] =
    for
        x <- xs
        if x >= 3
        if x <= 10
    yield
        x

println(allThoseBetween3and10(List(1,3,7,11)))   // List(3, 7)
```

`try/catch` 표현식과 같은 다른 구조들에도 동일한 기법을 사용할 수 있습니다.

이러한 도입부 예시들은 Scala 메서드의 여러 기능을 보여주지만, 알아야 할 내용은 훨씬 더 많습니다. 다음 레시피들은 다음과 같은 방법들을 보여줍니다.

- 메서드 접근 제어, 즉 메서드의 가시성(visibility)을 지정하기 (Recipe 8.1)
- 상위 클래스(superclass)나 트레이트(trait)의 메서드를 호출하기 (Recipe 8.2)
- 메서드를 호출할 때 메서드 파라미터의 이름을 지정하기 (Recipe 8.3)
- 메서드 파라미터에 기본값(default values)을 설정하기 (Recipe 8.4)
- 메서드에서 가변 인자(varargs) 필드를 사용하기 (Recipe 8.5)
- 호출자가 특정 메서드에서 괄호를 생략하도록 강제하기 (Recipe 8.6)
- 메서드가 던질 수 있는 예외(exceptions)를 선언하기 (Recipe 8.7)
- 유연한(fluent) 메서드 프로그래밍 스타일을 지원하기 위한 특별한 기법을 사용하기 (Recipe 8.8)
- 새로운 Scala 3의 확장 메서드(extension method) 문법을 사용하기 (Recipe 8.9)

마지막으로, **메서드(methods)** 를 정의하는 것 외에도, Scala에서는 `val` 키워드를 사용하여 **함수(functions)** 를 정의할 수 있다는 것을 아는 것이 중요합니다. 함수는 이 장에서 다루지 않지만, Chapter 10의 여러 레시피에서 다룹니다.

---

## 8.1 메서드 범위 제어 (접근 제어자, Access Modifiers)

### 문제

Scala 메서드는 기본적으로 public입니다. 여러분은 메서드의 범위(scope)를 제어하고자 합니다.

### 해법

Scala는 메서드의 가시성을 세밀하고 강력한 방식으로 제어할 수 있게 해줍니다. "가장 제한적인(most restrictive)" 것에서 "가장 개방적인(most open)" 것 순서로, Scala는 다음과 같은 범위 옵션들을 제공합니다.

- Private scope (private 범위)
- Protected scope (protected 범위)
- Package scope (패키지 범위)
- Package-specific scope (특정 패키지 범위)
- Public scope (public 범위)

이러한 범위들은 이어지는 예시들에서 설명됩니다.

> **private[this]와 protected[this]**
>
> Scala 2에는 `private[this]`와 `protected[this]`라는 범위 한정자(scope qualifier) 개념이 있었지만, 이것들은 더 이상 사용되지 않습니다(deprecated). 해당 기능에 대한 논의는 Scala 3 reference page를 참고하시기 바랍니다.

#### Private scope (private 범위)

가장 제한적인 접근 방식은 메서드를 `private`로 표시하는 것입니다. 이렇게 하면 메서드가 (a) 클래스의 현재 인스턴스와 (b) 현재 클래스의 다른 인스턴스들에서만 사용 가능해집니다. 다음 코드는 메서드를 `private`로 표시하는 방법과, 그것을 동일한 클래스의 다른 인스턴스에서 어떻게 사용할 수 있는지를 보여줍니다.

```scala
class Cat:
    private def isFriendlyCat = true
    def sampleMethod(other: Cat) =
        if other.isFriendlyCat then
            println("Can access other.isFriendlyCat")
            // ...
        end if
    end sampleMethod
end Cat
```

메서드가 `private`로 표시되면 서브클래스(subclass)에서는 사용할 수 없습니다. 다음 `Dog` 클래스는 `heartBeat` 메서드가 `Animal` 클래스에 대해 private이기 때문에 컴파일되지 않습니다.

```scala
class Animal:
    private def heartBeat() = println("Animal heart is beating")

class Dog extends Animal:
    heartBeat()   // ERROR: Not found: heartBeat
```

이 메서드를 `Dog` 클래스에서 사용 가능하게 하려면 protected 범위를 사용하면 됩니다.

#### Protected scope (protected 범위)

메서드를 `protected`로 표시하면 그 범위가 수정되어, (a) 동일한 클래스의 다른 인스턴스들에서 접근할 수 있고, (b) 현재 패키지(package)에서는 보이지 않으며, (c) 서브클래스에서 사용 가능해집니다. 다음 코드는 이러한 점들을 보여줍니다.

```scala
class Cat:
    protected def isFriendlyCat = true
    def catFoo(otherCat: Cat) =
        if otherCat.isFriendlyCat then   // this compiles
            println("Can access 'otherCat.isFriendlyCat'")
            // ...
        end if

@main def catTests =
    val c1 = Cat()
    val c2 = Cat()
    c1.catFoo(c2)          // this works

    // this code can't access this method:
    // c1.isFriendlyCat   // does not compile
```

이 코드에서:

- `catFoo` 안의 `if other.isFriendlyCat` 표현식은 하나의 `Cat` 인스턴스가 다른 인스턴스의 `isFriendlyCat`에 접근할 수 있음을 보여줍니다.
- `c1.catFoo(c2)` 표현식은 하나의 `Cat` 인스턴스가 다른 인스턴스에서 `catFoo`를 호출할 수 있고, `catFoo`가 그 다른 인스턴스에서 `isFriendlyCat`을 호출할 수 있음을 보여줍니다.
- 주석 처리된 `c1.isFriendlyCat`은 하나의 `Cat` 인스턴스가 다른 `Cat` 인스턴스에서 `isFriendlyCat`을 직접 호출할 수 **없음**을 보여줍니다. `CatHouse`가 `Cat`과 동일한 패키지에 있더라도, `protected`는 그것을 허용하지 않습니다.

`protected` 메서드는 서브클래스에서 사용 가능하므로, 다음 코드 역시 컴파일됩니다.

```scala
class Animal:
    protected def heartBeat() = println("Animal heart is beating")

class Dog extends Animal:
    heartBeat()   // this
```

#### Package scope (패키지 범위)

메서드를 현재 패키지의 모든 멤버에게만 사용 가능하게 하려면, `private[packageName]` 문법을 사용하여 메서드를 현재 패키지에 대해 private으로 표시하면 됩니다.

다음 예시에서, `privateModelMethod` 메서드는 동일한 패키지(`model` 패키지)에 있는 다른 클래스들에서 접근할 수 있지만, `privateMethod`와 `protectedMethod`는 접근할 수 없습니다.

```scala
package com.devdaily.coolapp.model:
    class Foo:
        // this is in "package scope"
        private[model] def privateModelMethod = ??? // can be accessed by
                                                     // classes in
                                                     // com.devdaily.coolapp.model

        private def privateMethod = ???
        protected def protectedMethod = ???

    class Bar:
        val f = Foo()
        f.privateModelMethod   // compiles
        // f.privateMethod      // won't compile
        // f.protectedMethod    // won't compile
```

#### Package-specific scope (특정 패키지 범위)

메서드를 현재 패키지의 클래스들에서 사용 가능하게 만드는 것을 넘어서, Scala는 클래스 계층 구조(class hierarchy)의 서로 다른 수준에서 메서드를 사용 가능하게 할 수 있는 세밀한(fine-grained) 수준의 접근 제어도 허용합니다. 다음 예시는 `doUnderModel`, `doUnderCoolapp`, `doUnderAcme` 메서드들을 서로 다른 패키지 수준에서 사용 가능하게 만드는 방법을 보여줍니다.

```scala
package com.devdaily.coolapp.model:
    class Foo:
        // available under com.devdaily.coolapp.model
        private[model] def doUnderModel = ???

        // available under com.devdaily.coolapp
        private[coolapp] def doUnderCoolapp = ???

        // available under com.devdaily
        private[devdaily] def doUnderAcme = ???

import com.devdaily.coolapp.model.Foo

package com.devdaily.coolapp.view:
    class Bar:
        val f = Foo()
        // f.doUnderModel  // won't compile
        f.doUnderCoolapp
        f.doUnderAcme

package com.devdaily.common:
    class Bar:
        val f = Foo()
        // f.doUnderModel      // won't compile
        // f.doUnderCoolapp    // won't compile
        f.doUnderAcme
```

이 예시에서, 메서드들은 다음과 같이 보일 수 있습니다.

- `doUnderModel` 메서드는 `model` 패키지(`com.devdaily.coolapp.model`)에 있는 다른 클래스들에서 볼 수 있습니다.
- `doUnderCoolapp` 메서드는 `com.devdaily.coolapp` 패키지 수준 아래의 모든 클래스에서 볼 수 있습니다.
- `doUnderAcme` 메서드는 `com.devdaily` 수준 아래의 모든 클래스에서 볼 수 있습니다.

#### Public scope (public 범위)

메서드 선언에 어떤 접근 제어자(access modifier)도 추가하지 않으면, 그 메서드는 public이 됩니다. 이는 어떤 패키지에 있는 어떤 코드든 그 메서드에 접근할 수 있다는 의미입니다. 다음 예시에서, 어떤 패키지에 있는 어떤 클래스든 `doPublic` 메서드에 접근할 수 있습니다.

```scala
package com.devdaily.coolapp.model:
    class Foo:
        def doPublic = ???

package some.other.scope:
    class Bar:
        val f = com.devdaily.coolapp.model.Foo()
        f.doPublic
```

### 논의

접근 제어자에 대한 Scala의 접근 방식은 Java와 다릅니다. 메서드는 기본적으로 public이며, 그런 다음 접근 제어를 제공하는 방식에 유연성이 필요할 때 Scala는 해법(Solution)에서 보여준 기능들을 제공합니다.

요약으로, Table 8-1은 해법에서 보여준 접근 제어 수준들을 설명합니다.

*Table 8-1. Scala의 접근 제어자(access control modifier)에 대한 설명*

| 접근 제어자 (Access modifier) | 설명 (Description) |
| --- | --- |
| `private` | 현재 인스턴스와, 그것이 선언된 클래스의 다른 인스턴스들에서 사용 가능합니다. |
| `protected` | 현재 클래스의 인스턴스들과 서브클래스들에서만 사용 가능합니다. |
| `private[model]` | `com.devdaily.coolapp.model` 패키지 아래의 모든 클래스에서 사용 가능합니다. |
| `private[coolapp]` | `com.devdaily.coolapp` 패키지 아래의 모든 클래스에서 사용 가능합니다. |
| `private[devdaily]` | `com.devdaily` 패키지 아래의 모든 클래스에서 사용 가능합니다. |
| (제어자 없음) | 해당 메서드는 public입니다. |

---

## 8.2 슈퍼클래스나 트레이트의 메서드 호출하기 (Calling a Method on a Superclass or Trait)

### 문제

코드를 DRY(Don't Repeat Yourself, 같은 것을 반복하지 않기) 상태로 유지하기 위해, 부모 클래스나 트레이트에 이미 정의되어 있는 메서드를 호출하고자 합니다.

### 해법

이 레시피에서는 고려해야 할 여러 가지 상황이 있습니다.

- 클래스의 메서드 이름이 슈퍼클래스 메서드와 다르며, 그 슈퍼클래스 메서드를 호출하려는 경우
- 클래스의 메서드가 슈퍼클래스 메서드와 같은 이름을 가지며, 그 슈퍼클래스 메서드를 호출해야 하는 경우
- 클래스의 메서드가 자신이 확장(extends)하는 여러 트레이트와 같은 이름을 가지며, 그중 어떤 트레이트의 동작을 사용할지 선택하고자 하는 경우

이러한 문제들에 대한 해법은 다음 절들에서 보여 드립니다.

#### walkThenRun이 walk와 run을 호출하기

클래스의 메서드가 슈퍼클래스의 메서드를 호출해야 하고, 그 클래스 내의 메서드 이름이 슈퍼클래스의 이름과 다른 경우에는 `super`를 사용하지 않고 슈퍼클래스 메서드를 호출하면 됩니다.

```scala
class AnimalWithLegs:
    def walk() = println("I'm walking")
    def run() = println("I'm running")

class Dog extends AnimalWithLegs:
    def walkThenRun() =
        walk()
        run()
```

이 예제에서 `Dog` 클래스의 `walkThenRun` 메서드는 `AnimalWithLegs`에 정의된 `walk`와 `run` 메서드를 호출합니다. 메서드 이름이 서로 다르기 때문에 `super` 참조를 사용할 필요가 없습니다. 이것은 객체 지향 프로그래밍에서의 일반적인 메서드 상속입니다.

이 예제에서는 슈퍼클래스를 보여 드렸지만, `AnimalWithLegs`가 트레이트(trait)인 경우에도 이 설명은 동일하게 적용됩니다.

#### walk 메서드가 super.walk를 호출해야 하는 경우

클래스의 메서드가 슈퍼클래스의 메서드와 같은 이름을 가지며, 그 슈퍼클래스 메서드를 호출하고자 할 때는, 클래스의 메서드를 `override`로 정의한 다음 `super`를 사용하여 슈퍼클래스 메서드를 호출합니다.

```scala
class AnimalWithLegs:
    // the superclass 'walk' method.
    def walk() = println("Animal is walking")

class Dog extends AnimalWithLegs:
    // the subclass 'walk' method.
    override def walk() =
        super.walk()              // invoke the superclass method.
        println("Dog is walking")   // add your own body.
```

이 예제에서 `Dog`의 `walk` 메서드는 슈퍼클래스의 메서드와 같은 이름을 가지므로, 슈퍼클래스의 `walk` 메서드를 호출하려면 `super.walk`를 사용해야 합니다.

이제 새로운 `Dog`를 생성하고 그 `walk` 메서드를 호출하면, 두 줄이 모두 출력되는 것을 볼 수 있습니다.

```scala
val d = Dog()
d.walk()

Animal is walking
Dog is walking
```

이 상황에서, 슈퍼클래스의 `walk` 동작을 원하지 않고 단지 그것을 오버라이드(override)하고 싶다면, 슈퍼클래스 메서드를 호출하지 말고 단지 여러분 자신의 메서드 본문만 정의하면 됩니다.

```scala
class Dog extends AnimalWithLegs:
    override def walk() =
        println("Dog is walking")
```

이제 새로운 `Dog` 인스턴스를 생성하고 그 `walk` 메서드를 호출하면, 다음 출력만 보게 됩니다.

```scala
Dog is walking
```

이전 예제와 마찬가지로, `AnimalWithLegs`가 트레이트인 경우에도 이 설명은 동일하게 적용됩니다.

#### 어떤 트레이트에서 메서드를 호출할지 제어하기

여러분의 클래스가 여러 트레이트를 상속하고, 그 트레이트들이 같은 메서드를 구현하고 있다면, `super`를 사용하여 메서드를 호출할 때 메서드 이름뿐만 아니라 트레이트 이름까지 선택할 수 있습니다. 예를 들어, 다음과 같은 트레이트들이 주어졌을 때를 살펴봅니다.

```scala
trait Human:
    def yo = "Human"

trait Mother extends Human:
    override def yo = "Mother"

trait Father extends Human:
    override def yo = "Father"
```

다음 코드는 `Child` 클래스가 상속하는 트레이트들로부터 `hello` 메서드를 호출하는 여러 가지 방법을 보여 줍니다.

```scala
class Child extends Human, Mother, Father:
    def printSuper  = super.yo
    def printMother = super[Mother].yo
    def printFather = super[Father].yo
    def printHuman  = super[Human].yo
```

새로운 `Child` 인스턴스를 생성하고 그 메서드들을 호출하면, 다음과 같은 출력을 보게 됩니다.

```scala
val c = Child()
println(c.printSuper)    // Father
println(c.printMother)   // Mother
println(c.printFather)   // Father
println(c.printHuman)    // Human
```

위에서 보듯이, 클래스가 여러 트레이트를 상속하고 그 트레이트들이 공통의 메서드 이름을 가질 때, `super[traitName].methodName` 구문을 사용하여 어떤 트레이트에서 메서드를 실행할지 선택할 수 있습니다. 또한 `c.printSuper`가 `Father`를 출력하는 점에 주목합니다. 이는 트레이트가 왼쪽에서 오른쪽으로 구성되며, `Father`가 `Child`에 가장 마지막으로 믹스인(mixed in)된 트레이트이기 때문입니다.

```scala
class Child extends Human, Mother, Father:
                                ------
```

이 기법을 사용할 때는, `extends` 키워드를 사용하여 대상 클래스나 트레이트를 직접 확장하지 않는 한, 부모 클래스 계층 구조를 따라 계속 위로 올라가서 도달할 수는 없습니다. 예를 들어, 다음 코드는 이 `Child`가 `Human` 트레이트를 직접 확장하지 않기 때문에 컴파일되지 않습니다.

```scala
class Child extends Mother, Father:      // removed `Human`
    def printSuper  = super.yo
    def printMother = super[Mother].yo
    def printFather = super[Father].yo
    def printHuman  = super[Human].yo    // won't compile
```

이 코드를 컴파일하려고 하면 "Human does not name a parent of class Child"(Human은 Child 클래스의 부모를 지칭하지 않습니다)라는 오류가 발생합니다.

---

## 8.3 메서드 호출 시 매개변수 이름 사용하기 (Using Parameter Names When Calling a Method)

### 문제

메서드를 호출할 때 메서드 매개변수 이름을 명시하는 코딩 스타일을 선호합니다.

### 해법

이름 붙은 매개변수(named parameters)로 메서드를 호출하는 일반적인 구문은 다음과 같습니다.

```scala
methodName(param1=value1, param2=value2, ...)
```

다음 예제에서 이를 보여 드립니다. 다음과 같은 `Pizza` 클래스 정의가 주어졌을 때를 살펴봅니다.

```scala
enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Regular, Thin, Thick

import CrustSize.*, CrustType.*

class Pizza:
    var crustSize = Medium
    var crustType = Regular
    def update(crustSize: CrustSize, crustType: CrustType) =
        this.crustSize = crustSize
        this.crustType = crustType
    override def toString = s"A $crustSize inch, $crustType crust pizza."
```

다음과 같이 `Pizza`를 생성할 수 있습니다.

```scala
val p = Pizza()
```

그런 다음 `update` 메서드를 호출할 때 매개변수 이름과 그에 해당하는 값을 명시하여 `Pizza`를 갱신할 수 있습니다.

```scala
p.update(crustSize = Large, crustType = Thick)
```

이 방식에는 매개변수를 어떤 순서로든 배치할 수 있다는 추가적인 이점이 있습니다.

```scala
p.update(crustType = Thick, crustSize = Large)
```

이 방식은 이름 붙은 매개변수를 사용하지 않는 것보다 더 장황하긴 하지만, 더 읽기 쉬울 수도 있습니다.

### 논의

이 기법은 여러 매개변수가 같은 타입을 가질 때, 예를 들어 한 메서드에 여러 개의 `Boolean`이나 `String` 매개변수가 있을 때 특히 유용합니다. 예를 들어, 이름 붙은 매개변수를 사용하지 않는 다음 메서드 호출을

```scala
engage(true, true, true, false)
```

이름 붙은 매개변수를 사용하는 다음 호출과 비교해 봅니다.

```scala
engage(
    speedIsSet = true,
    directionIsSet = true,
    picardSaidMakeItSo = true,
    turnedOffParkingBrake = false
)
```

메서드가 자신의 매개변수에 대한 기본값(default values)을 지정하는 경우(레시피 8.4에서 설명함)에는, 이 방식을 사용하여 오버라이드하고자 하는 매개변수만 지정할 수 있습니다. 이 두 레시피를 조합하면 유연하고 강력한 접근 방식을 만들 수 있습니다.

---

## 8.4 메서드 매개변수에 기본값 설정하기 (Setting Default Values for Method Parameters)

### 문제

메서드 매개변수에 기본값(default value)을 설정하여, 해당 매개변수에 값을 할당하지 않고도 메서드를 선택적으로 호출할 수 있게 하고 싶습니다.

### 해법

다음 구문을 사용하여 메서드 시그니처(signature) 안에서 매개변수의 기본값을 지정합니다.

```scala
parameterName: parameterType = defaultValue
```

예를 들어, 다음 코드에서는 `timeout` 필드에 기본값 `5_000`을 할당하고, `protocol` 필드에는 기본값 `"https"`를 부여합니다.

```scala
class Connection:
    def makeConnection(timeout: Int = 5_000, protocol: String = "https") =
        println(f"timeout = ${timeout}%d, protocol = ${protocol}%s")
        // more code here
```

`Connection` 인스턴스 `c`가 있을 때, 이 메서드는 다음과 같은 여러 방식으로 호출할 수 있으며, 그 결과는 주석으로 표시되어 있습니다.

```scala
val c = Connection()
c.makeConnection()                  // timeout = 5000, protocol = https
c.makeConnection(2_000)             // timeout = 2000, protocol = https
c.makeConnection(3_000, "http")     // timeout = 3000, protocol = http
```

만약 메서드 매개변수의 이름을 함께 제공하면서 메서드를 호출하고 싶다면, `makeConnection`은 다음과 같은 방식으로도 호출할 수 있습니다(이는 Recipe 8.3에서 다룬 내용입니다).

```scala
c.makeConnection(timeout=10_000)
c.makeConnection(protocol="http")
c.makeConnection(timeout=10_000, protocol="http")
c.makeConnection(protocol="http", timeout=10_000)
```

위 예제에서 볼 수 있듯이, 이 두 가지 레시피(기본값과 이름 지정 인자)는 함께 사용되어 특정 상황에서 유용하게 쓰일 수 있는 읽기 쉬운 코드를 만들어 낼 수 있습니다.

### 논의

생성자 매개변수(constructor parameters)와 마찬가지로, 메서드 인자에도 기본값을 제공할 수 있습니다. 기본값을 제공해 두었기 때문에, 여러분의 메서드를 사용하는 사람은 (a) 기본값을 재정의(override)하기 위해 인자를 제공하거나, (b) 인자를 생략하여 기본값을 사용하도록 둘 수 있습니다.

인자는 왼쪽에서 오른쪽 순서로 할당되므로, 다음 호출은 어떠한 인자도 할당하지 않으며 `timeout`과 `protocol` 모두에 대해 기본값을 사용합니다.

```scala
c.makeConnection()
```

다음 호출은 `timeout`을 `2_000`으로 설정하고 `protocol`은 기본값으로 둡니다.

```scala
c.makeConnection(2_000)
```

다음 호출은 `timeout`과 `protocol`을 모두 설정합니다.

```scala
c.makeConnection(3_000, "ftp")
```

이 방식으로는 `protocol`만 단독으로 설정할 수 없다는 점에 유의하십시오. 그렇게 시도하면 컴파일되지 않습니다. 하지만 해법(Solution)에서 보았듯이, 이름 지정 매개변수(named parameter)를 사용하면 됩니다.

```scala
c.makeConnection(protocol="http")
```

만약 여러분의 메서드가 기본값을 제공하는 일부 필드와 기본값을 제공하지 않는 다른 필드들을 혼합하여 가지고 있다면, 기본값을 가진 필드들을 마지막에 나열하십시오. 이는 기본값을 가진 필드들은 선택적으로 생략될 수 있지만 다른 필드들은 그럴 수 없기 때문입니다. 다음 예제는 기본값 필드를 마지막에 나열하는 올바른 방식을 보여줍니다.

```scala
class Connection:
    // correct implementation, default value is listed last
    def makeConnection(timeout: Int, protocol: String = "https") =
        println(f"timeout = ${timeout}%d, protocol = ${protocol}%s")

val c = Connection()
c.makeConnection(1_000)             // timeout = 1000, protocol = https
c.makeConnection(1_000, "http")     // timeout = 1000, protocol = http
```

반대로, 다음 코드는 기본값을 가진 필드를 먼저 나열했을 때 발생하는 문제를 보여 줍니다.

```scala
class Connection:
    // intentional error
    def makeConnection(timeout: Int = 5_000, protocol: String) =
        println(f"timeout = ${timeout}%d, protocol = ${protocol}%s")
```

이 코드는 컴파일되며 새로운 `Connection`을 생성할 수도 있지만, 다음 예제들에서 보듯이 기본값의 이점을 활용할 수는 없습니다.

```scala
val c = Connection()
c.makeConnection(1_000, "http")     // timeout = 1000, protocol = http
c.makeConnection(2_000)             // compiler error
c.makeConnection("https")           // compiler error

// but this still works
c.makeConnection(protocol = "http") // timeout = 5000, protocol = http
```

---

## 8.5 가변 인자(varargs) 필드를 받는 메서드 만들기 (Creating Methods That Take Variable-Argument Fields)

### 문제

메서드를 더 유연하게 만들기 위해, 가변 개수의 인자를 받을 수 있는 메서드 매개변수, 즉 가변 인자(varargs) 필드를 정의하고 싶습니다.

### 해법

메서드 선언에서 필드 타입 뒤에 `*` 문자를 추가하여 가변 인자(varargs) 필드를 정의합니다.

```scala
def printAll(strings: String*) =
    strings.foreach(println)
```

이 선언이 주어지면, `printAll`은 이제 0개 이상의 매개변수로 호출할 수 있습니다.

```scala
// these all work
printAll()
printAll("a")
printAll("a", "b")
printAll("a", "b", "c")
```

#### 시퀀스를 적응시키려면 `_*`를 사용하세요 (Use _* to adapt a sequence)

기본적으로는 시퀀스(`List`, `Seq`, `Vector` 등)를 가변 인자 매개변수로 전달할 수 없습니다. 하지만 Scala의 `_*` 연산자를 사용하면 시퀀스를 적응(adapt)시켜 가변 인자 필드의 인자로 사용할 수 있습니다.

```scala
val fruits = List("apple", "banana", "cherry")
printAll(fruits)        // fails (Found: List[String]), Required: String)
printAll(fruits: _*)    // works
```

> **`_*`를 "Splat"으로 생각하기 (Thinking of _* as "Splat")**
>
> Unix 배경 지식이 있다면, `_*`를 splat 또는 xargs 연산자로 생각하면 도움이 될 수 있습니다. 이 연산자는 컴파일러에게 시퀀스의 각 요소를 `fruits`를 하나의 단일 인자로 전달하는 대신, 별개의 인자로서 `printAll`에 전달하도록 지시합니다.

### 논의

메서드가 가변 개수의 인자를 담을 수 있는 필드를 가지도록 선언할 때, 그 가변 인자 필드는 메서드 시그니처에서 반드시 마지막 필드여야 합니다. 가변 인자 필드 *뒤에* 다른 필드를 정의하려고 시도하면 오류가 반환됩니다.

```scala
// error: this won't compile
def printAll(strings: String*, i: Int) =
    strings.foreach(println)
```

다행히도 컴파일러 오류 메시지는 매우 명확합니다.

```scala
def printAll(strings: String*, i: Int) =
                     ^^^^^^^
                     varargs parameter must come last
```

이 규칙의 함의로서, 하나의 메서드는 오직 하나의 가변 인자 필드만 가질 수 있습니다.

---

## 8.6 접근자 메서드 호출 시 괄호를 생략하도록 강제하기 (Forcing Callers to Leave Parentheses Off Accessor Methods)

### 문제

접근자(accessor, getter) 메서드를 호출할 때 괄호를 사용할 수 없도록 하는 코딩 스타일을 강제하고 싶습니다.

### 해법

접근자 메서드를 정의할 때 메서드 이름 뒤에 괄호를 붙이지 않고 선언합니다.

```scala
class Pizza:
    // no parentheses after 'crustSize'
    def crustSize = 12
```

이렇게 하면 해당 클래스를 사용하는 쪽에서 `crustSize`를 괄호 없이 호출하도록 강제됩니다. 괄호를 사용하려고 시도하면 컴파일러 오류가 발생합니다.

```scala
scala> val p = Pizza()
p: Pizza = Pizza@3a3e8692

// this fails because of the parentheses
scala> p.crustSize()
1 |p.crustSize()
  |^^^^^
  |method crustSize in class Pizza does not take parameters

// this works
scala> p.crustSize
res0: Int = 12
```

위 예시에서 볼 수 있듯이, 괄호를 붙여 `p.crustSize()`처럼 호출하면 "method crustSize in class Pizza does not take parameters"(Pizza 클래스의 crustSize 메서드는 매개변수를 받지 않습니다)라는 오류가 발생하고, 괄호 없이 `p.crustSize`로 호출하면 정상적으로 `12`라는 값이 반환됩니다.

### 논의

부수 효과(side effect)가 없는 접근자 메서드를 호출할 때 권장되는 방식은, 메서드를 호출할 때 괄호를 생략하는 것입니다. Scala 스타일 가이드(Scala style guide)에서도 다음과 같이 명시하고 있습니다.

> 어떤 종류든 접근자 역할을 하는 메서드(필드를 캡슐화하든, 논리적인 속성을 캡슐화하든)는 부수 효과가 있는 경우를 제외하고는 괄호 없이 선언되어야 합니다.

`crustSize`와 같은 단순한 접근자 메서드는 부수 효과가 없으므로 괄호를 붙여 호출해서는 안 되며, 이 레시피는 그 관례(convention)를 어떻게 강제할 수 있는지 보여 줍니다. 이것은 어디까지나 하나의 관례일 뿐이지만, 엄격하게 따르면 좋은 습관이 됩니다. 예를 들어, `printStuff`라는 이름의 메서드는 아마도 무언가를 출력하리라는 것을 알고 있더라도, 그것이 `printStuff()`처럼 괄호와 함께 호출되는 것을 보면 머릿속에서 작은 경고등이 켜집니다. 즉, 그것이 부수 효과를 가진 메서드라는 사실을 알게 되는 것입니다.

---

## 8.7 메서드가 예외를 던질 수 있음을 선언하기 (Declaring That a Method Can Throw an Exception)

### 문제

어떤 메서드가 예외를 던질 수 있다는 사실을, 호출자에게 알리기 위해서이든 그 메서드가 Java 코드에서 호출될 것이기 때문이든, 선언하고 싶습니다.

### 해법

던져질 수 있는 예외를 선언하기 위해 `@throws` 애너테이션(annotation)을 사용합니다. 메서드가 하나의 예외를 던질 수 있음을 다음과 같이 선언할 수 있습니다.

```scala
@throws(classOf[Exception])
def play =
    // exception throwing code here ...
```

또는 여러 개의 예외를 선언할 수도 있습니다.

```scala
@throws(classOf[IOException])
@throws(classOf[FileNotFoundException])
def readFile(filename: String) =
    // exception throwing code here ...
```

### 논의

해법에 나온 두 예시에서, 이 메서드들이 예외를 던질 수 있다고 선언한 데에는 두 가지 이유가 있습니다. 첫째, 소비자(consumer)가 Scala를 사용하든 Java를 사용하든, 견고한(robust) 코드를 작성하고자 한다면 예외가 던져질 수 있다는 사실을 알고 싶어 할 것입니다.

둘째, 소비자가 Java를 사용하는 경우, `@throws` 애너테이션은 Java 소비자에게 `throws` 메서드 시그니처를 제공하는 것과 Scala에서 동등한 역할을 합니다. 이것은 Java 메서드가 다음과 같은 구문으로 예외를 던진다고 선언하는 것과 똑같습니다.

```java
// java
public void play() throws Exception {
    // code here ...
}
```

또한, 검사 예외(checked exception)에 관한 Scala의 철학이 Java의 철학과 다르다는 점에 주목하는 것이 중요합니다. Scala는 메서드가 예외를 던질 수 있다고 선언하는 것을 요구하지 않으며, 그러한 메서드를 호출하는 쪽에서 예외를 잡도록(catch) 요구하지도 않습니다. 예를 들어, 다음과 같은 메서드가 주어졌을 때를 살펴봅시다.

```scala
def shortCircuit() = throw Exception("HERE'S AN EXCEPTION!")
```

`shortCircuit`이 예외를 던진다고 선언할 필요도 없고, 그것을 `try`/`catch` 블록으로 감쌀 필요도 없습니다. 하지만 그렇게 하지 않으면, 이 메서드는 애플리케이션을 중단(halt)시키게 됩니다.

```scala
scala> shortCircuit()
java.lang.Exception: HERE'S AN EXCEPTION!
        at rs$line$8$.except(rs$line$8:1)
    much more output ...
```

> **Java 예외 타입 (Java Exception Types)**
>
> 간단히 복습하자면, Java에는 (a) 검사 예외(checked exception), (b) `Error`의 자손(descendant), 그리고 (c) `RuntimeException`의 자손이 있습니다. 검사 예외와 마찬가지로, `Error`와 `RuntimeException`도 많은 하위 클래스(subclass)를 가집니다. 예를 들어 `RuntimeException`의 유명한 자손인 `NullPointerException`이 그러합니다.
>
> `Exception` 클래스에 대한 Java 문서에 따르면, "`Exception` 클래스와 그 하위 클래스 중 `RuntimeException`의 하위 클래스가 아닌 것들은 검사 예외입니다. 검사 예외는, 메서드나 생성자의 실행에 의해 던져질 수 있고 메서드나 생성자의 경계 밖으로 전파될 수 있다면, 메서드나 생성자의 `throws` 절(clause)에 선언되어야 합니다."

---

## 8.8 플루언트(fluent) 스타일 프로그래밍 지원하기 (Supporting a Fluent Style of Programming)

### 문제

OOP 스타일로 클래스를 만들면서, 개발자들이 *플루언트(fluent)* 프로그래밍 스타일(*메서드 체이닝(method chaining)*이라고도 부릅니다)로 코드를 작성할 수 있도록 API를 설계하고 싶습니다.

### 해법

플루언트 프로그래밍 스타일을 사용하면 API 사용자가 다음 예시처럼 메서드 호출을 서로 연결(chaining)하여 코드를 작성할 수 있습니다.

```scala
person.setFirstName("Frank")
    .setLastName("Jordan")
    .setAge(85)
    .setCity("Manassas")
    .setState("Virginia")
```

이러한 프로그래밍 스타일을 지원하려면 다음과 같이 합니다.

- 클래스가 확장(extend) 가능한 경우, 플루언트 스타일 메서드의 반환 타입으로 `this.type`을 지정합니다.
- 클래스가 확장 불가능한 경우, 플루언트 스타일 메서드에서 `this` 또는 `this.type`을 반환할 수 있습니다. (클래스 수정자(modifier)와 클래스 확장에 관해서는 논의 부분의 노트를 참고하세요.)

다음 코드는 위에서 보여 준 `set*` 메서드들의 반환 타입으로 `this.type`을 지정하는 방법을 보여 줍니다.

```scala
class Person:
    protected var _firstName = ""
    protected var _lastName = ""

    def setFirstName(firstName: String): this.type =    // note `this.type`
        _firstName = firstName
        this

    def setLastName(lastName: String): this.type =      // note `this.type`
        _lastName = lastName
        this
end Person

class Employee extends Person:
    protected var employeeNumber = 0

    def setEmployeeNumber(num: Int): this.type =
        this.employeeNumber = num
        this

    override def toString = s"$_firstName, $_lastName, $employeeNumber"
end Employee
```

다음 코드는 이러한 메서드들이 어떻게 함께 연결(chaining)될 수 있는지를 보여 줍니다.

```scala
val employee = Employee()

// use the fluent methods
employee.setFirstName("Maximillion")
        .setLastName("Alexander")
        .setEmployeeNumber(2)

println(employee)   // prints "Maximillion, Alexander, 2"
```

### 논의

클래스가 확장되지 않을 것이 확실하다면, `set*` 메서드의 반환 타입으로 `this.type`을 지정할 필요가 없습니다. 각 플루언트 스타일 메서드의 끝에서 단순히 `this` 참조를 반환하면 됩니다. 이 접근 방식은 다음 `Pizza` 클래스의 `addTopping`, `setCrustSize`, `setCrustType` 메서드에서 확인할 수 있습니다. 이 클래스는 확장되지 못하도록 `final`로 선언되어 있습니다.

```scala
enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Regular, Thin, Thick

enum Topping:
    case Cheese, Pepperoni, Mushrooms

import CrustSize.*, CrustType.*, Topping.*

final class Pizza:
    import scala.collection.mutable.ArrayBuffer

    private val toppings = ArrayBuffer[Topping]()
    private var crustSize = Medium
    private var crustType = Regular

    def addTopping(topping: Topping) =
        toppings += topping
        this

    def setCrustSize(crustSize: CrustSize) =
        this.crustSize = crustSize
        this

    def setCrustType(crustType: CrustType) =
        this.crustType = crustType
        this

    def print() =
        println(s"crust size: $crustSize")
        println(s"crust type: $crustType")
        println(s"toppings:   $toppings")
end Pizza
```

이 메서드들은 다음 코드로 시연됩니다.

```scala
val pizza = Pizza()
pizza.setCrustSize(Large)
    .setCrustType(Thin)
    .addTopping(Cheese)
    .addTopping(Mushrooms)
    .print()
```

위 코드는 다음과 같은 출력을 만들어 냅니다.

```
crust size: Large
crust type: Thin
toppings:   ArrayBuffer(Cheese, Mushrooms)
```

> **클래스 수정자(Class Modifiers)**
>
> 새로운 `open` 키워드에 대한 문서에 따르면, Scala 3에서 클래스를 만들 때 "확장 가능성(extensibility)에 대해 세 가지 가능한 기대치가 있습니다".
>
> - 확장을 허용하려면 클래스를 `open`으로 선언합니다.
> - 클래스의 확장을 금지하려면 클래스를 `final`로 선언합니다.
> - 어느 쪽으로도 확실한 결정을 내리지 않았다면 수정자를 사용하지 않습니다.
>
> 세 번째 상황에서는 다음 조건 중 하나가 충족될 때만 클래스를 확장할 수 있습니다.
>
> - 확장하는 클래스가 원본 클래스와 같은 파일에 있는 경우.
> - 확장하는 클래스의 소스 파일에서 `scala.language.adhocExtensions`를 임포트하는 것과 같이, `adhocExtensions` 언어 기능(language feature)이 활성화된 경우.
>
> `adhocExtensions`에 대한 기능 경고(feature warning)는 Scala 3.0에서는 활성화되어 있지 않지만, Scala 3.1 이후 버전에서는 기본적으로 생성됩니다.

---

## 8.9 확장 메서드(extension methods)로 닫힌 클래스에 새 메서드 추가하기 (Adding New Methods to Closed Classes with Extension Methods)

### 문제

`String`, `Int`, 그리고 소스 코드에 접근할 수 없는 다른 클래스들에 메서드를 추가하는 것처럼, 닫힌(closed) 클래스에 새 메서드를 추가하고 싶습니다.

### 해법

Scala 3에서는 원하는 새로운 동작(behavior)을 만들기 위해 *확장 메서드(extension methods)*를 정의합니다. 예를 들어, `String` 클래스에 `hello`라는 이름의 메서드를 추가하여 다음과 같이 동작하는 코드를 작성하고 싶다고 가정해 봅시다.

```scala
println("joe".hello)   // prints "Hello, Joe
```

이 동작을 만들려면 `extension` 키워드를 사용하여 `hello`를 확장 메서드로 정의합니다.

```scala
extension (s: String)
    def hello: String = s"Hello, ${s.capitalize}"
```

REPL은 이것이 원하는 대로 동작함을 보여 줍니다.

```scala
scala> println("joe".hello)
Hello, Joe
```

#### 여러 개의 확장 메서드 정의하기

추가 메서드를 정의하려면, 그것들을 모두 `extension` 선언 아래에 둡니다.

```scala
extension (s: String)
    def hello: String = s"Hello, ${s.capitalize}"
    def increment: String = s.map(c => (c + 1).toChar)
    def hideAll: String = s.replaceAll(".", "*")
```

다음 예시들은 이 메서드들이 어떻게 동작하는지 보여 줍니다.

```scala
"joe".hello          // Hello, Joe
"hal".increment      // ibm
"password".hideAll   // ********
```

#### 매개변수를 받는 확장 메서드

작동 대상이 되는 타입 외에 추가로 매개변수를 받는 확장 메서드를 만들어야 할 때는, 다음 접근 방식을 사용합니다.

```scala
extension (s: String)
    def makeInt(radix: Int): Int = Integer.parseInt(s, radix)
```

다음 예시들은 `makeInt`가 어떻게 동작하는지 보여 줍니다.

```scala
"1".makeInt(2)     // Int = 1
"10".makeInt(2)    // Int = 2
"100".makeInt(2)   // Int = 4

"1".makeInt(8)     // Int = 1
"10".makeInt(8)    // Int = 8
"100".makeInt(8)   // Int = 64

"foo".makeInt(2)   // java.lang.NumberFormatException
```

마지막 예시를 보여 드린 것은, 작성된 그대로의 이 메서드가 잘못된 문자열 입력을 제대로 처리하지 못한다는 점을 명확히 하기 위해서입니다.

### 논의

다음은 원본 `hello` 예시를 사용하여, Scala 3에서 확장 메서드가 어떻게 동작하는지를 단순화하여 설명한 것입니다.

1. 컴파일러가 `String` 리터럴을 봅니다.
2. 컴파일러는 여러분이 `String`에 대해 `hello`라는 이름의 메서드를 호출하려 한다는 것을 봅니다.
3. `String` 클래스에는 `hello`라는 이름의 메서드가 없기 때문에, 컴파일러는 단일 `String` 매개변수를 받고 `String`을 반환하는 `hello`라는 이름의 메서드를 찾기 위해 알려진 스코프(known scope) 주변을 둘러보기 시작합니다.
4. 컴파일러가 그 확장 메서드를 찾습니다.

이것은 실제로 일어나는 일을 지나치게 단순화한 것이지만, 확장 메서드가 어떻게 동작하는지에 대한 전반적인 개념을 제공해 줍니다.
