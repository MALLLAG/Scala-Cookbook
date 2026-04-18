# Chapter 3. Numbers and Dates

## 챕터 개요

이번 챕터에서는 Scala의 **numeric 타입**을 다루는 레시피들을 살펴보며, 더불어 Java 8에서 도입된 **Date and Time API**를 다루는 레시피들도 포함되어 있습니다.

Scala에서는 `Byte`, `Short`, `Int`, `Long`, `Char` 타입을 **integral types** 이라고 부릅니다. 이들 타입은 integer, 즉 소수점이 없는 whole number로 표현되기 때문입니다. 이 정수형 타입과 함께 `Double` 그리고 `Float` 타입까지 합쳐서 Scala의 **numeric types** 을 구성합니다. 이 숫자 타입들은 `AnyVal` trait를 확장하며, `Boolean`과 `Unit` 타입 역시 `AnyVal`을 확장합니다. Scala의 unified types 페이지에서 논의된 것처럼, 이 아홉 가지 타입은 **predefined value types** 이라고 불리며, 모두 **non-nullable** 타입입니다.

미리 정의된 값 타입들과 `AnyVal`, `Any`(그리고 `Nothing`)의 관계는 Figure 3-1에서 확인하실 수 있습니다. 이미지에서 보이는 것처럼 다음과 같은 관계가 성립합니다.

- 모든 숫자 타입은 `AnyVal`을 확장합니다.
- Scala 클래스 계층 구조의 나머지 모든 타입은 `AnyRef`를 확장합니다.

<img width="675" height="523" alt="Image" src="https://github.com/user-attachments/assets/fb00a28c-11c0-4e5a-a38c-e8c8eae93302" />

Table 3-1에서 보이듯이, Scala의 숫자 타입은 Java primitive 타입과 동일한 데이터 범위를 가집니다.

### Table 3-1: Scala 내장 숫자 타입의 데이터 범위

| 데이터 타입 | 설명 | 범위 |
|------------|------|------|
| `Char` | 16비트 unsigned Unicode 문자 | 0 ~ 65,535 |
| `Byte` | 8비트 signed 값 | -128 ~ 127 |
| `Short` | 16비트 signed 값 | -32,768 ~ 32,767 |
| `Int` | 32비트 signed 값 | -2,147,483,648 ~ 2,147,483,647 |
| `Long` | 64비트 signed 값 | -2^63 ~ 2^63 - 1 (이하 내용 참조) |
| `Float` | 32비트 IEEE 754 단정밀도 부동소수점 | 이하 내용 참조 |
| `Double` | 64비트 IEEE 754 배정밀도 부동소수점 | 이하 내용 참조 |

이 외에도 `Boolean`은 `true` 또는 `false`의 값을 가질 수 있습니다.

만약 이 책을 손에 들고 있지 않은 상황에서 정확한 데이터 범위를 알아야 한다면, Scala REPL에서 다음처럼 확인하실 수 있습니다.

```
Char.MinValue.toInt   // 0
Char.MaxValue.toInt   // 65535
Byte.MinValue         // -128
Byte.MaxValue         // +127
Short.MinValue        // -32768
Short.MaxValue        // +32767
Int.MinValue          // -2147483648
Int.MaxValue          // +2147483647
Long.MinValue         // -9,223,372,036,854,775,808
Long.MaxValue         // +9,223,372,036,854,775,807
Float.MinValue        // -3.4028235e38
Float.MaxValue        // +3.4028235e38
Double.MinValue       // -1.7976931348623157e308
Double.MaxValue       // +1.7976931348623157e308
```

이러한 기본 숫자 타입 외에도, 이번 챕터에서는 `BigInt`와 `BigDecimal` 클래스도 다룹니다.

### Underscores in Numeric Literals

Scala 2.13부터는 숫자 리터럴 값에 **underscore** 을 사용할 수 있게 되었습니다.

```scala
// Int
val x = 1_000
val x = 100_000
val x = 1_000_000

// Long (소문자 'l'도 사용 가능하지만 혼동을 줄 수 있음)
val x = 1_000_000L

// Double
val x = 1_123.45
val x = 1_123.45D
val x = 1_123.45d
val x = 1_234e2      // 123400.0

// Float
val x = 3_456.7F
val x = 3_456.7f
val x = 1_234e2F

// BigInt and BigDecimal
val x: BigInt = 1_000_000
val x: BigDecimal = 1_234.56
```

밑줄이 포함된 숫자 리터럴은 일반적인 모든 상황에서 사용할 수 있습니다.

```scala
val x = 1_000 + 1

if x > 1_000 && x < 1_000_000 then println(x)

x match
    case 1_000 => println("got 1,000")
    case _     => println("got something else")

for
    i <- 1 to 1_000
    if i > 999
do
    println(i)
```

다만 현재 사용할 수 없는 한 가지 경우는 `String`을 숫자 타입으로 casting할 때입니다.

```scala
Integer.parseInt("1_000")   // NumberFormatException
"1_000".toInt               // NumberFormatException
```

### Complex Numbers

표준 Scala 배포판에 포함된 수학 클래스보다 더 강력한 기능이 필요하신 경우에는 **Spire 프로젝트**를 살펴보시길 바랍니다. Spire에는 `Rational`, `Complex`, `Real` 같은 클래스가 포함되어 있으며, 그 밖에도 다양한 클래스가 제공됩니다.

### Dates and Times

이번 챕터의 후반부 몇 개 레시피는 Java 8에서 도입된 Date and Time API를 다룹니다. `LocalDate`, `LocalTime`, `LocalDateTime`, `Instant`, `ZonedDateTime` 같은 새로운 클래스들을 어떻게 다루는지 보여 드립니다.

---

## 3.1 Parsing a Number from a String

### Problem

`String`을 Scala의 숫자 타입 중 하나로 변환하려 하는 경우입니다.

### Solution

`String`에서 사용할 수 있는 `to*` 메서드를 사용하시면 됩니다.

```scala
"1".toByte     // Byte = 1
"1".toShort    // Short = 1
"1".toInt      // Int = 1
"1".toLong     // Long = 1
"1".toFloat    // Float = 1.0
"1".toDouble   // Double = 1.0
```

주의하실 점은, 이 메서드들이 `NumberFormatException`을 던질 수 있다는 것입니다.

```scala
"hello!".toInt   // java.lang.NumberFormatException
```

따라서 변환이 성공하면 `Some`을 반환하고 실패하면 `None`을 반환하는 `to*Option` 메서드들을 사용하시는 편이 더 나을 수 있습니다.

```scala
"1".toByteOption      // Option[Byte] = Some(1)
"1".toShortOption     // Option[Short] = Some(1)
"1".toIntOption       // Option[Int] = Some(1)
"1".toLongOption      // Option[Long] = Some(1)
"1".toFloatOption     // Option[Float] = Some(1.0)
"1".toDoubleOption    // Option[Double] = Some(1.0)
"one".toIntOption     // Option[Int] = None
```

`BigInt`와 `BigDecimal` 인스턴스도 문자열로부터 직접 생성할 수 있습니다.

```scala
val b = BigInt("1")          // BigInt = 1
val b = BigDecimal("1.234")  // BigDecimal = 1.234
```

그리고 이 역시 `NumberFormatException`을 던질 수 있습니다.

```scala
val b = BigInt("yo")        // NumberFormatException
val b = BigDecimal("dude!") // NumberFormatException
```

#### Handling a base and radix with Int

10진법 이외의 진법을 사용해서 계산을 수행해야 하는 경우에는, 다음 예시처럼 `java.lang.Integer` 클래스의 `parseInt` 메서드를 사용하실 수 있습니다.

```scala
Integer.parseInt("1", 2)     // Int = 1
Integer.parseInt("10", 2)    // Int = 2
Integer.parseInt("100", 2)   // Int = 4
Integer.parseInt("1", 8)     // Int = 1
Integer.parseInt("10", 8)    // Int = 8
```

### Discussion

Java에서 `String`을 숫자 데이터 타입으로 변환해 보신 적이 있다면 `NumberFormatException`이 익숙하실 것입니다. 다만 Scala에는 checked exceptions가 없기 때문에, 이런 상황을 다른 방식으로 처리하고 싶을 수 있습니다.

먼저 알아 두셔야 할 점은, Scala에서는 메서드가 예외를 던질 수 있다는 것을 선언할 필요가 없다는 것입니다. 따라서 다음과 같이 메서드를 작성하는 것이 전혀 문제 되지 않습니다.

