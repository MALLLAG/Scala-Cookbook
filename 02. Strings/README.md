# Chapter 2. Strings

## 챕터 개요

프로그래머로서 우리는 이름, 주소, 전화번호 등과 같은 **string** 을 항상 다루게 됩니다. Scala의 문자열은 Java 문자열이 제공하는 모든 기능을 그대로 가지고 있을 뿐만 아니라, 그 이상의 것들을 제공하기 때문에 매우 훌륭합니다. 이 챕터에서는 Java와 Scala가 **공유하는 기능**(문자열 포맷팅, 정규식 패턴 사용 등)도 보여 드리며, 동시에 **Scala에 고유한 기능**들도 시연해 드립니다.

Scala 언어의 문법 특성상 Java와의 큰 차이점 중 하나는 **문자열을 선언하는 방식**에 있습니다. Scala의 모든 변수는 `val`이나 `var`로 선언되기 때문에, 문자열 변수 또한 일반적으로 다음과 같이 생성됩니다.

```scala
val s = "Hello, world"
```

위 표현식은 다음 Java 코드와 동등한 의미를 가집니다.

```java
final String s = "Hello, world";
```

Scala에서의 **rule of thumb** 은, `var`를 사용해야 할 특별한 이유가 없다면 **항상 변수를 `val`로 선언**하라는 것입니다. (순수 함수형 프로그래밍에서는 이 원칙을 더욱 엄격하게 적용하여, `var` 필드 사용을 엄금하기도 합니다.)

또한 아래와 같이 `String` 타입을 **explicitly** 선언하실 수도 있습니다.

```scala
val s: String = "Hello, world"   // don't do this
```

하지만 이러한 방식은 코드를 불필요하게 장황하게 만들기 때문에 권장되지 않습니다. Scala의 **type inference** 기능이 매우 강력하기 때문에, 첫 번째 예시에 보여 드린 **implicit 문법**만으로도 충분하며, 오히려 그 방식이 선호됩니다. 실무적으로 제가 변수를 생성할 때 타입을 명시적으로 선언하는 유일한 경우는, 어떤 메서드를 호출하는데 그 메서드 이름만으로는 반환 타입이 무엇인지 분명하지 않을 때입니다.

```scala
val s: String = someObject.someMethod(42)
```

### Scala String Features 

Scala 문자열에 **힘(슈퍼파워!)** 을 부여해 주는 추가 기능들은 다음과 같습니다.

- `==` 연산자로 문자열을 비교할 수 있는 기능
- multiline 문자열
- **String interpolator** 으로, `println(s"Name: $name")` 같은 코드를 작성할 수 있는 기능
- 문자열을 sequence of characters처럼 다룰 수 있게 해 주는 수십 개의 추가 함수형 메서드

이 챕터의 레시피들은 이러한 기능들을 모두 시연합니다.

### Strings are a sequence of characters 

앞서 언급한 중요한 점은, Scala의 문자열은 **sequence of characters**, 즉 `Seq[Char]`처럼 다룰 수 있다는 것입니다. 이러한 특성 덕분에 아래와 같은 예시 문자열이 주어졌을 때,

```scala
val s = "Big Belly Burger"
```

다음과 같이 문자열에서 호출할 수 있는 **"sequence" 계열의 메서드들**을 사용하실 수 있습니다 (일부 자주 쓰이는 예시만 보여 드립니다).

```scala
s.count(_ == 'B')        // 3
s.dropRight(3)           // "Big Belly Bur"
s.dropWhile(_ != ' ')    // " Belly Burger"
s.filter(_ != ' ')       // "BigBellyBurger"
s.sortWith(_ < _)        // "   BBBeeggilllrruy"
s.take(3)                // "Big"
s.takeRight(3)           // "ger"
s.takeWhile(_ != 'r')    // "Big Belly Bu"
```

위 메서드들은 모두 표준 `Seq` 메서드이며, Chapter 11에서 자세히 다룹니다.

### Chaining Method Calls Together

`foreach` 메서드를 제외한 `Seq`의 모든 메서드는 **functional** 입니다. `foreach`는 `Unit`을 반환하기 때문에 일부 정의에 따르면 "함수형"이 아니지만, 나머지 메서드들은 **기존 시퀀스를 mutate하지 않고, 적용될 때마다 새로운 값을 반환**합니다. 이러한 함수형 특성 덕분에, 한 문자열에 대해 여러 메서드를 **연쇄적으로 호출**할 수 있습니다.

```
scala> "scala".drop(2).take(2).capitalize
res0: String = Al
```

이 기법이 생소하신 분들을 위해 위 예제가 어떻게 동작하는지 간단히 설명해 드리면 다음과 같습니다. `drop`은 컬렉션의 처음부터 지정된 수만큼의 요소를 **drops**, 나머지 요소들을 유지하는 컬렉션 메서드입니다. 문자열 `"scala"`에 대해 `drop(2)`를 호출하면, 맨 앞의 두 문자(`sc`)를 버리고 나머지(`ala`)를 반환합니다.

```
scala> "scala".drop(2)
res0: String = ala
```

그다음으로 호출되는 `take(2)` 메서드는, 주어진 시퀀스(`"ala"`)에서 **처음 두 개의 요소를 retains** 하고 나머지는 버립니다.

```
scala> "scala".drop(2).take(2)
res1: String = al
```

마지막으로 `capitalize` 메서드가 호출되어 최종 결과를 얻습니다.

```
scala> "scala".drop(2).take(2).capitalize
res2: String = Al
```

이처럼 메서드를 연쇄적으로 호출하는 방식이 익숙하지 않으시다면, 이를 **fluent 스타일의 프로그래밍**이라고 부른다는 점을 알아 두시면 좋습니다. 자세한 내용은 Recipe 8.8, "Supporting a Fluent Style of Programming"을 참고하시기 바랍니다. 이와 같은 코드 스타일은 함수형 프로그래밍에서 매우 흔하게 사용됩니다. 함수형 프로그래밍에서는 모든 함수가 pure하며 항상 값을 반환합니다. 이 스타일은 RxJava와 RxScala 같은 Rx 기술에서 인기가 많으며, Spark에서도 매우 많이 사용됩니다.

### Where do those methods come from?

Java를 아시는 분이라면 Java `String` 클래스에는 `capitalize` 메서드가 없다는 사실을 아실 것입니다. 그렇기 때문에 이 메서드가 Scala 문자열에서 사용 가능하다는 것이 놀라울 수 있습니다. 실제로 Scala 문자열에는 Java 문자열이 제공하는 것 외에 **수십 개의 추가 메서드**가 있으며, Eclipse나 IntelliJ IDEA 같은 IDE의 **code assist** 기능으로 모두 확인하실 수 있습니다.

이 모든 메서드를 본 후에는, **Scala에 `String` 클래스가 존재하지 않는다는 사실**을 알게 되실 때 더욱 놀라실지도 모릅니다. Scala `String` 클래스가 없는데 어떻게 Scala 문자열이 그 모든 메서드를 가질 수 있을까요?

그 원리는, **Scala가 Java `String` 클래스를 상속**한 다음, **implicit conversion** 및 **extension method** 라고 하는 기능을 통해 메서드들을 추가한다는 것입니다. Scala 2에서는 **암묵적 변환**이 closed 클래스에 메서드를 추가하는 방법이었으며, Scala 3에서는 **확장 메서드**로 그것을 수행합니다. 확장 메서드를 만드는 방법에 대한 자세한 내용은 Recipe 8.9, "Adding New Methods to Closed Classes with Extension Methods"를 참고하시길 바랍니다.

시간이 지나면서 변경될 수는 있지만, Scala 3.0에서 Scala `String`에서 찾을 수 있는 추가 메서드들의 다수는 `StringOps` 클래스에 정의되어 있습니다. 이 메서드들은 `StringOps`에 정의된 뒤, `scala.Predef` 오브젝트에 의해 여러분 코드의 스코프로 **자동으로 import**됩니다. `Predef`는 모든 Scala 소스 코드 파일에 암묵적으로 import되어 있습니다. 여전히 Scala 3.0에서 사용되는 Scala 2.13 `Predef` 오브젝트에는 아래와 같은 문서와 암묵적 변환이 있습니다.

```scala
/** The `String` type in Scala has all the methods of the underlying
 * `java.lang.String`, of which it is just an alias ... In addition,
 * extension methods in scala.collection.StringOps
 * are added implicitly through the conversion augmentString.
 */
@inline implicit def augmentString(x: String): StringOps = new StringOps(x)
```

`augmentString`은 `String`을 `StringOps` 타입으로 변환해 줍니다. 그 결과, `StringOps` 클래스에 있는 메서드들이 모든 Scala `String` 인스턴스에 추가됩니다. 여기에는 문자열을 문자의 시퀀스처럼 다룰 수 있게 해 주는 `drop`, `take`, `filter` 같은 메서드들이 포함됩니다.

