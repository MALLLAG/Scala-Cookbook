# Chapter 12. Collections: Common Sequence Classes

## 들어가며

Scala 컬렉션을 다루는 이 장에서는 가장 흔히 사용되는 **시퀀스 클래스(sequence classes)** 들을 살펴봅니다. Recipe 11.1 "Choosing a Collections Class"에서 언급했던 것처럼, 일반적인 시퀀스 클래스 추천 사항은 다음과 같습니다.

- **`Vector`** 를 여러분의 기본(go-to) 불변(immutable) 인덱스 시퀀스(indexed sequence)로 사용하세요.
- **`List`** 를 여러분의 기본 불변 선형(linear) 시퀀스로 사용하세요.
- **`ArrayBuffer`** 를 여러분의 기본 가변(mutable) 인덱스 시퀀스로 사용하세요.
- **`ListBuffer`** 를 여러분의 기본 가변 선형 시퀀스로 사용하세요.

### Vector

Recipe 11.1 "Choosing a Collections Class"에서 논의했듯이, `Vector`는 그 전반적인 성능 특성 때문에 선호되는 불변 인덱스 시퀀스 클래스입니다. 불변 시퀀스가 필요할 때 여러분은 항상 이것을 사용하게 될 것입니다.

`Vector`는 불변이므로, 하나의 `Vector`에 필터링(filtering)과 변환(transformation) 메서드를 적용하여 또 다른 `Vector`를 만들게 됩니다. 빠르게 미리 보자면, 다음 예시들은 `Vector`를 생성하고 사용하는 방법을 보여줍니다.

```scala
val a = Vector(1, 2, 3, 4, 5)
val b = a.filter(_ > 2)   // Vector(3, 4, 5)
val c = a.map(_ * 10)     // Vector(10, 20, 30, 40, 50)
```

### List

자바(Java)에서 Scala로 넘어왔다면, 이름은 비슷하지만 Scala의 `List` 클래스가 자바의 `ArrayList` 같은 자바 `List` 클래스들과는 전혀 다르다는 것을 금방 알게 될 것입니다. Scala의 `List` 클래스는 불변이므로, 그 크기뿐만 아니라 포함하고 있는 요소들도 변경할 수 없습니다. 이것은 연결 리스트(linked list)로 구현되어 있는데, 여기서 선호되는 접근 방식은 요소를 **앞쪽에 추가(prepend)** 하는 것입니다. 연결 리스트이기 때문에 보통 리스트를 head에서 tail 방향으로 순회하며, 실제로 (`isEmpty` 메서드와 더불어) `head`와 `tail` 메서드의 관점에서 자주 생각하게 됩니다.

`Vector`와 마찬가지로 `List`도 불변이므로, 하나의 리스트에 필터링과 변환 메서드를 적용하여 또 다른 리스트를 만듭니다. 빠르게 미리 보자면, 다음 예시들은 `List`를 생성하고 사용하는 방법을 보여줍니다.

```scala
val a = List(1, 2, 3, 4, 5)
val b = a.filter(_ > 2)   // List(3, 4, 5)
val c = a.map(_ * 10)     // List(10, 20, 30, 40, 50)
```

