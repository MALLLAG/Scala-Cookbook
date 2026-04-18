# Chapter 4. Control Structures

## 챕터 개요

이름에서 알 수 있듯이, **control structures** 는 프로그래머에게 프로그램의 흐름을 제어할 수 있는 수단을 제공합니다. 이는 decision making과 looping 작업을 다룰 수 있게 해 주는 프로그래밍 언어의 근본적인 특성입니다.

저자는 2010년에 Scala를 배우기 전까지는 `if/then` 문 같은 제어 구조가 프로그래밍 언어의 지루한 부분이라고 생각했지만, 이는 단지 다른 방법이 있다는 사실을 몰랐기 때문이었다고 회고합니다. 오늘날에는 제어 구조가 프로그래밍 언어를 정의하는 **defining feature** 기능이라는 점을 이해하게 되셨다고 합니다.

Scala의 제어 구조는 다음과 같습니다.

- `for` 루프와 `for` expressions
- `if/then/else if` 표현식
- `match` 표현식 (패턴 매칭)
- `try/catch/finally` 블록
- `while` 루프

본 챕터에서는 우선 이들을 간단히 소개한 뒤, 각 레시피에서 해당 기능을 사용하는 세부 사항을 보여 드립니다.

### for Loops and for Expressions

가장 기본적인 사용 예로서, `for` 루프는 collection을 순회하면서 각 요소에 대해 어떤 동작을 수행할 수 있게 해 줍니다.

```scala
for i <- List(1, 2, 3) do println(i)
```

그러나 이는 가장 단순한 사용 사례입니다. `for` 루프에는 **guards** 라 불리는 임베디드 `if` 문도 포함할 수 있습니다.

```scala
for
    i <- 1 to 10
    if i > 3
    if i < 6
do
    println(i)
```

`yield` 키워드와 함께 사용하면, `for` 루프는 **결과값을 yield하는 반복문**, 즉 `for` 표현식이 됩니다.

```scala
val listOfInts = for
    i <- 1 to 10
    if i > 3
    if i < 6
yield
    i * 10
```

이 루프가 실행되고 나면 `listOfInts`는 `Vector(40, 50)`이 됩니다. 루프 내부의 가드들이 4와 5를 제외한 모든 값을 걸러 내고, 이 값들이 `yield` 블록 안에서 10으로 곱해지기 때문입니다.

`for` 루프와 표현식에 대한 더 많은 자세한 내용은 본 챕터 초반의 레시피들에서 다룹니다.

### if/then/else-if Expressions

`for` 루프와 표현식이 컬렉션을 순회하는 데 사용된다면, `if/then/else` 표현식은 branching 결정을 할 수 있는 방법을 제공합니다. Scala 3에서 선호되는 문법이 변경되었으며, 이제는 다음과 같은 형태가 됩니다.

```scala
val absValue = if a < 0 then -a else a

def compare(a: Int, b: Int): Int =
    if a < b then
        -1
    else if a == b then
        0
    else
        1
end compare
```

위 두 예시에서 보이듯, `if` 표현식은 진정한 의미에서 **expression** 이며 값을 반환합니다. (표현식에 대해서는 Recipe 4.5에서 다룹니다.)

### match Expressions and Pattern Matching

다음으로 `match` 표현식과 **pattern matching** 은 Scala의 defining feature 기능이며, 이들의 역량을 시연하는 데 본 챕터의 대부분을 할애합니다. `if` 표현식과 마찬가지로 `match` 표현식 역시 값을 반환하므로 메서드의 body 으로도 사용할 수 있습니다. 예시로, 이 메서드는 Perl 프로그래밍 언어의 `true`와 `false` 정의와 유사합니다.

```scala
def isTrue(a: Matchable): Boolean = a match
    case false | 0 | "" => false
    case _ => true
```

이 코드에서 `isTrue`가 `0` 혹은 빈 문자열을 받으면 `false`를 반환하고, 그 외의 경우에는 `true`를 반환합니다. 본 챕터의 10개 레시피가 `match` 표현식의 기능들을 상세히 다룹니다.

### try/catch/finally Blocks

Scala의 `try/catch/finally` 블록은 Java와 유사하지만, 주된 차이점은 `catch` 블록이 `match` 표현식과 동일한 방식으로 작성된다는 점입니다.

```scala
try
    // some exception-throwing code here
catch
    case e1: Exception1Type => // handle that exception
    case e2: Exception2Type => // handle that exception
finally
    // close your resources and do anything else necessary here
```

`if`와 `match`처럼, `try` 또한 값을 반환하는 표현식이므로 다음 코드처럼 `String`을 `Int`로 변환하는 코드를 작성할 수 있습니다.

```scala
def toInt(s: String): Option[Int] =
    try
        Some(s.toInt)
    catch
        case e: NumberFormatException => None
```

다음 예시는 `toInt`가 어떻게 동작하는지를 보여 줍니다.

```scala
toInt("1")    // Option[Int] = Some(1)
toInt("Yo")   // Option[Int] = None
```

`try/catch` 블록에 대한 더 자세한 내용은 Recipe 4.16에서 다룹니다.

### while Loops

`while` 루프에 이르러서는 Scala에서 거의 사용되지 않음을 보시게 됩니다. 그 이유는 `while` 루프가 주로 가변 변수를 갱신하거나 `println`으로 출력하는 등 side effect를 위해 사용되기 때문이며, 이러한 작업은 `for` 루프와 컬렉션의 `foreach` 메서드로도 수행할 수 있기 때문입니다. 그러나 `while` 루프를 사용해야 할 일이 있다면, 그 문법은 다음과 같습니다.

```scala
while
    i < 10
do
    println(i)
    i += 1
```

`while` 루프는 Recipe 4.1에서 간단히 다룹니다.

마지막으로, 여러 Scala 기능들의 조합 덕분에 **여러분만의 제어 구조**를 직접 만드실 수 있으며, 이러한 역량은 Recipe 4.17에서 다룹니다.

### Control Structures as a Defining Feature of Programming Languages

저자는 2020년 말에 Scala 공식 문서 사이트의 *Scala 3 Book* 집필에 공동 저자로 참여하는 행운이 있었으며, 다음 세 개 챕터를 포함합니다.

- "Scala for Java Developers"
- "Scala for Java Script Developers"
- "Scala for Python Developers"

앞서 제어 구조를 "프로그래밍 언어의 defining feature"이라고 말했을 때, 저자가 의미했던 바 중 하나는 저 챕터들을 쓰고 난 뒤 본 챕터의 기능이 지닌 힘과, Scala가 다른 프로그래밍 언어들과 비교해 얼마나 **consistency** 이 있는지를 깨달았다는 점입니다. 이 일관성이야말로 Scala를 즐겁게 쓸 수 있는 요인 중 하나입니다.

---

## 4.1 Looping over Data Structures with for

### Problem

컬렉션 안의 요소들을 전통적인 `for` 루프 방식으로 순회하고자 합니다.

### Solution

Scala 컬렉션을 순회하는 방법은 여러 가지가 있습니다. `for` 루프, `while` 루프, 그리고 `foreach`, `map`, `flatMap` 같은 컬렉션 메서드들이 대표적입니다. 이 해결 방법은 주로 `for` 루프에 초점을 맞춥니다.

간단한 리스트가 주어진다면 다음과 같이 할 수 있습니다.

```scala
val fruits = List("apple", "banana", "orange")
```

리스트의 요소들을 순회하면서 출력하실 수 있습니다.

```scala
scala> for f <- fruits do println(f)
apple
banana
orange
```

이러한 접근은 `List`, `Seq`, `Vector`, `Array`, `ArrayBuffer` 등 모든 시퀀스에서 동일하게 동작합니다.

알고리즘이 여러 줄의 코드를 필요로 할 때에는 동일한 `for` 루프 문법을 사용하시면 되며, 블록 내부에서 필요한 작업을 수행하시면 됩니다.

```scala
scala> for f <- fruits do
     |     // imagine this requires multiple lines
     |     val s = f.toUpperCase
     |     println(s)
APPLE
BANANA
ORANGE
```

#### for loop counters

`for` 루프 내부에서 counter에 접근해야 한다면, 다음과 같은 접근 방식 중 하나를 사용하실 수 있습니다. 첫 번째로, 카운터를 통해 시퀀스의 요소에 다음처럼 접근할 수 있습니다.

```scala
for i <- 0 until fruits.length do
    println(s"$i is ${fruits(i)}")
```

이 루프는 다음과 같은 출력을 냅니다.

```
0 is apple
1 is banana
2 is orange
```

시퀀스 요소를 index로 접근할 일은 드물지만, 필요하다면 이 방식이 한 가지 방법이 됩니다. Scala 컬렉션은 또한 루프 카운터를 생성할 수 있는 `zipWithIndex` 메서드도 제공합니다.

```scala
for (fruit, index) <- fruits.zipWithIndex do
    println(s"$index is $fruit")
```

출력은 다음과 같습니다.

```
0 is apple
1 is banana
2 is orange
```

#### Generators

관련하여, 다음 예시는 `Range`를 사용해 루프를 세 번 실행하는 방법을 보여 줍니다.

```scala
scala> for i <- 1 to 3 do println(i)
1
2
3
```

루프의 `1 to 3` 부분은 REPL에서 확인할 수 있듯 `Range`를 만들어 냅니다.

```scala
scala> 1 to 3
res0: scala.collection.immutable.Range.Inclusive = Range(1, 2, 3)
```

이렇게 `Range`를 사용하는 것을 **generator** 를 사용한다고 표현합니다. Recipe 4.2에서는 이 기법을 활용해 여러 개의 루프 카운터를 생성하는 방법을 시연합니다.

#### Looping over a Map

`Map`의 키와 값을 순회할 때에는, 저자가 가장 간결하고 읽기 쉽다고 생각하는 `for` 루프가 다음과 같은 형태입니다.

```scala
val names = Map(
    "firstName" -> "Robert",
    "lastName" -> "Goren"
)

for (k,v) <- names do println(s"key: $k, value: $v")
```

REPL에서 출력은 다음과 같습니다.

```
scala> for (k,v) <- names do println(s"key: $k, value: $v")
key: firstName, value: Robert
key: lastName, value: Goren
```

### Discussion

저자는 functional programming 스타일로 전환한 뒤로는 수년간 `while` 루프를 사용하지 않았지만, 다음 REPL 예시는 그 동작 방식을 보여 줍니다.

```scala
scala> var i = 0
i: Int = 0

scala> while i < 3 do
     |     println(i)
     |     i += 1
0
1
2
```

`while` 루프는 일반적으로 `i` 같은 mutable variable를 갱신하거나 외부로 출력하는 것과 같은 부수 효과를 위해 사용됩니다. 저자는 순수 함수형 프로그래밍—가변 상태가 없는—에 가까워지면서 더는 이를 사용할 필요가 없어졌다고 합니다.

그렇지만 객체 지향 프로그래밍 스타일에서 코드를 작성하실 때에는 `while` 루프가 여전히 자주 쓰이며, 위 예시가 그 문법을 보여 줍니다. `while` 루프도 다음과 같이 여러 줄로 작성할 수 있습니다.

```scala
while
    i < 10
do
    println(i)
    i += 1
```

