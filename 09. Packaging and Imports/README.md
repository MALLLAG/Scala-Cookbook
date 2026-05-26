# Chapter 9. Packaging and Imports

## 들어가며

**패키지(package)** 는 서로 관련된 코드 모듈들을 묶어 구성하고, 이름 공간 충돌(namespace collision)을 방지하는 데 사용됩니다. 가장 일반적인 형태로, Scala 패키지는 Java와 동일한 문법으로 만듭니다. 그래서 대부분의 Scala 소스 코드 파일은 다음과 같이 `package` 선언으로 시작합니다.

```scala
package com.alvinalexander.myapp.model

class Person ...
```

하지만 Scala는 이보다 더 유연합니다. 위와 같은 방식에 더해, C++과 C#의 이름 공간(namespace)과 비슷하게 중괄호(curly brace)를 사용하는 패키징 스타일도 쓸 수 있습니다. 이 문법은 레시피 9.1에서 다룹니다.

멤버를 임포트(import)하는 Scala의 방식 역시 Java와 비슷하면서도 더 유연합니다. Scala에서는 다음과 같은 일을 할 수 있습니다.

- `import` 문을 어디에나 배치할 수 있습니다.
- 패키지, 클래스, 오브젝트(object), 메서드를 임포트할 수 있습니다.
- 멤버를 임포트할 때 그 멤버를 숨기거나(hide) 이름을 바꿀(rename) 수 있습니다.

이러한 모든 방식을 이 장에서 다룹니다.

본격적으로 레시피들로 들어가기 전에, 두 개의 패키지가 모든 소스 코드 파일의 범위(scope)에 **암묵적으로(implicitly)** 임포트된다는 사실을 알아 두면 도움이 됩니다.

- `java.lang.*`
- `scala.*`

Scala 3에서 `import` 문에 쓰이는 `*` 문자는 Java의 `*` 문자와 비슷합니다. 따라서 위 두 문장은 해당 패키지의 "모든 멤버를 임포트하라"는 의미입니다.

### The Predef 오브젝트

위의 두 패키지에 더해, `scala.Predef` 오브젝트의 모든 멤버 또한 소스 코드 파일에 암묵적으로 임포트됩니다.

Scala가 어떻게 동작하는지 이해하고 싶다면, `Predef` 오브젝트의 소스 코드를 들여다보는 데 약간의 시간을 들여 보는 것을 강력히 권합니다. 코드가 그리 길지 않으면서도 Scala 언어의 여러 기능을 잘 보여 줍니다.

`Predef` 오브젝트에서는 암묵적 변환(implicit conversion)이 범위로 들어옵니다. 이는 Scala 3.0에서도 여전히 사용되는 Scala 2.13의 `Predef` 오브젝트에 있는 코드로, 다음과 같은 모습입니다.

```scala
implicit def long2Long(x: Long): java.lang.Long = x.asInstanceOf[java.lang.Long]
implicit def Long2long(x: java.lang.Long): Long = x.asInstanceOf[Long]
// more implicit conversions ...
```

마찬가지로, `Map`, `Set`, `println` 같은 코드를 임포트 문 없이도 호출할 수 있는 이유가 궁금했다면, 그 정의 또한 `Predef`에서 찾을 수 있습니다.

```scala
type Map[A, +B] = immutable.Map[A, B]
type Set[A]     = immutable.Set[A]

def println(x: Any) = Console.println(x)
def printf(text: String, xs: Any*) = Console.print(text.format(xs: _*))
def assert(assertion: Boolean) { ... }
def require(requirement: Boolean) { ... }
```

---

## 9.1 Packaging with the Curly Braces Style Notation

### 문제

C++과 C#의 이름 공간 표기법과 비슷하게, 중첩(nested) 스타일의 패키지 표기법을 사용하고 싶습니다.

### 해법

다음 예시처럼, 패키지 이름을 지정하면서 하나 이상의 클래스를 중괄호 한 쌍 안에 감쌉니다.

```scala
package com.acme.store {
    class Foo:
        override def toString = "I am com.acme.store.Foo"
}
```