#### Tip: Look at the Predef Source Code 

Scala를 처음 배우시는 분이라면, Scala 2.13 `scala.Predef` 오브젝트의 **소스 코드를 꼭 살펴보시기를 권장**드립니다. Scaladoc 페이지에서 소스 코드 링크를 찾을 수 있으며, 거기에는 많은 Scala 프로그래밍 기능의 훌륭한 예시들이 나와 있습니다. 또한 `StringOps`와 `WrappedString` 같은 다른 타입들이 어떻게 포함되어 있는지도 보실 수 있습니다.

---

## 2.1 Testing String Equality 

### Problem

두 문자열이 **equal** 비교하고자 합니다. 즉, 그 두 문자열이 동일한 **문자의 시퀀스**를 담고 있는지 확인하고자 합니다.

### Solution

Scala에서는 두 `String` 인스턴스를 **`==` 연산자**로 비교합니다. 다음과 같은 문자열이 주어졌을 때,

```scala
val s1 = "Hello"
val s2 = "Hello"
val s3 = "H" + "ello"
```

아래와 같이 동등성을 테스트하실 수 있습니다.

```scala
s1 == s2   // true
s1 == s3   // true
```

`==` 메서드의 큰 장점 중 하나는, 어느 한쪽 문자열이 `null`인 경우에도 **기본적인 동등성 테스트에서 `NullPointerException`을 던지지 않는다**는 점입니다.

```scala
val s4: String = null   // String = null
s3 == s4                // false
s4 == s3                // false
```

두 문자열을 **case-insensitive** 비교하고 싶다면, 한 가지 방법은 두 문자열을 모두 대문자나 소문자로 변환한 뒤 `==` 메서드로 비교하는 것입니다.

```scala
val s1 = "Hello"                    // Hello
val s2 = "hello"                    // hello
s1.toUpperCase == s2.toUpperCase    // true
```

또한 Java `String` 클래스와 함께 제공되는 **`equalsIgnoreCase` 메서드**도 사용하실 수 있습니다.

```scala
val a = "Kimberly"
val b = "kimberly"

a.equalsIgnoreCase(b)   // true
```

다만, `null` 문자열에 대해 동등성 테스트를 하는 것은 예외를 던지지 않지만, **`null` 문자열에 대해 메서드를 호출하면 `NullPointerException`을 던진다**는 점에 유의하시기 바랍니다.

```scala
val s1: String = null
val s2: String = null

scala> s1.toUpperCase == s2.toUpperCase
java.lang.NullPointerException   // more output here ...
```

### Discussion 

Scala에서는 객체 동등성을 **`==` 메서드**로 테스트합니다. 이는 Java와 다른 점인데, Java에서는 두 객체를 비교할 때 `equals` 메서드를 사용합니다.

`==` 메서드는 **`AnyRef` 클래스**(모든 **reference types** 의 루트 클래스)에 정의되어 있으며, 먼저 `null` 값 여부를 확인한 뒤, 첫 번째 객체(즉, `this`)에 대해 `equals` 메서드를 호출하여 두 객체(`this`와 `that`)가 같은지를 확인합니다. 그 결과, 문자열을 비교할 때 `null` 값을 따로 검사하지 않아도 됩니다.

#### Better Yet, Don't Use Null

**idiomatic Scala**에서는 `null` 값을 **절대로 사용하지 않습니다**. 이 레시피의 논의는 만약 `null` 값을 만나게 된다면(아마도 `null`을 사용하는 Java 라이브러리나 기타 다른 라이브러리에서 작업하는 경우), `==`이 어떻게 동작하는지를 이해하시는 데 도움을 드리기 위한 것입니다.

Java 같은 언어에서 넘어오신 분이라면, `null`을 사용하고 싶은 때마다 **`Option`** 을 대신 사용하십시오. 저는 Scala에 `null` 키워드조차 없다고 상상하는 것이 도움이 된다고 생각합니다. 더 많은 정보와 예시를 원하신다면 Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"를 참고하시길 바랍니다.

Scala 3에서는 타입 시스템을 변경해서, `AnyRef`를 확장하는 모든 타입(즉, `String`, `List`, `Option` 등)을 **non-nullable**로 만들 수도 있습니다. 실험적인 `-Yexplicit-nulls` 컴파일러 옵션을 사용하면, Scala 타입 계층이 바뀌어서 아래 코드가 컴파일되지 않게 됩니다.

```scala
val s: String = null
// won't compile with '-Yexplicit-nulls'
```

자세한 내용은 Scala **explicit nulls** 페이지를 참고하시길 바랍니다.

### See Also

`==`과 `equals` 메서드 정의에 대한 더 많은 정보는 Recipe 5.9, "Defining an equals Method (Object Equality)"를 참고하시길 바랍니다.

---

## 2.2 Creating Multiline Strings

### Problem

다른 언어의 **heredoc** 문법처럼 Scala 소스 코드 안에서 **multiline string** 을 만들고자 합니다.

### Solution 

Scala에서는 텍스트를 **큰따옴표 세 개(`"""`)** 로 감싸서 여러 줄 문자열을 만드실 수 있습니다.

```scala
val foo = """This is
    a multiline
    String"""
```

이렇게 하는 것도 동작은 하지만, 이 예시의 두 번째와 세 번째 줄은 각 줄 앞부분에 **whitespace** 을 포함하게 됩니다. 이 문자열을 출력하면 다음과 같이 보입니다.

```
This is
    a multiline
    String
```

이 문제는 여러 방법으로 해결하실 수 있습니다. 가장 좋은 방법은 여러 줄 문자열 끝에 **`stripMargin` 메서드**를 추가하고, 첫 번째 줄 이후의 모든 줄은 **파이프 기호(`|`)** 로 시작하도록 작성하는 것입니다.

```scala
val speech = """Four score and
               |seven years ago""".stripMargin
```

`|` 기호를 사용하고 싶지 않으시다면, `stripMargin`을 호출할 때 사용하고자 하는 문자를 지정해 주시면 됩니다.

```scala
val speech = """Four score and
               #seven years ago""".stripMargin('#')
```

또한 첫 번째 줄 이후의 모든 줄을 **left-justify** 할 수도 있습니다.

```scala
val foo = """Four score and
seven years ago"""
```

이 세 가지 방법 모두 동일한 결과를 만들어 냅니다. 즉, 각 줄이 왼쪽 정렬된 여러 줄 문자열입니다.

```
Four score and
seven years ago
```

이러한 접근법들은 각 줄 끝에 숨겨진 `\n` 문자가 있는 **진정한 여러 줄 문자열**을 만들어 냅니다. 만약 이 여러 줄 문자열을 **한 줄의 연속된 문자열로 변환**하고 싶으시다면, `stripMargin` 호출 뒤에 **`replaceAll` 메서드**를 추가하여 모든 개행 문자를 공백 문자로 치환해 주시면 됩니다.

```scala
val speech = """Four score and
               |seven years ago
               |our fathers...""".stripMargin.replaceAll("\n", " ")
```

그 결과는 다음과 같습니다.

```
Four score and seven years ago our fathers...
```

### Discussion

Scala의 여러 줄 문자열 문법이 가진 또 하나의 훌륭한 기능은, **single-quote** 와 **double-quote** 를 **escape하지 않고도** 문자열에 포함시킬 수 있다는 점입니다.

```scala
val s = """This is known as a
          |"multiline" string
          |or 'heredoc' syntax.""". stripMargin.replaceAll("\n", " ")
```

이 코드는 다음과 같은 문자열을 만들어 냅니다.

```
This is known as a "multiline" string or 'heredoc' syntax.
```

---

## 2.3 Splitting Strings

### Problem 

CSV(comma-separated value) 또는 파이프(`|`) 구분 파일에서 얻은 문자열처럼, **field separator** 를 기준으로 문자열을 여러 조각으로 나누고자 합니다.

### Solution 

`String` 객체에서 사용 가능한 **오버라이딩된 `split` 메서드** 중 하나를 사용하시면 됩니다.

```
scala> "hello world".split(" ")
res0: Array[String] = Array(hello, world)
```

`split` 메서드는 **문자열 배열(`Array[String]`)** 을 반환하며, 평소처럼 이 배열을 다루실 수 있습니다.

```
scala> "hello world".split(" ").foreach(println)
hello
world
```

### Discussion

CSV 파일에서의 쉼표 같은 **단순한 character** 를 기준으로 문자열을 분할하실 수 있습니다.