```scala
// "throws NumberFormatException"을 선언할 필요가 없음
def makeInt(s: String) = s.toInt
```

#### Writing a pure function

하지만 FP에서는 이런 식으로 작성하지 않습니다. 위와 같이 작성된 메서드는 호출자의 코드를 갑자기 중단시킬 수 있는데, 이는 FP에서 절대 하지 않는 행위입니다. (여러분 스스로 이렇게 하지 않으려 하시거나, 다른 개발자가 이렇게 하는 것을 원치 않으신다고 생각하시면 됩니다.) 대신 **pure function** 는 항상 시그니처에 명시된 타입을 반환합니다. 따라서 FP에서는 이 함수를 다음과 같이 작성하게 됩니다.

```scala
def makeInt(s: String): Option[Int] =
    try
        Some(s.toInt)
    catch
        case e: NumberFormatException => None
```

이 함수는 `Option[Int]`를 반환하도록 선언되어 있어서, `"10"`을 주면 `Some(10)`을 반환하고, `"Yo"`를 주면 `None`을 반환합니다. 이 함수는 Solution에 나온 `toIntOption`과 동등하며, 해당 기능은 Scala 2.13에서 도입되었습니다.

#### Shorter makeInt Functions

위 코드는 `Option[Int]`를 반환하는 `makeInt` 함수의 완전히 올바른 작성 방식이지만, 다음과 같이 좀 더 짧게 작성할 수도 있습니다.

```scala
import scala.util.Try
def makeInt(s: String): Option[Int] = Try(s.toInt).toOption
```

이 함수도 이전의 `makeInt` 함수와 마찬가지로 항상 `Some[Int]` 또는 `None`을 반환합니다.

```scala
makeInt("a")             // None
makeInt("1")             // Some(1)
makeInt("2147483647")    // Some(2147483647)
makeInt("2147483648")    // None
```

만약 함수에서 `Option` 대신 `Try`를 반환하고 싶으시다면, 다음과 같이 작성하실 수 있습니다.

```scala
import scala.util.{Try, Success, Failure}
def makeInt(s: String): Try[Int] = Try(s.toInt)
```

`Try`를 사용하면 좋은 점은, 실패가 발생했을 때 그 실패 이유가 `Failure` 객체 내부에 담겨 반환된다는 점입니다.

```scala
makeInt("1")  // Success(1)
makeInt("a")  // Failure(java.lang.NumberFormatException: For input string: "a")
```

#### Document methods that throw exceptions

요즘 저는 예외를 던지는 메서드를 선호하지 않지만, 어떤 이유로든 예외를 던져야 한다면 그 동작을 문서화하는 편을 선호합니다. 따라서 예외가 발생할 수 있도록 허용하실 거라면, 메서드에 `@throws` Scaladoc 주석을 추가하는 것을 고려하시길 권해 드립니다.

```scala
@throws(classOf[NumberFormatException])
def makeInt(s: String) = s.toInt
```

이 접근 방식은 해당 메서드가 Java 코드에서 호출될 경우 **필수적으로** 요구됩니다. 이에 관한 자세한 내용은 Recipe 22.7, "Adding Exception Annotations to Scala Methods"에서 설명됩니다.

### See Also

- Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"에서 `Option`, `Some`, `None`의 사용 방법에 대한 자세한 내용을 확인하실 수 있습니다.

---

## 3.2 Converting Between Numeric Types (Casting)

### Problem

어떤 숫자 타입을 다른 숫자 타입으로 변환하고자 합니다. 예를 들어 `Int`에서 `Double`로, `Double`에서 `Int`로, 또는 `BigInt`나 `BigDecimal`과 관련된 변환이 필요한 경우입니다.

### Solution

숫자 값은 일반적으로 `to*` 계열의 메서드들을 사용해서 한 타입에서 다른 타입으로 변환합니다. 여기에는 `toByte`, `toChar`, `toDouble`, `toFloat`, `toInt`, `toLong`, `toShort`가 포함됩니다. 이런 메서드들은 기본 숫자 타입에 `RichDouble`, `RichInt`, `RichFloat` 등의 클래스에 의해 추가되며, 이들은 `scala.Predef`에 의해 자동으로 scope에 포함됩니다.

<img width="676" height="189" alt="Image" src="https://github.com/user-attachments/assets/998cb9ac-5e56-4789-b37a-b338e5836788" />

이 방향으로 캐스팅하는 방법을 몇 가지 예시로 보여 드리겠습니다.

```scala
val b: Byte = 1
b.toShort      // Short = 1
b.toInt        // Int = 1
b.toLong       // Long = 1
b.toFloat      // Float = 1.0
b.toDouble     // Double = 1.0
```

이렇게 자연스러운 with the flow을 따라가면 변환이 간단합니다. 반대 방향으로, 즉 흐름을 거슬러 올라가는 변환도 가능합니다. 다음과 같이 작성할 수 있습니다.

```scala
val d = 100.0   // Double = 100.0
d.toFloat       // Float = 100.0
d.toLong        // Long = 100
d.toInt         // Int = 100
d.toShort       // Short = 100
d.toByte        // Byte = 100
```

그러나 이 방향으로 진행할 때 심각한 문제가 발생할 수 있다는 점을 알아 두셔야 합니다.

```scala
val d = Double.MaxValue   // 1.7976931348623157E308

// 의도적 오류: 이런 행위는 하지 마시길 바랍니다
d.toFloat   // Float = Infinity
d.toLong    // Long = 9223372036854775807
d.toInt     // Int = 2147483647
d.toShort   // Short = -1
d.toByte    // Byte = -1
```

따라서 이러한 메서드를 사용하기 전에는, 변환 시도가 유효한지 항상 먼저 확인해 보셔야 합니다.

```scala
val d: Double = 65_535.0
d.isValidByte    // false (Byte 범위는 -128 ~ 127)
d.isValidChar    // true  (Char 범위는 0 ~ 65,535)
d.isValidShort   // false (Short 범위는 -32,768 ~ 32,767)
d.isValidInt     // true  (Int 범위는 -2,147,483,648 ~ 2,147,483,647)
```

다만 이러한 메서드들은 `Double` 값에서 사용할 수 없다는 점에 유의하시길 바랍니다.

```scala
d.isValidFloat   // Double의 멤버가 아님
d.isValidLong    // Double의 멤버가 아님
```

또한 예상하시는 것처럼, `Double` 값에 0이 아닌 소수 부분이 있는 경우에는 `Int`/`Short`/`Byte` 테스트가 실패하게 됩니다.

```scala
val d = 1.5      // Double = 1.5
d.isValidInt     // false
d.isValidShort   // false
d.isValidByte    // false
```

#### asInstanceOf

필요에 따라 `asInstanceOf`를 사용하여 "흐름에 따라" 캐스팅할 수도 있습니다.

```scala
val b: Byte = 1          // Byte = 1
b.asInstanceOf[Short]    // Short = 1
b.asInstanceOf[Int]      // Int = 1
b.asInstanceOf[Long]     // Long = 1
b.asInstanceOf[Float]    // Float = 1.0
b.asInstanceOf[Double]   // Double = 1.0
```

### Discussion

이러한 숫자 타입들이 모두 (원시 값이 아닌) 클래스이기 때문에, `BigInt`와 `BigDecimal`도 비슷한 방식으로 동작합니다. 다음 예시들은 이 두 클래스가 숫자 값 타입들과 어떻게 동작하는지 보여 줍니다.

#### BigInt

`BigInt` 생성자는 overloaded되어 있어서, 아홉 가지 다른 방식으로 `BigInt`를 생성할 수 있습니다. `Int`, `Long`, 또는 `String`을 넘겨서 생성하는 방식도 포함됩니다.

```scala
val i: Int = 101
val l: Long = 102
val s = "103"

val b1 = BigInt(i)    // BigInt = 101
val b2 = BigInt(l)    // BigInt = 102
val b3 = BigInt(s)    // BigInt = 103
```

`BigInt`에도 `BigInt` 값을 숫자 타입으로 캐스팅하도록 도와 주는 `isValid*` 및 `to*` 메서드들이 있습니다.

- `isValidByte`, `toByte`
- `isValidChar`, `toChar`
- `isValidDouble`, `toDouble`
- `isValidFloat`, `toFloat`
- `isValidInt`, `toInt`
- `isValidLong`, `toLong`
- `isValidShort`, `toShort`