이 클래스의 정규화된 이름(canonical name)은 `com.acme.store.Foo`입니다. 이는 다음과 같이 코드를 선언한 것과 동일합니다.

```scala
package com.acme.store

class Foo:
    override def toString = "I am com.acme.store.Foo"
```

### 장점

이 방식을 사용하면 여러 개의 패키지를 하나의 파일 안에 배치할 수 있으며, 중첩된 패키지도 만들 수 있습니다. 두 가지 방식을 모두 보여 주기 위해, 다음 예시는 모두 서로 다른 패키지에 속하는 세 개의 `Foo` 클래스를 만듭니다.

```scala
package orderentry {
    class Foo:
        override def toString = "I am orderentry.Foo"
}

package customers {
    class Foo:
        override def toString = "I am customers.Foo"

    package database {
        class Foo:
            override def toString = "I am customers.database.Foo"
    }
}

// test/demonstrate the Foo classes.
// the output is shown after the comment tags.
@main def packageTests =
    println(orderentry.Foo())          // I am orderentry.Foo
    println(customers.Foo())           // I am customers.Foo
    println(customers.database.Foo())  // I am customers.database.Foo
```

이 예시는 각각의 `Foo` 클래스가 서로 다른 패키지에 있다는 것, 그리고 `database` 패키지가 `customers` 패키지 안에 중첩되어 있다는 것을 보여 줍니다.

### 토론

저는 많은 Scala 코드를 살펴보았는데, 제가 본 바로는 파일 맨 위에 패키지 이름을 선언하는 것이 단연 가장 인기 있는 패키징 스타일입니다.

```scala
package foo.bar.baz

class Foo:
    override def toString = "I'm foo.bar.baz.Foo"
```

하지만 Scala 코드는 매우 간결해질 수 있으므로, 여러 개의 클래스와 패키지를 한 파일에 선언하고 싶을 때는 중괄호 패키징 문법이 편리할 수 있습니다. 예를 들어, 이 책의 소스 코드 저장소에서는 제가 이 스타일을 자주 사용하는 것을 볼 수 있습니다.

#### 연쇄된 package 절 (Chained package clauses)

가끔 Scala 프로그램을 보면, 소스 코드 파일 맨 위에 다음과 같이 여러 개의 패키지 선언이 있는 것을 볼 수 있습니다.

```scala
package com.alvinalexander
package tests
...
```

이 코드는 다음과 같이 두 개의 중첩된 패키지를 작성한 것과 정확히 동일합니다.

```scala
package com.alvinalexander {
    package tests {
        ...
    }
}
```

첫 번째 형태가 사용되는 이유는, Scala 프로그래머들이 일반적으로 중괄호 스타일로 코드를 들여쓰기(indent)하는 것을 좋아하지 않기 때문입니다. 특히 큰 파일에서는 더욱 그렇습니다. 그래서 첫 번째 형태를 사용합니다.

한 개의 `package` 절 대신 두 개의 `package` 절을 사용하는 이유는, 각 방식에서 현재 범위(scope)에 무엇이 사용 가능해지는지와 관련이 있습니다. 만약 다음과 같이 하나의 `package` 문만 사용하면,

```scala
package com.alvinalexander.tests
```

`com.alvinalexander.tests`의 멤버만 범위로 들어옵니다. 하지만 다음과 같이 두 개의 `package` 선언을 사용하면,

```scala
package com.alvinalexander
package tests
...
```

`com.alvinalexander`와 `com.alvinalexander.tests` 양쪽의 멤버가 모두 범위로 들어옵니다.

이 방식을 사용하는 이유는 Scala 2.7에서 발견되어 Scala 2.8에서 해결된 어떤 상황과 관련이 있습니다.

---

## 9.2 Importing One or More Members

### 문제

하나 이상의 멤버를 현재 코드의 범위로 임포트하고 싶습니다.

### 해법

하나의 클래스를 임포트할 때는 다음 문법을 사용합니다.

```scala
import java.io.File
```

여러 개의 클래스는 다음과 같이 임포트할 수 있습니다.

```scala
import java.io.File
import java.io.IOException
import java.io.FileNotFoundException
```