```
scala> val s = "eggs, milk, butter, Cocoa Puffs"
s: java.lang.String = eggs, milk, butter, Cocoa Puffs

// 1st attempt
scala> s.split(",")
res0: Array[String] = Array("eggs", " milk", " butter", " Cocoa Puffs")
```

이 접근법을 사용하실 때에는 각 문자열을 **trim** 하는 것이 좋습니다. 배열을 반환하기 전에 각 문자열에 대해 `trim`을 호출하려면 **`map` 메서드**를 사용하시면 됩니다.

```
// 2nd attempt, cleaned up
scala> s.split(",").map(_.trim)
res1: Array[String] = Array(eggs, milk, butter, Cocoa Puffs)
```

**regular expression** 을 기반으로 문자열을 분할할 수도 있습니다. 다음 예시는 **공백 문자**를 기준으로 문자열을 분할하는 방법을 보여 드립니다.

```
scala> "Relax, nothing is under control".split("\\s+")
res0: Array[String] = Array(Relax,, nothing, is, under, control)
```

#### Tip: Not All CSV Files Are Created Equally 

CSV 파일이라고 명시된 일부 파일들은 실제로는 **필드 내부에 쉼표를 포함**할 수 있으며, 이 경우 일반적으로 작은따옴표나 큰따옴표로 감싸져 있습니다. 다른 파일들은 필드 안에 **newline characters** 를 포함할 수도 있습니다. 이런 파일을 처리하는 알고리즘은 지금 보여 드린 접근법보다 훨씬 복잡해집니다. 더 많은 정보는 **Wikipedia의 CSV files 항목**을 참고하시길 바랍니다.

#### About that split method

`split` 메서드는 **overloaded** 되어 있다는 점에서 흥미롭습니다. 어떤 버전은 Java `String` 클래스에서 오고, 또 어떤 버전은 Scala의 `StringOps` 클래스에서 옵니다. 예를 들어, `split`을 `String` 인수가 아닌 **`Char` 인수**로 호출하시면, `StringOps`의 `split` 메서드를 사용하게 됩니다.

```scala
// split with a String argument (from Java)
"hello world".split(" ")   //Array(hello, world)

// split with a Char argument (from Scala)
"hello world".split(' ')   //Array(hello, world)
```

---

## 2.4 Substituting Variables into Strings 

### Problem 

Perl, PHP, Ruby 같은 다른 언어들에서 하듯이, 문자열에 **variable substitution** 을 수행하고자 합니다.

### Solution

Scala에서 기본적인 **string interpolation** 을 사용하시려면, 문자열 앞에 **문자 `s`** 를 붙이고, 문자열 안에 각 변수 이름 앞에 **`$` 문자**를 붙여 변수를 포함시키시면 됩니다. 다음 예시의 `println` 문에서 보여 드리겠습니다.

```scala
val name = "Fred"
val age = 33
val weight = 200.00

scala> println(s"$name is $age years old and weighs $weight pounds.")
Fred is 33 years old and weighs 200.0 pounds.
```

공식 **Scala string interpolation 문서**에 따르면, 문자열 앞에 문자 `s`를 붙이면, 여러분은 **processed 문자열 리터럴**을 만들고 있는 것입니다. 이 예시는 **"s string interpolator"** 를 사용하고 있는데, 이는 변수들을 문자열 안에 끼워 넣고 그 변수들이 해당 값으로 치환되도록 해 줍니다.

#### Using expressions in string literals

단순한 변수를 문자열 안에 넣는 것 외에도, **중괄호(`{}`)** 로 표현식을 감싸서 **expression** 을 문자열 안에 포함시킬 수 있습니다. 아래 예시에서는 처리된 문자열 안에서 변수 `age`에 값 `1`이 더해집니다.

```
scala> println(s"Age next year: ${age + 1}")
Age next year: 34
```

아래 예시는 중괄호 안에서 **equality expression** 을 사용할 수 있음을 보여 줍니다.

```
scala> println(s"You are 33 years old: ${age == 33}")
You are 33 years old: true
```

**객체의 필드**를 출력할 때에도 중괄호를 사용해야 합니다.

```scala
case class Student(name: String, score: Int)
val hannah = Student("Hannah", 95)

scala> println(s"${hannah.name} has a score of ${hannah.score}")
Hannah has a score of 95
```

객체 필드 값을 **중괄호로 감싸지 않고** 출력하려고 하면, 잘못된 정보가 출력된다는 점에 유의하시기 바랍니다.

```scala
// error: this is intentionally wrong
scala> println(s"$hannah.name has a score of $hannah.score")
Student(Hannah,95).name has a score of Student(Hannah,95).score
```

### Discussion 

각 문자열 리터럴 앞에 붙는 `s`는 실제로는 **메서드**입니다. 단순히 문자열 안에 변수를 넣는 것보다 조금 불편해 보일 수는 있지만, 이 접근법에는 적어도 두 가지 장점이 있습니다.

- Scala는 더 많은 힘을 주기 위해 **다른 interpolation 함수**들을 제공합니다.
- **누구든지 자신만의 문자열 보간 함수를 정의**할 수 있습니다. 예를 들어, Scala SQL 라이브러리들은 이 기능을 활용하여 `sql"SELECT * FROM USERS"` 같은 쿼리를 작성할 수 있게 해 줍니다.

Scala에 내장된 다른 두 가지 문자열 보간 메서드를 살펴보겠습니다.

#### The f string interpolator (printf style formatting) 

Solution의 예시에서, `weight`는 `200.0`으로 출력되었습니다. 괜찮은 결과이기는 하지만, 만약 `weight`에 소수점 자리를 더 추가하거나, 아예 없애고 싶다면 어떻게 할 수 있을까요?

이러한 단순한 요구는 **"f string interpolator"** 로 이어집니다. 이는 문자열 안에서 **`printf` 스타일 format specifier** 를 사용할 수 있게 해 줍니다. 다음 예시들은 `weight`를 출력하는 방법을 보여 드리며, 먼저 소수점 두 자리로 출력합니다.

```
scala> println(f"$name is $age years old and weighs $weight%.2f pounds.")
Fred is 33 years old and weighs 200.00 pounds.
```

그리고 소수점 없이 출력합니다.

```
scala> println(f"$name is $age years old and weighs $weight%.0f pounds.")
Fred is 33 years old and weighs 200 pounds.
```

시연한 바와 같이 이 접근법을 사용하시려면, 다음 단계를 따라 주시면 됩니다.

1. 문자열 앞에 **문자 `f`** 를 붙이십시오.
2. 변수 바로 뒤에 **`printf` 스타일 포맷팅 지정자**를 사용하십시오.

#### Tip: printf Formatting Specifiers 

가장 흔히 쓰이는 `printf` 포맷팅 지정자들은 **Recipe 2.5**에 정리되어 있습니다.

위 예시들은 `println` 메서드를 사용하고 있지만, 다른 언어에서 `sprintf`를 호출하는 것과 유사하게, **변수 치환 결과를 새 변수에 할당**할 수도 있다는 점을 유의하시기 바랍니다.

```
scala> val s = f"$name, you weigh $weight%.0f pounds."
s: String = Fred, you weigh 200 pounds.
```

이제 `s`는 원하는 대로 사용할 수 있는 일반 문자열입니다.

#### The raw interpolator 

`s`와 `f` 문자열 보간기 외에도, Scala는 **`raw`** 라는 또 다른 보간기를 제공합니다. `raw` 보간기는 문자열 안의 **어떤 리터럴도 이스케이프하지 않습니다**. 다음 예시는 `raw`와 `s` 보간기를 비교해서 보여 드립니다.

```
scala> s"foo\nbar"
val res0: String = foo
bar

scala> raw"foo\nbar"
res1: String = foo\nbar
```

위와 같이 `s`는 `\n`을 **개행 문자**로 취급하지만, `raw`는 그것에 특별한 의미를 부여하지 않고 그대로 전달합니다.

#### Tip: Create Your Own Interpolator 

`s`, `f`, `raw` 보간기 외에도, **여러분 자신만의 보간기**를 정의할 수 있습니다. 보간기를 만드는 방법의 예시는 **Recipe 2.11**을 참고하시길 바랍니다.

### See Also 

- Recipe 2.5에 많은 일반적인 문자열 포맷팅 문자가 정리되어 있습니다.
- **Oracle `Formatter` 클래스 문서**에는 사용할 수 있는 포맷팅 문자의 전체 목록이 있습니다.
- **공식 Scala 문자열 보간 페이지**에 보간기에 관한 추가 세부 정보가 있습니다.
- Recipe 2.11은 여러분 자신만의 문자열 보간기를 만드는 방법을 시연합니다.

---

## 2.5 Formatting String Output 

### Problem 

정수, float, double, 문자 등을 포함한 문자열 출력을 **포맷팅**하고자 합니다.

