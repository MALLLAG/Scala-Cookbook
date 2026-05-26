# Chapter 7. Objects

## 챕터 개요

도메인 모델링을 다루는 챕터들의 연장선상에서, 이번 장은 Scala에서 "object"라는 단어가 가지는 두 가지 의미를 다룹니다. Java와 마찬가지로 Scala에서도 "object"라는 단어는 어떤 클래스의 **인스턴스(instance)** 를 가리키는 데 쓰입니다. 하지만 Scala에서는 `object`가 그보다 훨씬 더 널리 **키워드(keyword)** 로 알려져 있습니다. 이 장에서는 이 단어가 가지는 두 가지 의미를 모두 다룹니다.

처음 두 개의 레시피는 object를 "클래스의 인스턴스"라는 의미로 바라봅니다. 즉, 한 타입의 object를 다른 타입으로 캐스팅(cast)하는 방법을 보여 주고, Java의 `.class` 방식에 해당하는 Scala의 대응물을 보여 줍니다.

나머지 레시피들은 `object` 키워드가 다른 목적으로 어떻게 사용되는지를 보여 줍니다. 가장 기본적인 쓰임으로, 레시피 7.3은 `object` 키워드를 사용해 **싱글톤(singleton)** 을 만드는 방법을 보여 줍니다. 레시피 7.4는 클래스에 정적(static) 멤버를 추가하는 한 가지 방법으로서 **companion object** 를 사용하는 방법을 보여 주고, 이어서 레시피 7.5는 companion object 안의 `apply` 메서드를 사용해 클래스 인스턴스를 생성하는 또 다른 방법을 보여 줍니다.

이 레시피들 이후로, 레시피 7.6은 `object`를 사용해 정적 팩토리(static factory)를 만드는 방법을 보여 주고, 레시피 7.7은 하나 이상의 trait을 어떤 object에 결합하는 방법을 보여 주는데, 이 과정은 기술적으로 **reification** 이라고 불립니다. 마지막으로, 패턴 매칭은 Scala에서 매우 중요한 주제인데, 레시피 7.8은 companion object 안에 `unapply` 메서드를 작성해서 여러분이 만든 클래스를 `match` 표현식에서 사용할 수 있도록 하는 방법을 보여 줍니다.

> 참고로, 이 책의 이전 판에서는 **package object** 를 다루었습니다. 하지만 package object는 Scala 3.0 이후로 deprecated 될 예정이므로, 이 책에서는 다루지 않습니다.

## 7.1 Casting Objects

### Problem

어떤 클래스의 인스턴스를 한 타입에서 다른 타입으로 캐스팅해야 하는 경우가 있습니다. 예를 들어 object를 동적으로(dynamically) 생성할 때 이런 상황이 발생합니다.

### Solution

다음 예제에서는 Sphinx-4 음성 인식 라이브러리를 사용합니다. 이 라이브러리는 예전 버전의 Spring Framework에서 빈(bean)을 생성하던 방식과 비슷하게 동작합니다. 이 예제에서는 `lookup` 메서드가 반환한 object를 `Recognizer`라는 클래스의 인스턴스로 캐스팅합니다.

```scala
val recognizer = cm.lookup("recognizer").asInstanceOf[Recognizer]
```

이 Scala 코드는 다음 Java 코드와 동등합니다.

```scala
Recognizer recognizer = (Recognizer)cm.lookup("recognizer");
```

`asInstanceOf` 메서드는 Scala의 `Any` 클래스에 정의되어 있습니다. 따라서 모든 object에서 사용할 수 있습니다.

### Discussion

동적 프로그래밍(dynamic programming)에서는 한 타입에서 다른 타입으로 캐스팅하는 일이 자주 필요합니다. 예를 들어 SnakeYAML 라이브러리로 YAML 설정 파일을 읽을 때 이런 방식이 필요합니다.

```scala
val yaml = Yaml(new Constructor(classOf[EmailAccount]))
val emailAccount = yaml.load(text).asInstanceOf[EmailAccount]
```

`asInstanceOf` 메서드는 이런 상황에만 국한되지 않습니다. 숫자 타입을 캐스팅하는 데에도 사용할 수 있습니다.

```scala
val a = 10                  // Int = 10
val b = a.asInstanceOf[Long]   // Long = 10
val c = a.asInstanceOf[Byte]   // Byte = 10
```

또한 더 복잡한 코드에서도 사용할 수 있습니다. 예를 들어 Java와 상호작용하면서 `Object` 인스턴스들로 이루어진 배열을 Java 쪽으로 전달해야 할 때 사용할 수 있습니다.

```scala
val objects = Array("a", 1)
val arrayOfObject = objects.asInstanceOf[Array[Object]]
AJavaClass.sendObjects(arrayOfObject)
```