혹은 다음과 같이 더 간결하게 할 수도 있습니다.

```scala
import java.io.{File, IOException, FileNotFoundException}
```

저는 이것을 *중괄호 문법(curly brace syntax)* 이라고 부르지만, 더 공식적으로는 **임포트 선택자 절(import selector clause)** 이라고 알려져 있습니다.

`java.io` 패키지의 모든 것을 임포트하는 방법은 다음과 같습니다.

```scala
import java.io.*
```

### 토론

Scala는 매우 유연해서 다음을 할 수 있게 해 줍니다.

- `import` 문을 어디에나 배치할 수 있습니다. 여기에는 클래스의 맨 위, 클래스나 오브젝트 안, 메서드 안, 또는 코드 블록 안이 포함됩니다. 이 기법은 레시피 9.6에서 보여 줍니다.
- 패키지, 클래스, 오브젝트, 메서드를 임포트할 수 있습니다.
- 멤버를 임포트할 때 숨기거나 이름을 바꿀 수 있습니다. 이러한 기법들은 레시피 9.3과 9.4에서 보여 줍니다.

---

## 9.3 Renaming Members on Import

### 문제

이름 공간 충돌이나 혼동을 피하기 위해, 멤버를 임포트할 때 이름을 바꾸고 싶습니다.

### 해법

임포트할 클래스에 새 이름을 부여하려면 다음 문법을 사용합니다.

```scala
import java.awt.{List as AwtList}
```

그러면 코드 안에서는 부여한 별칭(alias)으로 그 클래스를 참조합니다.

```scala
scala> val alist = AwtList(1, false)
val alist: java.awt.List = java.awt.List[list0,0,0,0x0,invalid,selected=null]
```

이렇게 하면 `java.awt.List` 클래스를 `AwtList`라는 이름으로 사용하면서, 동시에 Scala의 `List` 클래스를 원래의 이름 그대로 사용할 수 있습니다.

```scala
scala> val x = List(1, 2, 3)
val x: List[Int] = List(1, 2, 3)
```

임포트 과정에서 여러 클래스의 이름을 한 번에 바꿀 수도 있습니다.

```scala
import java.util.{Date as JDate, HashMap as JHashMap}
```

또한 마지막 임포트 위치에 `*` 문자를 사용하여, (이름을 바꾸지 않은 채로) 그 패키지의 나머지 모든 것을 임포트할 수도 있습니다.

```scala
import java.util.{Date as JDate, HashMap as JHashMap, *}
```

임포트 과정에서 이러한 별칭을 만들었기 때문에, 코드 안에서는 클래스의 원래(실제) 이름을 사용할 수 없습니다. 예를 들어, 위의 마지막 임포트 문을 사용한 뒤에는 다음 코드가 실패하는데, 컴파일러가 `java.util.HashMap` 클래스를 찾을 수 없기 때문입니다. 이름을 바꿔 버렸기 때문입니다.

```scala
scala> val map = HashMap[String, String]()
<console>:12: error: not found: type HashMap
        val map =  HashMap[String, String]
                   ^
```

예상대로 이 코드는 실패하지만, 부여한 별칭으로는 이 클래스를 참조할 수 있습니다.

```scala
scala> val map = JHashMap[String, String]()
map: java.util.HashMap[String,String] = {}
```

임포트 문 끝에 `*`를 사용하여 `java.util` 패키지의 나머지 모든 것을 임포트했기 때문에, 다른 `java.util` 클래스를 사용하는 다음 코드 줄들도 동작합니다.

```scala
scala> val x = ArrayList[String]()
x: java.util.ArrayList[String] = []

scala> val y = LinkedList[String]()
y: java.util.LinkedList[String] = []
```

### 토론

보여 준 것처럼, 클래스를 임포트할 때 새 이름을 만들고 그 새 이름, 즉 별칭으로 그 클래스를 참조할 수 있습니다. 『Programming in Scala』에서는 이것을 **이름 변경 절(renaming clause)** 이라고 부릅니다.