### Solution 

**`f` 보간기**와 함께 **`printf` 스타일 포맷팅 문자열**을 사용하시면 됩니다. 다음 예시들에서 많은 설정 옵션들을 보여 드립니다.

#### Tip: Date/time formatting

날짜와 시간 포맷팅에 관심이 있으시다면, 해당 주제는 **Recipe 3.11, "Formatting Dates"** 에서 다룹니다.

#### Formatting strings 

문자열은 **`%s` 포맷 지정자**로 포맷팅할 수 있습니다. 다음 예시들은 문자열을 포맷팅하는 방법을 보여 주며, 특정 공간 내에서 **left-/right-justify** 하는 방법도 포함합니다.

```scala
val h = "Hello"

f"'$h%s'"     // 'Hello'
f"'$h%10s'"   // '     Hello'
f"'$h%-10s'"  // 'Hello     '
```

저는 변수 이름을 **중괄호로 감쌀 때** 포맷된 문자열이 더 읽기 쉽다고 느끼기 때문에, 이 레시피의 나머지 부분에서는 이 스타일을 사용하도록 하겠습니다.

```scala
f"'${h}%s'"     // 'Hello'
f"'${h}%10s'"   // '     Hello'
f"'${h}%-10s'"  // 'Hello     '
```

#### Formatting floating-point numbers

부동소수점 숫자는 **`%f` 포맷 지정자**로 출력됩니다. 다음 예시들은 `Double`과 `Float` 값을 포함한 부동소수점 숫자의 포맷팅 효과를 보여 드립니다.

```scala
val a = 10.3456       // a: Double = 10.3456
val b = 101234567.3456  // b: Double = 1.012345673456E8

f"'${a}%.1f'"       // '10.3'
f"'${a}%.2f'"       // '10.35'
f"'${a}%8.2f'"      // '   10.35'
f"'${a}%8.4f'"      // ' 10.3456'
f"'${a}%08.2f'"     // '00010.35'
f"'${a}%-8.2f'"     // '10.35   '

f"'${b}%-2.2f'"     // '101234567.35'
f"'${b}%-8.2f'"     // '101234567.35'
f"'${b}%-14.2f'"    // '101234567.35  '
```

이 예시들은 `Double` 값을 시연하지만, 동일한 문법이 `Float` 값에도 적용됩니다.

```scala
val c = 10.5f   // c: Float = 10.5
f"'${c}%.1f'"   // '10.5'
f"'${c}%.2f'"   // '10.50'
```

#### Integer formatting

정수는 **`%d` 포맷 지정자**로 출력됩니다. 다음 예시들은 padding과 justification의 효과를 보여 줍니다.

```scala
val ten = 10
f"'${ten}%d'"         // '10'
f"'${ten}%5d'"        // '   10'
f"'${ten}%-5d'"       // '10   '

val maxInt = Int.MaxValue
f"'${maxInt}%5d'"     // '2147483647'

val maxLong = Long.MaxValue
f"'${maxLong}%5d'"    // '9223372036854775807'
f"'${maxLong}%22d'"   // '   9223372036854775807'
```

#### Zero-fill integer options 

다음 예시들은 정수 값을 **zero-filling** 효과를 보여 드립니다.

```scala
val zero = 0
val one = 1
val negTen = -10
val bigPos = 12345
val bigNeg = -12345
val maxInt = Int.MaxValue

// non-negative integers
f"${zero}%03d"      // 000
f"${one}%03d"       // 001
f"${bigPos}%03d"    // 12345
f"${bigPos}%08d"    // 00012345
f"${maxInt}%08d"    // 2147483647
f"${maxInt}%012d"   // 002147483647

// negative integers
f"${negTen}%03d"    // -10
f"${negTen}%05d"    // -0010
f"${bigNeg}%03d"    // -12345
f"${bigNeg}%08d"    // -0012345
```

#### Character formatting

문자는 **`%c` 포맷 지정자**로 출력됩니다. 다음 예시들은 문자 출력을 포맷팅할 때의 패딩과 정렬 효과를 보여 드립니다.

```scala
val s = 's'
f"|${s}%c|"       // |s|
f"|${s}%5c|"      // |    s|
f"|${s}%-5c|"     // |s    |
```

#### f works with multiline strings

**`f` 보간기는 여러 줄 문자열과도 동작**한다는 점에 유의하시기 바랍니다. 이 예시에서 보여 드립니다.

```scala
val n = "Al"
val w = 200.0
val s = f"""Hi, my name is ${n}
    |and I weigh ${w}%.1f pounds.
    |""".stripMargin.replaceAll("\n", " ")
println(s)
```

위 코드는 다음과 같은 출력을 만들어 냅니다.

```
Hi, my name is Al and I weigh 200.0 pounds.
```

Recipe 2.2에서 언급된 바와 같이, **여러 줄 문자열을 사용할 때에는 작은따옴표와 큰따옴표를 이스케이프하지 않아도 된다**는 점을 기억해 두시기 바랍니다.

### Discussion 

참고용으로, Table 2-1은 자주 쓰이는 `printf` 스타일 포맷 지정자를 보여 드립니다.

#### Table 2-1. Common printf style format specifiers

| Format specifier | Description |
| --- | --- |
| `%c` | Character |
| `%d` | Decimal number (base 10) |
| `%e` | Exponential floating-point number |
| `%f` | Floating-point number |
| `%i` | Integer (base 10) |
| `%o` | Octal number |
| `%s` | A string of characters |
| `%u` | Unsigned decimal (integer) number |
| `%x` | Hexadecimal number |
| `%%` | Print a "percent" character |
| `$$` | Print a "dollar sign" character |

이 포맷 지정자들이 어떻게 동작하는지를 보여 드리기 위해, 다음 예시는 `%%`와 `$$`를 사용하는 방법을 보여 드립니다.

```scala
println(f"%%")   // prints %
println(f"$$")   // prints $
```

Table 2-2는 문자열을 포맷팅할 때 사용하실 수 있는 **character sequences** 들을 정리한 것입니다.

#### Table 2-2. Character sequences that can be used in strings 

| Character sequence | Description |
| --- | --- |
| `\b` | backspace |
| `\f` | form feed |
| `\n` | newline, or linefeed |
| `\r` | carriage return |
| `\t` | tab |
| `\\` | backslash |
| `\"` | double quote |
| `\'` | single quote |
| `\u` | beginning of a Unicode character |

### See Also

- **`java.util.Formatter` 클래스 문서**에 사용할 수 있는 모든 포맷팅 문자가 나와 있습니다.

---

## 2.6 Processing a String One Character at a Time 

### Problem

문자열의 각 문자를 **iterate** 하면서, 문자열을 따라 가며 각 문자에 대해 어떤 **operation** 을 수행하고자 합니다.

### Solution

문자열 안의 문자들을 **transform** 하여 새로운 결과를 얻고 싶으시다면(사이드 이펙트와 반대 개념), **`for` 표현식** 또는 **`map`, `filter` 같은 higher-order functions, HOFs** 를 사용하시면 됩니다. 만약 출력을 출력하는 것처럼 **side effect** 를 발생시키는 작업을 하고 싶으시다면, 단순한 **`for` 루프** 또는 **`foreach` 같은 메서드**를 사용하시면 됩니다. 문자열을 **sequence of bytes** 로 다뤄야 한다면, **`getBytes` 메서드**를 사용하시면 됩니다.

#### Transformers

다음은 문자열 안의 각 문자를 변환하는 **`for` 표현식**(`yield`가 있는 `for` 루프)의 예시입니다.

```
scala> val upper = for c <- "yo, adrian" yield c.toUpper
upper: String = YO, ADRIAN
```

아래는 이와 동등한 **`map` 메서드**입니다.

```
scala> val upper = "yo, adrian".map(c => c.toUpper)
upper: String = YO, ADRIAN
```

Scala의 **underscore** 마법을 사용하면 그 코드를 더 짧게 줄일 수 있습니다.

```
scala> val upper = "yo, adrian".map(_.toUpper)
upper: String = YO, ADRIAN
```

HOF와 **순수 변환 함수들**의 훌륭한 점 중 하나는, 원하는 결과를 얻기 위해 **여러 함수를 연쇄적으로 조합**할 수 있다는 것입니다. 다음은 `filter`를 호출한 뒤 `map`을 호출하는 예시입니다.

```scala
"yo, adrian".filter(_ != 'a').map(_.toUpper)   // String = YO, DRIN
```

#### Side effects

문자열 안의 각 문자를 STDOUT에 출력하는 것과 같은 **사이드 이펙트**를 수행해야 할 때에는, 단순한 `for` 루프를 사용하실 수 있습니다.