`java.net` 클래스를 사용해 프로그래밍하는 경우, HTTP URL 커넥션을 열 때 `asInstanceOf`를 사용해야 할 수도 있습니다.

```scala
import java.net.{URL, HttpURLConnection}
val connection = (new URL(url)).openConnection.asInstanceOf[HttpURLConnection]
```

이런 방식의 코딩은 `ClassCastException`을 일으킬 수 있다는 점에 유의해야 합니다. 다음 REPL 예제가 이를 보여 줍니다.

```scala
scala> val i = 1
i: Int = 1

scala> i.asInstanceOf[String]
ClassCastException: java.lang.Integer cannot be cast to java.lang.String
```

`Int` 값인 `1`을 `String`으로 캐스팅하려고 하자 예외가 발생했습니다. 이런 상황을 다룰 때에는 늘 그렇듯이 `try`/`catch` 표현식을 사용하면 됩니다.

## 7.2 Passing a Class Type with the classOf Method

### Problem

어떤 API가 `Class` 타입을 전달하라고 요구할 때, Java에서는 object에 대해 `.class`를 호출하면 됩니다. 하지만 그 방식은 Scala에서는 동작하지 않습니다.

### Solution

Java의 `.class` 대신 Scala의 `classOf` 메서드를 사용하면 됩니다. 다음은 Java Sound API 프로젝트에서 가져온 예제로, `TargetDataLine` 타입의 클래스를 `DataLine.Info`라는 메서드에 전달하는 방법을 보여 줍니다.

```scala
val info = DataLine.Info(classOf[TargetDataLine], null)
```

대조적으로, Java에서는 같은 메서드 호출을 다음과 같이 작성합니다.

```scala
// java
info = new DataLine.Info(TargetDataLine.class, null);
```

`classOf` 메서드는 Scala의 `Predef` object에 정의되어 있습니다. 따라서 별도의 import 없이도 사용할 수 있습니다.

### Discussion

일단 `Class` 참조(reference)를 얻고 나면, 간단한 리플렉션(reflection) 기법을 시작할 수 있습니다. 예를 들어 다음 REPL 예제는 `String` 클래스의 메서드들에 접근하는 방법을 보여 줍니다.

```scala
scala> val stringClass = classOf[String]
stringClass: Class[String] = class java.lang.String

scala> stringClass.getMethods
res0: Array[java.lang.reflect.Method] = Array(public boolean
java.lang.String.equals(java.lang.Object), public java.lang.String
(output goes on for a while ...)
```

`classOf[String]`을 호출하면 `Class[String]` 타입의 참조를 얻게 되며, 이 참조에 대해 `getMethods`를 호출하면 해당 클래스가 가진 메서드들의 배열(`Array[java.lang.reflect.Method]`)을 얻을 수 있습니다.

## 7.3 Creating Singletons with object

### Problem

어떤 클래스의 인스턴스가 오직 하나만 존재하도록 보장하기 위해, **싱글톤 object(singleton object)** 를 만들고 싶습니다.

### Solution

Scala에서는 `object` 키워드를 사용해 싱글톤 object를 만듭니다. 예를 들어, 인스턴스가 하나만 있어야 하는 어떤 것을 표현하기 위해 싱글톤 object를 만들 수 있습니다. 키보드나 마우스, 혹은 피자 가게의 금전등록기 같은 것이 그런 예입니다.

```scala
object CashRegister:
    def open() = println("opened")
    def close() = println("closed")
```

`CashRegister`를 `object`로 정의했기 때문에, 이것의 인스턴스는 오직 하나만 존재할 수 있습니다. 그리고 이 object의 메서드들은 마치 Java 클래스의 정적(static) 메서드처럼 호출됩니다.

```scala
@main def main =
    CashRegister.open()
    CashRegister.close()
```

`CashRegister.open()`과 `CashRegister.close()`처럼, 클래스 인스턴스를 따로 생성하지 않고도 object 이름에 바로 점(`.`)을 찍어 메서드를 호출합니다.

### Discussion

싱글톤 object는 정확히 하나의 인스턴스만 가지는 클래스입니다. 이 패턴은 유틸리티 메서드를 만드는 데에도 흔히 사용됩니다. 다음 `StringUtils` object가 그 예입니다.

```scala
object StringUtils:
    def isNullOrEmpty(s: String): Boolean =
        if s==null || s.trim.equals("") then true else false
    def leftTrim(s: String): String = s.replaceAll("^\\s+", "")
    def rightTrim(s: String): String = s.replaceAll("\\s+$", "")
    def capitalizeAllWordsInString(s: String): String =
        s.split(" ").map(_.capitalize).mkString(" ")
```