이는 이름 공간 충돌과 혼동을 피해야 할 때 유용합니다. `Listener`, `Message`, `Handler`, `Client`, `Server` 같은 클래스 이름은 모두 매우 흔하므로, 임포트할 때 별칭을 부여하면 도움이 될 수 있습니다.

이 문법은 Scala 3에서 변경되었습니다. 다음 코드는 Scala 2와의 차이를 보여 줍니다.

```scala
// scala 2
import java.util.{Date => JDate, HashMap => JHashMap, _}

// scala 3
import java.util.{Date as JDate, HashMap as JHashMap, *}
```

이 글을 쓰는 시점에는 Scala 3 코드에서도 여전히 Scala 2 문법을 사용할 수 있지만, 이 밑줄(`_`) 문법은 결국 더 이상 사용되지 않을(deprecated) 예정이므로 새로운 문법이 권장됩니다.

여러 레시피를 재미있게 조합해 보면, 임포트할 때 *클래스* 의 이름을 바꿀 수 있을 뿐만 아니라, 오브젝트의 *멤버* 와 Java 클래스의 `static` 멤버의 이름도 바꿀 수 있습니다. 예를 들어, 셸 스크립트에서 저는 `println` 메서드를 더 짧은 이름으로 바꾸는 경향이 있는데, REPL에서 다음과 같이 합니다.

```scala
scala> import System.out.{println as p}

scala> p("hello")
hello
```

이것이 동작하는 이유는, `out`이 `java.lang.System` 클래스 안에 있는 `PrintStream`의 `static final` 인스턴스이고, `println`은 `PrintStream`의 메서드이기 때문입니다. 결과적으로 `p`는 `println` 메서드의 별칭이 됩니다.

---

## 9.4 Hiding a Class During the Import Process

### 문제

이름 충돌이나 혼동을 피하기 위해, 같은 패키지에서 다른 멤버들을 임포트하면서 하나 이상의 클래스는 숨기고 싶습니다.

### 해법

임포트 과정에서 클래스를 숨기려면, 레시피 9.3에서 보여 준 이름 변경 문법을 사용하되 클래스 이름을 `_` 문자로 가리킵니다. 다음 예시는 `java.util` 패키지에서 다른 모든 것을 임포트하면서 `Random` 클래스를 숨깁니다.

```scala
import java.util.{Random => _, *}
```

이 방식은 REPL에서 다음과 같이 확인할 수 있습니다.

```scala
scala> import java.util.{Random => _, *}
import java.util.{Random=>_, _}

// can't access Random
scala> val r = Random()
1 |val r = Random()
  |        ^^
  |        Not found: Random

// can access other members
scala> val x = ArrayList()
val x: java.util.ArrayList[Nothing] = []
```

### 토론

위 예시에서, 코드의 이 부분이 `Random` 클래스를 숨기는 부분입니다.

```scala
import java.util.{Random => _}
```

그 뒤에 오는 중괄호 안의 `*` 문자는, 다음과 같이 패키지의 나머지 모든 것을 임포트하겠다고 명시하는 것과 동일합니다.

```scala
import java.util.*
```

`*` 임포트 와일드카드는 반드시 마지막 위치에 와야 한다는 점에 유의하세요. 다른 위치에서 사용하려고 하면 오류가 발생합니다.

```scala
scala> import java.util.{*, Random => _}
1 |import java.util.{*, Random => _}
  |                  ^^
  |                  named imports cannot follow wildcard imports
```

이는 임포트 과정에서 여러 멤버를 숨기고 싶을 수도 있기 때문이며, 그렇게 하려면 그것들을 먼저 나열해야 합니다.

여러 멤버를 숨기려면, 마지막 와일드카드 임포트를 사용하기 전에 그것들을 나열합니다.

```scala
import java.util.{List => _, Map => _, Set => _, *}
```

이 임포트 문 뒤에는 `java.util`의 다른 클래스들을 사용할 수 있습니다.

```scala
scala> val x = ArrayList[String]()
val x: java.util.ArrayList[String] = []
```

또한 같은 이름을 가진 `java.util` 클래스들과 이름 충돌 없이 Scala의 `List`, `Set`, `Map` 클래스를 사용할 수 있습니다.