#### Collection methods like foreach

Scala는 어찌 보면 Perl의 슬로건인 "There's more than one way to do it"를 떠올리게 하며, 컬렉션 순회는 그 훌륭한 예시 중 하나입니다. 컬렉션에서 사용할 수 있는 풍부한 메서드들을 고려하면, `for` 루프가 특정 문제에 대한 최선의 접근이 아닐 수도 있다는 점을 주목하는 것이 중요합니다. `foreach`, `map`, `flatMap`, `collect`, `reduce` 등의 메서드들이 `for` 루프 없이도 문제를 해결해 줄 수 있습니다.

예를 들어 컬렉션을 사용하실 때 컬렉션의 `foreach` 메서드를 호출해 각 요소를 순회하실 수 있습니다.

```scala
scala> fruits.foreach(println)
apple
banana
orange
```

컬렉션의 각 요소에 대해 실행하고 싶은 알고리즘이 있다면, 익명 함수를 `foreach`에 전달하시면 됩니다.

```scala
scala> fruits.foreach(e => println(e.toUpperCase))
APPLE
BANANA
ORANGE
```

`for` 루프와 마찬가지로, 알고리즘이 여러 줄을 필요로 한다면 블록 내부에서 작업을 수행하시면 됩니다.

```scala
scala> fruits.foreach { e =>
     |     val s = e.toUpperCase
     |     println(s)
     | }
APPLE
BANANA
ORANGE
```

### See Also

- `zipWithIndex` 사용 예시를 더 보시려면 Recipe 13.4, "Using zipWithIndex or zip to Create Loop Counters"를 참고하시길 바랍니다.
- `Map` 순회 예시를 더 보시려면 Recipe 14.9, "Traversing a Map"을 참고하시길 바랍니다.

`for` 루프의 동작 원리 뒤에 있는 이론은 매우 흥미로우며, 이를 알고 계시면 큰 도움이 됩니다. 저자는 다음 블로그 글들에서 자세히 다룹니다.

- "How to Make a Custom Sequence Work as a for Loop Generator"
- "How to Enable Filtering in a for Expression"
- "How to Enable the Use of Multiple Generators in a for Expression"

---

## 4.2 Using for Loops with Multiple Counters

### Problem

multidimensional array를 순회하실 때처럼, 여러 개의 카운터를 가진 루프를 만들고자 합니다.

### Solution

다음과 같이 두 개의 카운터를 가진 `for` 루프를 만드실 수 있습니다.

```scala
scala> for i <- 1 to 2; j <- 1 to 2 do println(s"i = $i, j = $j")
i = 1, j = 1
i = 1, j = 2
i = 2, j = 1
i = 2, j = 2
```

`i`가 1로 설정되고 `j`의 요소들을 모두 돌고 나면, 그 뒤에 `i`가 2로 설정되어 그 과정이 반복됩니다.

이러한 방식은 작은 예시에서는 잘 동작하지만, 코드가 커질수록 다음 스타일이 선호됩니다.

```scala
for
    i <- 1 to 3
    j <- 1 to 5
    k <- 1 to 10 by 2
do
    println(s"i = $i, j = $j, k = $k")
```

이 접근은 다차원 배열을 순회할 때 유용합니다. 다음과 같이 작은 2차원 배열을 생성하고 채운다고 가정해 봅시다.

```scala
val a = Array.ofDim[Int](2,2)
a(0)(0) = 0
a(0)(1) = 1
a(1)(0) = 2
a(1)(1) = 3
```

그러면 모든 배열 요소를 다음과 같이 출력하실 수 있습니다.

```scala
scala> for
     |     i <- 0 to 1
     |     j <- 0 to 1
     | do
     |     println(s"($i)($j) = ${a(i)(j)}")
(0)(0) = 0
(0)(1) = 1
(1)(0) = 2
(1)(1) = 3
```

### Discussion

Recipe 15.2, "Creating Ranges"에서 보이듯이 `1 to 5` 문법은 `Range`를 생성합니다.

```scala
scala> 1 to 5
val res0: scala.collection.immutable.Range.Inclusive = Range 1 to 5
```

`Range`는 많은 용도로 유용하며, `for` 루프에서 `<-` 기호와 함께 생성된 `Range`를 **generator** 라고 부릅니다. 보시는 것처럼 하나의 루프 안에서 여러 개의 generator를 쉽게 사용할 수 있습니다.

### See Also

- 제어 구조 포맷팅에 대한 Scala 스타일 가이드

---

## 4.3 Using a for Loop with Embedded if Statements (Guards)

### Problem

`for` 루프에 하나 이상의 conditional clause을 추가하고자 합니다. 일반적으로 컬렉션의 일부 요소는 걸러내고 나머지 요소에 대해 작업을 수행하기 위함입니다.

### Solution

generator 뒤에 하나 이상의 `if` 문을 다음과 같이 추가하시면 됩니다.

```scala
for
    i <- 1 to 10
    if i % 2 == 0
do
    print(s"$i ")

// output: 2 4 6 8 10
```

이러한 `if` 문은 **filter**, **filter expression** 또는 **guard** 라고 부릅니다. 필요한 만큼 가드를 여러 개 사용하실 수 있습니다. 다음 루프는 숫자 4를 출력하는 다소 복잡한 방법을 보여 줍니다.

```scala
for
    i <- 1 to 10
    if i > 3
    if i < 6
    if i % 2 == 0
do
    println(i)
```

### Discussion

더 오래된 스타일로 `if` 표현식을 가진 `for` 루프를 쓰는 것도 여전히 가능합니다. 예를 들어 다음 코드가 주어졌을 때,

```scala
import java.io.File
val dir = File(".")
val files: Array[java.io.File] = dir.listFiles()
```

이론적으로 C나 Java를 연상시키는 스타일로 `for` 루프를 쓰실 수도 있습니다.

```scala
// a C/Java style of writing a 'for' loop
for (file <- files) {
    if (file.isFile && file.getName.endsWith(".scala")) {
        println(s"Scala file: $file")
    }
}
```

하지만 Scala의 `for` 루프 문법에 익숙해지시면, looping and filtering 관심사를 business logic과 분리해 주는 덕분에 코드가 더 읽기 쉬워진다는 점을 느끼시게 될 것입니다.

```scala
for
    // loop and filter
    file <- files
    if file.isFile
    if file.getName.endsWith(".scala")
do
    // as much business logic here as needed
    println(s"Scala file: $file")
```

가드는 일반적으로 컬렉션을 필터링하기 위한 목적으로 사용되므로, 필요에 따라 `for` 루프 대신 컬렉션에서 사용 가능한 많은 필터링 메서드들—`filter`, `take`, `drop` 등—중 하나를 사용하고 싶을 수 있습니다. 이러한 메서드의 예시는 Chapter 11에서 확인하실 수 있습니다.

---

## 4.4 Creating a New Collection from an Existing Collection with for/yield

### Problem

기존 컬렉션의 각 요소에 알고리즘(필요하다면 하나 이상의 가드를 포함하는)을 적용하여 새 컬렉션을 만들고자 합니다.

### Solution

`for` 루프와 함께 `yield` 문을 사용하시면 기존 컬렉션으로부터 새 컬렉션을 만들 수 있습니다. 예를 들어 소문자 문자열 배열이 주어졌을 때,

```scala
scala> val names = List("chris", "ed", "maurice")
val names: List[String] = List(chris, ed, maurice)
```

capitalized된 새 문자열 배열을 `for` 루프와 간단한 알고리즘을 결합해 만드실 수 있습니다.

```scala
scala> val capNames = for name <- names yield name.capitalize
val capNames: List[String] = List(Chris, Ed, Maurice)
```

`yield` 문과 함께 사용하는 `for` 루프를 **for-comprehension** 이라고 합니다.

알고리즘이 여러 줄의 코드를 필요로 한다면, `yield` 키워드 뒤에 블록을 써서 그 안에 작업을 수행하시며, 결과 변수의 타입을 명시적으로 지정하셔도 좋고 생략하셔도 좋습니다.

```scala
// [1] declare the type of `lengths`
val lengths: List[Int] = for name <- names yield
    // imagine that this body requires multiple lines of code
    name.length

// [2] don't declare the type of `lengths`
val lengths = for name <- names yield
    // imagine that this body requires multiple lines of code
    name.length
```

두 접근 모두 같은 결과를 냅니다.

```scala
List[Int] = List(5, 2, 7)
```

`for` 컴프리헨션(또는 `for` 표현식)의 두 부분 모두 필요한 만큼 복잡해질 수 있습니다. 더 큰 예시는 다음과 같습니다.

```scala
val xs = List(1,2,3)
val ys = List(4,5,6)
val zs = List(7,8,9)

val a = for
    x <- xs
    if x > 2
    y <- ys
    z <- zs
    if y * z < 45
yield
    val b = x + y
    val c = b * z
    c
```

이 `for` 컴프리헨션은 다음과 같은 결과를 냅니다.

```
a: List[Int] = List(49, 56, 63, 56, 64, 63)
```

`for` 컴프리헨션은 메서드의 완전한 본문으로도 사용될 수 있습니다.

```scala
def between3and10(xs: List[Int]): List[Int] =
    for
        x <- xs
        if x >= 3
        if x <= 10
    yield x

between3and10(List(1,3,7,11))   // List(3, 7)
```

### Discussion

`for` 루프와 `yield`를 처음 사용하시는 경우, 루프를 이렇게 생각해 보시면 도움이 될 수 있습니다.

1. 실행이 시작되면 `for/yield` 루프는 입력 컬렉션과 동일한 타입의 새로운 빈 컬렉션을 즉시 만듭니다. 예를 들어 입력 타입이 `Vector`라면 출력 타입 또한 `Vector`가 됩니다. 이 새 컬렉션을 empty bucket처럼 생각하실 수 있습니다.
2. `for` 루프의 각 반복 시점에, 입력 컬렉션의 현재 요소로부터 새 출력 요소가 만들어질 수 있습니다. 출력 요소가 만들어지면 양동이에 담깁니다.
3. 루프 실행이 끝나면 양동이의 전체 내용물이 반환됩니다.

이는 단순화된 설명이지만, 저자는 이 과정을 설명할 때 이 설명이 유용하다고 느낍니다.

가드 없이 `for` 표현식을 작성하는 것은 컬렉션에서 `map` 메서드를 호출하는 것과 같다는 점에 유의하세요. 예를 들어 다음 `for` 컴프리헨션은 `fruits` 컬렉션의 모든 문자열을 대문자로 변환합니다.

```scala
scala> val namesUpper = for n <- names yield n.toUpperCase
val namesUpper: List[String] = List(CHRIS, ED, MAURICE)
```

컬렉션에 `map` 메서드를 호출해도 같은 결과를 냅니다.

```scala
scala> val namesUpper = names.map(_.toUpperCase)
val namesUpper: List[String] = List(CHRIS, ED, MAURICE)
```

저자가 처음 Scala를 배울 때에는 모든 코드를 `for/yield` 표현식으로 작성했으나, 어느 날 가드 없는 `for/yield`가 `map`과 같다는 사실을 깨달았다고 합니다.

### See Also