이 메서드들은 `class`가 아니라 `object` 안에 정의되어 있기 때문에, Java의 정적 메서드와 비슷하게 object에 대해 바로 호출할 수 있습니다.

```scala
scala> StringUtils.isNullOrEmpty("")
val res0: Boolean = true

scala> StringUtils.capitalizeAllWordsInString("big belly burger")
val res1: String = Big Belly Burger
```

`isNullOrEmpty("")`는 빈 문자열을 받았으므로 `true`를 반환하고, `capitalizeAllWordsInString("big belly burger")`는 각 단어의 첫 글자를 대문자로 바꾸어 `Big Belly Burger`를 반환합니다.

싱글톤 **case object** 또한 특정 상황에서 재사용 가능한 메시지로 사용하기에 아주 좋습니다. 예를 들어 Akka actor를 사용할 때가 그렇습니다. 시작(start)과 정지(stop) 메시지를 모두 받을 수 있는 여러 개의 actor가 있다면, 다음과 같이 싱글톤들을 만들 수 있습니다.

```scala
case object StartMessage
case object StopMessage
```

그런 다음 이 object들을 actor에게 보낼 수 있는 메시지로 사용할 수 있습니다.

```scala
inputValve ! StopMessage
outputValve ! StopMessage
```

여기서 `!` 연산자는 actor에게 메시지를 보내는 데 사용되며, `StopMessage`라는 싱글톤 case object가 `inputValve`와 `outputValve` 두 actor 모두에게 메시지로 전달됩니다.

## 7.4 Creating Static Members with Companion Objects

### Problem

Java 같은 언어에서 Scala로 넘어온 분이라면, instance member와 static member를 함께 가지는 class를 만들고 싶을 수 있습니다. 그런데 Scala에는 `static`이라는 keyword가 존재하지 않습니다.

### Solution

class 안에 nonstatic(instance) member와 static member를 함께 두고 싶다면, instance member는 `class` 안에 정의하고, "static" member로 보이게 하고 싶은 member들은 그 class와 **이름이 같고 같은 파일 안에 있는** `object` 안에 정의하면 됩니다. 이때 그 object를 class의 **companion object**라고 부르고, 반대로 그 class를 object의 **companion class**라고 부릅니다.

이 방식을 사용하면, 마치 class에 static member가 있는 것처럼 보이게 만들 수 있습니다. 다음 예제를 보겠습니다.

```scala
// Pizza class
class Pizza (var crustType: String):
    override def toString = s"Crust type is $crustType"

// companion object
object Pizza:
    val CRUST_TYPE_THIN = "THIN"    // static fields
    val CRUST_TYPE_THICK = "THICK"
    def getPrice = 0.0              // static method
```

여기서 `Pizza` class에는 instance member인 `crustType`과 `toString`이 정의되어 있고, 같은 이름의 `Pizza` object에는 static처럼 동작할 두 개의 field(`CRUST_TYPE_THIN`, `CRUST_TYPE_THICK`)와 static method(`getPrice`)가 정의되어 있습니다.

이렇게 `Pizza` class와 `Pizza` object를 같은 파일에 정의해 두면, `Pizza` object의 member들을 마치 Java class의 static member처럼 접근할 수 있습니다.

```scala
println(Pizza.CRUST_TYPE_THIN)   // THIN
println(Pizza.getPrice)          // 0.0
```

또한 평소처럼 새로운 `Pizza` instance를 만들어 사용할 수도 있습니다.

```scala
val p = Pizza(Pizza.CRUST_TYPE_THICK)
println(p)   // "Crust type is THICK"
```

이 코드에서는 companion object의 static field인 `Pizza.CRUST_TYPE_THICK`을 생성자 인자로 넘겨 `Pizza` instance `p`를 만들고, 이를 출력하면 instance member인 `toString`이 호출되어 `"Crust type is THICK"`이 출력됩니다.

> **Use enums for Constants**
>
> 실제 코드에서는 상수 값으로 문자열(string)을 사용하지 마십시오. 대신 enum을 사용하는 것이 좋습니다. (Recipe 6.12, "How to Create Sets of Named Values with Enums" 참고)

### Discussion

이 접근 방식은 Java와는 다르지만, recipe 자체는 매우 단순합니다. 정리하면 다음과 같습니다.

- class와 object를 같은 파일에 정의하고, 둘에 같은 이름을 부여합니다.
- "static"처럼 보이게 하고 싶은 member는 object 안에 정의합니다.
- nonstatic(instance) member는 class 안에 정의합니다.

이 recipe에서 *static*이라는 단어에 따옴표를 붙인 이유는, Scala는 object 안의 member를 static member라고 부르지 않기 때문입니다. 다만 이 맥락에서는 Java class의 static member와 같은 목적을 수행한다는 점에서 그렇게 표현했습니다.