```scala
// these are all Scala classes
scala> val a = List(1, 2, 3)
val a: List[Int] = List(1, 2, 3)

scala> val b = Set(1, 2, 3)
val b: Set[Int] = Set(1, 2, 3)

scala> val c = Map(1 -> 1, 2 -> 2)
val c: Map[Int, Int] = Map(1 -> 1, 2 -> 2)
```

임포트할 때 멤버를 숨기는 이 기능은, 한 패키지에서 많은 멤버가 필요해서 `*` 와일드카드 문법을 쓰고 싶지만, 동시에 임포트 과정에서 (보통 이름 충돌 때문에) 하나 이상의 멤버를 숨기고 싶을 때 유용합니다.

---

## 9.5 Importing Static Members

### 문제

Java의 정적 임포트(static import) 방식과 비슷하게 멤버를 임포트하여, 패키지나 클래스 이름을 접두사로 붙이지 않고도 멤버 이름을 직접 참조하고 싶습니다.

### 해법

정적 멤버를 이름으로 임포트하거나, Scala의 `*` 와일드카드 문자로 임포트합니다. 예를 들어, `scala.math` 패키지의 `cos` 메서드를 임포트하는 방법은 다음과 같습니다.

```scala
import scala.math.cos
val x = cos(0)  // 1.0
```

`scala.math` 패키지의 *모든* 멤버를 임포트하는 방법은 다음과 같습니다.

```scala
import scala.math.*
```

이 문법을 사용하면 클래스 이름을 앞에 붙이지 않고도 `scala.math` 패키지의 모든 정적 멤버에 접근할 수 있습니다.

```scala
import scala.math.*
val a = sin(0)    // Double = 0.0
val b = cos(Pi)   // Double = -1.0
```

Java의 `Color` 클래스 또한 이 기법의 유용함을 잘 보여 줍니다.

```scala
import java.awt.Color.*
println(RED)      // java.awt.Color[r=255,g=0,b=0]
println(BLUE)     // java.awt.Color[r=0,g=0,b=255]
```

### 토론

오브젝트(object)와 열거형(enumeration)도 이 기법의 훌륭한 대상입니다. 예를 들어, 다음과 같은 `StringUtils` 오브젝트가 있을 때,

```scala
object StringUtils:
    def truncate(s: String, length: Int): String = s.take(length)
    def leftTrim(s: String): String = s.replaceAll("^\\s+", "")
```

다음과 같이 그 메서드들을 임포트하여 사용할 수 있습니다.

```scala
import StringUtils.*
truncate("four score and seven ", 4)   // "four"
leftTrim("  four score and  ")          // "four score and  "
```

마찬가지로, 다음과 같은 Scala 3 열거형이 있을 때,

```scala
package days {
    enum Day:
        case Sunday, Monday, Tuesday, Wednesday,
            Thursday, Friday, Saturday
}
```

다음과 같이 열거값(enumeration value)을 임포트하여 사용할 수 있습니다.

```scala
// a different package
package bar {
    import days.Day.*

    @main def enumImportTest =
        val date = Sunday

        // more code here ...

        if date == Saturday || date == Sunday then
            println("It's the weekend")
}
```

일부 개발자는 정적 임포트를 좋아하지 않지만, 저는 이 방식이 열거형을 더 읽기 쉽게 만든다고 봅니다. 제 의견으로는, 상수 앞에 클래스나 열거형의 이름을 명시하는 단순한 행위만으로도 코드의 가독성이 떨어집니다.

```scala
if date == Day.Saturday || date == Day.Sunday then
    println("It's the weekend!")
```

정적 임포트 방식을 쓰면 코드에 `Day.`라는 앞부분이 필요 없으므로 읽기가 더 쉽습니다.

```scala
if date == Saturday || date == Sunday then ...
```

---

## 9.6 Using Import Statements Anywhere

### 문제

파일 맨 위가 아닌 다른 곳에서 `import` 문을 사용하고 싶습니다. 일반적으로 임포트의 범위를 제한하거나 코드를 더 명확하게 만들기 위해서입니다.

### 해법