- `for` 컴프리헨션과 `map`의 비교는 Recipe 13.5, "Transforming One Collection to Another with map"에서 더 자세히 보여 드립니다.

---

## 4.5 Using the if Construct Like a Ternary Operator

### Problem

Java의 특별한 **ternary operator** 문법에 익숙하십니다.

```java
int absValue = (a < 0) ? -a : a;
```

그리고 Scala의 대응물이 무엇인지 알고 싶어하십니다.

### Solution

이는 약간 함정 같은 문제입니다. Java와 달리 Scala에는 별도의 삼항 연산자가 없기 때문입니다. 그냥 `if/else/then` 표현식을 사용하시면 됩니다.

```scala
val a = 1
val absValue = if a < 0 then -a else a
```

`if` 표현식은 값을 반환하기 때문에 `print` 문 안에 삽입하실 수 있습니다.

```scala
println(if a == 0 then "a" else "b")
```

`hashCode` 메서드의 다음 부분과 같은 다른 표현식에 사용할 수도 있습니다.

```scala
hash = hash * prime + (if name == null then 0 else name.hashCode)
```

`if/else` 표현식이 값을 반환한다는 사실은 다음과 같이 간결한 메서드를 작성할 수 있게 해 줍니다.

```scala
// Version 1: one-line style
def abs(x: Int) = if x >= 0 then x else -x
def max(a: Int, b: Int) = if a > b then a else b

// Version 2: the method body on a separate line, if you prefer
def abs(x: Int) =
    if x >= 0 then x else -x

def max(a: Int, b: Int) =
    if a > b then a else b
```

### Discussion

Java의 "Equality, Relational, and Conditional Operators" 문서 페이지에서는 Java의 조건 연산자 `?:`을 일컬어 "세 개의 피연산자를 사용하기 때문에 **삼항 연산자** 라고 불립니다."라고 설명합니다.

Java에는 별도의 문법이 필요한데, 이는 Java의 `if/else` 구문이 **statement** 이기 때문입니다. 반환 값이 없고 가변 필드를 갱신하는 등의 부수 효과를 위해서만 사용됩니다. 반대로 Scala의 `if/else/then`은 진정한 의미에서 **expression** 이므로, 특별한 연산자가 필요 없습니다. 구문과 표현식에 대한 자세한 내용은 Recipe 24.3, "Writing Expressions (Instead of Statements)"를 참고하시길 바랍니다.

#### Arity

> **Arity**
>
> 단어 *ternary*(삼항의)는 함수의 **arity** 와 관련이 있습니다. 위키피디아의 "Arity" 페이지에 따르면 "논리학, 수학, 컴퓨터 과학에서 함수나 연산의 항수는 해당 함수나 연산이 취하는 argument 혹은 operand의 수를 의미합니다." **unary** 연산자는 피연산자를 한 개, **binary** 연산자는 두 개, **ternary** 연산자는 세 개를 취합니다.

---

## 4.6 Using a Match Expression Like a switch Statement

### Problem

the days in a week, the months in a year, 또는 정수가 결과에 매핑되는 다른 상황들처럼, Java의 간단한 정수 기반 `switch` 문 같은 것을 만들고 싶은 상황이 있습니다.

### Solution

Scala `match` 표현식을 간단한 정수 기반 `switch` 문처럼 사용하시려면, 다음과 같은 접근을 쓰시면 됩니다.

```scala
import scala.annotation.switch

// `i` is an integer
(i: @switch) match
    case 0 => println("Sunday")
    case 1 => println("Monday")
    case 2 => println("Tuesday")
    case 3 => println("Wednesday")
    case 4 => println("Thursday")
    case 5 => println("Friday")
    case 6 => println("Saturday")
    // catch the default with a variable so you can print it
    case whoa  => println(s"Unexpected case: ${whoa.toString}")
```

위 예시는 매칭에 기반하여 부수 효과(`println`)를 만들어내는 방법을 보여 줍니다. 더 함수형 접근은 `match` 표현식에서 값을 반환하는 것입니다.

```scala
import scala.annotation.switch

// `i` is an integer
val day = (i: @switch) match
    case 0 => "Sunday"
    case 1 => "Monday"
    case 2 => "Tuesday"
    case 3 => "Wednesday"
    case 4 => "Thursday"
    case 5 => "Friday"
    case 6 => "Saturday"
    case _ => "invalid day"   // the default, catch-all
```

#### The @switch annotation

위와 같이 간단한 `match` 표현식을 작성하실 때에는 보시는 것처럼 `@switch` 애노테이션을 사용하시길 권장합니다. 이 애노테이션은 `match`가 `tableswitch`나 `lookupswitch`로 컴파일될 수 없는 경우 컴파일 타임에 경고를 제공합니다. `match` 표현식을 `tableswitch`나 `lookupswitch`로 컴파일하는 것이 성능상 좋은 이유는, decision tree가 아닌 **branch table** 이 생성되기 때문입니다. 표현식에 값이 주어지면 결정 트리를 거치는 대신 결과로 곧바로 점프할 수 있습니다.

Scala `@switch` 애노테이션 문서에서는 다음과 같이 설명합니다.

> 만약 이 애노테이션이 존재한다면, 컴파일러는 `match`가 `tableswitch` 또는 `lookupswitch`로 컴파일되었는지를 확인하고, 대신 일련의 조건 표현식으로 컴파일된다면 에러를 냅니다.

`@switch` 애노테이션의 효과는 간단한 예시로 시연됩니다. 먼저 다음 코드를 *SwitchDemo.scala* 파일에 넣으세요.

```scala
// Version 1 - compiles to a tableswitch
import scala.annotation.switch

class SwitchDemo:
    val i = 1
    val x = (i: @switch) match
        case 1 => "One"
        case 2 => "Two"
        case 3 => "Three"
        case _ => "Other"
```

그런 다음 평소처럼 코드를 컴파일합니다.

```
$ scalac SwitchDemo.scala
```

이 클래스를 컴파일하면 경고 없이 *SwitchDemo.class* 출력 파일이 생성됩니다. 다음으로 `javap` 명령으로 그 파일을 디스어셈블해 보세요.

```
$ javap -c SwitchDemo
```

이 명령의 출력은 다음처럼 `tableswitch`를 보여 줍니다.

```
16: tableswitch   { // 1 to 3
             1: 44
             2: 52
             3: 60
       default: 68
  }
```

이것은 Scala가 여러분의 `match` 표현식을 `tableswitch`로 최적화할 수 있었다는 것을 보여 줍니다. (이는 좋은 현상입니다.)

다음으로 코드를 조금 수정해서, 정수 리터럴 1을 값으로 대체해 봅시다.

```scala
import scala.annotation.switch

// Version 2 - leads to a compiler warning
class SwitchDemo:
    val i = 1
    val one = 1            // added
    val x = (i: @switch) match
        case one  => "One"    // replaced the '1'
        case 2    => "Two"
        case 3    => "Three"
        case _    => "Other"
```

다시 `scalac`으로 코드를 컴파일하면, 곧바로 경고 메시지를 보실 수 있습니다.

```
$ scalac SwitchDemo.scala
SwitchDemo.scala:7: warning: could not emit switch for @switch annotated match
    val x = (i: @switch) match {
                         ^
one warning found
```

이 경고 메시지는 `match` 표현식에 대해 `tableswitch` 또는 `lookupswitch`가 생성될 수 없다는 의미입니다. 생성된 *SwitchDemo.class* 파일에 `javap` 명령을 실행해서 이를 확인하실 수 있습니다. 출력을 보시면 이전 예시에서 보였던 `tableswitch`가 사라져 있습니다.

Joshua Suereth의 저서 *Scala in Depth* (Manning)에서는 Scala가 `tableswitch` 최적화를 적용하려면 다음 조건들이 참이어야 한다고 말합니다.

- 매칭되는 값은 알려진 정수여야 합니다.
- 매칭되는 표현식은 "simple"해야 합니다. 타입 검사, `if` 문, 또는 extractor를 포함할 수 없습니다.
- 표현식은 컴파일 타임에 값이 가용해야 합니다.
- `case` 문이 두 개보다 많아야 합니다.

### Discussion

Solution의 예시는 기본 "catch all" 케이스를 처리하는 두 가지 방법을 보여 줍니다. 첫째, 기본 매치의 **값** 에 관심이 없다면 `_` 와일드카드로 잡으실 수 있습니다.

```scala
case _ => println("Got a default match")
```

반대로 기본 매치에 잡힌 값이 궁금하시다면, 거기에 변수 이름을 지정하시면 됩니다. 그 변수를 표현식 오른쪽에서 사용하실 수 있습니다.

```scala
case default => println(default)
```

`default` 같은 이름을 사용하는 것이 가장 합리적일 때가 많지만, 어떤 합법적인 변수 이름도 변수에 쓰실 수 있습니다.

```scala
case oops => println(oops)
```

기본 케이스를 처리하지 않으면 `MatchError`가 발생할 수 있다는 점을 알아 두시는 것이 중요합니다. 다음 `match` 표현식이 주어졌을 때,

```scala
i match
    case 0 => println("0 received")
    case 1 => println("1 is good, too")
```

`i`가 0 이나 1 이외의 값이면 `MatchError`가 발생합니다.

```
scala.MatchError: 42 (of class java.lang.Integer)
  at .<init>(<console>:9)
  at .<clinit>(<console>)
    much more error output here ...
```

따라서 partial function를 의도적으로 작성하는 것이 아니라면, 기본 케이스를 처리하시는 게 좋습니다.

#### Do you really need a match expression?

이러한 예시에 대해 `match` 표현식이 필요하지 않을 수도 있다는 점에 유의하세요. 예를 들어 어떤 값을 다른 값에 매핑하실 때에는 `Map`을 사용하는 편이 더 나을 수 있습니다.

```scala
val days = Map(
    0 -> "Sunday",
    1 -> "Monday",
    2 -> "Tuesday",
    3 -> "Wednesday",
    4 -> "Thursday",
    5 -> "Friday",
    6 -> "Saturday"
)

println(days(0))   // prints "Sunday"
```

### See Also

- JVM switch의 동작 방식에 관한 더 자세한 내용은 JVM spec on compiling switches를 참고하시길 바랍니다.
- `lookupswitch`와 `tableswitch`의 차이에 관한 Stack Overflow 페이지에서는 "차이점은 `lookupswitch`가 키와 레이블을 가지는 테이블을 사용하는 반면, `tableswitch`는 레이블만 있는 테이블을 사용한다는 점입니다."라고 설명합니다. 자세한 내용은 Java Virtual Machine (JVM) specification의 "Compiling Switches" 섹션을 참고하시길 바랍니다.
- 부분 함수에 관한 자세한 내용은 Recipe 10.7, "Creating Partial Functions"를 참고하시길 바랍니다.

---

## 4.7 Matching Multiple Conditions with One Case Statement

### Problem

여러 개의 `match` 조건이 동일한 비즈니스 로직을 실행해야 하는 상황이 있을 때, 각 케이스마다 비즈니스 로직을 반복하는 대신 하나의 복사본을 매칭 조건 여러 개에 사용하고자 합니다.

### Solution