#### Accessing private members

여기서 중요하게 알아 둘 점이 있습니다. class와 그 companion object는 **서로의 private member에 접근할 수 있다**는 점입니다. 다음 코드에서 object 안의 static method인 `doubleFoo`는 `Foo` class의 private 변수 `secret`에 접근할 수 있습니다.

```scala
class Foo:
    private val secret = 42

object Foo:
    // access the private class field `secret`
    def doubleFoo(foo: Foo) = foo.secret * 2

@main def fooMain =
    val f = Foo()
    println(Foo.doubleFoo(f))  // prints 84
```

`secret`이 `private`으로 선언되어 있음에도 불구하고, companion object인 `Foo`의 `doubleFoo` method는 인자로 받은 `Foo` instance `foo`의 `foo.secret`에 자유롭게 접근할 수 있습니다. 따라서 `Foo.doubleFoo(f)`는 `42 * 2`인 `84`를 출력합니다.

마찬가지로, 다음 코드에서는 반대 방향의 접근이 일어납니다. 즉 class의 instance member인 `printObj`가 object `Foo`의 private field인 `obj`에 접근할 수 있습니다.

```scala
class Foo:
    // access the private object field `obj`
    def printObj = println(s"I can see ${Foo.obj}")

object Foo:
    private val obj = "Foo's object"

@main def fooMain =
    val f = Foo()
    f.printObj   // prints "I can see Foo's object"
```

여기서 `obj`는 object `Foo` 안에 `private`으로 선언되어 있지만, companion class인 `Foo`의 instance member `printObj`는 `Foo.obj`에 접근할 수 있습니다. 따라서 `f.printObj`는 `"I can see Foo's object"`를 출력합니다. 이처럼 companion class와 companion object는 서로의 private member를 자유롭게 들여다볼 수 있습니다.


## 7.5 Using apply Methods in Objects as Constructors

### Problem

어떤 상황에서는 companion object 안에 `apply` method를 만들어 class의 constructor처럼 동작하게 하는 것이 더 낫거나, 더 쉽거나, 더 편리할 수 있습니다. 그래서 이런 method들을 어떻게 작성하는지 이해하고 싶을 수 있습니다.

### Solution

Recipe 5.2 "Creating a Primary Constructor"와 Recipe 5.4 "Defining Auxiliary Constructors for Classes"에서는 단일 constructor와 다중 constructor를 만드는 방법을 보여 줍니다. 하지만 constructor를 만드는 또 다른 기법으로, class의 companion object(class와 이름이 같고 같은 파일에 정의된 object) 안에 `apply` method를 정의하는 방법이 있습니다. 기술적으로 이들은 constructor가 아니며, 오히려 함수나 factory method에 가깝지만 비슷한 목적을 수행합니다.

`apply` method를 가진 companion object를 만드는 데는 몇 단계만 거치면 됩니다. `Person` class에 대한 constructor를 만든다고 가정해 보겠습니다.

1. `Person` class와 `Person` object를 같은 파일에 정의합니다 (이로써 둘은 companion 관계가 됩니다).
2. `Person` class의 constructor를 private으로 만듭니다.
3. object 안에 class의 builder 역할을 할 하나 이상의 `apply` method를 정의합니다.

처음 두 단계를 위해, class와 object를 같은 파일에 만들고 `Person` class의 constructor를 private으로 만듭니다.

```scala
class Person private(val name: String):
    // define any instance members you need here

object Person:
    // define any static members you need here
```

그런 다음 companion object 안에 하나 이상의 `apply` method를 만듭니다.

```scala
class Person private(val name: String):
    override def toString = name

object Person:
    // the "constructor"
    def apply(name: String): Person = new Person(name)
```

이 코드가 주어지면, 이제 다음 예제처럼 새로운 `Person` instance를 만들 수 있습니다.

```scala
val Regina = Person("Regina")
val a = List(Person("Regina"), Person("Robert"))
```

Scala 2에서는 이 방식의 장점이 class 이름 앞에 `new` keyword를 쓸 필요가 없게 해 준다는 것이었습니다. 하지만 Scala 3에서는 대부분의 상황에서 `new`가 필요 없기 때문에, 이 factory 방식을 선호하거나 드물게 이 방식이 꼭 필요한 상황에서 사용하게 됩니다.

### Discussion

class의 companion object 안에 정의된 `apply` method는 Scala compiler에 의해 특별하게 취급됩니다. 본질적으로, compiler 안에는 약간의 syntactic sugar(문법적 설탕)가 내장되어 있습니다. 그래서 compiler가 다음 코드를 보면,

```scala
val p = Person("Fred Flintstone")
```