```
scala> for c <- "hello" do println(c)
h
e
l
l
o
```

**`foreach` 메서드**도 사용하실 수 있습니다.

```
scala> "hello".foreach(println)
h
e
l
l
o
```

#### Working on string bytes

문자열을 **바이트의 시퀀스**로 작업해야 하신다면, **`getBytes` 메서드**를 사용하실 수도 있습니다. `getBytes`는 문자열로부터 **sequential collection 형태의 바이트**를 반환합니다.

```
scala> "hello".getBytes
res0: Array[Byte] = Array(104, 101, 108, 108, 111)
```

`getBytes` 뒤에 `foreach`를 추가하면, 각 `Byte` 값에 대해 작업하는 한 가지 방법을 보여 드립니다.

```
scala> "hello".getBytes.foreach(println)
104
101
108
108
111
```

#### Tip: Writing a Method to Work with map

`String` 안의 문자들에 대해 작업하기 위해 `map`에 전달할 메서드를 작성하려면, 단일 `Char`를 입력으로 받는 메서드를 정의하고, 메서드 내부에서 그 `Char`에 대해 작업을 수행한 다음, 여러분의 알고리즘에 필요한 어떤 **데이터 타입이든** 반환하면 됩니다. 다음 로직은 짧지만, **커스텀 메서드를 작성하고 이를 `map`에 전달하는 방법**을 보여 드립니다.

```scala
// write your own method that operates on a character
def toLower(c: Char): Char = (c.toByte+32).toChar

// use that method with map
"HELLO".map(toLower)
    // String = hello
```

메서드를 함수 매개변수를 기대하는 다른 메서드에 전달할 수 있는 방법에 대한 자세한 내용은, **Recipe 10.2, "Passing Functions Around as Variables"** 의 **Eta Expansion 논의**를 참고하시길 바랍니다.

### Discussion

Scala는 `String`을 문자의 시퀀스, 즉 `Seq[Char]`로 취급하기 때문에, 위 예시들 모두 자연스럽게 동작합니다.

#### for + yield

Java, C, C# 같은 **imperative language** 에서 Scala로 넘어오신 분이라면, `map` 메서드를 사용하는 것이 처음에는 편하지 않을 수 있습니다. 이 경우에는 다음과 같이 `for` 표현식을 작성하는 것을 선호하실 수도 있습니다.

```scala
val upper = for c <- "hello, world" yield c.toUpper
```

`for` 루프에 **`yield`를 추가**하면, 기본적으로 **각 루프 iteration의 결과를 holding area에 쌓습니다**. 루프가 끝나면, 그 저장 공간의 모든 요소들이 **단일 컬렉션**으로 반환됩니다. 이렇게 요소들이 `for` 루프에 의해 **yield** 된다고 말할 수 있습니다.

`map`이 어떻게 동작하는지 익숙해지는 것을 **(강력히!) 권장**드립니다. 다만 처음에 시작할 때 `for` 표현식을 사용하고 싶으시다면, `filter`와 `map`을 사용하는 아래 표현식이

```scala
val result = "hello, world"
    .filter(_ != 'l')
    .map(_.toUpper)
```

아래 `for` 표현식과 동등하다는 것을 아시면 도움이 될 것입니다.

```scala
val result = for
    c <- "hello, world"
    if c != 'l'
yield
    c.toUpper
```

#### Tip: Custom for Loops Are Rarely Needed

공식 Scala 웹사이트의 Scala Book 1판에서 제가 썼던 것처럼, **Scala 컬렉션 클래스들의 큰 강점**은 **수십 개의 prebuilt 메서드**들과 함께 제공된다는 것입니다. 이것의 큰 장점은 **컬렉션을 다룰 때마다 매번 커스텀 `for` 루프를 작성하지 않아도 된다**는 것입니다. 그리고 이것만으로 충분히 큰 이점이 아니라고 느끼신다면, 더 이상 다른 개발자가 작성한 **커스텀 `for` 루프를 읽지 않아도 된다**는 점도 의미합니다. ;)

더 진지한 얘기를 해 드리자면, 연구에 따르면 개발자들은 코드를 **작성**하는 것보다 **읽는** 데에 훨씬 더 많은 시간을 쓴다고 합니다. 읽기/쓰기 비율은 20:1 정도로 높게 추정되기도 하며, 최소 10:1은 됩니다. 코드를 읽는 데에 많은 시간을 쓰기 때문에, 코드가 **간결하고 읽기 쉬워야** 한다는 점이 중요한데, Scala 개발자들은 이를 **expressive** 코드라고 부릅니다.

#### Transformer methods

"Scala 방식"에 익숙해지시면, 즉 커스텀 `for` 루프를 작성할 필요가 없도록 **Scala의 내장 변환 함수들**을 활용하는 방식에 익숙해지시면, **`map` 메서드 호출**을 사용하고 싶으실 것입니다. 아래 두 `map` 표현식은 앞서 본 `for` 표현식과 동일한 결과를 만들어 냅니다.

```scala
val upper = "hello, world".map(c => c.toUpper)
val upper = "hello, world".map(_.toUpper)
```

`map` 같은 변환기 메서드는 위에 보여 드린 것처럼 **한 줄짜리 anonymous function** 를 받을 수 있으며, 훨씬 더 큰 알고리즘도 받을 수 있습니다. 다음은 **여러 줄 블록 코드**를 사용하는 `map`의 예시입니다.

```scala
val x = "HELLO".map { c =>
    // 'c' represents each character from "HELLO" ('H', 'E', etc.)
    // that's passed one at a time into this algorithm
    val i: Int = c.toByte + 32
    i.toChar
}

// x: String = "hello"
```

이 알고리즘은 **중괄호로 감싸져 있다**는 것에 주목하십시오. 이런 식으로 여러 줄 블록 코드를 만들고 싶을 때에는 중괄호가 필요합니다.

위 예시들로 미루어 짐작하실 수 있듯이, `map`은 내부에 **loop가 내장**되어 있고, 그 루프에서 주어진 알고리즘에 한 번에 한 `Char`씩 전달합니다.

이제 넘어가기 전에, 문자열 변환기 메서드의 추가 예시 몇 가지를 더 보여 드립니다.

```scala
val f = "foo bar baz"
f.dropWhile(_ != ' ')   // " bar baz"
f.filter(_ != 'a')      // "foo br bz"
f.takeWhile(_ != 'r')   // "foo ba"
```

#### Side effect approaches

`map`이나 `for/yield` 접근법이 하나의 컬렉션을 다른 컬렉션으로 **변환**하는 데에 쓰이는 반면, **`foreach` 메서드**는 결과를 반환하지 않고 **각 요소에 대해 작업**할 때 사용됩니다. 이는 메서드의 시그니처가 `Unit`을 반환하는 것으로도 확인할 수 있습니다.

```scala
def foreach[U](f: (A) => U): Unit
                             ----
```

이는 `foreach`가 **출력과 같은 사이드 이펙트**를 처리하는 데에 유용하다는 것을 알려 줍니다.

```
scala> "hello".foreach(println)
h
e
l
l
o
```

#### A Complete Example

다음 예시는 문자열에 대해 `getBytes`를 호출한 뒤, **`foreach`에 코드 블록을 전달**하여 문자열에 대한 **Adler-32 체크섬 값**을 계산하는 방법을 시연합니다.

```scala
/**
 * Calculate the Adler-32 checksum using Scala.
 * @see https://en.wikipedia.org/wiki/Adler-32
 */
def adler32sum(s: String): Int =
    val MOD_ADLER = 65521
    var a = 1
    var b = 0

    // loop through each byte, updating `a` and `b`
    s.getBytes.foreach{ byte =>
        a = (byte + a) % MOD_ADLER
        b = (b + a) % MOD_ADLER
    }

    // this is the return value.
    // note that Int is 32 bits, which this requires.
    b * 65536 + a     // or (b << 16) + a

@main def adler32Checksum =
    val sum = adler32sum("Wikipedia")
    println(f"checksum (int) = ${sum}%d")
    println(f"checksum (hex) = ${sum.toHexString}%s")
```

`@main` 메서드의 두 번째 `println` 문은 **16진수 값 `11e60398`** 을 출력하는데, 이는 Adler-32 알고리즘 페이지의 `0x11E60398`과 일치합니다.

이 예시에서 `map` 대신 `foreach`를 사용한 이유는, 목표가 문자열 안의 각 바이트를 순회하면서 각 바이트에 대해 뭔가를 수행하되, **루프에서 아무것도 반환하지 않는 것**이기 때문입니다. 대신 이 알고리즘은 **mutable variables `a`와 `b`를 갱신**합니다.

### See Also