같은 비즈니스 로직을 호출하는 매치 조건들을 `|` (파이프, pipe) 문자로 구분해 한 줄에 두시면 됩니다.

```scala
// `i` is an Int
i match
    case 1 | 3 | 5 | 7 | 9 => println("odd")
    case 2 | 4 | 6 | 8 | 10 => println("even")
    case _ => println("too big")
```

같은 문법은 문자열이나 다른 타입에도 적용됩니다. 다음은 `String` 매칭 기반의 예시입니다.

```scala
val cmd = "stop"
cmd match
    case "start" | "go" => println("starting")
    case "stop" | "quit" | "exit" => println("stopping")
    case _ => println("doing nothing")
```

이 예시는 각 `case` 문에서 여러 객체를 매칭하는 방법을 보여 줍니다.

```scala
enum Command:
    case Start, Go, Stop, Whoa

import Command.*
def executeCommand(cmd: Command): Unit = cmd match
    case Start | Go => println("start")
    case Stop | Whoa => println("stop")
```

보시는 것처럼 각 `case` 문에 대해 여러 매칭 가능한 값들을 정의할 수 있는 기능은 코드를 단순하게 만들 수 있습니다.

### See Also

- 관련된 접근 방식은 Recipe 4.12를 참고하시길 바랍니다.

---

## 4.8 Assigning the Result of a Match Expression to a Variable

### Problem

`match` 표현식에서 값을 반환하여 변수에 할당하거나, `match` 표현식을 메서드의 본문으로 사용하고자 합니다.

### Solution

`match` 표현식의 결과를 변수에 할당하려면, 다음 예시의 `evenOrOdd` 변수처럼 표현식 앞에 변수 할당을 삽입하시면 됩니다.

```scala
val someNumber = scala.util.Random.nextInt()
val evenOrOdd = someNumber match
    case 1 | 3 | 5 | 7 | 9 => "odd"
    case 2 | 4 | 6 | 8 | 10 => "even"
    case _ => "other"
```

이러한 접근은 짧은 메서드나 함수를 만드는 데 일반적으로 사용됩니다. 예를 들어 다음 메서드는 Perl의 `true`와 `false` 정의를 구현합니다.

```scala
def isTrue(a: Matchable): Boolean = a match
    case false | 0 | "" => false
    case _ => true
```

### Discussion

Scala가 **expression-oriented programming, EOP** 언어라는 말을 들으실 수 있습니다. EOP란 모든 구성 요소가 표현식이며, 값을 산출하고, 부수 효과를 가지지 않는 것을 의미합니다. 다른 언어들과 달리 Scala에서는 `if`, `match`, `for`, `try` 같은 모든 구성 요소가 값을 반환합니다. 자세한 내용은 Recipe 24.3, "Writing Expressions (Instead of Statements)"를 참고하시길 바랍니다.

---

## 4.9 Accessing the Value of the Default Case in a Match Expression

### Problem

`match` 표현식에서 default "catch all" 케이스의 값에 접근하고자 하지만, `_` 와일드카드 문법으로 매칭하면 그 값에 접근할 수 없습니다.

### Solution

`_` 와일드카드 문자 대신 기본 케이스에 변수 이름을 할당하시면 됩니다.

```scala
i match
    case 0 => println("1")
    case 1 => println("2")
    case default => println(s"You gave me: $default")
```

기본 매치에 변수 이름을 부여함으로써, 표현식 오른쪽에서 그 변수에 접근하실 수 있습니다.

### Discussion

이 레시피의 핵심은 일반적으로 쓰는 `_` 와일드카드 문자 대신 기본 매치에 변수 이름을 사용하는 것입니다. 할당하는 이름은 어떤 합법적인 변수 이름도 될 수 있으며, `default` 대신 `what` 같은 다른 이름을 쓰실 수도 있습니다.

```scala
i match
    case 0 => println("1")
    case 1 => println("2")
    case what => println(s"You gave me: $what" )
```

기본 매치를 제공하시는 것이 중요합니다. 그렇지 않으면 `MatchError`를 일으킬 수 있습니다.

```scala
scala> 3 match
     |     case 1 => println("one")
     |     case 2 => println("two")
     |     // no default match
scala.MatchError: 3 (of class java.lang.Integer)
many more lines of output ...
```

더 많은 `MatchError` 세부 사항에 관해서는 Recipe 4.6의 Discussion을 참고하시길 바랍니다.

---

## 4.10 Using Pattern Matching in Match Expressions

### Problem

`match` 표현식에서 하나 이상의 패턴을 매칭해야 하며, 그 패턴은 constant pattern, variable pattern, constructor pattern, sequence pattern, tuple pattern, 또는 type pattern이 될 수 있습니다.

### Solution

매칭하고자 하는 각 패턴마다 `case` 문을 정의하세요. 다음 메서드는 `match` 표현식에서 사용하실 수 있는 많은 다른 패턴 유형들의 예시를 보여 줍니다.

```scala
def test(x: Matchable): String = x match

    // constant patterns
    case 0 => "zero"
    case true => "true"
    case "hello" => "you said 'hello'"
    case Nil => "an empty List"

    // sequence patterns
    case List(0, _, _) => "a 3-element list with 0 as the first element"
    case List(1, _*) => "list, starts with 1, has any number of elements"

    // tuples
    case (a, b) => s"got $a and $b"
    case (a, b, c) => s"got $a, $b, and $c"

    // constructor patterns
    case Person(first, "Alexander") => s"Alexander, first name = $first"
    case Dog("Zeus") => "found a dog named Zeus"

    // typed patterns
    case s: String => s"got a string: $s"
    case i: Int => s"got an int: $i"
    case f: Float => s"got a float: $f"
    case a: Array[Int] => s"array of int: ${a.mkString(",")}"
    case as: Array[String] => s"string array: ${as.mkString(",")}"
    case d: Dog => s"dog: ${d.name}"
    case list: List[_] => s"got a List: $list"
    case m: Map[_, _] => m.toString

    // the default wildcard pattern
    case _ => "Unknown"

end test
```

이 메서드의 큰 `match` 표현식은 *Programming in Scala* 책에서 설명된 여러 종류의 패턴—상수 패턴, 시퀀스 패턴, 튜플 패턴, 생성자 패턴, 타입 패턴—을 보여 줍니다.

다음 코드는 `match` 표현식의 모든 케이스를 시연하며, 각 표현식의 출력은 주석에 표시되어 있습니다. `println` 메서드를 더 간결한 예시를 위해 import에서 `p`로 이름을 변경한 것을 볼 수 있습니다.

```scala
import System.out.{println => p}

case class Person(firstName: String, lastName: String)
case class Dog(name: String)

// trigger the constant patterns
p(test(0))                 // zero
p(test(true))              // true
p(test("hello"))           // you said 'hello'
p(test(Nil))               // an empty List

// trigger the sequence patterns
p(test(List(0,1,2)))       // a 3-element list with 0 as the first element
p(test(List(1,2)))         // list, starts with 1, has any number of elements
p(test(List(1,2,3)))       // list, starts with 1, has any number of elements
p(test(Vector(1,2,3)))     // vector, starts w/ 1, has any number of elements

// trigger the tuple patterns
p(test((1,2)))                          // got 1 and 2
p(test((1,2,3)))                        // got 1, 2, and 3

// trigger the constructor patterns
p(test(Person("Melissa", "Alexander"))) // Alexander, first name = Melissa
p(test(Dog("Zeus")))                    // found a dog named Zeus

// trigger the typed patterns
p(test("Hello, world"))                 // got a string: Hello, world
p(test(42))                             // got an int: 42
p(test(42F))                            // got a float: 42.0
p(test(Array(1,2,3)))                   // array of int: 1,2,3
p(test(Array("coffee", "apple pie")))   // string array: coffee,apple pie
p(test(Dog("Fido")))                    // dog: Fido
p(test(List("apple", "banana")))        // got a List: List(apple, banana)
p(test(Map(1->"Al", 2->"Alexander")))   // Map(1 -> Al, 2 -> Alexander)

// trigger the wildcard pattern
p(test("33d"))                          // you gave me this string: 33d
```

`match` 표현식에서 다음처럼 작성된 `List`와 `Map` 표현식은,

```scala
case m: Map[_, _] => m.toString
case list: List[_] => s"thanks for the List: $list"
```

다음과 같이 작성할 수도 있었습니다.

```scala
case m: Map[A, B] => m.toString
case list: List[X] => s"thanks for the List: $list"
```

저자는 `List`나 `Map`에 무엇이 담겼는지에 관심이 없다는 점을 분명히 하기 위해 밑줄 문법을 선호합니다. 실제로는 `List`나 `Map`에 무엇이 담겼는지에 관심이 있을 때도 있지만, JVM의 **type erasure** 로 인해 이는 어려운 문제가 됩니다.

> **Type Erasure**
>
> 저자가 처음 이 예시를 작성했을 때 `List` 표현식을 다음과 같이 썼다고 합니다.
>
> ```scala
> case l: List[Int] => "List"
> ```
>
> Java 플랫폼의 **type erasure** 에 익숙하시다면, 이것이 동작하지 않는다는 걸 아실 것입니다. Scala 컴파일러는 다음 경고 메시지로 이 문제를 친절히 알려 줍니다.
>
> ```
> Test1.scala:7: warning: non-variable type argument Int in
> type pattern List[Int] is unchecked since it is eliminated
> by erasure case l: List[Int] => "List[Int]"
>                    ^
> ```
>
> 타입 소거에 익숙하지 않으시다면, 이 레시피의 See Also 섹션에 JVM에서 타입 소거가 어떻게 동작하는지 설명하는 페이지 링크가 포함되어 있습니다.

### Discussion

일반적으로 이 기법을 사용하실 때, 메서드는 base class or trait에서 상속받은 인스턴스를 기대하며, 그다음 `case` 문들이 그 베이스 타입의 서브타입을 참조합니다. 이는 모든 Scala 타입이 `Matchable`의 서브타입인 `test` 메서드에서 유추할 수 있었습니다. 다음 코드는 더 분명한 예시를 보여 줍니다.

저자의 Blue Parrot 애플리케이션은 랜덤한 간격으로 소리 파일을 재생하거나 주어진 텍스트를 "말하는" 프로그램이며, 다음과 같은 메서드를 가지고 있습니다.

```scala
import java.io.File

sealed trait RandomThing

case class RandomFile(f: File) extends RandomThing
case class RandomString(s: String) extends RandomThing

class RandomNoiseMaker:
    def makeRandomNoise(thing: RandomThing) = thing match
        case RandomFile(f) => playSoundFile(f)
        case RandomString(s) => speakText(s)
```

`makeRandomNoise` 메서드는 `RandomThing` 타입을 받도록 선언되어 있으며, `match` 표현식은 그 두 서브타입인 `RandomFile`과 `RandomString`을 처리합니다.

#### Patterns

Solution의 큰 `match` 표현식은 Scala 언어의 창시자인 Martin Odersky가 공동 저술한 *Programming in Scala*에서 정의된 여러 패턴을 보여 줍니다. 그 패턴들은 다음과 같습니다.

- Constant patterns
- Variable patterns
- Constructor patterns
- Sequence patterns
- Tuple patterns
- Typed patterns
- Variable-binding patterns