이때 compiler가 하는 일 중 하나는, companion object 안에 `apply` method가 있는지 찾아보는 것입니다. 만약 있다면 위 코드를 다음 코드로 바꿉니다.

```scala
val p = Person.apply("Fred Flintstone")
```

따라서 `apply`는 흔히 factory method, 함수, 또는 builder라고 불립니다. 기술적으로는 constructor가 아닙니다.

이 기법을 사용하면서 class를 만드는 여러 가지 방법을 제공하고 싶다면, 원하는 시그니처를 가진 여러 개의 `apply` method를 정의하면 됩니다.

```scala
class Person private(var name: String, var age: Int):
    override def toString = s"$name is $age years old"

object Person:
    // three ways to build a Person
    def apply(): Person = new Person("", 0)
    def apply(name: String): Person = new Person(name, 0)
    def apply(name: String, age: Int): Person = new Person(name, age)
```

이 코드는 새로운 `Person` instance를 만드는 세 가지 방법을 제공합니다.

```scala
println(Person())              //  is 0 years old
println(Person("Regina"))      // Regina is 0 years old
println(Person("Robert", 22))  // Robert is 22 years old
```

`apply`는 그저 하나의 함수일 뿐이므로, 원하는 대로 구현할 수 있습니다. 예를 들어 tuple로부터 `Person`을 만들 수도 있고, 심지어 가변 개수의 tuple로부터 만들 수도 있습니다.

```scala
object Person:
    def apply(t: (String, Int)) = new Person(t(0), t(1))
    def apply(ts: (String, Int)*) =
        for t <- ts yield new Person(t(0), t(1))
```

첫 번째 `apply`는 `(String, Int)` 형태의 tuple 하나를 받아 그 tuple의 0번 요소와 1번 요소로 `Person`을 만듭니다. 두 번째 `apply`는 `(String, Int)*`처럼 가변 개수의 tuple을 받아, `for` 루프로 각 tuple을 순회하면서 `yield`로 각각의 `Person`을 만들어 collection으로 반환합니다.

이 두 `apply` method는 다음과 같이 사용됩니다.

```scala
// create a person from a tuple
val john = Person(("John", 30))

// create multiple people using a variable number of tuples
val peeps = Person(
    ("Barb", 33),
    ("Cheryl", 31)
)
```

`Person(("John", 30))`은 tuple 하나를 받는 `apply`를 호출하여 한 명의 `Person`을 만들고, `peeps`는 여러 개의 tuple을 넘겨 가변 인자 `apply`를 호출하여 여러 명의 `Person`을 한 번에 만듭니다.


## 7.6 Implementing a Static Factory with apply

### Problem

객체 생성 로직을 한곳에 모아 두기 위해, Scala에서 static factory를 구현하고 싶을 수 있습니다.

### Solution

static factory는 *factory pattern*을 단순화한 형태입니다. static factory를 만들려면, Scala의 syntactic sugar를 활용하여 object 안에 `apply` method를 두는 방식으로 factory를 만들 수 있는데, 보통은 companion object에 둡니다.

예를 들어, 요청하는 내용에 따라 `Cat`과 `Dog` class의 instance를 반환하는 `Animal` factory를 만들고 싶다고 가정해 보겠습니다. `Animal` trait의 companion object 안에 `apply` method를 작성하면, 이 factory의 사용자들은 다음과 같이 새로운 `Cat`과 `Dog` instance를 만들 수 있습니다.

```scala
val cat = Animal("cat")   // creates a Cat
val dog = Animal("dog")   // creates a Dog
```

이 동작을 구현하려면, `Animals.scala`라는 이름의 파일을 만들고 그 파일 안에 (a) 부모인 `Animal` trait, (b) 그 trait를 extend하는 class들, (c) 적절한 `apply` method를 가진 companion object를 만듭니다.

```scala
package animals

sealed trait Animal:
    def speak(): Unit

private class Dog extends Animal:
    override def speak() = println("woof")

private class Cat extends Animal:
    override def speak() = println("meow")

object Animal:
    // the factory method
    def apply(s: String): Animal =
        if s == "dog" then Dog() else Cat()
```

여기서 `Animal`은 `speak()` method를 선언한 `sealed trait`이고, `Dog`와 `Cat`은 그 trait를 extend하면서 `speak()`를 각각 `"woof"`, `"meow"`를 출력하도록 override한 `private class`입니다. companion object인 `Animal`의 `apply` method는 문자열 `s`가 `"dog"`이면 `Dog()`를, 그렇지 않으면 `Cat()`을 반환하는 factory method 역할을 합니다.

다음으로, `Factory.scala`라는 이름의 파일에 위 코드를 테스트할 `@main` method를 정의합니다.