`import` 문은 프로그램 안 거의 모든 곳에 배치할 수 있습니다. 가장 기본적인 사용법으로, 클래스 정의의 맨 위에 멤버를 임포트한 뒤(Java나 다른 언어와 마찬가지로) 코드의 뒷부분에서 임포트된 리소스를 사용합니다.

```scala
package foo

import scala.util.Random

class MyClass:
    def printRandom =
        val r = Random()   //use the imported class
```

더 세밀하게 제어하려면, 클래스 안에서 멤버를 임포트할 수 있습니다.

```scala
package foo

class ClassA:
    import scala.util.Random   //inside ClassA
    def printRandom =
        val r = Random()

class ClassB:
    // the import is not visible here
    val r = Random()   //error: not found: Random
```

이렇게 하면 임포트의 범위가 `import` 문 뒤에 오는 `ClassA` 안의 코드로 제한됩니다.

메서드 안에서도 `import` 문을 사용할 수 있습니다.

```scala
def getPandoraItem(): Any =
    import com.alvinalexander.pandorasbox.*
    val p = Pandora()
    p.getRandomItem
```

심지어 블록(block) 안에 `import` 문을 배치하여, 임포트의 범위를 그 블록 안에서 해당 문 뒤에 오는 코드로만 제한할 수도 있습니다. 다음 예시에서 필드 `r1`은 블록 안에 있고 `import` 문 뒤에 있으므로 올바르게 선언되지만, 필드 `r2`의 선언은 컴파일되지 않습니다. 그 지점에서는 `Random` 클래스가 범위 안에 있지 않기 때문입니다.

```scala
def printRandom =
    {
        import scala.util.Random
        val r1 = Random()   //this works, as expected
    }
    val r2 = Random()       //error: not found: Random
```

### 토론

`import` 문은 임포트된 멤버를 임포트된 지점 *이후* 부터 사용할 수 있게 하며, 이는 그 멤버의 범위 또한 제한합니다. 다음 코드는 `import` 문이 선언되기 *전* 에 `Random` 클래스를 참조하려고 하기 때문에 컴파일되지 않습니다.

```scala
// this does not compile
class ImportTests:
    def printRandom =
        val r = Random()   //error: not found: type Random

import scala.util.Random
```

여러 클래스와 패키지를 한 파일에 담고 싶을 때는, 임포트 문과 (레시피 9.1에서 보여 준) 중괄호 패키징 방식을 결합하여 임포트 문의 범위를 제한할 수 있습니다. 다음 예시들이 이를 보여 줍니다.

```scala
package orderentry {
    import foo.*
    // more code here ...
}

package customers {
    import bar.*
    // more code here ...

    package database {
        import baz.*
        // more code here ...
    }
}
```

이 예시에서 멤버들은 다음과 같이 접근할 수 있습니다.

- `orderentry` 패키지의 코드는 `foo`의 멤버에 접근할 수 있지만, `bar`나 `baz`의 멤버에는 접근할 수 없습니다.
- `customers`와 `customers.database`의 코드는 `foo`의 멤버에 접근할 수 없습니다.
- `customers`의 코드는 `bar`의 멤버에 접근할 수 있습니다.
- `customers.database`의 코드는 `bar`와 `baz`의 멤버에 접근할 수 있습니다.

한 파일에 여러 클래스를 정의할 때도 동일한 개념이 적용됩니다.

```scala
package foo

// available to all classes defined below
import java.io.File
import java.io.PrintWriter

class Foo:
    // only available inside this class
    import javax.swing.JFrame
    // ...

class Bar:
    // only available inside this class
    import scala.util.Random
    // ...
```

`import` 문을 파일 맨 위에 두느냐 아니면 사용되기 바로 직전에 두느냐는 스타일의 문제일 수 있지만, 저는 한 파일에 여러 클래스나 패키지가 있을 때 이 유연함이 유용하다고 느낍니다. 이런 상황에서는 이름 공간 충돌을 제한하고, 코드가 커질 때 리팩터링(refactoring)을 더 쉽게 하기 위해, 임포트를 가능한 한 가장 좁은 범위에 두는 것이 좋습니다.

---