#### BigDecimal

마찬가지로, `BigDecimal`도 다음과 같이 여러 가지 방식으로 생성할 수 있습니다.

```scala
BigDecimal(100)
BigDecimal(100L)
BigDecimal(100.0)
BigDecimal(100F)
BigDecimal("100")
BigDecimal(BigInt(100))
```

`BigDecimal`은 다른 타입들이 가지고 있는 `isValid*` 및 `to*` 메서드들을 모두 가지고 있습니다. 또한 다음과 같이 동작하는 `to*Exact` 메서드들도 가지고 있습니다.

```scala
BigDecimal(100).toBigIntExact      // Some(100)
BigDecimal(100.5).toBigIntExact    // None

BigDecimal(100).toIntExact         // Int = 100
BigDecimal(100.5).toIntExact       // java.lang.ArithmeticException:
                                   //     (Rounding necessary)
BigDecimal(100.5).toLongExact      // java.lang.ArithmeticException
BigDecimal(100.5).toByteExact      // java.lang.ArithmeticException
BigDecimal(100.5).toShortExact     // java.lang.ArithmeticException
```

더 많은 메서드는 `BigInt` Scaladoc과 `BigDecimal` Scaladoc을 참고하시길 바랍니다.

---

## 3.3 Overriding the Default Numeric Type

### Problem

implicit type declaration 스타일을 사용할 때 Scala는 숫자 값에 따라 자동으로 타입을 할당하는데, 숫자 필드를 생성할 때 이 기본 타입을 override해야 하는 경우입니다.

### Solution

`1`을 타입을 명시적으로 선언하지 않고 변수에 할당하면, Scala는 이 변수에 `Int` 타입을 할당합니다.

```
scala> val a = 1
a: Int = 1
```

따라서 타입을 제어하셔야 할 때에는, 명시적으로 선언해 주시면 됩니다.

```scala
val a: Byte = 1     // Byte = 1
val a: Short = 1    // Short = 1
val a: Int = 1      // Int = 1
val a: Long = 1     // Long = 1
val a: Float = 1    // Float = 1.0
val a: Double = 1   // Double = 1.0
```

저는 이 스타일을 선호하지만, 다음과 같이 표현식의 끝에 타입을 지정하는 방식도 문법적으로 허용됩니다.

```scala
val a = 0: Byte
val a = 0: Int
val a = 0: Short
val a = 0: Double
val a = 0: Float
```

`long`, `double`, `float`의 경우에는 다음과 같은 스타일도 사용할 수 있습니다.

```scala
val a = 1l   // Long = 1
val a = 1L   // Long = 1
val a = 1d   // Double = 1.0
val a = 1D   // Double = 1.0
val a = 1f   // Float = 1.0
val a = 1F   // Float = 1.0
```

hex 값을 생성하시려면 숫자 앞에 `0x` 또는 `0X`를 붙이면 되고, 이를 `Int`나 `Long`으로 저장하실 수 있습니다.

```scala
val a = 0x20    // Int = 32
val a = 0x20L   // Long = 32
```

### Discussion

이 접근 방식은 어떤 객체 인스턴스를 생성하든 알아 두시면 유용합니다. 일반적인 문법은 다음과 같습니다.

```scala
// 일반적 형태
var [name]: [Type] = [initial value]

// 예시
var a: Short = 0
```

이 형태는 클래스 내부에서 `var` 필드를 초기화해야 할 때 유용합니다.

```scala
class Foo:
    var a: Short = 0     // 기본값 지정
    var b: Short = _     // 기본값 0
    var s: String = _    // 기본값 null
```

위에서 보이는 것처럼, 초깃값을 할당할 때 밑줄(`_`) 문자를 placeholder로 사용하실 수 있습니다. 이 방식은 클래스 변수를 만들 때는 잘 동작하지만, 메서드 내부 같은 다른 곳에서는 동작하지 않습니다. 숫자 타입의 경우 이는 문제가 되지 않습니다 (값에 0을 할당하면 되기 때문입니다). 하지만 다른 대부분의 타입에서 실제로 null 값을 원하시면, 메서드 내부에서 이 방식을 사용하실 수 있습니다.

```scala
var name = null.asInstanceOf[String]
```

다만 일반적인 경고가 적용됩니다. null 값은 사용하지 마시길 바랍니다. Play Framework 같은 우수한 Scala 라이브러리와 프레임워크에서 볼 수 있듯이, `Option/Some/None` 패턴을 사용하는 것이 더 좋습니다. 이 중요한 주제에 관한 논의는 Recipe 24.5, "Eliminating null Values from Your Code"와 Recipe 24.6, "Using Scala's Error-Handling Types (Option, Try, and Either)"를 참고하시길 바랍니다.

#### Type Ascription

드물게 **type ascription**이라는 기법을 활용해야 하는 경우가 있으며, 그 문법은 다음 예시와 같은 모습입니다. Stack Overflow의 "What Is the Purpose of Type Ascriptions in Scala?" 질문은 `String`을 `Object`로 upcast하는 것이 유리한 경우를 보여 줍니다.

```scala
val s = "Hala"     // s: String = Hala
val o = s: Object  // o: Object = Hala
```

보시는 것처럼, 문법은 이 레시피와 유사합니다. 이런 업캐스팅은 type ascription라고 하며, Scala style guide on types는 이를 다음과 같이 설명합니다.

"Ascription은 기본적으로 type checker를 위해 컴파일 시점에 수행되는 up-cast입니다. 자주 사용되지는 않지만 가끔 사용됩니다. 가장 자주 보이는 ascription 사례는 단일 `Seq` 매개변수를 사용하여 vararg 메서드를 호출하는 것입니다. 이때는 `_*` 타입을 ascribe하면 됩니다."

---

## 3.4 Replacements for ++ and --

### Problem

다른 언어에 있는 `++`와 `--` 같은 연산자로 숫자를 increment하거나 decrement하고 싶지만, Scala에는 이러한 연산자가 없습니다.

### Solution

`val` 필드는 **immutable** 이기 때문에 증가시키거나 감소시킬 수 없지만, `var Int` 필드는 `+=` 및 `-=` 메서드로 변경할 수 있습니다.

```scala
var a = 1   // a = 1
a += 1      // a = 2
a -= 1      // a = 1
```

추가적인 이점으로, 곱셈과 나눗셈에도 비슷한 메서드를 사용하실 수 있습니다.

```scala
var i = 1   // i = 1
i *= 4      // i = 4
i /= 2      // i = 2
```

`val` 필드에 이 접근 방식을 사용하려 하면 compile-time error가 발생합니다.

```
scala> val x = 1
x: Int = 1

scala> x += 1
<console>:9: error: value += is not a member of Int
              x += 1
                ^
```

### Discussion

이 접근 방식의 또 다른 이점은 `Int` 이외의 숫자 타입에서도 이런 연산자들을 사용할 수 있다는 점입니다. 예를 들어 `Double`과 `Float` 클래스도 같은 방식으로 사용하실 수 있습니다.

```scala
var d = 1.2   // Double = 1.2
d += 1        // 2.2
d *= 2        // 4.4
d /= 2        // 2.2

var f = 1.2F  // Float = 1.2
f += 1        // 2.2
f *= 2        // 4.4
f /= 2        // 2.2
```

---

## 3.5 Comparing Floating-Point Numbers

### Problem

두 개의 floating-point 숫자를 비교해야 하는데, 다른 몇몇 프로그래밍 언어와 마찬가지로 **should be equivalent** 두 부동소수점 숫자가 그렇지 않을 수 있는 경우입니다.

### Solution

부동소수점 숫자를 다루기 시작하시면, `0.1` 더하기 `0.1`이 `0.2`라는 것을 금방 알게 되십니다.

```
scala> 0.1 + 0.1
res0: Double = 0.2
```

그러나 `0.1` 더하기 `0.2`는 정확히 `0.3`이 아닙니다.

```
scala> 0.1 + 0.2
res1: Double = 0.30000000000000004
```

이러한 부정확성은 두 부동소수점 숫자의 비교를 상당히 큰 문제로 만듭니다.

```scala
val a = 0.3           // Double = 0.3
val b = 0.1 + 0.2     // Double = 0.30000000000000004
a == b                // false
```

이 문제에 대한 해결책은 **tolerance** 를 가지고 부동소수점 숫자를 비교하는 자체 함수를 작성하는 것입니다. 다음의 `approximately equals` 메서드는 이 접근 방식을 보여 드립니다.