- 내부적으로 Scala 컴파일러는 `for` 루프를 **`foreach` 메서드 호출**로 변환합니다. 루프에 하나 이상의 `if` 문(guards)이나 `yield` 표현식이 있으면 더 복잡해집니다. 이는 제 책 **Functional Programming, Simplified** (CreateSpace)에서 매우 자세히 다룹니다.
- Adler 코드는 Wikipedia의 **Adler-32 체크섬 알고리즘**에 관한 논의를 기반으로 합니다.

---

## 2.7 Finding Patterns in Strings

### Problem

문자열이 **regular expression pattern** 을 포함하는지 검색하고자 합니다.

### Solution

`String`에 **`.r` 메서드**를 호출하여 **`Regex` 객체**를 생성한 뒤, 그 패턴을 이용해 한 개의 일치 항목을 찾을 때에는 **`findFirstIn`** 을, 모든 일치 항목을 찾을 때에는 **`findAllIn`** 을 사용하시면 됩니다.

이를 시연하기 위해, 먼저 검색할 패턴에 대한 `Regex`를 만들어 보겠습니다. 여기서는 **하나 이상의 숫자 문자 시퀀스**에 해당하는 패턴을 사용합니다.

```scala
val numPattern = "[0-9]+".r   // scala.util.matching.Regex = [0-9]+
```

그다음, 검색할 예시 문자열을 만듭니다.

```scala
val address = "123 Main Street Suite 101"
```

`findFirstIn` 메서드는 **첫 번째 일치 항목**을 찾습니다.

```
scala> val match1 = numPattern.findFirstIn(address)
match1: Option[String] = Some(123)
```

이 메서드가 **`Option[String]`** 을 반환한다는 점에 유의하시기 바랍니다.

**여러 개의 일치 항목**을 찾을 때에는 `findAllIn` 메서드를 사용하십시오.

```
scala> val matches = numPattern.findAllIn(address)
val matches: scala.util.matching.Regex.MatchIterator = <iterator>
```

보시는 것처럼, `findAllIn`은 **iterator** 를 반환하므로, 결과를 루프로 순회할 수 있습니다.

```
scala> matches.foreach(println)
123
101
```

`findAllIn`이 어떤 결과도 찾지 못하면 **empty iterator** 를 반환하므로, 결과가 `null`인지 검사할 필요 없이 평소처럼 코드를 작성할 수 있습니다. 만약 결과를 **`Vector`** 로 받고 싶으시다면, `findAllIn` 호출 뒤에 **`toVector` 메서드**를 추가하시면 됩니다.

```
scala> val matches = numPattern.findAllIn(address).toVector
val matches: Vector[String] = Vector(123, 101)
```

일치 항목이 없으면, 이 접근법은 **빈 벡터**를 반환합니다. `toList`, `toSeq`, `toArray` 같은 다른 메서드들도 사용 가능합니다.

### Discussion

`String`에 대해 **`.r` 메서드**를 사용하는 것이 `Regex` 객체를 만드는 가장 쉬운 방법입니다. 또 다른 접근법은 `Regex` 클래스를 **import**하여 `Regex` 인스턴스를 만든 다음, 같은 방식으로 사용하는 것입니다.

```scala
import scala.util.matching.Regex
val numPattern = Regex("[0-9]+")

val address = "123 Main Street Suite 101"
val match1 = numPattern.findFirstIn(address)   // Option[String] = Some(123)
```

이 방법은 조금 더 많은 작업이 필요하지만, 더 **명시적**이기도 합니다. 저는 문자열 끝의 `.r`을 **놓치기 쉽다**는 것을 경험해 봤는데, 그럴 경우 바라보고 있는 코드가 어떻게 동작하는지 몇 분 동안 의아해하게 됩니다.

#### A brief discussion of the Option returned by findFirstIn

Solution에서 언급된 바와 같이, `findFirstIn` 메서드는 예시 문자열에서 첫 번째 일치 항목을 찾아 **`Option[String]`** 을 반환합니다.

```
scala> val match1 = numPattern.findFirstIn(address)
match1: Option[String] = Some(123)
```

`Option`/`Some`/`None` 패턴은 **Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"** 에서 다루기 때문에 여기서 자세히 설명하지는 않겠지만, `Option`을 **0 또는 1개의 값을 담고 있는 컨테이너**로 생각하시면 됩니다. `findFirstIn`의 경우, 성공하면 문자열 `"123"`을 `Some`으로 감싸서 반환합니다. 즉 `Some("123")`입니다. 하지만 검색 대상 문자열에서 패턴을 찾지 못하면 `None`을 반환합니다.

```
scala> val address = "No address given"
address: String = No address given

scala> val match1 = numPattern.findFirstIn(address)
match1: Option[String] = None
```

요약하자면, (a) `Option[String]`을 반환하도록 정의되어 있고, (b) 예외를 던지지 않도록 보장되며, (c) 종료가 보장된(무한 루프에 빠지지 않는) 메서드는 항상 **`Some[String]` 또는 `None`** 을 반환하게 됩니다.

### See Also

- 정규 표현식 작업에 대한 더 많은 방법은 **Scala `Regex` 클래스 문서**를 참고하시길 바랍니다.
- `Option` 값 다루기에 대한 자세한 내용은 **Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"** 를 참고하시길 바랍니다.

---

## 2.8 Replacing Patterns in Strings

### Problem

문자열에서 **정규식 패턴**을 검색한 뒤 **replace** 하고자 합니다.

### Solution

문자열은 **immutable** 이기 때문에, 문자열 자체에 대해 직접 찾아 치환 작업을 수행할 수는 없습니다. 하지만 치환된 내용을 담은 **새로운 문자열**을 만드실 수 있습니다. 이를 수행하는 여러 방법이 있습니다.

문자열에 대해 **`replaceAll`** 을 호출하여 그 결과를 새 변수에 할당하실 수 있습니다.

```
scala> val address = "123 Main Street".replaceAll("[0-9]", "x")
address: String = xxx Main Street
```

**정규 표현식**을 먼저 만든 뒤 해당 표현식에 대해 **`replaceAllIn`** 을 호출할 수도 있습니다. 역시 결과를 새 문자열에 할당하는 것을 잊지 마십시오.

```
scala> val regex = "[0-9]".r
regex: scala.util.matching.Regex = [0-9]

scala> val newAddress = regex.replaceAllIn("123 Main Street", "x")
newAddress: String = xxx Main Street
```

**패턴의 첫 번째 occurrence만 치환**하고 싶으시다면, **`replaceFirst` 메서드**를 사용하시면 됩니다.

```
scala> val result = "123".replaceFirst("[0-9]", "x")
result: String = x23
```

`Regex`와 함께 **`replaceFirstIn`** 도 사용하실 수 있습니다.

```
scala> val regex = "H".r
regex: scala.util.matching.Regex = H

scala> val result = regex.replaceFirstIn("Hello world", "J")
result: String = Jello world
```

### See Also

- **`scala.util.matching.Regex` 문서**에 `Regex` 인스턴스를 만들고 사용하는 더 많은 예시가 있습니다.

---

## 2.9 Extracting Parts of a String That Match Patterns

### Problem

지정한 **정규식 패턴**과 일치하는 문자열의 하나 이상의 **부분**을 추출하고자 합니다.

### Solution

추출하려는 **regex 패턴**들을 정의하고, 그것들을 **정규식 group** 으로 추출할 수 있도록 **괄호로 감쌉니다**. 먼저 원하는 패턴을 정의해 봅시다.

```scala
val pattern = "([0-9]+) ([A-Za-z]+)".r
```

이것은 `pattern`을 **`scala.util.matching.Regex` 클래스의 인스턴스**로 만듭니다. 여기에 사용된 정규식은 **"하나 이상의 숫자 뒤에 공백이 하나 있고, 그 뒤에 하나 이상의 영숫자 문자가 온다"** 라고 읽을 수 있습니다.

다음은 대상 문자열에서 정규식 그룹을 추출하는 방법입니다.

```scala
val pattern(count, fruit) = "100 Bananas"
// count: String = 100
// fruit: String = Bananas
```

주석에 표시된 바와 같이, 이 코드는 주어진 문자열에서 **숫자 필드**와 **영숫자 필드**를 **두 개의 별도 변수 `count`와 `fruit`로 추출**합니다.

### Discussion

여기에 보인 문법은 `pattern`을 `val` 필드로 **두 번** 정의하는 것처럼 보이기 때문에 조금 특이하게 느껴질 수 있지만, 이 문법은 **`match` 표현식**을 사용하는 실제 예시에서 더 편리하고 가독성이 좋습니다.