## 9.7 Importing Givens

### 문제

같은 패키지에서 타입을 함께 임포트하면서, 동시에 하나 이상의 `given` 인스턴스를 현재 범위로 임포트해야 합니다.

### 해법

`given` 인스턴스는 더 간단하게 *given* 이라고 알려져 있으며, 일반적으로 별도의 모듈에 정의되고, 특별한 `import` 문을 통해 현재 범위로 임포트되어야 합니다. 예를 들어, `co.kbhr.givens`라는 패키지의 `Adder`라는 오브젝트 안에 다음과 같은 `given` 코드가 있을 때,

```scala
package co.kbhr.givens

object Adder:
    trait Adder[T]:
        def add(a: T, b: T): T
    given intAdder: Adder[Int] with
        def add(a: Int, b: Int): Int = a + b
```

다음 두 개의 `import` 문으로 그것을 현재 범위로 임포트합니다.

```scala
@main def givenImports =
    import co.kbhr.givens.Adder.*      // import all nongiven definitions
    import co.kbhr.givens.Adder.given  // import all `given` definitions

    def genericAdder[A](x: A, y: A)(using adder: Adder[A]): A = adder.add(x, y)
    println(genericAdder(1, 1))
```

또한 이 두 `import` 문을 하나로 합칠 수도 있습니다.

```scala
import co.kbhr.givens.Adder.{given, *}
```

익명(anonymous) `given` 인스턴스는 그 타입으로 임포트할 수 있습니다. 이는 다음 예시의 두 번째 `import` 문에서 보여 줍니다.

```scala
package co.kbhr.givens

object Adder:
    trait Adder[T]:
        def add(a: T, b: T): T
    given Adder[Int] with
        def add(a: Int, b: Int): Int = a + b
    given Adder[String] with
        def add(a: String, b: String): String = "" + (a.toInt + b.toInt)

@main def givenImports =
    // when put on separate lines, the order of the imports is important.
    // the second import statement imports the givens by their type.
    import co.kbhr.givens.Adder.*
    import co.kbhr.givens.Adder.{given Adder[Int], given Adder[String]}

    def genericAdder[A](x: A, y: A)(using adder: Adder[A]): A = adder.add(x, y)
    println(genericAdder(1, 1))        // 2
    println(genericAdder("2", "2"))    // 4
```

이 예시에서, 다음 두 줄의 코드는 `Adder` 트레이트와 given 인스턴스들이 어떻게 임포트되는지 보여 줍니다.

```scala
import co.kbhr.givens.Adder.*
import co.kbhr.givens.Adder.{given Adder[Int], given Adder[String]}
```

필요에 따라, given 인스턴스를 다음과 같이 그 타입으로 임포트할 수도 있습니다.

```scala
import co.kbhr.givens.Adder.*
import co.kbhr.givens.Adder.{given Adder[?]}
```

여기서 두 번째 줄은 "`Adder[Int]`나 `Adder[String]` 같은, 임의의 타입에 대한 `Adder` given을 임포트하라"는 의미로 읽을 수 있습니다.

### 토론

Scala 3 문서에 따르면, 이 새로운 문법에는 두 가지 이유와 이점이 있습니다.

- 범위 안의 given들이 어디에서 오는지를 더 명확하게 만들어 줍니다.
- 다른 어떤 것도 임포트하지 않으면서 모든 given만 임포트할 수 있게 해 줍니다.

given 인스턴스는 Scala 2에서 사용되던 implicit를 대체합니다. 앞서 언급했듯이, given의 주요 목표 중 하나는 implicit보다 더 명확하게 만드는 것입니다. given, 특히 `given` 임포트 문에 대한 동기 중 하나는, Scala 2에서는 implicit가 현재 범위로 어떻게 들어오는지가 항상 명확하지는 않았다는 점입니다.

Scala 3에서 given과 관련된 이 상황을 해결하기 위해, 이 새로운 import given 문법이 만들어졌습니다. 이 예시들에서 볼 수 있듯이, 이제는 `import` 문 목록만 살펴봐도 given들이 어디에서 오는지를 매우 쉽게 알 수 있습니다.