```scala
import scala.annotation.targetName
@targetName("approxEqual")
def ~=(x: Double, y: Double, tolerance: Double): Boolean =
    if (x - y).abs < tolerance then true else false
```

이 메서드를 다음과 같이 사용하실 수 있습니다.

```scala
val a = 0.3          // 0.3
val b = 0.1 + 0.2    // 0.30000000000000004
~=(a, b, 0.0001)     // true
~=(b, a, 0.0001)     // true
```

### Discussion

이 해결책에서 `@targetName` 애노테이션은 선택 사항이지만, 기호로 이루어진 symbolic method를 생성하실 때 다음과 같은 이유로 권장됩니다.

- 기호로 된 메서드 이름을 지원하지 않는 다른 언어와의 interoperability에 도움이 됩니다.
- stacktrace 사용이 쉬워지는데, 이때 제공하신 target name이 기호 이름 대신 표시되기 때문입니다.
- 문서화 시점에 기호로 된 메서드 이름의 alias으로서 대상 이름을 보여 주는 대안적인 이름을 제공합니다.

#### Extension methods

Recipe 8.9, "Adding New Methods to Closed Classes with Extension Methods"에서 보이는 것처럼, 이 메서드를 `Double` 클래스의 extension method로 정의할 수 있습니다. 허용 오차가 `0.5`라고 가정하시면, 다음과 같이 확장 메서드를 만드실 수 있습니다.

```scala
extension (x: Double)
    def ~=(y: Double): Boolean = (x - y).abs < 0.5
```

원하신다면, 메서드의 테스트 조건을 다음과 같이 확장할 수도 있습니다.

```scala
extension (x: Double)
    def ~=(y: Double): Boolean =
        if (x - y).abs < 0.5 then true else false
```

어느 방식이든, `Double` 값과 함께 다음과 같이 사용하실 수 있습니다.

```scala
if a ~= b then ...
```

이렇게 하면 매우 가독성 좋은 코드가 됩니다. 그러나 허용 오차를 하드코딩하기보다는, 허용 오차를 주어진 값 `x`의 percentage로 정의하는 편이 더 바람직할 것 같습니다.

```scala
extension (x: Double)
    def ~=(y: Double): Boolean =
        // +/- 10%의 변동 허용
        val xHigh = if x > 0 then x*1.1 else x*0.9
        val xLow = if x > 0 then x*0.9 else x*1.1
        if y >= xLow && y <= xHigh then true else false
```

또는 허용 오차를 메서드 매개변수로 받도록 원하신다면, 다음과 같이 확장 메서드를 정의하실 수 있습니다.

```scala
extension (x: Double)
    def ~=(y: Double, tolerance: Double): Boolean =
        if (x - y).abs < tolerance then true else false
```

그리고 다음과 같이 사용하시면 됩니다.

```scala
1.0 ~= (1.1, .2)     // true
1.0 ~= (0.9, .2)     // true

1.0 ~= (1.21, .2)    // false
1.0 ~= (0.79, .2)    // false
```

### See Also

- "What Every Computer Scientist Should Know About Floating-Point Arithmetic"
- Wikipedia의 floating-point accuracy arithmetic 페이지의 "Accuracy problems" 섹션
- Wikipedia의 arbitrary-precision arithmetic 페이지

---

## 3.6 Handling Large Numbers

### Problem

애플리케이션을 작성하고 있는데, 매우 큰 정수 또는 소수 값을 사용해야 하는 경우입니다.

### Solution

`Long`과 `Double` 타입이 충분히 크지 않다면, Scala `BigInt`와 `BigDecimal` 클래스를 사용하시면 됩니다.

```scala
val bi = BigInt(1234567890)          // BigInt = 1234567890
val bd = BigDecimal(123456.789)      // BigDecimal = 123456.789

// 숫자 리터럴에 밑줄 사용
val bi = BigInt(1_234_567_890)       // BigInt = 1234567890
val bd = BigDecimal(123_456.789)     // BigDecimal = 123456.789
```

`BigInt`와 `BigDecimal`은 Java의 `BigInteger`와 `BigDecimal` 클래스를 감싸며, Scala의 숫자 타입과 함께 사용되는 모든 연산자를 지원합니다.

```scala
bi + bi         // BigInt = 2469135780
bi * bi         // BigInt = 1524157875019052100
bi / BigInt(2)  // BigInt = 617283945
```

이들을 다른 숫자 타입으로 변환하실 수 있습니다.

```scala
// 잘못된 변환
bi.toByte       // -46
bi.toChar       // '
bi.toShort      // 722

// 올바른 변환
bi.toInt        // 1234567891
bi.toLong       // 1234567891
bi.toFloat      // 1.23456794E9
bi.toDouble     // 1.234567891E9
```

변환 오류를 피하기 위해, 먼저 테스트해 보시면 됩니다.

```scala
bi.isValidByte    // false
bi.isValidChar    // false
bi.isValidShort   // false
bi.isValidInt     // true
bi.isValidLong    // true
```

`BigInt`는 byte array로도 변환됩니다.

```scala
bi.toByteArray   // Array[Byte] = Array(73, -106, 2, -46)
```

### Discussion

`BigInt` 또는 `BigDecimal`을 사용하기 전에, `Long`과 `Double`이 처리할 수 있는 최솟값과 최댓값을 확인해 보실 수 있습니다.

```
Long.MinValue      // -9,223,372,036,854,775,808
Long.MaxValue      // +9,223,372,036,854,775,807
Double.MinValue    // -1.7976931348623157E308
Double.MaxValue    // +1.7976931348623157E308
```

필요에 따라서는, 표준 숫자 타입의 `PositiveInfinity`와 `NegativeInfinity`를 사용할 수도 있습니다.

```
scala> 1.7976931348623157E308 > Double.PositiveInfinity
res0: Boolean = false
```

#### BigDecimal Is Often Used for Currency

`BigDecimal`은 rounding behavior을 제어할 수 있기 때문에 currency 표현에 자주 사용됩니다. 이전 레시피에서 보이는 것처럼, `Double`을 사용하여 `$0.10 + $0.20`을 더하면 정확하게 `$0.30`이 되지 않습니다.

```
0.10 + 0.20   // Double = 0.30000000000000004
```

그러나 `BigDecimal`에는 이 문제가 없습니다.

```
BigDecimal(0.10) + BigDecimal(0.20)   // BigDecimal = 0.3
```

다만, `Double` 값으로 `BigDecimal` 값을 만들면 여전히 문제가 발생할 수 있습니다.

```
BigDecimal(0.1 + 0.2)   // BigDecimal = 0.30000000000000004
BigDecimal(.2 * .7)     // BigDecimal = 0.13999999999999999
```

따라서 원하시는 정확한 결과를 얻으려면 항상 `BigDecimal` 생성자의 `String` 버전을 사용하시는 것이 권장됩니다.

```
BigDecimal("0.1") + BigDecimal("0.2")   // BigDecimal = 0.3
BigDecimal("0.2") * BigDecimal("0.7")   // BigDecimal = 0.14
```

Joshua Bloch는 *Effective Java* (Addison-Wesley)에서 다음과 같이 말합니다. "금전 계산에는 `BigDecimal`, `int`, 또는 `long`을 사용하십시오."

### See Also

- Baeldung의 "BigDecimal and BigInteger in Java" 글에는 Scala의 `BigDecimal`과 `BigInt` 클래스가 감싸고 있는 Java 클래스들에 대한 많은 상세 정보가 있습니다.
- 이 데이터 타입들을 데이터베이스에 저장해야 하는 경우, 다음 페이지들이 도움이 될 것입니다.
  - Stack Overflow 페이지: "How to Insert BigInteger in Prepared Statement Java"
  - Stack Overflow 페이지: "Store BigInteger into MySql"
- "Unpredictability of the BigDecimal(double) Constructor" Stack Overflow 페이지에서는 Java에서 `double`을 `BigDecimal`에 전달할 때의 문제에 대해 논의합니다.

---

## 3.7 Generating Random Numbers

### Problem

애플리케이션을 테스트하거나, 시뮬레이션을 수행하는 등 다양한 상황에서 random number를 생성해야 하는 경우입니다.

### Solution

`scala.util.Random` 클래스로 난수를 생성하시면 됩니다. 다음 예시들은 일반적인 난수 사용 사례를 보여 줍니다.