Google 같은 검색 엔진용 코드를 작성하고 있다고 상상해 보겠습니다. 다양한 구문으로 영화를 검색할 수 있도록 허용하고 싶은데, 정말 편리하게 만들기 위해 사용자가 Colorado주 Boulder 근처의 영화 목록을 얻기 위해 아래와 같은 어떤 구문이라도 입력할 수 있게 하고 싶습니다.

```
"movies near 80301"
"movies 80301"
"80301 movies"
"movie: 80301"
"movies: 80301"
"movies near boulder, co"
"movies near boulder, colorado"
```

이 모든 구문을 사용할 수 있게 하는 한 가지 방법은, 그것들과 일치하는 일련의 **정규식 패턴**을 정의하는 것입니다. 표현식을 정의한 뒤, 사용자가 입력한 어떤 것이든 가능한 모든 표현식과 일치시켜 보면 됩니다.

작은 예시로, 다음 두 패턴만 허용하고 싶다고 상상해 봅시다.

```scala
// match "movies 80301"
val MoviesZipRE = "movies (\\d{5})".r

// match "movies near boulder, co"
val MoviesNearCityStateRE = "movies near ([a-z]+), ([a-z]{2})".r
```

이 패턴들은 다음과 같은 문자열들과 일치합니다.

```
"movies 80301"
"movies 99676"
"movies near boulder, co"
"movies near talkeetna, ak"
```

허용하고자 하는 정규식 패턴을 정의하신 뒤에는, `match` 표현식을 사용해서 사용자가 입력한 어떤 텍스트와도 매칭할 수 있습니다. 이 예시에서는 일치가 발생하면 **`Option[List[String]]`** 을 반환하는 **가상의 메서드 `getSearchResults`** 를 호출합니다.

```scala
val results = textUserTyped match
    case MoviesZipRE(zip) => getSearchResults(zip)
    case MoviesNearCityStateRE(city, state) => getSearchResults(city, state)
    case _ => None
```

보시는 것처럼, 이 문법 덕분에 `match` 표현식이 매우 **읽기 쉬워집니다**. 매칭하는 두 패턴 모두에 대해, **오버로딩된 버전의 `getSearchResults` 메서드**를 호출합니다. 첫 번째 case에는 `zip` 필드를 전달하고, 두 번째 case에는 `city`와 `state` 필드를 전달합니다.

이 기법을 사용할 때에는, 정규 표현식이 **사용자 입력 entire와 일치해야 한다**는 점에 유의하시기 바랍니다. 보여 드린 정규식 패턴들로는, 다음 문자열들은 줄 끝에 **공백**이 있기 때문에 매칭에 실패합니다.

```
"movies 80301 "
"movies near boulder, co "
```

이 문제는 입력 문자열을 **트리밍**하거나, 더 복잡한 정규식을 사용하여 해결하실 수 있으며, 실제 상황에서는 어차피 복잡한 정규식을 사용하게 될 것입니다.

짐작하시는 것처럼, 동일한 패턴 매칭 기법은 여러 다양한 상황에서 사용할 수 있습니다. 날짜와 시간 포맷, street addresses, 사람 이름 매칭 등 다른 많은 상황에 모두 적용할 수 있습니다.

### See Also

- `match` 표현식 예시에 대한 더 많은 내용은 **Recipe 4.6, "Using a Match Expression Like a switch Statement"** 를 참고하시길 바랍니다.
- `match` 표현식에서 `scala.util.matching.Regex`가 **extractor** 로 사용되는 것을 보실 수 있습니다. 추출자에 대한 내용은 **Recipe 7.8, "Implementing Pattern Matching with unapply"** 에서 다룹니다.

---

## 2.10 Accessing a Character in a String

### Problem

문자열의 특정 position에 있는 **문자**에 접근하고자 합니다.

### Solution

Scala의 **array notation** 을 사용하여 **인덱스 위치**로 문자에 접근하실 수 있습니다. 다만, 문자열의 끝을 넘어가지 않도록 주의하셔야 합니다.

```scala
"hello"(0)    // Char = h
"hello"(1)    // Char = e
"hello"(99)   // throws java.lang.StringIndexOutOfBoundsException
```

### Discussion

이 레시피를 포함한 이유는, Java에서는 이 목적으로 **`charAt` 메서드**를 사용하기 때문입니다. Scala에서도 이 메서드를 사용하실 수 있지만, 이 코드는 불필요하게 **verbose** 합니다.

```scala
"hello".charAt(0)    // Char = h
"hello".charAt(99)   // throws java.lang.StringIndexOutOfBoundsException
```

Scala에서는 Solution에 보여 드린 **배열 표기법**을 사용하는 것이 선호됩니다.

#### Tip: Array Notation Is Really a Method Call

Scala 배열 표기법은 보기 좋고 편리한 코드 작성 방법이지만, 내부 동작 원리를 알고 싶으시다면, 이 편리하고 사람이 읽기 쉬운 코드가

```scala
"hello"(1)    // 'e'
```

Scala 컴파일러에 의해 다음 코드로 번역된다는 점을 알아 두시면 흥미롭습니다.

```scala
"hello".apply(1)    // 'e'
```

자세한 내용은 이 작은 **syntactic sugar** 이 설명되어 있는 **Recipe 7.5, "Using apply Methods in Objects as Constructors"** 를 참고하시길 바랍니다.

---

## 2.11 Creating Your Own String Interpolator

### Problem

Scala와 함께 제공되는 `s`, `f`, `raw` 보간기처럼, **여러분 자신만의 string interpolator** 를 만들고자 합니다.

### Solution

자신만의 문자열 보간기를 만들기 위해서는, 프로그래머가 `foo"a b c"` 같은 코드를 작성하면, 그 코드가 **`StringContext` 클래스의 `foo` 메서드 호출**로 변환된다는 점을 알아 두셔야 합니다. 구체적으로, 다음과 같은 코드를 작성하시면,

```scala
val a = "a"
foo"a = $a"
```

아래와 같이 번역됩니다.

```scala
StringContext("a = ", "").foo(a)
```

따라서 커스텀 문자열 보간기를 만들려면, `foo`를 **`StringContext` 클래스의 Scala 3 extension method** 로 만드셔야 합니다. 알아 두셔야 할 몇 가지 추가적인 세부 사항이 있는데, 예시를 통해 보여 드리겠습니다.

아래처럼 문자열 안의 **모든 단어를 capitalize** **`caps`** 라는 이름의 문자열 보간기를 만들고자 한다고 가정해 봅시다.

```scala
caps"john c doe"   // "John C Doe"

val b = "b"
caps"a $b c"       // "A B C"
```

`caps`를 만들기 위해, `StringContext`의 **확장 메서드**로 정의합니다. 문자열 보간기를 만드는 것이기 때문에 메서드가 **`String`을 반환**해야 한다는 것을 알 수 있으므로, 다음과 같이 Solution을 작성하기 시작합니다.

```scala
extension (sc: StringContext)
    def caps(?): String = ???
```

미리 preinterpolation된 문자열은 **어떤 타입의 여러 표현식**도 담을 수 있기 때문에, `caps`는 **`Any` 타입의 varargs 매개변수**를 받도록 정의되어야 합니다. 그래서 다음과 같이 더 작성할 수 있습니다.

```scala
extension (sc: StringContext)
    def caps(args: Any*): String = ???
```

`caps`의 본문을 정의하기 위해, 알아야 할 다음 사항은 원래 문자열이 여러분에게 **두 가지 다른 변수 형태**로 전달된다는 것입니다.

- **`sc`** 는 `StringContext`의 인스턴스이며, 데이터를 **이터레이터**로 제공합니다.
- **`args.iterator`** 는 `Iterator[Any]`의 인스턴스입니다.

이 코드는 그 이터레이터들을 사용하여 **각 단어가 대문자로 된 `String`을 다시 구성하는 한 가지 방법**을 보여 드립니다.

```scala
extension (sc: StringContext)
    def caps(args: Any*): String =
        // [1] create variables for the iterators. note that for an
        // input string "a b c", `strings` will be "a b c" at this
        // point.
        val strings: Iterator[String] = sc.parts.iterator
        val expressions: Iterator[Any] = args.iterator

        // [2] populate a StringBuilder with the values in the iterators
        val sb = StringBuilder(strings.next.trim)
        while strings.hasNext do
            sb.append(expressions.next.toString)
            sb.append(strings.next)

        // [3] convert the StringBuilder back to a String,
        // then apply an algorithm to capitalize each word in
        // the string
        sb.toString
            .split(" ")
            .map(_.trim)
            .map(_.capitalize)
            .mkString(" ")
    end caps
end extension
```

코드에 대한 간단한 설명은 다음과 같습니다.