이 패턴들은 다음 단락들에서 간단히 설명됩니다.

*Constant patterns*
상수 패턴은 자기 자신만을 매칭할 수 있습니다. 어떤 리터럴이든 상수로 사용될 수 있습니다. `0`을 리터럴로 지정하면 `Int` 값 `0`만 매칭됩니다. 예시는 다음과 같습니다.

```scala
case 0 => "zero"
case true => "true"
```

*Variable patterns*
이는 Solution의 큰 `match` 예시에서는 보이지 않았지만, **변수 패턴** 은 `_` 와일드카드 문자처럼 어떤 객체든 매칭합니다. Scala는 변수를 그 객체 값에 바인딩시켜 주어서, `case` 문 오른쪽에서 그 변수를 사용할 수 있게 합니다. 예를 들어 `match` 표현식 끝에서 `_` 와일드카드 문자를 써서 그 외 무엇이든 잡을 수 있습니다.

```scala
case _ => s"Hmm, you gave me something ..."
```

하지만 변수 패턴을 쓰시면 다음처럼 작성할 수 있습니다.

```scala
case foo => s"Hmm, you gave me a $foo"
```

더 많은 정보는 Recipe 4.9를 참고하시길 바랍니다.

*Constructor patterns*
**생성자 패턴** 은 `case` 문에서 생성자를 매칭할 수 있도록 해 줍니다. 예시에서 보이듯이 필요에 따라 생성자 패턴에서 상수나 변수 패턴을 지정하실 수 있습니다.

```scala
case Person(first, "Alexander") => s"found an Alexander, first name = $first"
case Dog("Zeus") => "found a dog named Zeus"
```

*Sequence patterns*
`List`, `Array`, `Vector` 등의 시퀀스를 매칭하실 수 있습니다. 시퀀스 내 한 요소를 나타내는 데에는 `_` 문자를, 0개 이상의 요소를 나타내는 데에는 `_*` 를 사용하시면 됩니다. 예시는 다음과 같습니다.

```scala
case List(0, _, _) => "a 3-element list with 0 as the first element"
case List(1, _*) => "list, starts with 1, has any number of elements"
case Vector(1, _*) => "vector, starts with 1, has any number of elements"
```

*Tuple patterns*
예시에서 보이듯이, **튜플 패턴** 을 매칭하고 튜플 내 각 요소의 값에 접근하실 수 있습니다. 한 요소의 값에 관심이 없으시다면 `_` 와일드카드를 쓰실 수도 있습니다.

```scala
case (a, b, c) => s"3-elem tuple, with values $a, $b, and $c"
case (a, b, c, _) => s"4-elem tuple: got $a, $b, and $c"
```

*Typed patterns*
다음 예시에서 `str: String`은 **타입 패턴** 이며, `str`은 **pattern variable** 입니다.

```scala
case str: String => s"you gave me this string: $str"
```

예시에서 보이듯, 패턴 변수를 선언한 뒤에는 표현식 오른쪽에서 그 변수에 접근하실 수 있습니다.

*Variable-binding patterns*
때로는 패턴에 변수를 추가하고 싶을 수 있습니다. 다음과 같은 일반 문법으로 하실 수 있습니다.

```scala
case variableName @ pattern => ...
```

이를 **variable-binding** 패턴이라고 합니다. 사용 시 `match` 표현식의 입력 변수가 패턴과 비교되고, 매치되면 입력 변수가 `variableName`에 바인딩됩니다.

이것의 유용성은 이 패턴이 해결하는 문제를 시연하는 것이 가장 좋습니다. 이전에 보여드린 `List` 패턴이 있다고 가정해 봅시다.

```scala
case List(1, _*) => "a list beginning with 1, having any number of elements"
```

시연된 것처럼 이 방식은 첫 번째 요소가 1인 `List`를 매칭할 수 있게 해 주지만, 지금까지는 표현식 오른쪽에서 `List`에 접근하지 않았습니다. `List`에 접근해 보실 때 다음과 같이 할 수 있다는 점을 알고 계실 겁니다.

```scala
case list: List[_] => s"thanks for the List: $list"
```

그래서 시퀀스 패턴으로도 이를 시도해 보셔야 할 것 같습니다.

```scala
case list: List(1, _*) => s"thanks for the List: $list"
```

하지만 불행히도 이 코드는 다음의 컴파일러 오류로 실패합니다.

```
Test2.scala:22: error: '=>' expected but '(' found.
    case list: List(1, _*) => s"thanks for the List: $list"
                   ^
one error found
```

이 문제의 해결책은 시퀀스 패턴에 변수 바인딩 패턴을 추가하는 것입니다.

```scala
case list @ List(1, _*) => s"$list"
```

이 코드는 컴파일되고 예상대로 동작하여, 표현식 오른쪽에서 `List`에 접근하실 수 있게 해 줍니다.

다음 코드는 이 예시와 이 접근의 유용성을 시연합니다.

```scala
case class Person(firstName: String, lastName: String)

def matchType(x: Matchable): String = x match
    //case x: List(1, _*) => s"$x"    // doesn't compile
    case x @ List(1, _*) => s"$x"     // prints the list

    //case Some(_) => "got a Some"    // works, but can't access the Some
    //case Some(x) => s"$x"           // returns "foo"
    case x @ Some(_) => s"$x"         // returns "Some(foo)"

    case p @ Person(first, "Doe") => s"$p" // returns "Person(John,Doe)"
end matchType

@main def test2 =
    println(matchType(List(1,2,3)))             // prints "List(1, 2, 3)"
    println(matchType(Some("foo")))             // prints "Some(foo)"
    println(matchType(Person("John", "Doe")))   // prints "Person(John,Doe)"
```

`match` 표현식 내부의 두 `List` 예시에서 주석 처리된 코드 라인은 컴파일되지 않지만, 두 번째 라인은 원하는 `List` 객체를 매치한 뒤 변수 `x`에 그 리스트를 바인딩하는 방법을 보여 줍니다. 이 코드가 `List(1,2,3)`과 매치되면, 첫 번째 `println` 문의 출력에서 보시다시피 결과는 `List(1, 2, 3)`이 됩니다.

첫 번째 `Some` 예시는 보여 드린 접근으로 `Some`을 매칭할 수 있지만, 오른쪽에서 그 정보에 접근할 수 없다는 점을 보여 줍니다. 두 번째 `Some` 예시는 `Some` 내부 값에 어떻게 접근할 수 있는지를 보여 주며, 세 번째 예시는 한 단계 더 나아가서 `Some` 객체 자체에 접근할 수 있게 해 줍니다. 두 번째 `println` 호출에 의해 매치되면 `Some(foo)`가 출력되어, 이제 `Some` 객체에 접근할 수 있음을 시연합니다.

마지막으로 이 접근은 성이 `Doe`인 `Person`을 매칭하는 데 사용됩니다. 이 문법은 패턴 매칭의 결과를 변수 `p`에 할당하도록 해 주고, 표현식 오른쪽에서 그 변수에 접근할 수 있게 해 줍니다.

#### Using Some and None in match expressions

이 예시들을 마무리하면서, `match` 표현식에서 종종 `Some`과 `None`을 함께 사용하게 되실 겁니다. 예를 들어 `toIntOption` 같은 메서드로 문자열로부터 숫자를 생성하려 하실 때, 그 결과를 `match` 표현식으로 처리하실 수 있습니다.

```scala
val s = "42"

// later in the code ...
s.toIntOption match
    case Some(i) => println(i)
    case None => println("That wasn't an Int")
```

`match` 표현식 안에서는 성공 조건과 실패 조건을 처리하기 위해 `Some`과 `None` 케이스만 지정하시면 됩니다. `Option`, `Some`, `None` 사용 예시는 Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"를 참고하시길 바랍니다.

### See Also

- Stack Overflow에서 `match` 표현식을 사용할 때 타입 소거를 우회하는 방법에 대한 논의
- 저자의 Blue Parrot 애플리케이션
- 타입 소거에 관한 문서

---

## 4.11 Using Enums and Case Classes in match Expressions

### Problem

`match` 표현식에서 enum, case class, 혹은 case object를 매칭하고자 합니다.

### Solution

다음 예시는 `match` 표현식의 오른쪽에서 필요한 정보에 따라, 패턴을 사용하여 enum을 다양한 방법으로 매칭하는 방법을 보여 줍니다. 먼저 `Dog`, `Cat`, `Woodpecker` 세 개의 인스턴스를 가진 `Animal` enum을 다음과 같이 정의합니다.

```scala
enum Animal:
    case Dog(name: String)
    case Cat(name: String)
    case Woodpecker
```

그 enum이 주어졌을 때, 다음의 `getInfo` 메서드는 `match` 표현식에서 enum 타입을 매칭하는 다양한 방법을 보여 줍니다.

```scala
import Animal.*

def getInfo(a: Animal): String = a match
    case Dog(moniker) => s"Got a Dog, name = $moniker"
    case _: Cat       => "Got a Cat (ignoring the name)"
    case Woodpecker   => "That was a Woodpecker"
```

다음 예시는 `Dog`, `Cat`, `Woodpecker`가 주어졌을 때 `getInfo`가 어떻게 동작하는지 보여 줍니다.

```scala
println(getInfo(Dog("Fido")))    // Got a Dog, name = Fido
println(getInfo(Cat("Morris")))  // Got a Cat (ignoring the name)
println(getInfo(Woodpecker))     // That was a Woodpecker
```

`getInfo`에서 `Dog` 클래스가 매치되면 그 이름이 추출되어 표현식 오른쪽의 문자열을 만드는 데 사용됩니다. 이름을 추출할 때 사용하는 변수 이름은 어떤 합법적인 변수 이름이든 될 수 있음을 보여 주기 위해 `moniker`라는 이름을 사용합니다.

`Cat`을 매칭할 때에는 이름을 무시하고 싶으므로, 어떤 `Cat` 인스턴스든 매치하는 문법을 사용합니다. `Woodpecker`는 매개변수 없이 생성되었으므로, 역시 시연된 대로 매치됩니다.

### Discussion

Scala 2에서는 enum과 같은 효과를 내기 위해 sealed trait을 case class, case object와 함께 사용했습니다.

```scala
sealed trait Animal
case class Dog(name: String) extends Animal
case class Cat(name: String) extends Animal
case object Woodpecker extends Animal
```

Recipe 6.12, "How to Create Sets of Named Values with Enums"에서 설명하듯, `enum`은 (a) sealed 클래스 혹은 트레이트와 (b) 해당 클래스의 companion object 멤버로 정의된 값들을 함께 묶는 shortcut입니다. case class는 내장된 `unapply` 메서드를 가지므로 `match` 표현식에서 잘 동작하기 때문에, 두 접근 모두 `getInfo`의 `match` 표현식에서 사용될 수 있습니다. 이 동작 방식은 Recipe 7.8, "Implementing Pattern Matching with unapply"에서 설명해 드립니다.

---

## 4.12 Adding if Expressions (Guards) to Case Statements

### Problem

`match` 표현식의 `case` 문에 qualifying 로직을 추가해서, 숫자의 범위를 허용하거나 추가 기준에 매치될 때만 패턴이 매치되도록 하고자 합니다.