```scala
val r = scala.util.Random

// 난수 정수
r.nextInt      // 455978773
r.nextInt      // -1837771511

// 0.0 ~ 1.0 사이의 값 반환
r.nextDouble   // 0.22095085955974536
r.nextDouble   // 0.3349793259700605

// 0.0 ~ 1.0 사이의 값 반환
r.nextFloat    // 0.34705013
r.nextFloat    // 0.79055405

// 새 Random을 생성할 때 시드(seed) 설정
val r = scala.util.Random(31)

// 이미 Random 인스턴스가 있을 때 시드 갱신
r.setSeed(1_000L)

// 정수를 최댓값으로 제한
r.nextInt(6)   // 0
r.nextInt(6)   // 5
r.nextInt(6)   // 1
```

`nextInt`에 최댓값을 설정하는 경우, 반환되는 `Int`는 `0`(포함)과 지정하신 값(제외) 사이가 됩니다. 따라서 `100`을 지정하시면 `0`부터 `99`까지의 `Int`가 반환됩니다.

### Discussion

이 섹션에서는 `Random` 클래스로 할 수 있는 다른 유용한 기능들을 몇 가지 보여 드립니다.

#### Random length ranges

Scala에서는 무작위 길이의 숫자 range를 만들기가 쉽습니다. 이는 특히 테스트에 유용합니다.

```scala
// 무작위 길이 범위
0 to r.nextInt(10)   // Range 0 to 9
0 to r.nextInt(10)   // Range 0 to 3
0 to r.nextInt(10)   // Range 0 to 7
```

필요하시면 항상 `Range`를 sequence로 변환할 수 있음을 기억하시길 바랍니다.

```scala
// 결과 리스트 크기는 무작위가 됨
(0 to r.nextInt(10)).toList         // List(0, 1, 2, 3, 4)
(0 to r.nextInt(10)).toList         // List(0, 1, 2)

// 무작위 크기의 LazyList
(0 to r.nextInt(1_000_000)).to(LazyList)
    // result: LazyList[Int] = LazyList(<not computed>)
```

`for/yield` 루프는 시퀀스의 값을 수정하는 멋진 방법을 제공합니다.

```scala
for i <- 0 to r.nextInt(10) yield i * 10
```

이 접근 방식은 다음과 같은 시퀀스를 생성합니다.

```
Vector(0, 10, 20, 30)
Vector(0, 10)
Vector(0, 10, 20, 30, 40, 50, 60, 70, 80)
```

#### Fixed-length ranges with random values

다른 방법으로는 길이가 정해진 시퀀스를 만들고 그 안에 난수로 채우는 접근 방식도 있습니다.

```scala
val seq = for i <- 1 to 5 yield r.nextInt(100)
```

이 방식은 다음과 같이 다섯 개의 무작위 정수로 구성된 시퀀스를 만듭니다.

```
Vector(99, 6, 40, 77, 19)
Vector(1, 75, 87, 55, 39)
Vector(46, 40, 4, 82, 92)
```

`nextFloat`와 `nextDouble`로도 같은 작업을 하실 수 있습니다.

```scala
val floats = for i <- 1 to 5 yield r.nextFloat()
val doubles = for i <- 1 to 5 yield r.nextDouble()
```

#### Shuffling an existing sequence

또 다른 흔한 필요성은 기존 시퀀스를 "randomize"하는 것입니다. 이를 위해서는 `Random` 클래스의 `shuffle` 메서드를 사용하시면 됩니다.

```scala
import scala.util.Random
val x = List(1, 2, 3)

Random.shuffle(x)   // List(3, 1, 2)
Random.shuffle(x)   // List(2, 3, 1)
```

#### Getting a random element from a sequence

기존 시퀀스가 있는데 그중에서 단일 무작위 요소를 얻고 싶으시다면, 다음 함수를 사용하실 수 있습니다.

```scala
import scala.util.Random

def getRandomElement[A](list: Seq[A], random: Random): A =
    list(random.nextInt(list.length))
```

이 메서드를 사용하는 몇 가지 예시를 보여 드리겠습니다.

```scala
val r = scala.util.Random

// 정수
val ints = (1 to 100).toList
getRandomElement(ints, r)   // Int = 66
getRandomElement(ints, r)   // Int = 11

// 문자열
val names = List("Hala", "Helia", "Hannah", "Hope")
getRandomElement(names, r)   // Hala
getRandomElement(names, r)   // Hannah
```

---

## 3.8 Formatting Numbers and Currency

### Problem

숫자나 통화를 포매팅하여 소수점 자릿수와 구분 기호(쉼표와 소수점 등)를 제어하려는 경우입니다. 일반적으로 출력용으로 사용됩니다.

### Solution

기본적인 숫자 포매팅은 `f` 문자열 interpolator를 사용하시면 됩니다. 쉼표 추가, locale 및 currency 작업 같은 다른 요구 사항에는 `java.text.NumberFormat` 클래스의 인스턴스를 사용하시면 됩니다.

```scala
NumberFormat.getInstance           // 일반 목적 숫자(부동소수점)
NumberFormat.getIntegerInstance    // 정수
NumberFormat.getCurrencyInstance   // 통화
NumberFormat.getPercentInstance    // 백분율
```

`NumberFormat` 인스턴스는 로케일에 맞춰 커스터마이징하실 수도 있습니다.

#### The f string interpolator

Recipe 2.4, "Substituting Variables into Strings"에서 자세히 논의되는 `f` 문자열 인터폴레이터는 간단한 숫자 포매팅 기능을 제공합니다.

```scala
val pi = scala.math.Pi   // Double = 3.141592653589793
println(f"${pi}%1.5f")   // 3.14159
```

기법을 보여 주는 추가 예시들은 다음과 같습니다.

```scala
// 부동소수점
f"${pi}%1.2f"     // String = 3.14
f"${pi}%1.3f"     // String = 3.142
f"${pi}%1.5f"     // String = 3.14159
f"${pi}%6.2f"     // String = "  3.14"
f"${pi}%06.2f"    // String = 003.14

// 정수
val x = 10_000
f"${x}%d"         // 10000
f"${x}%2d"        // 10000
f"${x}%8d"        // "   10000"
f"${x}%-8d"       // "10000   "
```

문자열에서 사용할 수 있는 `format` 메서드를 명시적으로 사용하는 방식을 선호하신다면, 다음과 같이 코드를 작성하실 수 있습니다.

```scala
"%06.2f".format(pi)   // String = 003.14
```

#### Commas, locales, and integers

미국 같은 로케일에서 쉼표를 추가하는 등 정수 값을 포매팅하시려면, `NumberFormat`의 `getIntegerInstance` 메서드를 사용하시면 됩니다.

```scala
import java.text.NumberFormat
val formatter = NumberFormat.getIntegerInstance
formatter.format(10_000)      // String = 10,000
formatter.format(1_000_000)   // String = 1,000,000
```

이 결과는 제 로케일(미국 콜로라도 덴버 근처) 때문에 쉼표가 표시되지만, `getIntegerInstance`와 `Locale` 클래스로 로케일을 설정하실 수 있습니다.

```scala
import java.text.NumberFormat
import java.util.Locale

val formatter = NumberFormat.getIntegerInstance(Locale.GERMANY)
formatter.format(1_000)       // 1.000
formatter.format(10_000)      // 10.000
formatter.format(1_000_000)   // 1.000.000
```

#### Commas, locales, and floating-point values

`getInstance`가 반환하는 formatter를 사용하여 부동소수점 값을 처리하실 수 있습니다.

```scala
val formatter = NumberFormat.getInstance
formatter.format(12.34)         // 12.34
formatter.format(1_234.56)      // 1,234.56
formatter.format(1_234_567.89)  // 1,234,567.89
```

`getInstance`로 **로케일**을 설정할 수도 있습니다.

```scala
val formatter = NumberFormat.getInstance(Locale.GERMANY)
formatter.format(12.34)         // 12,34
formatter.format(1_234.56)      // 1.234,56
formatter.format(1_234_567.89)  // 1.234.567,89
```

#### Currency

통화 출력의 경우에는 `getCurrencyInstance` 포매터를 사용하시면 됩니다. 미국에서의 기본 출력은 다음과 같습니다.

```scala
val formatter = NumberFormat.getCurrencyInstance
formatter.format(123.456789)    // $123.46
formatter.format(12_345.6789)   // $12,345.68
formatter.format(1_234_567.89)  // $1,234,567.89
```