1. 먼저 두 이터레이터를 위한 변수들이 만들어집니다. `strings` 변수는 **입력 문자열의 모든 문자열 리터럴**을 담고 있고, `expressions`는 `$a` 변수 같은 **입력 문자열의 모든 표현식의 값**들을 담고 있습니다.
2. 다음으로, `while` 루프에서 두 이터레이터를 순회하면서 **`StringBuilder`에 값들을 채웁니다**. 이는 모든 문자열 리터럴과 표현식을 포함하여 문자열을 다시 하나로 조립하기 시작합니다.
3. 마지막으로, `StringBuilder`가 다시 `String`으로 변환되고, 일련의 **변환 함수들**이 호출되어 문자열의 각 단어를 대문자로 만듭니다.

이 메서드의 본문을 구현하는 다른 방법들도 있지만, 저는 `caps"a $b c ${d*e}"` 같은 보간기가 만들어질 때 **두 이터레이터로부터 문자열을 다시 구성해야 한다**는 관련 단계들을 명확하게 보여 드리기 위해 이 접근법을 사용합니다.

### Discussion

Solution을 이해하기 위해서는, **문자열 보간이 어떻게 동작하는지**, 즉 여러분이 IDE에 입력하는 Scala 코드가 어떻게 다른 Scala 코드로 변환되는지를 이해하시면 도움이 됩니다. 문자열 보간에서는 여러분이 만든 메서드의 consumer가 다음과 같은 코드를 작성합니다.

```
id"text0${expr1}text1 ... ${exprN}textN"
```

이 코드에서는,

- **`id`** 는 여러분의 문자열 보간 메서드 이름으로, 제 경우에는 `caps`입니다.
- **`textN`** 조각들은 preinterpolated 문자열 안의 **문자열 상수**들입니다.
- **`exprN`** 조각들은 입력 문자열에서 `$expr` 또는 `${expr}` 문법으로 작성된 **표현식**들입니다.

`id` 코드를 컴파일하면, 컴파일러는 이를 다음과 같은 코드로 번역합니다.

```scala
StringContext("text0", "text1", ..., "textN").id(expr1, ..., exprN)
```

보시는 것처럼, **문자열의 상수 부분들(문자열 리터럴들)** 은 추출되어 `StringContext` 생성자에 매개변수로 전달됩니다. `StringContext` 인스턴스의 `id` 메서드(제 예시에서는 `caps`)에는 초기 문자열에 포함된 **어떠한 표현식들**이 전달됩니다.

이것이 어떻게 동작하는지 **구체적인 예시**를 보여 드리기 위해, `yo`라는 이름의 보간기를 가지고 있고 이런 코드가 있다고 가정해 봅시다.

```scala
val b = "b"
val d = "d"
yo"a $b c $d"
```

컴파일 단계의 첫 번째 단계에서, 마지막 줄은 다음과 같이 변환됩니다.

```scala
val listOfFruits = StringContext("a ", " c ", "").yo(b, d)
```

이제 `yo` 메서드는 Solution에서 보여 드린 `caps` 메서드처럼 작성되어야 하며, 아래 두 이터레이터를 처리해야 합니다.

```
args.iterators contains:   "a ", " c ", ""    // String type
exprs.iterators contains:  b, d               // Any type
```

#### Tip: More Interpolators

더 많은 세부 정보와, 여러 줄 문자열 입력을

```scala
val fruits = Q"""
    apples
    bananas
    cherries
"""
```

다음과 같은 결과 리스트로 변환하는 저의 `Q` 보간기를 포함한 여러 보간기 예시는, 이 책의 제 GitHub 프로젝트를 참고하시기 바랍니다.

```scala
List("apples", "bananas", "cherries")
```

### See Also

- 이 레시피는 **extension methods** 를 사용하며, 이는 **Recipe 8.9, "Adding New Methods to Closed Classes with Extension Methods"** 에서 다룹니다.
- 문자열 보간에 관한 **공식 Scala 페이지**를 참고하시길 바랍니다.

---

## 2.12 Creating Random Strings

### Problem

`Random` 클래스의 **`nextString` 메서드**를 사용하여 랜덤 문자열을 생성하려고 시도할 때, 이상한 출력이나 `?` 문자가 많이 보입니다. 전형적인 문제는 다음과 같습니다.

```
scala> val r = scala.util.Random()
val r: scala.util.Random = scala.util.Random@360d41d0

scala> r.nextString(10)
res0: String = ??????????
```

### Solution

`nextString`에서 일어나는 일은, **Unicode 문자들**을 반환한다는 것인데, 이 문자들은 여러분의 시스템에서 제대로 표시되지 않을 수 있습니다. **영숫자 문자만**(문자 `A-Za-z`와 숫자 `0-9`) 생성하시려면 다음 접근법을 사용하십시오.

```scala
import scala.util.Random

// examples of two random alphanumeric strings
Random.alphanumeric.take(10).mkString    // 7qowB9jjPt
Random.alphanumeric.take(10).mkString    // a8WylvJKmX
```

`Random.alphanumeric`는 **`LazyList`** 를 반환하므로, 스트림으로부터 처음 10개의 문자를 가져오기 위해 **`take(10).mkString`** 를 사용합니다. `Random.alphanumeric.take(10)`만 호출하신다면, 다음과 같은 결과를 얻게 됩니다.

```scala
Random.alphanumeric.take(10)    // LazyList[Char] = LazyList(<not computed>)
```

`LazyList`는 **lazy** 되어 있습니다. 즉, 필요할 때에만 그 요소들을 계산하기 때문에, **문자열 결과를 강제로 얻으려면 `mkString` 같은 메서드를 호출**해야 합니다.

### Discussion

`Random` 클래스의 Scaladoc에 따르면, `alphanumeric`은 **"A-Z, a-z, 0-9에서 균등하게 선택된, pseudorandomly으로 선택된 영숫자 문자들의 `LazyList`"를 반환합니다.**

**더 넓은 범위의 문자**를 원하신다면, **`nextPrintableChar` 메서드**는 ASCII 범위 33부터 126까지의 값을 반환합니다. 여기에는 문자, 숫자, `!`, `-`, `+`, `]`, `>` 같은 문자를 포함해 키보드의 **거의 모든 단순한 문자**가 포함됩니다. 예를 들어, 다음은 **랜덤 길이의 출력 가능한 문자 시퀀스**를 생성하는 짧은 알고리즘입니다.

```scala
val r = scala.util.Random
val randomSeq = for i <- 0 to r.nextInt(10) yield r.nextPrintableChar
```

이 알고리즘으로 생성된 랜덤 시퀀스의 몇 가지 예시는 다음과 같습니다.

```
Vector(s, `, t, e, o, e, r, {, S)
Vector(X, i, M, ., H, x, h)
Vector(f, V, +, v)
```

다음 예시처럼, 이 시퀀스들은 **`mkString`** 을 사용해 `String`으로 변환할 수 있습니다.

```scala
randomSeq.mkString    // @Wvz#y#Rj|
randomSeq.mkString    //b0F:P&!WT$
```

ASCII 범위 33-126의 전체 문자 목록은 **asciitable.com** 이나 유사한 웹사이트를 참고하시길 바랍니다.

#### Lazy methods

**Recipe 20.1, "Getting Started with Spark"** 에서 설명되는 바와 같이, Apache Spark에서는 컬렉션 메서드를 **transformation 메서드** 혹은 **action 메서드**로 생각할 수 있습니다.

- **변환 메서드**는 컬렉션의 요소들을 **변환**합니다. `List`, `Vector`, `LazyList` 같은 **불변 클래스**에서는, 이 메서드들이 기존 요소들을 변환하여 **새로운 컬렉션**을 만듭니다. Spark와 마찬가지로, Scala의 `LazyList`를 사용할 때에는, 이 메서드들이 **lazily evaluated** 됩니다(이를 **lazy** 또는 **nonstrict**라고도 합니다). `map`, `filter`, `take` 등 많은 메서드들이 변환 메서드로 간주됩니다.
- **액션 메서드**는 본질적으로 **결과를 강제로 계산**하는 메서드들입니다. 이들은 **"지금 결과를 원해"** 라고 말하는 방식입니다. `foreach`와 `mkString` 같은 메서드들이 액션 메서드로 간주될 수 있습니다.

변환 메서드에 대한 더 많은 논의와 예시는 **Recipe 11.1, "Choosing a Collections Class"** 를 참고하시길 바랍니다.

### See Also

- 제 블로그 글 **"How to Create Random Strings in Scala (A Few Different Examples)"** 에서는 알파 및 영숫자 문자열을 포함한 랜덤 문자열을 생성하는 **7가지 다른 방법**을 보여 드립니다.
- **"Scala: A Function to Generate a Random-Length String with Blank Spaces"** 에서는 **공백이 포함된 랜덤 길이의 랜덤 문자열**을 생성하는 방법을 보여 드립니다.