### Solution

`case` 문에 `if` 가드를 추가하세요. 숫자의 범위를 매치하는 데 사용하실 수 있습니다.

```scala
i match
    case a if 0 to 9 contains a => println("0-9 range: " + a)
    case b if 10 to 19 contains b => println("10-19 range: " + b)
    case c if 20 to 29 contains c => println("20-29 range: " + c)
    case _ => println("Hmmm...")
```

객체의 서로 다른 값들을 매치하실 수도 있습니다.

```scala
i match
    case x if x == 1 => println("one, a lonely number")
    case x if (x == 2 || x == 3) => println(x)
    case _ => println("some other value")
```

클래스에 `unapply` 메서드가 있는 한, `if` 가드에서 클래스 필드들을 참조하실 수 있습니다. 예를 들어 case class는 자동으로 생성되는 `unapply` 메서드가 있으므로, 다음 `Stock` 클래스와 인스턴스가 주어졌을 때,

```scala
case class Stock(symbol: String, price: BigDecimal)
val stock = Stock("AAPL", BigDecimal(132.50))
```

패턴 매칭과 가드 조건에서 클래스 필드들을 사용하실 수 있습니다.

```scala
stock match
    case s if s.symbol == "AAPL" && s.price < 140 => buy(s)
    case s if s.symbol == "AAPL" && s.price > 160 => sell(s)
    case _ => // do nothing
```

case class에서도—그리고 `unapply` 메서드가 적절히 구현된 클래스에서도—필드를 추출하여 가드 조건에 사용하실 수 있습니다. 예를 들어 다음 `match` 표현식의 `case` 문은

```scala
// extract the 'name' in the 'case' and then use that value
def speak(p: Person): Unit = p match
    case Person(name) if name == "Fred" =>
        println("Yabba dabba doo")
    case Person(name) if name == "Bam Bam" =>
        println("Bam bam!")
    case _ =>
        println("Watch the Flintstones!")
```

`Person`이 case class로 정의되어 있을 때 동작합니다.

```scala
case class Person(aName: String)
```

혹은 `unapply` 메서드가 제대로 구현된 클래스여도 됩니다.

```scala
class Person(val aName: String)
object Person:
    // 'unapply' deconstructs a Person. it's also known as an
    // extractor, and Person is an "extractor object."
    def unapply(p: Person): Option[String] = Some(p.aName)
```

`unapply` 메서드 작성 방법에 관한 자세한 내용은 Recipe 7.8, "Implementing Pattern Matching with unapply"를 참고하시길 바랍니다.

### Discussion

`case` 문 왼쪽(즉, `=>` 기호 앞)에 boolean 테스트를 추가하고 싶을 때마다 이와 같이 `if` 표현식을 사용하실 수 있습니다.

이 모든 예시들은 `if` 테스트를 표현식의 오른쪽에 두는 방식으로도 작성할 수 있다는 점에 유의하세요. 다음과 같이요.

```scala
case Person(name) =>
    if name == "Fred" then println("Yabba dabba doo")
    else if name == "Bam Bam" then println("Bam bam!")
```

하지만 많은 상황에서 `if` 가드를 `case` 문에 직접 결합하면 코드가 더 단순하고 읽기 쉬워집니다. 가드를 뒤쪽의 비즈니스 로직으로부터 분리해 주기 때문입니다.

또한, 이 `Person` 예시는 조금 작위적이라는 점도 언급해 둡니다. Scala의 패턴 매칭 능력은 다음처럼 케이스를 쓸 수 있게 해 주기 때문입니다.

```scala
def speak(p: Person): Unit = p match
    case Person("Fred") => println("Yabba dabba doo")
    case Person("Bam Bam") => println("Bam bam!")
    case _ => println("Watch the Flintstones!")
```

이 경우 `Person`이 더 복잡해지고 그 매개변수에 매치하는 것 이상의 작업이 필요할 때 가드가 실제로 필요해집니다.

또한 Recipe 4.10에서 시연된 것처럼, Solution에서 다음 코드를 사용하는 대신,

```scala
case x if (x == 2 || x == 3) => println(x)
```

또 다른 가능한 해결책은 **변수 바인딩 패턴** 을 사용하는 것입니다.

```scala
case x @ (2|3) => println(x)
```

이 코드는 "`match` 표현식의 값(`i`)이 2 또는 3이면 그 값을 변수 `x`에 할당한 뒤 `println`으로 `x`를 출력하세요."로 읽을 수 있습니다.

---

## 4.13 Using a Match Expression Instead of isInstanceOf

### Problem

한 타입 혹은 여러 다른 타입에 매치되는 코드 블록을 작성하고자 합니다.

### Solution

객체의 타입을 테스트하는 데 `isInstanceOf` 메서드를 사용하실 수 있습니다.

```scala
if x.isInstanceOf[Foo] then ...
```

하지만 "Scala way"은 이러한 작업에 `match` 표현식을 선호하는 것입니다. 일반적으로 `match`를 쓰는 편이 `isInstanceOf`를 쓰는 것보다 훨씬 강력하고 편리하기 때문입니다.

예를 들어 기본적인 사용 사례로, 알 수 없는 타입의 객체가 주어졌을 때 그 객체가 `Person`의 인스턴스인지 확인하고 싶을 수 있습니다. 이 코드는 타입이 `Person`이면 `true`를 반환하고, 그 외에는 `false`를 반환하는 `match` 표현식 작성법을 보여 줍니다.

```scala
def isPerson(m: Matchable): Boolean = m match
    case p: Person => true
    case _ => false
```

더 흔한 시나리오는 다음과 같은 모델이 있을 때입니다.

```scala
enum Shape:
    case Circle(radius: Double)
    case Square(length: Double)
```

그다음 `Shape`의 area을 계산하는 메서드를 쓰고자 하실 수 있습니다. 이 문제의 한 가지 해결책은 패턴 매칭을 사용해 `area`를 작성하는 것입니다.

```scala
import Shape.*

def area(s: Shape): Double = s match
    case Circle(r) => Math.PI * r * r
    case Square(l) => l * l

// examples
area(Circle(2.0))   // 12.566370614359172
area(Square(2.0))   // 4.0
```

이는 흔한 사용 사례로, `area`는 타입이 매개변수이며 `match` 내부에서 deconstruct하는 그 타입의 immediate parent입니다.

`Circle`과 `Square`가 추가 생성자 매개변수들을 가지고 있고 각각의 `radius`와 `length`에만 접근해야 한다면, 완전한 해결책은 다음과 같이 보이게 됩니다.

```scala
enum Shape:
    case Circle(x0: Double, y0: Double, radius: Double)
    case Square(x0: Double, y0: Double, length: Double)

import Shape.*

def area(s: Shape): Double = s match
    case Circle(_, _, r) => Math.PI * r * r
    case Square(_, _, l) => l * l

// examples
area(Circle(0, 0, 2.0))   // 12.566370614359172
area(Square(0, 0, 2.0))   // 4.0
```

`match` 표현식 내 `case` 문에서 보이듯, 필요하지 않은 매개변수는 `_` 문자로 무시하시면 됩니다.

### Discussion

시연된 것처럼, `match` 표현식은 여러 타입을 매치할 수 있게 해 주므로 `isInstanceOf` 메서드를 대체하는 데 사용하는 것은 `match/case` 문법과 Scala 애플리케이션에서 일반적으로 사용되는 패턴 매칭 접근의 자연스러운 활용입니다.

가장 기본적인 사용 사례에서는 `isInstanceOf` 메서드가 객체 하나가 어떤 타입인지 판단하는 더 간단한 접근일 수 있습니다.

```scala
if (o.isInstanceOf[Person]) { // handle this ...
```

하지만 이보다 복잡한 경우에는 길게 이어지는 `if/then/else if` 문보다 `match` 표현식이 더 읽기 쉽습니다.

> **isInstanceOf in OOP**
>
> 객체 지향 프로그래밍에서 `isInstanceOf`의 사용은 상속을 제대로 활용하지 못하고 있다는 신호일 수 있습니다. 예를 들어 누군가 다음과 같은 코드를 작성했다고 가정해 봅시다.
>
> ```scala
> enum Animal:
>     case Cat, Dog, Ostrich
> ```
>
> 그런데 어떤 이유로 다음과 같은 `isInstanceOf` 코드를 작성하고 계시다면, 이는 무언가 잘못되고 있다는 신호일 수 있습니다.
>
> ```scala
> if currentInstance.isInstanceOf[Ostrich] then ...
> ```
>
> 대신 OOP 코드는 다음과 같은 모습을 의도합니다.
>
> ```scala
> val animal: Animal =
>     AnimalFactory.getAnimal("big flightless bird")
>
> // some time later in your code ...
> animal.walk()
> ```
>
> 혹은 이렇게요.
>
> ```scala
> val oz: Ostrich =
>     AnimalFactory.getAnimal(
>         "big flightless bird"
>     ).asInstanceOf[Ostrich]
>
> // some time later in your code ...
> oz.tryToFly()
> ```
>
> `Animal`을 받는 이런 코드를 작성하신다면, 받은 `Animal`이 `Cat`, `Dog`, 혹은 `Ostrich`인지에 코드가 신경 쓸 필요가 없어야 합니다. 객체를 즉시 `Ostrich`로 캐스팅하시게 된다면, 해당 추가 메서드를 호출하실 수 있습니다. 따라서 OOP에서는 인스턴스를 `isInstanceOf`로 테스트하는 일이 드물어야 합니다.
>
> 반대로 함수형 프로그래밍 코드에서는 타입을 다루기 위해 `match` 표현식의 패턴 매칭이 항상 사용됩니다.

### See Also

- 더 많은 `match` 기법은 Recipe 4.10을 참고하시길 바랍니다.

---

## 4.14 Working with a List in a Match Expression

### Problem

`List` 자료구조가 다른 순차 자료구조와 약간 다르다는 것을 아십니다. **cons cell** 로부터 구성되며 `Nil` 요소로 끝납니다. `match` 표현식으로 작업할 때, 특히 재귀 함수를 작성할 때 이를 활용하고자 하십니다.

### Solution

정수 1, 2, 3을 담고 있는 `List`를 이렇게 만드실 수 있습니다.

```scala
val xs = List(1, 2, 3)
```

또는 이렇게요.

```scala
val ys = 1 :: 2 :: 3 :: Nil
```

두 번째 예시에서 보이듯이 `List`는 `Nil` 요소로 끝나며, `match` 표현식을 쓸 때 이를 활용하실 수 있습니다. 특히 재귀 알고리즘을 작성하실 때 그렇습니다. 예를 들어 다음 `listToString` 메서드에서는, 현재 요소가 `Nil`이 아니면 `List`의 나머지 부분으로 메서드를 재귀적으로 다시 호출하고, 현재 요소가 `Nil`이면 재귀 호출을 멈추고 빈 `String`을 반환하여, 이 시점에서 재귀 호출들이 unwind됩니다.

```scala
def listToString(list: List[String]): String = list match
    case s :: rest => s + " " + listToString(rest)
    case Nil => ""
```

REPL은 이 메서드가 어떻게 동작하는지 시연해 드립니다.