```scala
@main def test1 =
    import animals.*

    val cat = Animal("cat")   // returns a Cat
    val dog = Animal("dog")   // returns a Dog

    cat.speak()
    dog.speak()
```

이 코드를 실행하면 다음과 같은 출력을 볼 수 있습니다.

```
meow
woof
```

`Animal("cat")`은 `apply` method를 통해 `Cat` instance를 반환하고, `Animal("dog")`은 `Dog` instance를 반환합니다. 그런 다음 각각의 `speak()`를 호출하면 `meow`와 `woof`가 차례로 출력됩니다.

이 접근 방식의 장점은, `Dog`와 `Cat` class의 instance가 오직 factory method를 통해서만 만들어질 수 있다는 점입니다. 이들을 직접 만들려고 시도하면 컴파일이 실패합니다.

```scala
val c = Cat()   // compile error
val d = Dog()   // compile error
```

`Cat`과 `Dog`가 `private class`로 선언되어 있기 때문에, 외부에서 직접 생성하려는 시도는 compile error로 막힙니다.

### Discussion

static factory를 구현하는 방법은 여러 가지가 있으므로, 특히 `Cat`과 `Dog` class를 얼마나 접근 가능하게 만들고 싶은지에 따라 다양한 접근 방식을 실험해 보십시오. factory method의 핵심 아이디어는, 구체적인 instance가 *오직* factory를 통해서만 만들어지도록 보장하는 것입니다. 따라서 class의 constructor는 다른 모든 class로부터 숨겨져야 합니다. Solution에 나온 코드는 이 문제에 대한 하나의 가능한 해결책을 보여 줍니다.

## 7.7 Reifying Traits as Objects

### Problem

메서드를 포함하는 trait를 하나 이상 만들어 두었고, 이제 그것들을 구체적인(concrete) 형태로 만들고 싶은 상황입니다. 또는, 다음과 같은 코드를 보고 마지막 줄이 도대체 무슨 의미인지 궁금했던 적이 있을 수도 있습니다.

```scala
trait Foo:
    println("Foo")
    // more code ...

object Foo extends Foo
```

여기서 `object Foo extends Foo`라는 마지막 줄이 바로 이번 레시피에서 다루는 핵심입니다.

### Solution

`object`가 하나 이상의 trait를 extends 하는 것을 보게 되면, 그 object는 해당 trait(들)를 **reify** 하기 위해 사용되고 있는 것입니다. **reify**라는 단어는 "추상적인 개념을 구체적인 것으로 만드는 것(to take an abstract concept and making it concrete)"을 의미하며, 이 경우에는 하나 이상의 trait로부터 singleton object를 인스턴스화(instantiating)하는 것을 가리킵니다.

예를 들어, 다음과 같은 trait와 이를 extends 하는 두 개의 class가 주어졌다고 합시다.

```scala
trait Animal

// in a world where all dogs and cats have names
case class Dog(name: String) extends Animal
case class Cat(name: String) extends Animal
```

함수형 프로그래밍(functional programming) 스타일에서는 동물과 관련된 일련의 서비스(animal services)들을 trait로 묶어서 만들 수도 있습니다.

```scala
// assumes that all animal have legs
trait AnimalServices:
    def walk(a: Animal) = println(s"$a is walking")
    def run(a: Animal)  = println(s"$a is running")
    def stop(a: Animal) = println(s"$a is stopped")
```

이와 같은 trait를 만든 다음, 많은 개발자들이 그다음 단계로 하는 일은 바로 이 `AnimalServices` trait를 object로 **reify** 하는 것입니다.

```scala
object AnimalServices extends AnimalServices
```

이렇게 하면 이제 `AnimalServices`의 함수들을 사용할 수 있게 됩니다.

```scala
val zeus = Dog("Zeus")
AnimalServices.walk(zeus)
AnimalServices.run(zeus)
AnimalServices.stop(zeus)
```

`zeus`라는 `Dog` 인스턴스를 만든 뒤, reify 된 `AnimalServices` object를 통해 `walk`, `run`, `stop` 함수를 호출하는 모습입니다.

> **About the Name "Service"**
>
> 'service'라는 이름은 이 함수들이 외부 클라이언트(external clients)에게 제공되는 일련의 공개 서비스(public services)를 제공한다는 사실에서 유래합니다. 저자는 이 함수들이 일련의 웹 서비스 호출(web service calls)로 구현되어 있다고 상상해 보면 이 이름이 자연스럽게 와닿는다고 말합니다. 예를 들어, Twitter의 REST API를 사용해서 Twitter 클라이언트를 작성할 때, 그 API가 제공하는 함수들은 일련의 웹 서비스라고 여겨지는 것과 같은 맥락입니다.

### Discussion