국제 통화를 포매팅하시려면 `Locale`을 사용하시면 됩니다.

```scala
import java.util.{Currency, Locale}

val deCurrency = Currency.getInstance(Locale.GERMANY)
val deFormatter = java.text.NumberFormat.getCurrencyInstance
deFormatter.setCurrency(deCurrency)

deFormatter.format(123.456789)    // €123.46
deFormatter.format(12_345.6789)   // €12,345.68
deFormatter.format(1_234_567.89)  // €1,234,567.89
```

통화 라이브러리를 사용하지 않으시는 경우, `BigDecimal`을 사용하실 가능성이 큽니다. `BigDecimal`도 `getCurrencyInstance`와 함께 동작합니다. 미국에서의 기본 출력은 다음과 같습니다.

```scala
import java.text.NumberFormat
import scala.math.BigDecimal.RoundingMode

val a = BigDecimal("10000.995")          // BigDecimal = 10000.995
val b = a.setScale(2, RoundingMode.DOWN) // BigDecimal = 10000.99

val formatter = NumberFormat.getCurrencyInstance
formatter.format(b)                      // String = $10,000.99
```

다음은 로케일을 사용하는 `BigDecimal` 값의 두 가지 예시입니다.

```scala
import java.text.NumberFormat
import java.util.Locale
import scala.math.BigDecimal.RoundingMode

val b = BigDecimal("1234567.891").setScale(2, RoundingMode.DOWN)
  // result: BigDecimal = 1234567.89

val deFormatter = NumberFormat.getCurrencyInstance(Locale.GERMANY)
deFormatter.format(b)     // String = 1.234.567,89 €

val ukFormatter = NumberFormat.getCurrencyInstance(Locale.UK)
ukFormatter.format(b)     // String = £1,234,567.89
```

#### Custom formatting patterns

`DecimalFormat` 클래스를 사용하면 원하는 자신만의 포매팅 패턴을 만드실 수도 있습니다. 원하는 패턴을 만드시고 `format` 메서드로 숫자에 그 패턴을 적용하시면 됩니다. 다음 예시들이 이를 보여 줍니다.

```scala
import java.text.DecimalFormat

val df = DecimalFormat("0.##")
df.format(123.45)            // 123.45 (type = String)
df.format(123.4567890)       // 123.46
df.format(.1234567890)       // 0.12
df.format(1_234_567_890)     // 1234567890

val df = DecimalFormat("0.####")
df.format(.1234567890)       // 0.1235
df.format(1234.567890)       // 1234.5679
df.format(1_234_567_890)     // 1234567890

val df = DecimalFormat("#,###,##0.00")
df.format(123)               // 123.00
df.format(123.4567890)       // 123.46
df.format(1_234.567890)      // 1,234.57
df.format(1_234_567_890)     // 1,234,567,890.00
```

추가 포매팅 패턴 문자에 대해서는 Java `DecimalFormat` 클래스를 참고하시길 바랍니다 (그리고 일반적으로 `DecimalFormat`의 직접 인스턴스를 생성해서는 안 된다는 경고도 함께 확인하시길 바랍니다).

#### Locales

`java.util.Locale` 클래스에는 세 가지 생성자가 있습니다.

```scala
Locale(String language)
Locale(String language, String country)
Locale(String language, String country, String data)
```

`CANADA`, `CHINA`, `FRANCE`, `GERMANY`, `JAPAN`, `UK`, `US` 같은 로케일에 대한 12개 이상의 정적 인스턴스도 포함되어 있으며, 더 많은 인스턴스가 있습니다. `Locale` 상수가 없는 국가와 언어의 경우에는, 언어 또는 언어/국가 문자열 쌍을 사용하여 지정하실 수 있습니다. 예를 들어 Oracle의 JDK 10 and JRE 10 Supported Locales 페이지에 따르면, 인도의 로케일은 다음과 같이 지정하실 수 있습니다.

```scala
Locale("hi-IN", "IN")
Locale("en-IN", "IN")
```

몇 가지 다른 예시는 다음과 같습니다.

```scala
Locale("en-AU", "AU")   // Australia
Locale("pt-BR", "BR")   // Brazil
Locale("es-ES", "ES")   // Spain
```

다음 예시들은 위에서 첫 번째 인도 로케일이 어떻게 사용되는지 보여 줍니다.

```scala
// India
import java.util.{Currency, Locale}

val indiaLocale = Currency.getInstance(Locale("hi-IN", "IN"))
val formatter = java.text.NumberFormat.getCurrencyInstance
formatter.setCurrency(indiaLocale)

formatter.format(123.456789)    // ₹123.46
formatter.format(1_234.56789)   // ₹1,234.57
```

`NumberFormat`의 모든 `get*Instance` 메서드를 사용하면서, default locale을 설정할 수도 있습니다.

```scala
import java.text.NumberFormat
import java.util.Locale

val default = Locale.getDefault
val formatter = NumberFormat.getInstance(default)

formatter.format(12.34)         // 12.34
formatter.format(1_234.56)      // 1,234.56
formatter.format(1_234_567.89)  // 1,234,567.89
```

### Discussion

이 레시피는 통화 및 기타 포매팅된 숫자 필드를 출력할 때 Java의 접근 방식으로 되돌아가지만, 물론 통화 솔루션은 애플리케이션에서 통화를 어떻게 처리하는지에 따라 달라집니다. 컨설턴트로서의 제 업무를 통해 보면, 대부분의 회사가 Java `BigDecimal` 클래스를 사용해 통화를 처리하고 있으며, 다른 회사들은 자체 커스텀 통화 클래스를 만드는데, 이들 역시 일반적으로 `BigDecimal`을 감싸는 wrapper입니다.

---

## 3.9 Creating New Date and Time Instances

### Problem

Java 8에서 도입된 Date and Time API를 사용하여 새 날짜와 시간 인스턴스를 생성해야 하는 경우입니다.

### Solution

Java 8 API를 사용하여 새 날짜, 시간, 날짜/시간 값을 생성하실 수 있습니다. Table 3-2는 여러분이 사용하게 될 몇 가지 새 클래스에 대한 설명을 제공합니다 (설명은 `java.time` Javadoc에서 가져왔습니다). 이 클래스들은 모두 ISO-8601 calendar system에서 동작합니다.

### Table 3-2 내용: 일반적인 Java 8 Date and Time 클래스 설명

| 클래스 | 설명 |
|--------|------|
| `LocalDate` | 시간대 정보 없는 날짜. 예: `2007-12-03` |
| `LocalTime` | 시간대 정보 없는 시각. 예: `10:15:30` |
| `LocalDateTime` | 시간대 정보 없는 날짜-시간. 예: `2007-12-03T10:15:30` |
| `ZonedDateTime` | 시간대 정보가 포함된 날짜-시간. 예: `2007-12-03T10:15:30+01:00 Europe/Paris` |
| `Instant` | timeline 상의 단일 순간 시점을 모델링합니다. 애플리케이션에서 이벤트 타임스탬프를 기록할 때 사용될 수 있습니다. |

새 date/time 인스턴스를 생성하시려면 다음과 같이 하시면 됩니다.

- 해당 클래스들의 `now` 메서드를 사용하여 **현재 순간**을 나타내는 새 인스턴스를 생성하실 수 있습니다.
- 해당 클래스들의 `of` 메서드를 사용하여 **과거 또는 미래**의 날짜/시간 값을 나타내는 날짜를 생성하실 수 있습니다.

#### Now

현재 날짜와 시간을 나타내는 인스턴스를 생성하시려면, API의 새 클래스에서 사용할 수 있는 `now` 메서드를 사용하시면 됩니다.

```scala
import java.time.*

LocalDate.now       // 2019-01-20
LocalTime.now       // 12:19:26.270
LocalDateTime.now   // 2019-01-20T12:19:26.270
Instant.now         // 2019-01-20T19:19:26.270Z
ZonedDateTime.now   // 2019-01-20T12:44:53.466-07:00[America/Denver]
```

이 메서드들의 결과는 각 타입에 어떤 데이터가 저장되는지를 잘 보여 줍니다.

#### Past or future

과거나 미래의 날짜와 시간을 생성하시려면, 위에서 보여 드린 각 클래스의 `of` factory method를 사용하시면 됩니다. 예를 들어 다음은 `java.time.LocalDate` 인스턴스를 `of` 팩토리 메서드로 생성하는 몇 가지 방법입니다.