```scala
scala> val fruits = "Apples" :: "Bananas" :: "Oranges" :: Nil
fruits: List[java.lang.String] = List(Apples, Bananas, Oranges)

scala> listToString(fruits)
res0: String = "Apples Bananas Oranges "
```

같은 접근은 다른 타입 리스트와 서로 다른 알고리즘을 다룰 때에도 사용할 수 있습니다. 예를 들어 `List(1,2,3).sum`을 그냥 쓰실 수도 있지만, 이 예시는 `match`와 재귀를 사용해 직접 `sum` 메서드를 작성하는 방법을 보여 줍니다.

```scala
def sum(list: List[Int]): Int = list match
    case Nil => 0
    case n :: rest => n + sum(rest)
```

비슷하게, 이것은 *product* 알고리즘입니다.

```scala
def product(list: List[Int]): Int = list match
    case Nil => 1
    case n :: rest => n * product(rest)
```

REPL은 이 메서드들이 어떻게 동작하는지 보여 줍니다.

```scala
scala> val nums = List(1,2,3,4,5)
nums: List[Int] = List(1, 2, 3, 4, 5)

scala> sum(nums)
res0: Int = 15

scala> product(nums)
res1: Int = 120
```

> **Don't Forget reduce and fold**
>
> 재귀는 훌륭하지만, 컬렉션 클래스의 다양한 **reduce** 와 **fold** 메서드들이 알고리즘을 적용하면서 컬렉션을 순회할 수 있도록 만들어져 있어, 재귀의 필요성을 제거해 주는 경우가 많습니다. 예를 들어 다음 두 가지 형태 중 어느 쪽이든 `reduce`를 사용해 sum 알고리즘을 작성하실 수 있습니다.
>
> ```scala
> // long form
> def sum(list: List[Int]): Int = list.reduce((x,y) => x + y)
>
> // short form
> def sum(list: List[Int]): Int = list.reduce(_ + _)
> ```
>
> 더 자세한 내용은 Recipe 13.10, "Walking Through a Collection with the reduce and fold Methods"를 참고하시길 바랍니다.

### Discussion

시연된 것처럼 재귀는 메서드가 문제를 해결하기 위해 자기 자신을 호출하는 기법입니다. 함수형 프로그래밍에서는—모든 변수가 불변이므로—재귀는 `List`의 요소들을 순회하면서 모든 요소의 합이나 곱을 계산하는 것과 같은 문제를 해결하는 방법을 제공합니다.

특히 `List` 클래스로 작업할 때 한 가지 좋은 점은 `List`가 `Nil` 요소로 끝난다는 사실이며, 덕분에 여러분의 재귀 알고리즘은 일반적으로 다음과 같은 패턴을 가집니다.

```scala
def myTraversalMethod[A](xs: List[A]): B = xs match
    case head :: tail =>
        // do something with the head
        // pass the tail of the list back to your method, i.e.,
        // `myTraversalMethod(tail)`
    case Nil =>
        // end condition here (0 for sum, 1 for product, etc.)
        // end the traversal
```

> **Variables in Functional Programming**
>
> 함수형 프로그래밍에서 *variable* 이라는 단어를 사용하지만, 불변 변수만을 사용하기 때문에 이 단어가 말이 안 되는 것처럼 보일 수 있습니다. 즉 **변할 수 없는 변수** 를 가진다는 의미가 되는 것이죠.
>
> 여기서 일어나는 일은 컴퓨터 프로그래밍의 의미가 아닌 **algebraic** 의미의 변수를 뜻한다는 것입니다. 예를 들어 대수학에서는 다음 대수 방정식을 쓸 때 a, b, c를 변수라고 부릅니다.
>
> ```
> a = b * c
> ```
>
> 그러나 이들은 값이 할당되면 변할 수 없습니다. *variable*이라는 용어는 함수형 프로그래밍에서도 같은 의미를 가집니다.

### See Also

저자는 처음에 재귀가 이해하기 힘든 주제라고 느꼈기 때문에, 여러 블로그 글을 작성했습니다.

- "Scala Recursion Examples (Recursive Programming)"
- "Recursive: How Recursive Function Calls Work"
- "Tail-Recursive Algorithms in Scala"
- "Recursion: Visualizing the Recursive sum Function"
- "Recursion: Thinking Recursively"에서 저자는 **identity element** 에 대해 씁니다. `0`은 sum 연산의 항등 원소이고, `1`은 product 연산의 항등 원소이며, `""`(빈 문자열)은 문자열 작업의 항등 원소가 되는 등입니다.

---

## 4.15 Matching One or More Exceptions with try/catch

### Problem

`try/catch` 블록에서 하나 이상의 exception를 잡고자 합니다.

### Solution

Scala `try/catch/finally` 문법은 Java와 유사하지만, `catch` 블록에서 `match` 표현식 접근을 사용합니다.

```scala
try
    doSomething()
catch
    case e: SomeException => e.printStackTrace
finally
    // do your cleanup work
```

여러 예외를 잡고 처리해야 할 때에는 예외 타입들을 서로 다른 `case` 문으로 추가하시면 됩니다.

```scala
try
    openAndReadAFile(filename)
catch
    case e: FileNotFoundException =>
        println(s"Couldn't find $filename.")
    case e: IOException =>
        println(s"Had an IOException trying to read $filename.")
```

원하시면 다음처럼 쓰실 수도 있습니다.

```scala
try
    openAndReadAFile(filename)
catch
    case e: (FileNotFoundException | IOException) =>
        println(s"Had an IOException trying to read $filename")
```

### Discussion

시연된 것처럼 Scala `case` 문법은 가능한 다양한 예외를 매치하는 데 쓰입니다. 어떤 특정 예외가 던져질지에 관심이 없고 모두 잡아서 어떤 처리를 하고 싶으시다면—예를 들어 로그를 남기는 것처럼—이러한 문법을 사용하시면 됩니다.

```scala
try
    openAndReadAFile(filename)
catch
    case t: Throwable => logger.log(t)
```

어떤 이유로 예외 값에 관심이 없으시다면, 다음처럼 모두 잡고 무시하실 수도 있습니다.

```scala
try
    openAndReadAFile(filename)
catch
    case _: Throwable => println("Nothing to worry about, just an exception")
```

#### Methods based on try/catch

본 챕터의 도입부에서 보여 드렸듯이, `try/catch/finally` 블록은 값을 반환할 수 있으므로 메서드의 본문으로 쓸 수 있습니다. 다음 메서드는 `Option[String]`을 반환합니다. 파일이 발견되면 `String`을 담은 `Some`을 반환하고, 파일을 읽는 데 문제가 있으면 `None`을 반환합니다.

```scala
import scala.io.Source
import java.io.{FileNotFoundException, IOException}

def readFile(filename: String): Option[String] =
    try
        Some(Source.fromFile(filename).getLines.mkString)
    catch
        case _: (FileNotFoundException|IOException) => None
```

이는 `try` 표현식에서 값을 반환하는 한 가지 방법을 보여 줍니다.

요즘 저자는 예외를 던지는 메서드를 거의 작성하지 않지만, Java처럼 `catch` 절에서 예외를 던질 수는 있습니다. 하지만 Scala에는 checked exception가 없기 때문에, 메서드가 예외를 던진다는 사실을 명시할 필요는 없습니다. 다음 예시에서 보이듯 메서드에 어떤 애노테이션도 쓰지 않습니다.

```scala
// danger: this method doesn't warn you that an exception can be thrown
def readFile(filename: String): String =
    try
        Source.fromFile(filename).getLines.mkString
    catch
        case t: Throwable => throw t
```

사실 이는 매우 위험한 메서드입니다. 이런 코드를 작성하지 마세요!

메서드가 예외를 던진다는 사실을 선언하려면, `@throws` 애노테이션을 메서드 정의에 추가하세요.

```scala
// better: this method warns others that an exception can be thrown
@throws(classOf[NumberFormatException])
def readFile(filename: String): String =
    try
        Source.fromFile(filename).getLines.mkString
    catch
        case t: Throwable => throw t
```

비록 이 마지막 메서드는 앞의 것보다 낫지만, 둘 다 선호되는 방식은 아닙니다. "Scala 식"은 예외를 **절대 던지지 않는** 것입니다. 대신 보여 드렸던 것처럼 `Option`을 사용하시거나, 실패에 대한 정보를 반환하고자 하실 때 `Try/Success/Failure` 또는 `Either/Right/Left` 클래스를 사용하시면 됩니다. 이 예시는 `Try` 사용 방법을 보여 줍니다.

```scala
import scala.io.Source
import java.io.{FileNotFoundException, IOException}
import scala.util.{Try,Success,Failure}

def readFile(filename: String): Try[String] =
    try
        Success(Source.fromFile(filename).getLines.mkString)
    catch
        case t: Throwable => Failure(t)
```

예외 메시지와 관련된 상황이라면 저자는 `Option`보다 항상 `Try`나 `Either`를 선호합니다. `Failure`나 `Left`는 메시지에 접근할 수 있도록 해 주지만, `Option`은 `None`만 반환하기 때문입니다.

#### A concise way to catch everything

모든 예외를 간결히 잡는 또 다른 방법은 `scala.util.control.Exception` 객체의 `allCatch` 메서드를 사용하는 것입니다. 다음 예시들은 `allCatch` 사용법을 시연하며, 먼저 성공 케이스를, 그다음 실패 케이스를 보여 줍니다. 각 표현식의 출력은 각 라인의 주석 뒤에 표시되어 있습니다.

```scala
import scala.util.control.Exception.allCatch

// OPTION
allCatch.opt("42".toInt)       // Option[Int] = Some(42)
allCatch.opt("foo".toInt)      // Option[Int] = None

// TRY
allCatch.toTry("42".toInt)     // Matchable = 42
allCatch.toTry("foo".toInt)
    // Matchable = Failure(NumberFormatException: For input string: "foo")

// EITHER
allCatch.either("42".toInt)    // Either[Throwable, Int] = Right(42)
allCatch.either("foo".toInt)
    // Either[Throwable, Int] =
    // Left(NumberFormatException: For input string: "foo")
```

### See Also

- 메서드가 예외를 던질 수 있음을 선언하는 더 많은 예시는 Recipe 8.7, "Declaring That a Method Can Throw an Exception"을 참고하시길 바랍니다.
- `Option/Some/None`과 `Try/Success/Failure` 사용법에 관한 더 자세한 내용은 Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"를 참고하시길 바랍니다.
- `allCatch`에 관한 자세한 내용은 `scala.util.control.Exception` Scaladoc 페이지를 참고하시길 바랍니다.

---

## 4.16 Declaring a Variable Before Using It in a try/catch/finally Block

### Problem

`try` 블록에서 객체를 사용하고, 그 블록의 `finally` 부분에서도 접근해야 하는 경우입니다. 예를 들어 객체에 `close` 메서드를 호출해야 할 때입니다.

### Solution

일반적으로 필드를 `try/catch` 블록 앞에 `Option`으로 선언하신 뒤, `try` 절 안에서 변수를 `Some`에 바인딩하시면 됩니다. 다음 예시에서 보여 드립니다. 여기서는 `sourceOption` 필드를 `try/catch` 블록 앞에 선언하고, `try` 절 내부에서 할당합니다.