여기서 보여 준 접근 방식은 함수형 프로그래밍에서 사용되는데, 함수형 프로그래밍에서는 데이터를 보통 `case` class로 모델링하고, 그와 관련된 함수들은 trait 안에 넣어 둡니다. 일반적인 레시피(general recipe)는 다음과 같은 순서를 따릅니다.

1. 데이터를 `case` class를 사용해서 모델링합니다.
2. 관련된 함수들을 trait 안에 넣습니다.
3. 필요에 따라 여러 trait를 결합하여, 하나의 해법(solution)이 되도록 trait들을 object로 **reify** 합니다.

앞서 본 코드를 조금 더 실제 세계에 가까운 형태로 구현하면 다음과 같은 모습이 될 수 있습니다. 먼저, 간단한 데이터 모델입니다.

```scala
trait Animal
trait AnimalWithLegs
trait AnimalWithTail
case class Dog(name: String) extends Animal, AnimalWithLegs, AnimalWithTail
```

`Dog`는 `Animal`, `AnimalWithLegs`, `AnimalWithTail`라는 세 개의 trait를 동시에 extends 합니다.

다음으로, 이 trait들에 대응되는 일련의 서비스(services), 즉 함수들의 집합을 만듭니다.

```scala
trait TailServices:
    def wagTail(a: AnimalWithTail) = println(s"$a is wagging tail")
    def stopTail(a: AnimalWithTail) = println(s"$a tail is stopped")

trait AnimalWithLegsServices:
    def walk(a: AnimalWithLegs) = println(s"$a is walking")
    def run(a: AnimalWithLegs) = println(s"$a is running")
    def stop(a: AnimalWithLegs) = println(s"$a is stopped")

trait DogServices:
    def bark(d: Dog) = println(s"$d says ‘woof’")
```

각 trait는 자신이 다루는 동물 유형(꼬리가 있는 동물, 다리가 있는 동물, 개)에 맞는 함수들을 담고 있습니다.

이제 이 모든 trait를 reify 하여 개와 관련된 완전한 함수 목록(complete list of dog-related functions)을 만들 수 있습니다.

```scala
object DogServices extends DogServices, AnimalWithLegsServices, TailServices
```

하나의 object가 여러 trait를 한꺼번에 결합하여 reify 하는 모습입니다. 그러면 이 함수들을 다음과 같이 사용할 수 있습니다.

```scala
import DogServices.*

val rocky = Dog("Rocky")
walk(rocky)
wagTail(rocky)
bark(rocky)
```

`DogServices.*`를 import 했기 때문에 `walk`, `wagTail`, `bark` 함수를 마치 일반 함수처럼 이름만으로 호출할 수 있습니다.

#### Even more specific!

때로는 코드를 좀 더 구체적으로 만들고 싶어서 trait를 타입으로 파라미터화(parameterize)하고 싶을 때가 있습니다. 예를 들면 다음과 같습니다.

```scala
trait TailServices[AnimalWithTail] ...
                   ----------------

trait AnimalWithLegsServices[AnimalWithLegs] ...
                             ----------------
```

이것은 "이 trait 안의 함수들은 오직 이 타입과 함께만 사용될 수 있다(The functions in this trait can only be used with this type.)"라고 말하는 하나의 방법입니다. 더 큰 규모의 애플리케이션에서는, 이 기법이 다른 개발자들로 하여금 해당 trait의 목적을 더 쉽게 이해할 수 있도록 도와줍니다. 이것은 정적 타입 언어(statically typed language)를 사용할 때 얻을 수 있는 장점 중 하나입니다.

이 기법을 현재의 `case` class들과 함께 사용하려면, trait들을 다음과 같이 수정합니다.

```scala
trait TailServices[AnimalWithTail]:
    def wagTail(a: AnimalWithTail) = println(s"$a is wagging tail")

trait AnimalWithLegsServices[AnimalWithLegs]:
    def walk(a: AnimalWithLegs) = println(s"$a is walking")

trait DogServices[Dog]:
    def bark(d: Dog) = println(s"$d says ‘woof’")
```

각 trait가 타입 파라미터를 받도록 바뀌었고, 함수의 파라미터 타입도 그 타입 파라미터를 사용하도록 바뀌었습니다.

그런 다음 reify 된 object를 다음과 같이 만듭니다.

```scala
object DogServices
extends DogServices[Dog], AnimalWithLegsServices[Dog], TailServices[Dog]
```

각 trait를 reify 할 때 구체적인 타입인 `Dog`를 타입 파라미터로 지정해 주는 것을 볼 수 있습니다.

마지막으로, 이전과 마찬가지로 잘 동작하는 것을 확인할 수 있습니다.