```scala
val squirrelDay = LocalDate.of(2020, 1, 21)
val squirrelDay = LocalDate.of(2020, Month.JANUARY, 21)
val squirrelDay = LocalDate.of(2020, 1, 1).plusDays(20)
```

`LocalDate`에서 January은 `0`이 아닌 `1`로 표시된다는 점에 유의하시길 바랍니다.

`java.time.LocalTime`에는 다음을 포함하여 다섯 개의 `of*` 팩토리 메서드가 있습니다.

```scala
LocalTime.of(hour: Int, minute: Int)
LocalTime.of(hour: Int, minute: Int, second: Int)

LocalTime.of(0, 0)    // 00:00
LocalTime.of(0, 1)    // 00:01
LocalTime.of(1, 1)    // 01:01
LocalTime.of(23, 59)  // 23:59
```

다음의 의도적인 예외들은 minute과 hour의 유효한 값이 무엇인지 잘 보여 줍니다.

```scala
LocalTime.of(23, 60)  // DateTimeException: Invalid value for MinuteOfHour,
                      // (valid values 0 - 59): 60

LocalTime.of(24, 1)   // DateTimeException: Invalid value for HourOfDay,
                      // (valid values 0 - 23): 24
```

`java.time.LocalDateTime`에는 다음을 포함하여 아홉 개의 `of*` 팩토리 메서드 생성자가 있습니다.

```scala
LocalDateTime.of(year: Int, month: Int, dayOfMonth: Int, hour: Int, minute: Int)
LocalDateTime.of(year: Int, month: Month, dayOfMonth: Int, hour: Int, minute: Int)
LocalDateTime.of(date: LocalDate, time: LocalTime)
```

`java.time.ZonedDateTime`에는 다음을 포함하여 일곱 개의 `of*` 팩토리 메서드 생성자가 있습니다.

```scala
of(int year, int month, int dayOfMonth, int hour, int minute, int second,
    int nanoOfSecond, ZoneId zone)
of(LocalDate date, LocalTime time, ZoneId zone)
of(LocalDateTime localDateTime, ZoneId zone)
ofInstant(Instant instant, ZoneId zone)
```

다음은 두 번째 메서드를 사용하는 예시입니다.

```scala
val zdt = ZonedDateTime.of(
    LocalDate.now,
    LocalTime.now,
    ZoneId.of("America/New_York")
)

// result: 2021-01-01T20:38:57.590542-05:00[America/New_York]
```

그와 관련해서 몇 가지 다른 `java.time.ZoneId` 값들은 다음과 같습니다.

```scala
ZoneId.of("Europe/Paris")        // java.time.ZoneId = Europe/Paris
ZoneId.of("Asia/Tokyo")          // java.time.ZoneId = Asia/Tokyo
ZoneId.of("America/New_York")    // java.time.ZoneId = America/New_York

// UTC(그리니치) 시간으로부터의 오프셋(offset)
ZoneId.of("UTC+1")               // java.time.ZoneId = UTC+01:00
```

`java.time.Instant`에는 세 개의 `of*` 팩토리 메서드가 있습니다.

```scala
Instant.ofEpochMilli(epochMilli: Long)
Instant.ofEpochSecond(epochSecond: Long)
Instant.ofEpochSecond(epochSecond: Long, nanoAdjustment: Long)

Instant.ofEpochMilli(100)   // Instant = 1970-01-01T00:00:00.100Z
```

`Instant` 클래스는 두 instant 사이의 시간 지속 duration을 계산하는 기능을 제공한다는 점을 비롯해 여러 이유로 유용합니다.

```scala
import java.time.{Instant, Duration}

val start = Instant.now                   // Instant = 2021-01-02T03:41:20.067769Z
Thread.sleep(2_000)
val stop = Instant.now                    // Instant = 2021-01-02T03:41:22.072429Z
val delta = Duration.between(start, stop) // Duration = PT2.00466S
delta.toMillis                            // Long = 2004
delta.toNanos                             // Long = 2004660000
```

---

## 3.10 Calculating the Difference Between Two Dates

### Problem

두 날짜 사이의 차이를 알아내야 하는 경우입니다.

### Solution

두 날짜 사이의 **days** 수를 알아내야 하는 경우에는, `java.time.temporal.ChronoUnit` 클래스의 `DAYS` enum 상수가 가장 쉬운 해결책입니다.

```scala
import java.time.LocalDate
import java.time.temporal.ChronoUnit.DAYS

val now  = LocalDate.of(2019,  1, 20)   // 2019-01-20
val xmas = LocalDate.of(2019, 12, 25)   // 2019-12-25

DAYS.between(now, xmas)                 // Long = 339
```

두 날짜 사이의 **years** 수 또는 **months** 수가 필요하시다면, `ChronoUnit`의 `YEARS`와 `MONTHS` enum 상수를 사용하실 수 있습니다.

```scala
import java.time.LocalDate
import java.time.temporal.ChronoUnit.*

val now = LocalDate.of(2019,  1, 20)                // 2019-01-20
val nextXmas = LocalDate.of(2020, 12, 25)           // 2020-12-25

val years: Long  = YEARS.between(now, nextXmas)     // 1
val months: Long = MONTHS.between(now, nextXmas)    // 23
val days: Long   = DAYS.between(now, nextXmas)      // 705
```

같은 `LocalDate` 값을 사용해서 `Period` 클래스도 사용할 수 있지만, `ChronoUnit`과 `Period` 접근 방식 간의 출력에 상당한 차이가 있다는 점을 주목해 주시길 바랍니다.

```scala
import java.time.Period

val diff = Period.between(now, nextXmas)    // P1Y11M5D
diff.getYears                               // 1
diff.getMonths                              // 11
diff.getDays                                // 5
```

### Discussion

`ChronoUnit` 클래스의 `between` 메서드는 두 개의 `Temporal` 인수를 받습니다.

```scala
between(temporal1Inclusive: Temporal, temporal2Exclusive: Temporal)
```

따라서 `Instant`, `LocalDate`, `LocalDateTime`, `LocalTime`, `ZonedDateTime` 등을 포함하여 모든 `Temporal` 하위 클래스와 함께 동작합니다. 다음은 `LocalDateTime` 예시입니다.

```scala
import java.time.LocalDateTime
import java.time.temporal.ChronoUnit

// of(year, month, dayOfMonth, hour, minute)
val d1 = LocalDateTime.of(2020, 1, 1, 1, 1)
val d2 = LocalDateTime.of(2063, 4, 5, 1, 1)

ChronoUnit.DAYS.between(d1, d2)     // Long = 15800
ChronoUnit.YEARS.between(d1, d2)    // Long = 43
ChronoUnit.MINUTES.between(d1, d2)  // Long = 22752000
```

`ChronoUnit` 클래스에는 이 외에도 `CENTURIES`, `DECADES`, `HOURS`, `MICROS`, `MILLIS`, `SECONDS`, `WEEKS`, `YEARS` 등을 포함한 많은 enum 상수가 있습니다.

---

## 3.11 Formatting Dates

### Problem

원하는 형식으로 날짜를 출력해야 하는 경우입니다.

### Solution

`java.time.format.DateTimeFormatter` 클래스를 사용하시면 됩니다. 이 클래스는 날짜/시간 값을 출력하기 위한 세 가지 유형의 포매터를 제공합니다.

- Predefined formatters
- Locale formatters
- 자신만의 커스텀 포매터를 만드는 기능

#### Predefined formatters

`DateTimeFormatter`는 사용 가능한 15개의 미리 정의된 포매터를 제공합니다. 다음 예시는 `LocalDate`와 함께 포매터를 사용하는 방법을 보여 줍니다.

```scala
import java.time.LocalDate
import java.time.format.DateTimeFormatter
val d = LocalDate.now   // 2021-02-04

val f = DateTimeFormatter.BASIC_ISO_DATE
f.format(d)             // 20210204
```

다음 예시는 다른 날짜 포매터들이 어떤 모습인지 보여 줍니다.

```
ISO_LOCAL_DATE       // 2021-02-04
ISO_DATE             // 2021-02-04
BASIC_ISO_DATE       // 20210204
ISO_ORDINAL_DATE     // 2021-035
ISO_WEEK_DATE        // 2021-W05-4
```

#### Locale formatters

다음 정적 `DateTimeFormatter` 메서드를 사용하여 로케일 포매터를 생성하실 수 있습니다.

- `ofLocalizedDate`
- `ofLocalizedTime`
- `ofLocalizedDateTime`