```scala
import scala.io.Source
import java.io.*

var sourceOption: Option[Source] = None
try
    sourceOption = Some(Source.fromFile("/etc/passwd"))
    sourceOption.foreach { source =>
        // do whatever you need to do with 'source' here ...
        for line <- source.getLines do println(line.toUpperCase)
    }
catch
    case ioe: IOException => ioe.printStackTrace
    case fnf: FileNotFoundException => fnf.printStackTrace
finally
    sourceOption match
        case None =>
            println("bufferedSource == None")
        case Some(s) =>
            println("closing the bufferedSource ...")
            s.close
```

이것은 작위적인 예시이며—Recipe 16.1, "Reading Text Files"에서는 파일을 읽는 훨씬 좋은 방법을 보여 드립니다—그러나 이 접근을 시연합니다. 먼저 `try` 블록 전에 `Option` 타입으로 `var` 필드를 정의하세요.

```scala
var sourceOption: Option[Source] = None
```

그다음 `try` 절 안에서 변수를 `Some` 값에 할당합니다.

```scala
sourceOption = Some(Source.fromFile("/etc/passwd"))
```

자원을 닫아야 하는 경우에는 보여 드린 기법을 쓰시면 됩니다. (다만 Recipe 16.1, "Reading Text Files"는 자원을 닫는 훨씬 더 나은 방법도 보여 드립니다.) 이 코드에서 예외가 던져지면 `finally` 내부의 `sourceOption`은 `None` 값이 된다는 점에 유의하세요. 예외가 던져지지 않으면 `match` 표현식의 `Some` 분기가 평가됩니다.

### Discussion

이 레시피의 한 가지 핵심은 초기에 채워지지 않은 `Option` 필드를 선언하는 문법을 아는 것입니다.

```scala
var in: Option[FileInputStream] = None
var out: Option[FileOutputStream] = None
```

두 번째 형태도 사용될 수 있지만, 첫 번째 형태가 선호됩니다.

```scala
var in = None: Option[FileInputStream]
var out = None: Option[FileOutputStream]
```

#### Don't use null

저자가 처음 Scala로 작업을 시작했을 때, 이 코드를 작성하는 유일한 방법이라고 생각했던 것은 `null` 값을 사용하는 것이었습니다. 다음 코드는 저자가 자신의 이메일 계정을 확인하는 애플리케이션에서 사용했던 접근 방식을 보여 줍니다. 이 코드의 `store`와 `inbox` 필드들은 `javax.mail` 패키지의 `Store`와 `Folder` 타입을 가진 `null` 필드로 선언되어 있습니다.

```scala
// (1) declare the null variables (don't use null; this is just an example)
var store: Store = null
var inbox: Folder = null

try
    // (2) use the variables/fields in the try block
    store = session.getStore("imaps")
    inbox = getFolder(store, "INBOX")
    // rest of the code here ...
catch
    case e: NoSuchProviderException => e.printStackTrace
    case me: MessagingException => me.printStackTrace
finally
    // (3) call close() on the objects in the finally clause
    if (inbox != null) inbox.close
    if (store != null) store.close
```

하지만 Scala로 작업하면 `null` 값이 존재한다는 사실조차 잊어버릴 수 있는 기회가 주어지기 때문에, 이 방식은 **권장되지 않는** 접근입니다.

### See Also

다음 레시피들에서 (a) `null` 값을 **쓰지 말아야** 하는 방법과 (b) 대신 `Option`, `Try`, `Either`를 사용하는 방법에 대한 더 자세한 내용을 보실 수 있습니다.

- Recipe 24.5, "Eliminating null Values from Your Code"
- Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"
- Recipe 24.8, "Handling Option Values with Higher-Order Functions"

자원을 여는 코드와 작업이 끝나면 닫는 코드를 작성해야 할 때마다 `scala.util.Using` 객체를 사용하시면 도움이 될 수 있습니다. 이 객체를 사용하는 예시와 훨씬 더 나은 텍스트 파일 읽기 방법은 Recipe 16.1, "Reading Text Files"를 참고하시길 바랍니다.

또한 Recipe 24.8, "Handling Option Values with Higher-Order Functions"는 `match` 표현식 외에도 `Option` 값으로 작업하는 다른 방법을 보여 드립니다.

---

## 4.17 Creating Your Own Control Structures

### Problem

Scala 언어를 커스터마이즈하거나, 코드를 단순화하거나, domain-specific language, DSL를 만들기 위해 여러분만의 제어 구조를 정의하고자 합니다.

### Solution

multiple parameter lists, by-name parameters, extension methods, higher-order functions 등의 기능들 덕분에, 제어 구조처럼 동작하는 코드를 직접 만드실 수 있습니다.

예를 들어 Scala에 내장 `while` 루프가 없다고 가정하고, 다음처럼 사용할 수 있는 여러분만의 커스텀 `whileTrue` 루프를 만들고 싶다고 해 봅시다.

```scala
var i = 0
whileTrue (i < 5) {
    println(i)
    i += 1
}
```

이 `whileTrue` 제어 구조를 만들기 위해 두 개의 매개변수 목록을 받는 `whileTrue`라는 메서드를 정의합니다. 첫 번째 매개변수 목록은 테스트 조건(위 예시에서는 `i < 5`)을 처리하고, 두 번째 매개변수 목록은 사용자가 실행하고자 하는 코드 블록(중괄호 사이의 코드)을 처리합니다. 두 매개변수 모두 이름에 의한 매개변수로 정의하세요. `whileTrue`는 가변 변수 갱신이나 콘솔 출력처럼 부수 효과를 위해서만 사용되므로 `Unit`을 반환하도록 선언합니다. 메서드 시그니처의 초기 스케치는 다음과 같습니다.

```scala
def whileTrue(testCondition: => Boolean)(codeBlock: => Unit): Unit = ???
```

이 메서드의 본문을 구현하는 한 가지 방법은 재귀 알고리즘을 쓰는 것입니다. 이 코드는 완전한 해결책을 보여 줍니다.

```scala
import scala.annotation.tailrec

object WhileTrue:
    @tailrec
    def whileTrue(testCondition: => Boolean)(codeBlock: => Unit): Unit =
        if (testCondition) then
            codeBlock
            whileTrue(testCondition)(codeBlock)
        end if
    end whileTrue
```

이 코드에서 `testCondition`이 평가되어 `true`이면 `codeBlock`이 실행된 뒤 `whileTrue`가 재귀적으로 호출됩니다. `testCondition`이 `false`를 반환할 때까지 계속 자기 자신을 호출합니다.

이 코드를 테스트하려면 먼저 import를 해 주세요.

```scala
import WhileTrue.whileTrue
```

그러고 나서 앞서 보여드린 `whileTrue` 루프를 실행하시면 원하는 대로 동작하는 것을 보실 수 있습니다.

### Discussion

Scala 언어의 창시자들은 일부 키워드를 Scala에 구현하지 않기로 의식적인 결정을 내렸으며, 대신 Scala 라이브러리를 통해 기능을 구현했습니다. 예를 들어 Scala에는 내장 `break`와 `continue` 키워드가 없습니다. 대신 저자의 블로그 글 "Scala: How to Use break and continue in for and while Loops"에서 설명하는 것처럼 이를 라이브러리를 통해 구현합니다.

Solution에서 보여 드린 것처럼 여러분만의 제어 구조를 만드는 능력은 다음과 같은 기능에서 나옵니다.

- **Multiple parameter lists** 은 저자가 `whileTrue`에서 한 것처럼 하나의 매개변수 그룹을 테스트 조건용으로, 두 번째 그룹을 코드 블록용으로 만들 수 있게 해 줍니다.
- **By-name parameters** 또한 `whileTrue`에서 한 것처럼 메서드 내부에서 접근될 때까지 평가되지 않는 매개변수들을 받을 수 있게 해 줍니다.

비슷하게 infix notation, 고차 함수, 확장 메서드, fluent interface 같은 다른 기능들도 다른 커스텀 제어 구조와 DSL을 만들 수 있게 해 줍니다.

#### By-name parameters

이름에 의한 매개변수는 `whileTrue` 제어 구조의 중요한 부분입니다. Scala에서 `=>` 문법으로 메서드 매개변수를 정의하면,

```scala
def whileTrue(testCondition: => Boolean)(codeBlock: => Unit) =
                         -----                       -----
```

**call-by-name** 혹은 **by-name** 매개변수라고 알려진 것을 만드시는 것입니다. 이름에 의한 매개변수는 메서드 내부에서 접근될 때만 평가됩니다. 저자의 블로그 글 "How to Use By-Name Parameters in Scala"와 "Scala and Call-By-Name Parameters"에서 설명하듯이, 이 매개변수의 더 정확한 이름은 **evaluate when accessed** 입니다. 왜냐하면 바로 그것이 그들의 동작 방식이기 때문입니다. 이름에 의한 매개변수는 메서드 내부에서 접근될 때만 평가됩니다. 그 두 번째 블로그 글에서 저자가 언급한 것처럼 Rob Norris는 이름에 의한 매개변수가 `def` 메서드를 받는 것과 같다고 비교합니다.

#### Another example

`whileTrue` 예시에서는 루프를 계속 유지하기 위해 재귀 호출을 사용했지만, 더 간단한 제어 구조의 경우에는 재귀가 필요하지 않습니다. 예를 들어 두 개의 테스트 조건을 받고, 두 조건이 모두 `true`로 평가되면 제공된 코드 블록을 실행하는 제어 구조를 만들고 싶다고 가정해 봅시다. 그 제어 구조를 사용하는 표현식은 다음과 같은 모습입니다.

```scala
doubleIf(age > 18)(numAccidents == 0) { println("Discount!") }
```

이 경우 `doubleIf`를 세 개의 매개변수 목록을 받는 메서드로 정의하며, 각 매개변수는 역시 이름에 의한 매개변수입니다.

```scala
// two 'if' condition tests
def doubleIf(test1: => Boolean)(test2: => Boolean)(codeBlock: => Unit) =
    if test1 && test2 then codeBlock
```

`doubleIf`는 테스트 하나만 수행하면 되고 무한정 반복할 필요가 없기 때문에, 메서드 본문에 재귀 호출이 필요하지 않습니다. 두 테스트 조건을 그저 확인하고, 둘 다 `true`로 평가되면 `codeBlock`을 실행합니다.

### See Also

- 저자가 이 기법의 좋아하는 사용법 중 하나는 David Pollak (Apress)의 *Beginning Scala* 책에서 보여줍니다. `scala.util.Using` 객체에 의해 쓸모없어졌지만, 저자의 블로그 글 "The using Control Structure in Beginning Scala"에서 이 기법이 어떻게 동작하는지 설명합니다.
- Scala `Breaks` 클래스는 `for` 루프에서 `break`와 `continue` 기능을 구현하는 데 쓰이며, 저자는 이에 대해 "Scala: How to Use break and continue in for and while Loops"에 글을 썼습니다. `Breaks` 클래스 소스 코드는 상당히 단순하며, 제어 구조를 구현하는 또 다른 예시를 제공합니다. Scaladoc 페이지의 링크에서 그 소스 코드를 확인하실 수 있습니다.