```scala
import DogServices.*

val xena = Dog("Xena")
walk(xena)      // Dog(Xena) is walking
wagTail(xena)   // Dog(Xena) is wagging tail
bark(xena)      // Dog(Xena) says ‘woof’
```

`xena`라는 `Dog` 인스턴스에 대해 `walk`, `wagTail`, `bark` 함수를 호출하면 주석에 표시된 것과 같은 결과가 출력됩니다.

## 7.8 Implementing Pattern Matching with unapply

### Problem

어떤 class에 대해 `unapply` 메서드를 작성하여, `match` 표현식 안에서 그 class의 필드(fields)를 추출(extract)할 수 있게 하고 싶은 상황입니다.

### Solution

여러분의 class의 companion object 안에, 올바른 반환 시그니처(return signature)를 가진 `unapply` 메서드를 작성하면 됩니다. 이번 해법은 두 단계로 나누어 설명합니다.

1. `String`을 반환하는 `unapply` 메서드를 작성하기
2. `match` 표현식과 함께 동작하도록 `unapply` 메서드를 작성하기

#### Writing an unapply method that returns a String

`unapply`가 어떻게 동작하는지 보여 주기 위한 출발점으로, 형식화된 문자열(formatted string)을 반환하는 `unapply` 메서드를 가진 companion object와 함께 `Person` class를 살펴봅니다.

```scala
class Person(val name: String, val age: Int)
object Person:
    def unapply(p: Person): String = s"${p.name}, ${p.age}"
```

이렇게 구성한 상태에서, 평소처럼 새로운 `Person` 인스턴스를 만듭니다.

```scala
val p = Person("Lori", 33)
```

`unapply` 메서드의 이점은, person 인스턴스를 해체(deconstruct)할 수 있는 방법을 제공한다는 점입니다.

```scala
val personAsAString = Person.unapply(p)   // "Lori, 33"
```

위에서 보듯, `unapply`는 자신에게 주어진 `Person` 인스턴스를 `String` 표현(representation)으로 해체합니다. Scala에서는 companion object 안에 `unapply` 메서드를 넣으면, **extractor** 메서드를 만들었다고 말합니다. 왜냐하면 object 바깥으로 필드들을 추출(extract)하는 방법을 만든 것이기 때문입니다.

#### Writing an unapply method to work with a match expression

앞의 예제는 `Person`을 `String`으로 해체하는 방법을 보여 주었습니다. 하지만 `match` 표현식 안에서 `Person`의 필드들을 추출하고 싶다면, `unapply`가 특정한 타입(specific type)을 반환하도록 해야 합니다.

- 여러분의 class가 단 하나의 파라미터만 가지고 있고 그 타입이 `A`라면, `Option[A]`를 반환합니다. 즉, 파라미터 값을 `Some`으로 감싸서 반환합니다.
- 여러분의 class가 `A1`, `A2`, ... `An` 타입의 여러 파라미터를 가지고 있다면, 그 값들을 `Option[(A1, A2 ... An)]`로 반환합니다. 즉, 그 값들을 담은 튜플(tuple)을 `Some`으로 감싸서 반환합니다.

만약 어떤 이유로든 `unapply` 메서드가 파라미터들을 적절한 값으로 해체할 수 없다면, 대신 `None`을 반환합니다.

예를 들어, 앞서의 `unapply` 메서드를 다음과 같은 것으로 교체하면 됩니다.

```scala
class Person(val name: String, val age: Int)
object Person:
    def unapply(p: Person): Option[(String, Int)] = Some(p.name, p.age)
```

`Person`은 `name: String`과 `age: Int`라는 두 개의 파라미터를 가지므로, `Option[(String, Int)]`를 반환하고 두 값을 튜플로 묶어 `Some`으로 감쌌습니다.

이렇게 하면 이제 `match` 표현식 안에서 `Person`을 사용할 수 있습니다.

```scala
val p = Person("Lori", 33)
val deconstructedPerson: String = p match
    case Person(n, a) => s"name: $n, age: $a"
    case null => "null!"
```

`case Person(n, a)` 패턴은 `unapply`를 호출하여 `Person`을 해체하고, 그 결과로 얻은 `name`과 `age` 값을 각각 `n`과 `a`에 바인딩합니다. `case null`은 대상이 `null`인 경우를 처리합니다.

REPL은 다음과 같은 결과를 보여 줍니다.

```scala
scala> println(deconstructedPerson)
name: Lori, age: 33
```

`case` class는 이 코드를 자동으로 생성해 줍니다. 하지만 `case` class를 사용하고 싶지 않으면서도 여러분의 class가 `match` 표현식에서 동작하기를 원한다면, 바로 이렇게 직접 class 안에 **extractor**를 활성화할 수 있습니다.