> **List와 Vector 비교 (List Versus Vector)**
>
> 언제 `Vector` 대신 `List`를 사용해야 하는지 궁금할 수 있습니다. Recipe 11.2 "Understanding the Performance of Collections"에 자세히 설명된 성능 특성이 둘 중 무엇을 선택해야 하는지에 대한 일반적인 규칙을 제공합니다.
>
> 흥미로운 실험으로, Scala 언어의 창시자인 마틴 오더스키(Martin Odersky)는 [Scala Contributors 웹사이트의 이 스레드](https://contributors.scala-lang.org)에서, Tiark Rompf가 한때 Scala 컴파일러 안의 모든 `List`를 `Vector`로 교체하려고 시도했는데 성능이 약 10% 더 느려졌다고 언급했습니다. 이는 `Vector`가 일정한 오버헤드를 가지고 있어 작은 시퀀스에서는 덜 효율적이기 때문이라고 여겨집니다.
>
> 따라서 `List`는, 단순한 단일 연결 리스트(singly linked list)라는 그 본질을 생각해 보면 분명히 쓰임새가 있습니다. (오더스키 씨의 댓글 뒤에, Scala futures의 공동 제작자이자 자바 챔피언인 빅터 클랑(Viktor Klang)은 `List`가 훌륭한 스택(stack)이라고 생각한다고 언급합니다.)

### ArrayBuffer

`ArrayBuffer`는 선호되는 가변 인덱스 시퀀스 클래스입니다. 가변이므로, 변환 메서드를 그 위에 직접 적용하여 내용을 갱신할 수 있습니다. 예를 들어, `Vector`나 `List`에서는 `map` 메서드를 사용하고 그 결과를 새 변수에 할당합니다.

```scala
val x = Vector(1, 2, 3)
val y = x.map(_ * 2)   // y: ArrayBuffer(2, 4, 6)
```

`ArrayBuffer`에서는 `map` 대신 `mapInPlace`를 사용하면 값을 제자리에서(in place) 수정합니다.

```scala
import collection.mutable.ArrayBuffer
val ab = ArrayBuffer(1, 2, 3)
ab.mapInPlace(_ * 2)   // ab: ArrayBuffer(2, 4, 6)
```

> **버퍼 (Buffers)**
>
> Scala에서 *버퍼(buffer)* 란 단지 커지고 줄어들 수 있는 시퀀스를 말합니다.

### Array

Scala `Array`는 독특합니다. 요소들이 변경될 수 있다는 점에서 가변이지만, 크기 면에서는 불변이라서 — 커지거나 줄어들 수 없습니다. 이에 비해 `List`와 `Vector` 같은 다른 컬렉션들은 완전히 불변이고, `ArrayBuffer`는 완전히 가변입니다.

`Array`는 자바 배열에 의해 뒷받침된다(backed)는 독특한 특징을 가지고 있어서, Scala의 `Array[Int]`는 자바의 `int[]`에 의해 뒷받침됩니다.

`Array`가 Scala 예제들에서 자주 시연되긴 하지만, 추천 사항은 `Vector` 클래스를 기본 *불변* 인덱스 시퀀스 클래스로, `ArrayBuffer`를 기본 *가변* 인덱스 시퀀스로 사용하는 것입니다. 이 권장 사항에 따라, 실제 코드에서 저자는 그러한 용도로 `Vector`와 `ArrayBuffer`를 사용하고, 필요할 때 `Array`로 변환합니다.

어떤 연산들에서는 `Array`가 다른 컬렉션들보다 더 나은 성능을 낼 수 있으므로, 그것이 어떻게 동작하는지 알아 두는 것이 중요합니다. 자세한 내용은 Recipe 11.2 "Understanding the Performance of Collections"를 참고하세요.

---

## 12.1 Vector를 기본 불변 시퀀스로 만들기 (Making Vector Your Go-To Immutable Sequence)

### 문제

Scala 애플리케이션을 위해 빠르고 범용적인(general-purpose) 불변 순차 컬렉션 타입을 원합니다.

### 해법

`Vector` 클래스는 기본으로 삼을 범용 *인덱스(indexed)* 불변 순차 컬렉션으로 간주됩니다. *선형(linear)* 불변 순차 컬렉션으로 작업하는 것을 선호한다면 `List`를 사용하세요.

#### Vector 생성하기

다른 불변 시퀀스들과 마찬가지로 `Vector`를 생성하고 사용합니다. 초기 요소들을 가진 `Vector`를 생성한 다음, 인덱스로 그 요소들에 효율적으로 접근할 수 있습니다.

```scala
val v = Vector("a", "b", "c")
v(0)   // "a"
v(1)   // "b"
```

`Vector`는 인덱스 기반이므로, 다음 `x(9_999_999)` 호출은 거의 즉시 반환됩니다.

```scala
val x = (1 to 10_000_000).toVector
x(9_999_999)   // 10000000
```

또한 빈 `Vector`를 생성한 뒤 요소들을 추가할 수도 있는데, 그 결과를 새 변수에 할당하는 것을 잊지 마세요.

```scala
val a = Vector[String]()       // a: Vector[String] = Vector()
val b = a ++ List("a", "b")    // b: Vector(a, b)
```

#### 요소 추가, 뒤에 붙이기, 앞에 붙이기 (Adding, appending, prepending elements)

벡터는 수정할 수 없으므로, 기존 벡터에 요소를 추가하면서 그 결과를 새 변수에 할당합니다.

```scala
val a = Vector(1, 2, 3)
val b = a ++ List(4, 5)   // b: Vector(1, 2, 3, 4, 5)
val c = b ++ Seq(6)       // c: Vector(1, 2, 3, 4, 5, 6)
```

다른 불변 시퀀스들과 마찬가지로, 다음 메서드들을 사용하여 `Vector`에 요소를 뒤에 붙이거나(append) 앞에 붙입니다(prepend).

- `+:` 메서드는 `prepended`의 별칭(alias)입니다.
- `++:`는 `prependedAll`의 별칭입니다.
- `:+`는 `appended`의 별칭입니다.
- `:++`는 `appendedAll`의 별칭입니다.

다음은 `var` 변수를 사용하여 각 연산의 결과를 그 변수에 다시 할당하는 예시들입니다.

```scala
// prepending
var a = Vector(6)

a = 5 +: a                     // a: Vector(5, 6)
a = a.prepended(4)             // a: Vector(4, 5, 6)

a = List(2,3) ++: a            // a: Vector(2, 3, 4, 5, 6)
a = a.prependedAll(Seq(0,1))   // a: Vector(0, 1, 2, 3, 4, 5, 6)

// appending
var b = Vector(1)

b = b :+ 2                     // b: Vector(1, 2)
b = b.appended(3)              // b: Vector(1, 2, 3)

b = b :++ List(4,5)            // b: Vector(1, 2, 3, 4, 5)
b = b.appendedAll(List(6,7))   // b: Vector(1, 2, 3, 4, 5, 6, 7)
```

#### 요소 수정하기 (Modifying elements)

`Vector` 안의 한 요소를 수정하려면, `updated` 메서드를 호출하여 한 요소를 교체하면서 그 결과를 새 변수에 할당하되, `index`와 `elem` 파라미터를 설정합니다.

```scala
val a = Vector(1, 2, 3)
val b = a.updated(index=0, elem=10)   // b: Vector(10, 2, 3)
val c = b.updated(1, 20)              // c: Vector(10, 20, 3)
```

비슷하게, 한 번에 여러 요소를 교체하려면 `patch` 메서드를 사용합니다.

```scala
val a = Vector(1, 2, 3, 4, 5, 6)

// (a) 시작할 인덱스, (b) 원하는 새 시퀀스,
// (c) 교체할 요소의 개수를 지정합니다
val b = a.patch(0, List(10,20), 2)   // b: Vector(10, 20, 3, 4, 5, 6)
val b = a.patch(0, List(10,20), 3)   // b: Vector(10, 20, 4, 5, 6)
val b = a.patch(0, List(10,20), 4)   // b: Vector(10, 20, 5, 6)

val b = a.patch(2, List(30,40), 2)   // b: Vector(1, 2, 30, 40, 5, 6)
val b = a.patch(2, List(30,40), 3)   // b: Vector(1, 2, 30, 40, 6)
val b = a.patch(2, List(30,40), 4)   // b: Vector(1, 2, 30, 40)
```

`patch`를 사용하면 교체할 요소의 개수로 `0`을 지정하여 요소들을 삽입(insert)할 수 있습니다.

```scala
val a = Vector(10, 20, 30)
val b = a.patch(1, List(15), 0)   // b: Vector(10, 15, 20, 30)
val b = a.patch(2, List(25), 0)   // b: Vector(10, 20, 25, 30)
```

### 논의 (Discussion)

[구체적인 불변 컬렉션 클래스에 관한 Scala 문서](https://docs.scala-lang.org)는 다음과 같이 명시합니다.

> `Vector`는 리스트에 대한 임의 접근(random access)의 비효율성을 해결하는 컬렉션 타입이다. 벡터는 리스트의 어떤 요소든 "효율적으로(effectively)" 일정 시간(constant time)에 접근할 수 있게 해 준다…. 벡터는 빠른 임의 선택과 빠른 임의 함수형 갱신 사이에서 좋은 균형을 이루기 때문에, 현재 불변 인덱스 시퀀스의 기본 구현체로 사용된다.

"Understanding the Collections Hierarchy"(319페이지)에서 언급했듯이, `IndexedSeq`의 인스턴스를 생성하면 Scala는 `Vector`를 반환합니다.

```scala
scala> val x = IndexedSeq(1,2,3)
x: IndexedSeq[Int] = Vector(1, 2, 3)
```

그 결과, 일부 개발자들은 코드에서 인덱스 불변 시퀀스를 생성하려는 의도를 표현하면서 구현 세부 사항을 컴파일러에게 맡기기 위해, `Vector` 대신 `IndexedSeq`를 사용하는 것을 보게 됩니다.

---

## 12.2 List 생성하고 채우기 (Creating and Populating a List)

### 문제

`List`를 생성하고 채우고자 합니다.

### 해법

`List`를 생성하고 초기 데이터로 채우는 방법은 여러 가지가 있으며, 다음 코드는 여섯 가지 예시를 보여 줍니다. 두 가지 기본적이고 일반적인 사용 사례로 시작합니다.

```scala
// (1) 기본적이고 일반적인 사용 사례
val xs = List(1, 2, 3)         // List(1, 2, 3)
val xs = 1 :: 2 :: 3 :: Nil    // List(1, 2, 3)
val xs = 1 :: List(2, 3)

// (2) 두 가지 모두 빈 리스트를 생성합니다
val xs: List[String] = List()
val xs: List[String] = Nil
```

다음 예시들은 컴파일러가 `List` 타입을 암시적으로(implicitly) 설정하도록 하는 방법과, 그다음 타입을 명시적으로(explicitly) 제어하는 방법을 보여 줍니다.

```scala
// (3a) 혼합된 값을 가진 암시적·명시적 타입
val xs = List(1, 2.0, 33D, 4_000L)   // 암시적 타입 (List[AnyVal])
val xs: List[Double] = List(1, 2.0, 33D, 4_000L)   // 명시적 타입

// (3b) 명시적으로 리스트 타입을 설정하는 또 다른 예시로,
// 두 번째 예시에서는 타입을 List[Long]으로 선언합니다
val xs = List(1, 2, 3)             // List[Int] = List(1, 2, 3)
val xs: List[Long] = List(1, 2, 3) // List[Long] = List(1, 2, 3)
```

다음 예시들은 범위(range)로부터 리스트를 생성하는 여러 방법을 보여 주는데, `Int`와 `Char` 타입에서 사용 가능한 `to`와 `by` 메서드를 포함합니다(이것들은 그 타입들에 대한 암시적 변환 덕분에 가능합니다).

```scala
// (4) 범위 사용
val xs = List.range(1, 10)   // List(1, 2, 3, 4, 5, 6, 7, 8, 9)
val xs = List.range(0, 10, 2)   // List(0, 2, 4, 6, 8)

(1 to 5).toList            // List(1, 2, 3, 4, 5)
(1 until 5).toList         // List(1, 2, 3, 4)
(1 to 10 by 2).toList      // List(1, 3, 5, 7, 9)
(1 to 10 by 3).toList      // List(1, 4, 7, 10)

('a' to 'e').toList        // List(a, b, c, d, e)
('a' to 'e' by 2).toList   // List(a, c, e)
```

다음 예시들은 리스트를 채우는 다양한 방법을 보여 줍니다.

```scala
// (5) 리스트를 채우는 여러 가지 방법
val xs = List.fill(3)("foo")        // xs: List(foo, foo, foo)
val xs = List.tabulate(5)(n => n * n)   // xs: List(0, 1, 4, 9, 16)
val xs = "hello".toList                 // xs: List[Char] = List(h,e,l,l,o)

// 영숫자(alphanumeric) 문자들의 리스트 생성
val alphaNum = (('a' to 'z') ++ ('A' to 'Z') ++ ('0' to '9')).toList
    // 결과는 52개의 글자와 10개의 숫자를 포함합니다

// 10개의 출력 가능한 문자들의 리스트 생성
val r = scala.util.Random
val printableChars = (for i <- 0 to 10 yield r.nextPrintableChar).toList
    // 결과는 다음과 비슷합니다: List(=, *, W, ?, W, 1, L, <, F, d, O)
```

마지막으로, `List`를 사용하고 싶지만 데이터가 자주 변경된다면, 데이터가 변경되는 동안에는 `ListBuffer`를 사용하고, 변경이 멈추면 `List`로 변환합니다.

```scala
// (6) 데이터가 자주 변경되는 동안에는 ListBuffer를 사용합니다
import collection.mutable.ListBuffer
val a = ListBuffer(1)   // a: ListBuffer(1)
a += 2                  // a: ListBuffer(1, 2)
a += 3                  // a: ListBuffer(1, 2, 3)

// 변경이 멈추면 List로 변환합니다
val b = a.toList        // b: List(1, 2, 3)
```

`ListBuffer`는 연결 리스트로 뒷받침되는 `Buffer`입니다. 일정 시간의 prepend와 append 연산을 제공하며, 대부분의 다른 연산들은 선형 시간입니다.

### 논의 (Discussion)

Scala `List` 클래스가 자바의 `ArrayList` 같은 자바 `List` 클래스들과는 전혀 다르다는 점을 아는 것이 중요합니다. 예를 들어 Recipe 22.1 "Using Java Collections in Scala"는 `java.util.List`가 Scala `List`가 아니라 Scala `Buffer`나 `Seq`로 변환된다는 것을 보여 줍니다.

Scala의 `List`는 단순히 `Nil` 요소로 끝나는 순차 컬렉션입니다.

```scala
// 빈 리스트
val xs: List[String] = Nil     // List[String] = List()

// Nil 요소로 끝나는 세 개의 요소
val xs = 1 :: 2 :: 3 :: Nil    // List(1, 2, 3)

// 이것은 Nil로 끝나지 않으므로 에러입니다
val xs = 1 :: 2 :: 3           // error

// `List(2, 3)`의 앞에 `1`을 붙이기
val xs = 1 :: List(2, 3)       // List(1, 2, 3)
```

위에서 보듯이, `::` 메서드는 — *cons* 라고 불리며 — 두 개의 인자를 받습니다.

- *head* 요소, 즉 단일 요소
- *tail*, 즉 나머지 `List` 또는 `Nil` 값

`::` 메서드와 `Nil` 값은 리스프(Lisp) 프로그래밍 언어에 뿌리를 두고 있으며, 리스프에서는 이런 리스트가 매우 많이 사용됩니다. `List`에 관한 중요한 점은, 요소를 추가할 때 항상 *앞에 추가하는(prepend)* 방식으로 사용하도록 의도되었다는 것입니다.

```scala
val a = List(3)  // List(3)
val b = 2 :: a   // List(2, 3)
val c = 1 :: b   // List(1, 2, 3)
```

[List 클래스 Scaladoc](https://www.scala-lang.org/api/current/scala/collection/immutable/List.html)의 이 인용문은 `List` 클래스의 중요한 속성들을 논합니다.

> 이 클래스는 후입선출(LIFO, last-in-first-out), 스택 같은(stack-like) 접근 패턴에 최적이다. 임의 접근이나 FIFO 같은 다른 접근 패턴이 필요하다면, `List`보다 그 용도에 더 적합한 컬렉션을 고려하라. `List`는 O(1)의 prepend와 head/tail 접근을 가진다. 대부분의 다른 연산들은 리스트의 요소 개수에 대해 O(n)이다.

---

## 12.3 List에 요소 추가하기 (Adding Elements to a List)

### 문제

작업 중인 `List`에 요소를 추가하고자 합니다.

### 해법

"리스트에 어떻게 요소를 추가하나요?"는 약간 함정이 있는 질문입니다. 왜냐하면 `List`는 불변이므로 실제로는 요소를 추가할 수 없기 때문입니다. 계속해서 변경되는 `List`가 필요하다면, (Recipe 12.5에서 설명하듯이) `ListBuffer`를 사용한 다음 필요할 때 `List`로 변환하는 것을 고려하세요.

그 조언은 리스트 구조에서 데이터를 끊임없이 수정하는 경우에는 옳지만, 리스트를 지속적으로 갱신하는 것이 아니라 단지 몇 개의 요소를 추가하고 싶을 뿐이라면, *앞에 추가하는(prepending)* 것은 `List`에서 빠른 연산입니다. 선호되는 접근 방식은 `::` 메서드로 요소를 앞에 추가하면서 그 결과를 새 `List`에 할당하는 것입니다.

```scala
val a = List(2)             // a: List(2)

// :: 로 앞에 추가
val b = 1 :: a              // b: List(1, 2)
val c = 0 :: b              // c: List(0, 1, 2)
```

`:::` 메서드를 사용하면 한 리스트를 다른 리스트의 앞에 붙일 수도 있습니다.

```scala
val a = List(3, 4)          // a: List(3, 4)
val b = List(1, 2) ::: a    // b: List(1, 2, 3, 4)
```

prepend 연산의 결과를 새 변수에 계속 다시 할당하는 대신, 변수를 `var`로 선언하고 그 결과를 다시 할당할 수 있습니다.

```scala
var x = List(5)             // x: List[Int] = List(5)
x = 4 :: x                  // x: List(4, 5)
x = 3 :: x                  // x: List(3, 4, 5)
x = List(1, 2) ::: x        // x: List(1, 2, 3, 4, 5)
```

이 예시들이 보여 주듯이, `::`와 `:::` 메서드는 *우측 결합(right-associative)* 입니다. 이는 리스트가 오른쪽에서 왼쪽으로 구성됨을 의미하며, 다음 예시들에서 더 명확하게 볼 수 있습니다.

```scala
val a = 3 :: Nil            // a: List(3)
val b = 2 :: a              // b: List(2, 3)
val c = 1 :: b              // c: List(1, 2, 3)
val d = 1 :: 2 :: Nil       // d: List(1, 2)
```

`::`와 `:::`가 어떻게 동작하는지 분명히 하기 위해, Scala 컴파일러가 첫 번째 예시의 코드를 두 번째 예시에 나온 코드로 변환한다는 것을 알아 두면 도움이 됩니다.

```scala
List(1, 2) ::: List(3, 4)    // 여러분이 작성하는 코드
List(3, 4).:::(List(1, 2))   // 컴파일러가 그 코드를 해석하는 방식
```

둘 다 `List(1,2,3,4)`라는 결과를 냅니다.

### 논의 (Discussion)

이 스타일의 리스트 생성은 리스프 프로그래밍 언어에 뿌리를 두고 있습니다.

```scala
val x = 1 :: 2 :: 3 :: Nil   // x: List(1, 2, 3)
```

놀랍게도, 리스프는 1958년에 처음 명세화되었으며, 이것이 연결 리스트를 만드는 매우 직접적인 방식이기 때문에 이 스타일은 오늘날에도 여전히 사용됩니다.

#### prepend와 append를 위한 다른 메서드들 (Other methods to prepend, append)

`::`와 `:::`가 리스트에서 흔히 쓰이는 메서드이긴 하지만, 단일 요소를 `List`에 앞에 붙이거나 뒤에 붙일 수 있게 해 주는 추가적인 메서드들도 있습니다.

```scala
val x = List(1)

// prepend
val y = 0 +: x   // y: List(0, 1)

// append
val y = x :+ 2   // y: List(1, 2)
```

하지만 `List`에 요소를 뒤에 붙이는(append) 것은 상대적으로 느린 연산이며, 특히 큰 리스트에서는 이 접근 방식을 사용하지 않는 것이 좋다는 점을 기억하세요. [List 클래스 Scaladoc](https://www.scala-lang.org/api/current/scala/collection/immutable/List.html)이 명시하듯이, "이 클래스는 후입선출(LIFO), 스택 같은 접근 패턴에 최적이다. 임의 접근이나 FIFO 같은 다른 접근 패턴이 필요하다면, `List`보다 그 용도에 더 적합한 컬렉션을 고려하라." `List` 클래스 성능에 대한 논의는 Recipe 11.2 "Understanding the Performance of Collections"를 참고하세요.

`List` 클래스로 작업을 많이 하지 않는다면, 두 리스트를 새 리스트로 이어 붙이는 또 다른 방법은 `++` 또는 `concat` 메서드를 사용하는 것입니다.

```scala
val a = List(1, 2, 3)
val b = List(4, 5, 6)

// '++'는 'concat'의 별칭이므로 둘은 동일하게 동작합니다
val c = a ++ b        // c: List(1, 2, 3, 4, 5, 6)
val c = a.concat(b)   // c: List(1, 2, 3, 4, 5, 6)
```

이 메서드들은 불변 컬렉션 전반에 걸쳐 일관되게 사용되므로 기억하기가 더 쉬울 수 있습니다.

> **콜론(:)으로 끝나는 메서드 (Methods that end in :)**
>
> 콜론(`:`) 문자로 끝나는 모든 Scala 메서드는 오른쪽에서 왼쪽으로 평가됩니다. 이는 그 메서드가 오른쪽 피연산자에서 호출됨을 의미합니다. 다음 코드를 분석하면 이것이 어떻게 동작하는지 알 수 있는데, 두 메서드 모두 숫자 42를 출력합니다.
>
> ```scala
> @main def rightAssociativeExample =
>     val p = Printer()
>     p >> 42      // "42"를 출력
>     42 >>: p     // "42"를 출력
>
> class Printer:
>     def >>(i: Int) = println(s"$i")
>     def >>:(i: Int) = println(s"$i")
> ```
>
> 그 예시에서 보여 준 것처럼 메서드를 사용하는 것 외에도, 두 메서드는 다음과 같이 호출될 수도 있습니다.
>
> ```scala
> p.>>(42)
> p.>>:(42)
> ```
>
> 요약하면, 두 번째 메서드를 콜론으로 끝나도록 정의함으로써, 그것을 우측 결합 연산자로 사용할 수 있습니다.

---

## 12.4 List(또는 ListBuffer)에서 요소 삭제하기 (Deleting Elements from a List (or ListBuffer))

### 문제

`List`나 `ListBuffer`에서 요소를 삭제하고자 합니다.

### 해법

`List`의 요소를 필터링하려면 `filter`, `take`, `drop` 같은 메서드를 사용하고, `ListBuffer`의 요소를 삭제하려면 `-=`, `--=`, `remove` 같은 메서드를 사용합니다.

#### List

`List`는 불변이므로 그것에서 요소를 삭제할 수 없지만, 원하지 않는 요소들을 걸러내면서 그 결과를 새 변수에 할당할 수 있습니다.

```scala
val a = List(5, 1, 4, 3, 2)
val b = a.filter(_ > 2)   // b: List(5, 4, 3)
val b = a.take(2)         // b: List(5, 1)
val b = a.drop(2)         // b: List(4, 3, 2)
val b = a diff List(1)    // b: List(5, 4, 3, 2)
```

이런 연산의 결과를 새 변수에 계속 할당하는 대신, 변수를 `var`로 선언하고 그 연산 결과를 자기 자신에게 다시 할당할 수 있습니다.

```scala
var x = List(5, 1, 4, 3, 2)
x = x.filter(_ > 2)       // x: List(5, 4, 3)
```

`filter`, `partition`, `splitAt`, `take` 같은 메서드를 사용하여 컬렉션의 부분집합을 얻는 다른 방법들은 Recipe 13.7 "Using filter to Filter a Collection"을 참고하세요.

#### ListBuffer

리스트를 자주 수정하게 될 것이라면, `List` 대신 `ListBuffer`를 사용하는 것이 더 나을 수 있습니다. `ListBuffer`는 가변이므로, 다른 가변 컬렉션들과 마찬가지로 `-=`와 `--=` 메서드를 사용하여 요소를 삭제합니다. 예를 들어, 다음과 같은 `ListBuffer`를 생성했다고 가정합니다.

```scala
import scala.collection.mutable.ListBuffer
val x = ListBuffer(1, 2, 3, 4, 1, 2, 3, 4)
    // 결과: x: scala.collection.mutable.ListBuffer[Int] =
    // ListBuffer(1, 2, 3, 4, 1, 2, 3, 4)
```

`-=`를 사용하여 값으로 한 번에 하나의 요소를 삭제할 수 있습니다.

```scala
x -= 2                     // x: ListBuffer(1, 3, 4, 1, 2, 3, 4)
```

숫자 2의 첫 번째 출현만 `x`에서 제거된다는 점에 주목하세요.

`--=`를 사용하여 값으로 두 개 이상의 요소를 삭제할 수 있습니다.

```scala
val x = ListBuffer(1, 2, 3, 4, 5, 6)

// 1, 2, 3 이 제거됩니다:
x --= Seq(1,2,3)           // x: ListBuffer(4, 5, 6)

// 매칭되는 것이 없으므로 아무것도 제거되지 않습니다:
x --= Seq(8, 9)            // x: ListBuffer(4, 5, 6)
```

`remove`를 사용하여 인덱스 위치로 요소를 삭제할 수 있습니다. 인덱스를 지정하거나, 시작 인덱스와 삭제할 요소의 개수를 지정합니다.

```scala
val x = ListBuffer(1, 2, 3, 4, 5, 6)

// 0번째 요소를 제거
val a = x.remove(0)        // a=1, x=ListBuffer(2, 3, 4, 5, 6)

// 인덱스 1부터 시작하여 세 개의 요소를 제거합니다. 이 `remove`
// 메서드는 값을 반환하지 않습니다.
x.remove(1, 3)             // x: ListBuffer(2, 6)

// `remove`가 예외를 던질 수 있음에 주의하세요
x.remove(100)              // java.lang.IndexOutOfBoundsException
```

### 논의 (Discussion)

Scala를 처음 사용하기 시작할 때는, 이름이 기호(symbol)로만 이루어진 풍부한 메서드들(예: `++`, `--`, `--=` 같은 메서드)이 부담스러워 보일 수 있습니다. 하지만 `++`와 `--`는 *불변* 컬렉션에서 일관되게 사용되고, `-=`와 `--=`는 *가변* 컬렉션 전반에 걸쳐 일관되게 사용되므로, 금세 자연스럽게 사용하게 됩니다.

---

## 12.5 ListBuffer로 가변 리스트 만들기 (Creating a Mutable List with ListBuffer)

### 문제

가변 리스트, 즉 (`IndexedSeq`가 아닌) `LinearSeq`를 사용하고 싶은데, `List`는 가변이 아닙니다.

### 해법

가변 리스트로 작업하려면, 데이터가 변경되는 동안에는 `ListBuffer`를 사용하고, 필요할 때 `ListBuffer`를 `List`로 변환합니다.

다음 예시들은 `ListBuffer`를 생성한 다음, 원하는 대로 요소를 추가하고 제거하고, 마지막으로 작업이 끝나면 `List`로 변환하는 방법을 보여 줍니다.

```scala
import scala.collection.mutable.ListBuffer

// 빈 ListBuffer[String]을 생성합니다
val b = new ListBuffer[String]()

// ListBuffer에 한 번에 하나의 요소를 추가합니다
b += "a"   // b: ListBuffer(a)
b += "b"   // b: ListBuffer(a, b)
b += "c"   // b: ListBuffer(a, b, c)

// 여러 요소를 추가합니다 (++= 는 addAll의 별칭입니다)
b ++= List("d", "e", "f")        // b: ListBuffer(a, b, c, d, e, f)
b.addAll(Vector("d", "e", "f"))  // b: ListBuffer(a, b, c, d, e, f, d, e, f)

// 첫 번째 "d"를 제거합니다
b -= "d"                         // b: ListBuffer(a, b, c, e, f, d, e, f)

// 다른 시퀀스로 지정된 여러 요소를 제거합니다
b --= Seq("e", "f")              // b: ListBuffer(a, b, c, d)

// 필요할 때 ListBuffer를 List로 변환합니다
val xs = b.toList                // xs: List(a, b, c, d)
```

### 논의 (Discussion)

`List`는 불변이므로, 계속해서 변경되는 리스트를 만들어야 한다면, 리스트가 수정되는 동안에는 `ListBuffer`를 사용하고, `List`가 필요할 때 그것을 `List`로 변환하는 것이 더 나을 수 있습니다.

[ListBuffer Scaladoc](https://www.scala-lang.org/api/current/scala/collection/mutable/ListBuffer.html)은 다음과 같이 명시합니다.

> `ListBuffer`는 리스트로 뒷받침되는 `Buffer` 구현체이다. 일정 시간의 prepend와 append를 제공한다. 대부분의 다른 연산들은 선형이다.

따라서, 인덱스로 항목에 접근하는 것(예: `list(1_000_000)`)처럼 요소에 임의로 접근하고 싶다면 `ListBuffer`를 사용하지 마세요. 대신 `ArrayBuffer`를 사용하세요. 자세한 내용은 Recipe 11.2 "Understanding the Performance of Collections"를 참고하세요.

#### 작은 리스트 (Small lists)

필요에 따라서는, 기존 `List`로부터 새 `List`를 만드는 것이 괜찮을 수도 있는데, 특히 리스트가 작고 새 `List`를 만들 때 요소를 앞에 추가하는(prepend) 것이 괜찮은 경우에 그렇습니다.

```scala
val a = List(2)   // a: List(2)
val b = 1 :: a    // b: List(1, 2)
val c = 0 :: b    // c: List(0, 1, 2)
```

이 기법은 Recipe 12.3에서 더 자세히 논의됩니다.

---

## 12.6 List의 게으른 버전인 LazyList 사용하기 (Using LazyList, a Lazy Version of a List)

### 문제

`List`처럼 동작하지만 그 변환 메서드들(`map`, `filter` 등)을 게으르게(lazily) 호출하는 컬렉션을 사용하고자 합니다.

### 해법

`LazyList`는 `List`와 비슷하지만, 그 요소들이 게으르게 계산된다는 점이 다른데, 이는 *뷰(view)* 가 컬렉션의 게으른 버전을 만드는 방식과 유사합니다. `LazyList` 요소들은 게으르게 계산되기 때문에, `LazyList`는 길 수 있고… 무한히 길 수도 있습니다. 뷰처럼, 접근되는 요소들만 계산됩니다. 이 동작을 제외하면, `LazyList`는 `List`와 비슷하게 동작합니다.

예를 들어, `List`가 `::`로 구성될 수 있는 것처럼, `LazyList`는 `#::` 메서드로 구성될 수 있는데, 표현식 끝에 `Nil` 대신 `LazyList.empty`를 사용합니다.

```scala
scala> val xs = 1 #:: 2 #:: 3 #:: LazyList.empty
val xs: LazyList[Int] = LazyList(<not computed>)
```

REPL 출력은 `LazyList`가 아직 계산되지 않았음을 보여 줍니다. 이는 `LazyList`가 생성되었지만 아직 어떤 요소도 할당되지 않았음을 의미합니다. 또 다른 예시로, 범위로 `LazyList`를 생성할 수 있습니다.

```scala
scala> val xs = (1 to 100_000_000).to(LazyList)
val xs: LazyList[Int] = LazyList(<not computed>)
```

이제 `LazyList`의 head와 tail에 접근을 시도할 수 있습니다. head는 즉시 반환됩니다.

```scala
scala> xs.head
res0: Int = 1
```

하지만 tail은 아직 평가되지 않습니다.

```scala
scala> xs.tail
val res1: LazyList[Int] = LazyList(<not computed>)
```

출력은 여전히 "not computed"를 보여 줍니다. Recipe 11.4 "Creating a Lazy View on a Collection"에서 논의했듯이, 변환 메서드들은 게으르게 계산되므로, 변환 메서드들을 호출하면 REPL에서 "<not computed>" 출력을 보게 됩니다.

```scala
scala> xs.take(3)
val res2: LazyList[Int] = LazyList(<not computed>)

scala> xs.filter(_ < 200)
val res3: LazyList[Int] = LazyList(<not computed>)

scala> xs.filter(_ > 200)
val res4: LazyList[Int] = LazyList(<not computed>)

scala> xs.map { _ * 2 }
val res5: LazyList[Int] = LazyList(<not computed>)
```

하지만 변환 메서드가 아닌 메서드들에는 주의하세요. 다음과 같은 *strict* 메서드들에 대한 호출은 즉시 평가되며, 쉽게 `java.lang.OutOfMemoryError` 에러를 일으킬 수 있습니다.

```scala
xs.max
xs.size
xs.sum
```

> **변환 메서드 (Transformer Methods)**
>
> *변환 메서드(transformer methods)* 는 여러분이 데이터를 변환하기 위해 제공하는 알고리즘에 기반하여, 주어진 입력 컬렉션을 새 출력 컬렉션으로 변환하는 컬렉션 메서드입니다. 여기에는 `map`, `filter`, `reverse` 같은 메서드가 포함됩니다. 이 메서드들을 사용할 때, 여러분은 입력 컬렉션을 새 출력 컬렉션으로 변환하는 것입니다.
>
> `max`, `size`, `sum` 같은 메서드들은 그 정의에 들어맞지 않으므로, `LazyList`에 대해 연산을 시도하게 되며, `LazyList`가 여러분이 할당할 수 있는 것보다 더 많은 메모리를 필요로 한다면 `java.lang.OutOfMemoryError`를 얻게 됩니다.

비교를 위한 예로, 만약 이 예시들에서 `List`를 사용하려 했다면, `List`를 생성하려고 시도하는 순간 곧바로 `java.lang.OutOfMemory` 에러를 만났을 것입니다.

```scala
val xs = (1 to 100_000_000).toList
// 결과: java.lang.OutOfMemoryError: Java heap space
```

반대로, `LazyList`는 거대한 리스트를 지정하고 그 요소들로 작업을 시작할 기회를 줍니다.

```scala
val xs = (1 to 100_000_000).to(LazyList)
xs(0)   // 1을 반환
xs(1)   // 2를 반환
```

### 논의 (Discussion)

Scala 2.13 컬렉션 재설계에서, `LazyList`는 더 이상 사용되지 않게 된(deprecated) `Stream` 클래스를 대체했습니다. [컬렉션 재설계에 관한 공식 블로그 게시물](https://www.scala-lang.org/blog/)에 따르면, 이는 부분적으로 컬렉션 설계에서의 혼란을 줄이려는 전반적인 노력 안에서 이루어졌습니다. 또한 그 블로그 게시물에 따르면, `LazyList`는 head와 tail 모두 게으르게 접근되는 반면, `Stream`에서는 tail만 게으르게 접근되었습니다.

[LazyList Scaladoc](https://www.scala-lang.org/api/current/scala/collection/immutable/LazyList.html)은 몇 가지 중요한 성능 관련 노트를 담고 있는데, 다음을 포함합니다.

- 요소들은 메모이즈(memoize)됩니다. 즉, 각 요소의 값은 최대 한 번만 계산됩니다.
- 요소들은 순서대로 계산되며 절대 건너뛰지 않습니다.
- `LazyList`는 길이가 무한일 수 있는데, 그런 경우 `count`, `sum`, `max`, `min` 같은 메서드들은 종료되지 않습니다.

그 Scaladoc 페이지에서 더 자세한 내용과 예시들을 참고하세요.

---

## 12.7 ArrayBuffer를 기본 가변 시퀀스로 만들기 (Making ArrayBuffer Your Go-To Mutable Sequence)

### 문제

크기가 변경될 수 있는 배열, 즉 완전히 가변인 배열을 생성하고자 합니다.

### 해법

`Array`는 그 요소들이 변경될 수 있다는 점에서 가변이지만, 그 크기는 변경될 수 없습니다. 크기가 변경될 수 있는 가변 인덱스 시퀀스를 생성하려면, `ArrayBuffer` 클래스를 사용하세요.

`ArrayBuffer`를 사용하려면, 그것을 스코프로 임포트(import)한 다음 인스턴스를 생성합니다. 초기 요소 없이 `ArrayBuffer`를 선언하려면 그것이 포함할 타입을 지정한 다음, 나중에 요소를 추가할 수 있습니다.

```scala
import scala.collection.mutable.ArrayBuffer
val a = ArrayBuffer[String]()

a += "Ben"     // a: ArrayBuffer(Ben)
a += "Jerry"   // a: ArrayBuffer(Ben, Jerry)
a += "Dale"    // a: ArrayBuffer(Ben, Jerry, Dale)
```

`ArrayBuffer`는 다른 가변 시퀀스들에서 볼 수 있는 모든 메서드를 가지고 있습니다. 다음은 `ArrayBuffer`에 요소를 추가하는 몇 가지 흔한 방법입니다.

```scala
import scala.collection.mutable.ArrayBuffer

// 요소들로 초기화합니다
val characters = ArrayBuffer("Ben", "Jerry")

// 하나의 요소를 추가합니다
characters += "Dale"

// 임의의 IterableOnce 타입으로 여러 요소를 추가합니다
characters += List("Gordon", "Harry")
characters += Vector("Andy", "Big Ed")

// 여러 요소를 추가하는 또 다른 방법
characters.appendAll(List("Laura", "Lucy"))

// `characters`는 이제 다음 문자열들을 포함합니다:
ArrayBuffer(Ben, Jerry, Dale, Gordon, Harry, Andy, Big Ed, Laura, Lucy)
```

이전 예시들에서 요소를 추가하는 방식은 *append(뒤에 붙이기)* 입니다. 다음 예시들은 `ArrayBuffer`에 요소를 *prepend(앞에 붙이기)* 하는 몇 가지 방법을 보여 줍니다.

```scala
val a = ArrayBuffer(10)    // a: ArrayBuffer[Int] = ArrayBuffer(10)
a.prepend(9)               // a: ArrayBuffer(9, 10)
a.prependAll(Seq(7,8))     // a: ArrayBuffer(7, 8, 9, 10)

// `+=:`는 `prepend`의 별칭, `++=:`는 `prependAll`의 별칭입니다
6 +=: a                    // a: ArrayBuffer(6, 7, 8, 9, 10)
List(4,5) ++=: a           // a: ArrayBuffer(4, 5, 6, 7, 8, 9, 10)
```

### 논의 (Discussion)

다음은 `ArrayBuffer` 요소를 제자리에서(in place) 갱신하는 몇 가지 방법입니다.

```scala
import scala.collection.mutable.ArrayBuffer

// ArrayBuffer[Char]를 생성합니다
val a = ArrayBuffer.range('a', 'f')   // a: ArrayBuffer(a, b, c, d, e)

a.update(0, 'A')                      // a: ArrayBuffer(A, b, c, d, e)
a(2) = 'C'                            // a: ArrayBuffer(A, b, C, d, e)

a.patchInPlace(0, Seq('X', 'Y'), 2)   // a: ArrayBuffer(X, Y, c, d, e)
a.patchInPlace(0, Seq('X', 'Y'), 3)   // a: ArrayBuffer(X, Y, d, e)
a.patchInPlace(0, Seq('X', 'Y'), 4)   // a: ArrayBuffer(X, Y)
```

`patchInPlace`를 사용할 때:

- 첫 번째 `Int` 파라미터는 교체를 시작하고자 하는 요소의 인덱스입니다.
- 두 번째 `Int`는 교체하고자 하는 요소의 개수입니다.

첫 번째 예시는 두 개의 요소를 두 개의 새 요소로 교체하는 방법을 보여 줍니다. 마지막 예시는 네 개의 기존 요소를 두 개의 새 요소로 교체하는 방법을 보여 줍니다.

#### ArrayBuffer와 ListBuffer에 관한 노트 (Notes about ArrayBuffer and ListBuffer)

[ArrayBuffer Scaladoc](https://www.scala-lang.org/api/current/scala/collection/mutable/ArrayBuffer.html)은 `ArrayBuffer` 성능에 관해 다음 세부 사항을 제공합니다.

> Append, update, 그리고 임의 접근(random access)은 일정 시간(상각 시간, amortized time)이 걸린다. Prepend와 remove는 버퍼 크기에 선형(linear)이다.

`List`처럼(즉, 인덱스 시퀀스가 아니라 선형 시퀀스처럼) 더 비슷하게 동작하는 가변 순차 컬렉션이 필요하다면, `ArrayBuffer` 대신 `ListBuffer`를 사용하세요. [ListBuffer Scaladoc](https://www.scala-lang.org/api/current/scala/collection/mutable/ListBuffer.html)은 "`ListBuffer`는 리스트로 뒷받침되는 `Buffer` 구현체이다. 일정 시간의 prepend와 append를 제공한다. 대부분의 다른 연산들은 선형이다."라고 명시합니다. 더 자세한 `ListBuffer` 내용은 Recipe 12.5를 참고하세요.

---

## 12.8 Array와 ArrayBuffer 요소 삭제하기 (Deleting Array and ArrayBuffer Elements)

### 문제

`Array`나 `ArrayBuffer`에서 요소를 삭제하고자 합니다.

### 해법

`ArrayBuffer`는 가변 시퀀스이므로, 일반적인 `-=`, `--=`, `remove`, `clear` 메서드로 요소를 삭제할 수 있습니다.

`Array`의 경우, 그 크기를 변경할 수 없으므로 요소를 직접 삭제할 수는 없습니다. 하지만 `Array`의 요소들을 다시 할당(reassign)할 수 있는데, 이것은 그것들을 교체하는(replacing) 효과가 있습니다.

또한 `ArrayBuffer`와 `Array` 모두에 다른 함수형 메서드들을 적용하면서 그 결과를 새 변수에 할당할 수 있습니다.

#### ArrayBuffer 요소 삭제하기 (Deleting ArrayBuffer elements)

다음과 같은 `ArrayBuffer`가 주어졌다고 합시다.

```scala
import scala.collection.mutable.ArrayBuffer
val x = ArrayBuffer('a', 'b', 'c', 'd', 'e')
```

`-=`와 `--=`로 값에 의해 하나 이상의 요소를 제거할 수 있습니다.

```scala
// 하나의 요소를 제거
x -= 'a'               // x: ArrayBuffer(b, c, d, e)

// 여러 요소를 제거
x --= Seq('b', 'c')    // x: ArrayBuffer(d, e)
```

그 마지막 예시에서 보듯이, `IterableOnce`를 확장하는 어떤 컬렉션에든 선언된 여러 요소를 제거하려면 `--=`를 사용합니다.

```scala
val x = ArrayBuffer.range('a', 'f')   // ArrayBuffer(a, b, c, d, e)

x --= Seq('a', 'b')    // x: ArrayBuffer(c, d, e)
x --= Array('c')       // x: ArrayBuffer(d, e)
x --= Set('d')         // x: ArrayBuffer(e)
```

`remove` 메서드를 사용하여 인덱스로 하나의 요소를 삭제하거나, 시작 인덱스로 시작하는 일련의 요소들을 삭제합니다.

```scala
val x = ArrayBuffer('a', 'b', 'c', 'd', 'e', 'f')

x.remove(0)            // x: ArrayBuffer(b, c, d, e, f)

// 인덱스 1부터 시작하여 세 개의 요소를 삭제합니다 (결과적으로 c, d, e가 삭제됨)
x.remove(1, 3)         // x: ArrayBuffer(b, f)
```

`clear` 메서드는 `ArrayBuffer`에서 모든 요소를 제거합니다.

```scala
val x = ArrayBuffer(1,2,3,4,5)
x.clear                // x: ArrayBuffer[Int] = ArrayBuffer()
```

`clearAndShrink`는 `ArrayBuffer`에서 모든 요소를 제거하고 그 내부 표현(internal representation)의 크기를 조정합니다.

```scala
// ArrayBuffer를 생성하고 채웁니다
val x = ArrayBuffer.range(1, 1_000_000)

// 모든 요소를 제거하고 내부 표현의 크기를 조정합니다
x.clearAndShrink(0)    // x: ArrayBuffer[Int] = ArrayBuffer()
```

> **clear와 clearAndShrink**
>
> [ArrayBuffer Scaladoc](https://www.scala-lang.org/api/current/scala/collection/mutable/ArrayBuffer.html)에 따르면, `clear`는 "실제로 내부 표현의 크기를 조정하지는 않는다. 내부적으로도 크기를 조정하고 싶다면 `clearAndShrink`를 참고하라." `clearAndShrink` Scaladoc은 "이 버퍼를 비우고 `@param` 크기로 줄인다(다음 자연 크기로 올림)."라고 명시합니다.

#### Array의 요소 교체하기 (Replacing elements in an Array)

`Array`의 크기는 변경될 수 없으므로 요소를 직접 삭제할 수는 없습니다. 하지만 `Array`의 요소들을 다시 할당할 수 있는데, 이것은 그것들을 교체하는 효과가 있습니다.

```scala
val a = Array("apple", "banana", "cherry")
a(0) = ""     // a: Array("", banana, cherry)
a(1) = null   // a: Array("", null, cherry)
```

### 논의 (Discussion)

`Array`와 `ArrayBuffer` 모두에서, 일반적인 함수형 필터링 메서드들을 사용하여 원하지 않는 요소들을 걸러내면서 그 결과를 새 시퀀스에 할당할 수도 있습니다.

```scala
val a = Array(1,2,3,4,5)   // a: Array[Int] = Array(1, 2, 3, 4, 5)
val b = a.filter(_ > 3)    // b: Array(4, 5)
val c = a.take(2)          // c: Array(1, 2)
val d = a.drop(2)          // d: Array(3, 4, 5)
val e = a.find(_ > 3)      // e: Some(4)
val f = a.slice(0, 3)      // f: Array(1, 2, 3)
```

`Array`와 `ArrayBuffer` 모두에서 원하는 대로 다른 함수형 필터링 메서드들을 사용하세요.

---

## 12.9 Array 생성하고 갱신하기 (Creating and Updating an Array)

### 문제

`Array`를 생성하고 선택적으로 채우고자 합니다.

### 해법

`Array`를 정의하고 채우는 여러 가지 다른 방법이 있습니다. 초기 값을 가진 배열을 생성할 수 있는데, 이 경우 Scala가 배열 타입을 암시적으로 결정합니다.

```scala
val nums = Array(1,2,3)            // Array[Int] = Array(1, 2, 3)
val fruits = Array("a", "b", "c")  // Array[String] = Array(a, b, c)
```

Scala가 결정한 타입이 마음에 들지 않는다면, 수동으로 할당할 수 있습니다.

```scala
val a = Array(1, 2)                // a: Array[Int] = Array(1, 2)
val a = Array[Long](1, 2)          // a: Array[Long] = Array(1, 2)
```

빈 배열을 생성한 다음 새 요소들을 추가하면서 그 결과를 새 변수에 할당할 수 있습니다.

```scala
val a = Array[Int]()          // a: Array[Int] = Array()

// 하나 또는 여러 요소를 append
val b = a :+ 1                // b: Array(1)
val c = b ++ Seq(2,3)         // c: Array(1, 2, 3)

// 하나 또는 여러 요소를 prepend
val d = 10 +: c               // d: Array(10, 1, 2, 3)
val e = Array(8,9) ++: d      // e: Array(8, 9, 10, 1, 2, 3)
```

비슷하게, 초기 크기와 타입을 가진 배열을 정의한 다음 나중에 채울 수 있습니다. 첫 번째 단계에서 이 예시는 1,000개의 초기 `null` 요소를 가진 `Array[String]`을 생성하고, 그다음 요소들을 추가하기 시작합니다.

```scala
// 초기 크기를 가진 배열을 생성합니다
val babyNames = new Array[String](1_000)

// 코드의 나중 어딘가에서 ...
babyNames(0) = "Alvin"       // Array(Alvin, null, null ...)
babyNames(1) = "Alexander"   // Array(Alvin, Alexander, null ...)
```

Scala에서 일반적으로 `null` 값은 피하려 하지만, 배열에 대한 `null` `var` 참조를 생성한 다음 나중에 할당할 수 있습니다.

```scala
// 이것은 `fruits`를 null 값으로 만듭니다
var fruits: Array[String] = _      // fruits: Array[String] = null

// 코드의 나중에 ...
fruits = Array("apple", "banana")  // fruits: Array(apple, banana)
```

다음 예시들은 `Array`를 생성하고 채우는 다른 여러 방법들을 보여 줍니다.

```scala
val x = (1 to 5).toArray              // x: Array(1, 2, 3, 4, 5)
val x = Array.range(1, 5)             // x: Array(1, 2, 3, 4)
val x = Array.range(0, 10, 2)         // x: Array(0, 2, 4, 6, 8)
val x = List(1, 2, 3).toArray         // x: Array(1, 2, 3)
"Hello".toArray                       // x: Array[Char] = Array(H,e,l,l,o)
val x = Array.fill(3)("foo")          // x: Array(foo, foo, foo)
val x = Array.tabulate(5)(n => n * n) // x: Array(0, 1, 4, 9, 16)
```

### 논의 (Discussion)

`Array`는 흥미로운 존재입니다.

- 자바 배열에 의해 뒷받침되지만, Scala 배열은 제네릭(generic)일 수도 있어서, `Array[A]`를 가질 수 있습니다.
- 자바 배열처럼, 요소들이 변경될 수 있다는 점에서 *가변(mutable)* 이지만, 크기가 변경될 수 없다는 점에서 *불변(immutable)* 입니다.
- Scala 시퀀스들과 호환되므로, 함수가 `Seq[A]`를 기대한다면 거기에 `Array[A]`를 전달할 수 있습니다.

배열은 변경될 수 있기 때문에, 함수형 프로그래밍 스타일로 코드를 작성할 때는 분명히 그것들을 사용하고 싶지 않을 것입니다.

자바 배열에 의해 뒷받침된다는 점과 관련해, [Scala 2.13 arrays 페이지](https://docs.scala-lang.org)는 `Array` 타입에 관해 다음과 같이 명시합니다.

> Scala 배열은 자바 배열과 일대일로 대응한다. 즉, Scala 배열 `Array[Int]`는 자바 `int[]`로 표현되고, `Array[Double]`은 자바 `double[]`로 표현되는 식이다.

이것을 직접 확인할 수 있습니다. *Test.scala* 라는 파일을 다음 코드로 생성합니다.

```scala
class Test:
    val nums = Array(1, 2, 3)
```

그 파일을 `scalac`로 컴파일한 다음 JAD 같은 도구로 디컴파일(decompile)하면, 다음 자바 코드를 보게 됩니다.

```java
private final int nums[] = {
    1, 2, 3
};
```

#### 요소 접근하고 갱신하기 (Accessing and updating elements)

`Array`는 *인덱스(indexed)* 순차 컬렉션이므로, 인덱스 위치로 값을 접근하고 변경하는 것은 직관적이고 빠릅니다. `Array`를 생성한 다음, 원하는 요소 번호를 괄호 안에 넣어 그 요소들에 접근합니다.

```scala
val a = Array('a', 'b', 'c')
val elem0 = a(0)   // elem0: a
val elem1 = a(1)   // elem1: b
```

인덱스로 배열 요소에 접근하는 것과 똑같이, 비슷한 방식으로 요소를 갱신합니다.

```scala
a(0) = 'A'         // a: Array(A, b, c)
a(1) = 'B'         // a: Array(A, B, c)
```

#### 왜 Array를 사용하는가? (Why use Array?)

`Array` 타입이 불변과 가변 특성의 조합을 가지고 있기 때문에, 언제 그것을 사용해야 하는지 궁금할 수 있습니다. 한 가지 이유는 성능입니다. 어떤 `Array` 연산들은 다른 컬렉션들보다 빠릅니다. 예를 들어, 저자의 현재 컴퓨터에서의 테스트에서, 500만 개의 무작위 `Int` 값을 가진 `Array`에 대해 `b.sortInPlace`를 실행하면 일관되게 약 500ms가 걸립니다.

```scala
import scala.util.Random
val v: Vector[Int] = (1 to 5_000_000).toVector

// 무작위 Array[Int]를 생성합니다
val a: Array[Int] = Random.shuffle(v).toArray

a.sortInPlace   // 약 500ms 걸림
```

반대로, 같은 방식으로 무작위 `Vector`를 생성하고 그것의 `sorted` 메서드를 호출하면 일관되게 3초가 넘게 걸립니다.

```scala
randomVector.sorted   // 약 3,100ms 걸림
```

따라서 이 예시에서, `Array[Int]`를 `sortInPlace`로 정렬하는 것이 `Vector[Int]`를 `sorted`로 정렬하는 것보다 약 여섯 배 더 빠릅니다. 흔히 말하듯이 여러분의 (성능) 결과는 다를 수 있지만(your mileage may vary), 어떤 상황에서는 `Array`가 다른 컬렉션 타입보다 빠를 수 있다는 것을 아는 것이 중요합니다.

> **Array는 어떻게 다른 시퀀스처럼 동작하는가? (How Does Array Work Like Other Sequences?)**
>
> Scala `Array`가 자바 배열에 의해 뒷받침된다는 것을 깨달으면, `Array`가 어떻게 다른 Scala 시퀀스처럼 동작할 수 있는지 궁금할 수 있습니다. *Programming in Scala* 책의 제4판은 "배열은 시퀀스와 호환되는데, `Array`에서 `ArraySeq`로의 암시적 변환이 있기 때문이다."라고 명시합니다. 또한, `ArrayOps`와 관련된 또 다른 암시적 변환은 "모든 시퀀스 메서드를 배열에 추가하지만, 배열을 시퀀스로 바꾸지는 않는다."고 합니다.

---

## 12.10 다차원 배열 생성하기 (Creating Multidimensional Arrays)

### 문제

다차원 배열, 즉 두 개 이상의 차원을 가진 배열을 생성해야 합니다.

### 해법

두 가지 주요 해법이 있습니다.

- 다차원 배열을 생성하기 위해 `Array.ofDim`을 사용합니다. 이 접근 방식으로 최대 다섯 차원까지의 배열을 생성할 수 있습니다. 이 접근 방식에서는 생성 시점에 행(row)과 열(column)의 개수를 알아야 합니다.
- 필요에 따라 배열의 배열(arrays of arrays)을 생성합니다.

이 해법에서 두 접근 방식 모두 보여 줍니다.

#### Array.ofDim 사용하기 (Using Array.ofDim)

`Array.ofDim` 메서드를 사용하여 필요한 배열을 생성합니다.

```scala
val rows = 2
val cols = 3

val a = Array.ofDim[String](rows, cols)

// `a`는 이제 다음과 같이 보입니다:
Array[Array[String]] = Array(
    Array(null, null, null),
    Array(null, null, null)
)
```

배열을 선언한 다음, 그것에 요소들을 추가합니다.

```scala
a(0)(0) = "a"   // 행 1
a(0)(1) = "b"
a(0)(2) = "c"
a(1)(0) = "d"   // 행 2
a(1)(1) = "e"
a(1)(2) = "f"
```

일차원 배열과 비슷하게, 괄호를 사용하여 요소들에 접근합니다.

```scala
a(0)(0)   // a
a(1)(2)   // f
```

`for` 루프로 배열을 순회합니다.

```scala
scala> for
     |     i <- 0 until rows
     |     j <- 0 until cols
     | do println(s"($i)($j) = ${a(i)(j)}")
(0)(0) = a
(0)(1) = b
(0)(2) = c
(1)(0) = d
(1)(1) = e
(1)(2) = f
```

더 많은 차원을 가진 배열을 생성하려면, 그 동일한 패턴을 따르면 됩니다. 다음은 삼차원 배열에 대한 코드입니다.

```scala
val x, y, z = 10
val a = Array.ofDim[Int](x,y,z)
for
    i <- 0 until x
    j <- 0 until y
    k <- 0 until z
do
    println(s"($i)($j)($k) = ${a(i)(j)(k)}")
```

#### 배열의 배열 사용하기 (Using an array of arrays)

또 다른 접근 방식은 요소들이 배열인 배열을 생성하는 것입니다.

```scala
val a = Array(
    Array("a", "b", "c"),
    Array("d", "e", "f")
)

val x = a(0)      // x: Array(a, b, c)
val x = a(1)      // x: Array(d, e, f)
val x = a(0)(0)   // x: a
```

이것은 프로세스에 대한 더 많은 제어를 주며, "들쭉날쭉한(ragged)" 배열, 즉 포함된 각 배열의 크기가 다를 수 있는 배열을 생성할 수 있게 해 줍니다.

```scala
val a = Array(
    Array("a", "b", "c"),
    Array("d", "e"),
    Array("f")
)
```

변수를 `var`로 선언하고 같은 배열을 여러 단계로 생성할 수도 있습니다.

```scala
var arr = Array(Array("a", "b", "c"))
arr ++= Array(Array("d", "e"))
arr ++= Array(Array("f"))

// 결과:
Array(Array(a, b, c), Array(d, e), Array(f))
```

### 논의 (Discussion)

`Array.ofDim` 해법을 디컴파일하면 이것이 배후에서 어떻게 동작하는지 이해하는 데 도움이 됩니다. *Test.scala* 라는 파일에 다음 Scala 클래스를 생성한다면:

```scala
class Test:
    val arr = Array.ofDim[String](2, 3)
```

그 클래스를 `scalac`로 컴파일한 다음 JAD 같은 도구로 디컴파일하면, 다음과 같이 생성된 자바 코드를 볼 수 있습니다.

```java
private final String arr[][];
```

비슷하게, 다음과 같이 Scala 삼차원 `Array`를 생성하면:

```scala
val arr = Array.ofDim[String](2, 2, 2)
```

다음과 같은 자바 배열이 됩니다.

```java
private final String arr[][][];
```

예상할 수 있듯이, "배열의 배열" 접근 방식을 사용하여 생성된 코드는 더 복잡합니다.

`Array.ofDim` 접근 방식은 `Array` 클래스에 고유합니다. `List`, `Vector`, `ArrayBuffer` 등에는 `ofDim` 메서드가 없습니다. 하지만 "배열의 배열" 해법은 `Array` 클래스에 고유한 것이 아닙니다. "리스트의 리스트", "벡터의 벡터" 등을 가질 수 있습니다.

마지막으로, 배열의 배열을 가지고 있다면, 필요할 경우 `flatten`으로 그것을 단일 배열로 변환할 수 있다는 점을 기억하세요.

```scala
val a = Array.ofDim[Int](2, 3)   // a: Array(Array(0, 0, 0), Array(0, 0, 0))
val b = a.flatten                // b: Array(0, 0, 0, 0, 0, 0)
```

---

## 12.11 배열 정렬하기 (Sorting Arrays)

### 문제

`Array`(또는 `ArrayBuffer`)의 요소들을 정렬하고자 합니다.

### 해법

Recipe 13.14 "Sorting a Collection"에 나오는 정렬 메서드들(`sortBy`, `sorted`, `sortWith`, `sortInPlace`, `sortInPlaceBy`, `sortInPlaceWith`)을 사용하거나, 특별한 `scala.util.Sorting.quickSort` 메서드를 사용합니다. 이 해법은 `quickSort` 메서드를 시연합니다.

`scala.math.Ordered`를 확장하는 타입의 요소들을 담고 있거나 암시적 또는 명시적 `Ordering`을 가진 `Array`로 작업하고 있다면, `scala.util.Sorting.quickSort` 메서드를 사용하여 `Array`를 제자리에서(in place) 정렬할 수 있습니다. 예를 들어, `String` 클래스는 암시적 `Ordering`을 가지고 있으므로 `quickSort`와 함께 사용할 수 있습니다.

```scala
val fruits = Array("cherry", "apple", "banana")
scala.util.Sorting.quickSort(fruits)
fruits   // Array(apple, banana, cherry)
```

`quickSort`가 `Array`를 제자리에서 정렬한다는 점에 주목하세요. 결과를 새 변수에 할당할 필요가 없습니다. 이 예시는 `String` 클래스가 (`StringOps`를 통해) 암시적 `Ordering`을 가지기 때문에 동작합니다.

### 논의 (Discussion)

다음과 같은 `Person` 클래스 같은 단순한 클래스는 데이터를 어떻게 정렬해야 하는지에 대한 정보를 전혀 제공하지 않기 때문에 `Sorting.quickSort`와 함께 동작하지 않습니다.

```scala
class Person(val firstName: String, val lastName: String):
    override def toString = s"$firstName $lastName"

val peeps = Array(
    Person("Jessica", "Day"),
    Person("Nick", "Miller"),
    Person("Winston", "Bishop"),
    Person("", "Schmidt"),
    Person("Coach", ""),
)
```

그 `Array`를 정렬하려고 시도하면 에러가 발생합니다.

```scala
// 결과 에러: "No implicit Ordering defined for Person"
scala.util.Sorting.quickSort(peeps)
```

해법은 Recipe 13.14 "Sorting a Collection"에서 시연하듯이 `scala.math.Ordered` 트레이트를 확장하거나, 암시적 또는 명시적 `Ordering`을 제공하는 것입니다. 이 해법은 명시적 `Ordering`을 시연합니다.

```scala
object LastNameOrdering extends Ordering[Person]:
    def compare(a: Person, b: Person) = a.lastName compare b.lastName

scala.util.Sorting.quickSort(peeps)(LastNameOrdering)
    // 결과: Array(Coach , Winston Bishop, Jessica Day, Nick Miller, Schmidt)
```

이 접근 방식이 동작하는 이유는, 오버로드된(overloaded) `quickSort` 메서드 중 하나가 그 두 번째 파라미터 목록에서 (암시적) `Ordering` 인자를 받기 때문이며, 이는 그 타입 시그니처에 다음과 같이 나타나 있습니다.

```scala
def quickSort[K](a: Array[K])(implicit arg0: math.Ordering[K]): Unit
                             // ------------------------------
```

#### given ordering 사용하기 (Using a given ordering)

언급했듯이, `arg0`은 *implicit* 파라미터로 표시되어 있습니다. `implicit`으로 표시된 파라미터는 Scala 3 `using` 파라미터에 해당하는 Scala 2의 표현입니다. 이는 현재 스코프에 `math.Ordering` `given` 값이 있을 때, 그것이 그 파라미터 그룹에서 `arg0` 파라미터로 자동으로 사용됨을 의미합니다. 그것을 지정할 필요조차 없습니다.

예를 들어, 먼저 `Ordering[Person]`의 인스턴스인 `given` 값을 정의합니다.

```scala
given personOrdering: Ordering[Person] with
    def compare(a: Person, b: Person) = a.lastName compare b.lastName
```

그런 다음 `peeps` 배열에서 `quickSort`를 호출하면, 그 결과는 이전 예시와 동일합니다.

```scala
import scala.util.Sorting.quickSort
quickSort(peeps)
// 결과: peeps: Array[Person] =
// Array(Coach , Winston Bishop, Jessica Day, Nick Miller,  Schmidt)
```

`quickSort`를 호출할 때 `personOrdering` 인스턴스를 전달할 필요가 없다는 점에 주목하세요(!). `personOrdering`이 `given` 값으로 정의되어 있기 때문에, 컴파일러가 친절하게도 그것을 찾아 줍니다. 컴파일러는 `Ordering[Person]` 파라미터가 필요하다는 것을 알고, `personOrdering`이 그 타입을 가지며 `given` 값으로 표시되어 있다는 것을 알기 때문입니다.

이것이 `given` 값과 결합된 Scala 2 `implicit` 파라미터 — 그리고 Scala 3 `using` 파라미터 — 가 우리를 위해 해 주는 일입니다. 마치 우리가 다음과 같이 `personOrdering` 파라미터를 두 번째 파라미터 그룹에 수동으로 전달하는 `quickSort` 코드를 작성한 것과 똑같습니다.

```scala
quickSort(peeps)(personOrdering)
                // ---------------
```

`personOrdering`이라는 이름이 코드에서 실제로 필요한 것은 아니기 때문에, `given` 파라미터는 다음과 같이 변수 이름 없이 선언될 수도 있었다는 점에 주목하세요.

```scala
given Ordering[Person] with
    def compare(a: Person, b: Person) = a.lastName compare b.lastName
```

이 접근 방식에 대한 더 자세한 내용은 Recipe 23.8 "Using Term Inference with given and using"을 참고하세요.

#### 성능 (Performance)

이 해법을 시연하는 이유는 그것이 `Array` 클래스에 고유하고, Recipe 13.14 "Sorting a Collection"에 나온 해법들보다 `Array`에 대해 더 나은 성능을 낼 수도 있기 때문입니다. 예를 들어, 저자의 테스트에 따르면 수백만 개의 요소를 가진 정수 배열에서 `quickSort`가 `sortInPlace`보다 약간 더 빠를 수 있습니다. 하지만 어떤 성능 논의에서든 그렇듯이, 여러분 자신의 애플리케이션에서 대안들을 반드시 테스트해 보세요.