로컬라이즈된 날짜를 생성하실 때에는 네 개의 `java.time.format.FormatStyle` 값 중 하나도 함께 적용합니다.

- `SHORT`
- `MEDIUM`
- `LONG`
- `FULL`

다음 예시는 `LocalDate`와 `FormatStyle.FULL`로 `ofLocalizedDate`를 사용하는 방법을 보여 줍니다.

```scala
import java.time.LocalDate
import java.time.format.{DateTimeFormatter, FormatStyle}

val d = LocalDate.of(2021, 1, 1)
val f = DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL)
f.format(d)    // Friday, January 1, 2021
```

같은 기법을 사용하면, 네 가지 포맷 스타일은 다음과 같은 모습이 됩니다.

```
SHORT    // 1/1/21
MEDIUM   // Jan 1, 2021
LONG     // January 1, 2021
FULL     // Friday, January 1, 2021
```

#### Custom patterns with ofPattern

포매팅 문자열을 직접 지정하여 커스텀 패턴도 만들 수 있습니다. 다음은 이 기법의 예시입니다.

```scala
import java.time.LocalDate
import java.time.format.DateTimeFormatter

val d = LocalDate.now   // 2021-01-01
val f = DateTimeFormatter.ofPattern("yyyy-MM-dd")
f.format(d)             // 2021-01-01
```

다른 일반적인 패턴 몇 가지는 다음과 같습니다.

```
"MM/dd/yyyy"       // 01/01/2021
"MMM dd, yyyy"     // Jan 01, 2021
"E, MMM dd yyyy"   // Fri, Jan 01 2021
```

다음 예시는 `LocalTime`을 포매팅하는 방법을 보여 줍니다.

```scala
import java.time.LocalTime
import java.time.format.DateTimeFormatter

val t = LocalTime.now
val f1 = DateTimeFormatter.ofPattern("h:mm a")
f1.format(t)    // 6:48 PM

val f2 = DateTimeFormatter.ofPattern("HH:mm:ss a")
f2.format(t)    // 18:48:33 PM
```

`LocalDateTime`을 사용하면 날짜와 시간 출력을 모두 포매팅하실 수 있습니다.

```scala
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter

val t = LocalDateTime.now
val f = DateTimeFormatter.ofPattern("MMM dd, yyyy h:mm a")
f.format(t)    // Jan 01, 2021 6:48 PM
```

사용 가능한 미리 정의된 포맷과 포매팅 패턴 문자의 전체 목록은 `DateTimeFormatter` 클래스를 참고하시길 바랍니다.

---

## 3.12 Parsing Strings into Dates

### Problem

문자열을 Java 8에서 도입된 date/time 타입 중 하나로 파싱해야 하는 경우입니다.

### Solution

문자열이 expected format에 맞는다면, 원하는 클래스의 `parse` 메서드에 그 문자열을 전달하시면 됩니다. 문자열이 예상 (기본) 형식이 아니라면, 받아들이고자 하는 형식을 정의하기 위해 formatter를 만드시면 됩니다.

#### LocalDate

다음 예시는 `java.time.LocalDate`의 기본 형식을 보여 줍니다.

```scala
import java.time.LocalDate
val d = LocalDate.parse("2020-12-10")   // LocalDate = 2020-12-10
```

`parse`에 잘못된 형식으로 문자열을 전달하려 하시면 예외가 발생합니다.

```scala
val d = LocalDate.parse("2020/12/10")   // java.time.format.DateTimeParseException
```

다른 형식의 문자열을 받아들이려면, 원하는 패턴에 대한 포매터를 만드시면 됩니다.

```scala
import java.time.format.DateTimeFormatter
val df = DateTimeFormatter.ofPattern("yyyy/MM/dd")
val d = LocalDate.parse("2020/12/10", df)   // LocalDate = 2020-12-10
```

#### LocalTime

다음 예시들은 `java.time.LocalTime`의 기본 형식을 보여 줍니다.

```scala
import java.time.LocalTime
val t = LocalTime.parse("01:02")     // 01:02
val t = LocalTime.parse("13:02:03")  // 13:02:03
```

각 필드는 반드시 앞자리에 `0`을 붙여야 한다는 점에 유의하시길 바랍니다.

```scala
val t = LocalTime.parse("1:02")      // java.time.format.DateTimeParseException
val t = LocalTime.parse("1:02:03")   // java.time.format.DateTimeParseException
```

다음 예시들은 포매터를 사용하는 여러 방식을 보여 줍니다.

```scala
import java.time.format.DateTimeFormatter
LocalTime.parse("00:00", DateTimeFormatter.ISO_TIME)
    // 00:00
LocalTime.parse("23:59", DateTimeFormatter.ISO_LOCAL_TIME)
    // 23:59
LocalTime.parse("23 59 59", DateTimeFormatter.ofPattern("HH mm ss"))
    // 23:59:59
LocalTime.parse("11 59 59 PM", DateTimeFormatter.ofPattern("hh mm ss a"))
    // 23:59:59
```

#### LocalDateTime

다음 예시는 `java.time.LocalDateTime`의 기본 형식을 보여 줍니다.

```scala
import java.time.LocalDateTime
val s = "2021-01-01T12:13:14"
val ldt = LocalDateTime.parse(s)   // LocalDateTime = 2021-01-01T12:13:14
```

다음 예시들은 포매터를 사용하는 여러 방식을 보여 줍니다.

```scala
import java.time.LocalDateTime
import java.time.format.DateTimeFormatter

val s = "1999-12-31 23:59"
val f = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm")
val ldt = LocalDateTime.parse(s, f)
    // 1999-12-31T23:59

val s = "1999-12-31 11:59:59 PM"
val f = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss a")
val ldt = LocalDateTime.parse(s, f)
    // 1999-12-31T23:59:59
```

#### Instant

`java.time.Instant`에는 올바른 형식의 date/time 스탬프를 요구하는 `parse` 메서드가 하나만 있습니다.

```scala
import java.time.Instant
Instant.parse("1970-01-01T00:01:02.00Z")   // 1970-01-01T00:01:02Z
Instant.parse("2021-01-22T23:59:59.00Z")   // 2021-01-22T23:59:59Z
```

#### ZonedDateTime

다음 예시들은 `java.time.ZonedDateTime`의 기본 형식을 보여 줍니다.

```scala
import java.time.ZonedDateTime

ZonedDateTime.parse("2020-12-31T23:59:59-06:00")
    // ZonedDateTime = 2020-12-31T23:59:59-06:00

ZonedDateTime.parse("2020-12-31T23:59:59-00:00[US/Mountain]")
    // ZonedDateTime = 2020-12-31T16:59:59-07:00[US/Mountain]
```

다음 예시들은 `ZonedDateTime`과 함께 포매터를 사용하는 여러 방식을 보여 줍니다.

```scala
import java.time.ZonedDateTime
import java.time.format.DateTimeFormatter.*

val zdt = ZonedDateTime.parse("2021-01-01T01:02:03Z", ISO_ZONED_DATE_TIME)
    // ZonedDateTime = 2021-01-01T01:02:03Z

ZonedDateTime.parse("2020-12-31T23:59+01:00", ISO_DATE_TIME)
    // ZonedDateTime = 2020-12-31T23:59+01:00

ZonedDateTime.parse("2020-02-29T00:00-05:00", ISO_OFFSET_DATE_TIME)
    // ZonedDateTime = 2020-02-29T00:00-05:00

ZonedDateTime.parse("Sat, 29 Feb 2020 00:01:02 GMT", RFC_1123_DATE_TIME)
    // ZonedDateTime = 2020-02-29T00:01:02Z
```

부적절한 날짜(또는 부적절하게 형식이 지정된 날짜)는 예외를 던진다는 점에 유의하시길 바랍니다.

```scala
ZonedDateTime.parse("2021-02-29T00:00-05:00", ISO_OFFSET_DATE_TIME)
  // java.time.format.DateTimeParseException: Text '2021-02-29T00:00-05:00'
  // could not be parsed: Invalid date 'February 29' as '2021' is not a leap year

ZonedDateTime.parse("Fri, 29 Feb 2020 00:01:02 GMT", RFC_1123_DATE_TIME)
  // java.time.format.DateTimeParseException: Text
  // 'Fri, 29 Feb 2020 00:01:02 GMT' could not be parsed: Conflict found:
  // Field DayOfWeek 6 differs from DayOfWeek 5 derived from 2020-02-29
```
