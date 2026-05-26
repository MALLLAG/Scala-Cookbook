# Chapter 11. Collections: Introduction

## 챕터 개요

이 장은 Scala 컬렉션(collections) 클래스를 다루는 다섯 개의 장 가운데 첫 번째입니다. 컬렉션은 어떤 프로그래밍 언어에서든 매우 중요하기 때문에, 이 장들에서는 Scala의 컬렉션 클래스와 메서드를 깊이 있게 다룹니다. 또한 이 장들은 레시피를 더 쉽게 찾을 수 있도록 *Scala Cookbook*의 이번 2판에서 완전히 재구성되었습니다.

이 첫 번째 컬렉션 장은 컬렉션 *클래스(classes)* 에 대한 소개를 제공합니다. 이 장의 목적은 클래스들이 어떻게 구성되어 있는지를 보여 주고, 여러분의 필요에 맞는 컬렉션 클래스를 선택하는 데 도움을 주는 것입니다. 예를 들어, 인덱스를 가진(indexed) 불변(immutable) 시퀀스를 원한다면 `Vector`가 권장되는 기본 시퀀스이고, 인덱스를 가진 가변(mutable) 시퀀스를 원한다면 `ArrayBuffer`가 대신 권장됩니다.

이 장 이후로, 12장은 `Vector`, `ArrayBuffer`, `List`, `Array`를 비롯해 가장 흔히 사용되는 Scala 시퀀스 클래스를 다룹니다. 추가 레시피에서는 `ListBuffer`와 `LazyList`도 다룹니다.

13장은 Scala 시퀀스 클래스에서 사용할 수 있는 가장 흔한 *메서드(methods)* 에 대한 레시피를 제공합니다. 컬렉션 클래스는 사용할 수 있는 내장 메서드의 깊이로 잘 알려져 있으며, 그 장은 그러한 메서드들을 시연합니다.

14장은 `Map` 타입을 다룹니다. Scala 맵은 Java의 `Map`, Ruby의 `Hash`, Python의 dictionary와 비슷한데, 키(key)가 반드시 유일해야 하는 키/값(key/value) 쌍으로 구성된다는 점에서 그렇습니다. Scala에는 불변 맵과 가변 맵이 모두 있으며, 둘 다 그 장에서 다룹니다.

마지막으로, 15장은 흔히 사용되는 튜플(tuple)과 `Range` 타입을 비롯해 셋(set), 큐(queue), 스택(stack) 같은 다른 컬렉션 타입을 다룹니다.

### Scala는 Java가 아닙니다 (Scala Is Not Java)

Scala의 컬렉션 클래스는 풍부하고 깊이가 있으며, Java 같은 다른 언어의 컬렉션 클래스와 상당히 다릅니다. 단기적으로는 이것이 약간의 과속방지턱처럼 느껴질 수 있지만, 장기적으로는 그 우아함과 내장 메서드를 높이 평가하게 될 것입니다.

이러한 메서드의 깊이 덕분에, 여러분이 직접 만든(custom) `for` 루프를 작성하거나 읽어야 할 일은 거의 없을 것입니다. 알고 보면, 개발자들이 수년간 작성해 온 그 많은 맞춤형 `for` 루프들은 특정 패턴을 따르고 있어서, 그러한 루프들은 `filter`, `map`, `foreach` 등과 같은 내장 컬렉션 메서드 안에 캡슐화되어 있습니다. *Scala Cookbook*의 초판이 2013년에 출간되었을 때, 이 풍부한 컬렉션 메서드들은 Java 배경을 가진 사람에게는 상당한 충격일 수 있었지만, 이제 Java 컬렉션이 더 함수형(functional) 인터페이스를 갖추게 되었으므로 그 전환은 훨씬 쉬워졌을 것입니다.

하지만 Scala로 작업을 시작할 때에는 여전히 Java 컬렉션 클래스를 잊고 Scala 컬렉션에 집중하는 편이 가장 좋습니다. 예를 들어, Java 개발자가 Scala로 처음 넘어오면 "좋아, 리스트와 배열을 쓰면 되겠지?"라고 생각할 수 있습니다. 그런데, 아닙니다, 그렇지 않습니다. Scala의 `List` 클래스는 Java의 `List` 클래스와 매우 다릅니다 — Scala `List`가 불변이라는 점을 포함해서 말입니다. 그리고 Scala의 `Array`는 Java 배열 타입을 감싼(wrapper) 것이며 배열을 다루는 많은 내장 메서드를 제공하긴 하지만, 일반적인 시퀀스 컬렉션 클래스로 권장조차 되지 않습니다.

저자 자신의 경험으로도, 저는 Java에서 Scala로 넘어왔고 Scala 애플리케이션에서 Java 컬렉션 클래스를 계속 사용하려고 했습니다. 이것은 시간 낭비였습니다. Java 컬렉션이 Scala에서 잘 동작하는 것은 사실이지만, 이 기능은 실제로는 Java 코드와의 상호운용(interoperating)을 위해 의도된 것입니다. 돌이켜보면, Scala 애플리케이션에서 기본 컬렉션으로 Java 컬렉션 클래스를 쓰려고 했던 것은 제 학습 곡선을 더디게 만들 뿐이었습니다. 제가 했던 대로 하지 말고, 바로 뛰어들어 필요한 클래스를 Scala 방식으로 배우시기를 권합니다! 이 장이 필요한 클래스를 찾는 데 도움이 될 것입니다.

### Scala 2.13 컬렉션 대대적 개편 (The Scala 2.13 Collections Overhaul)

마지막 소개 사항으로, 2018년에 마무리된 Scala 2.13 릴리스는 컬렉션에 대한 대대적인 개편으로 잘 알려져 있었습니다. 컬렉션의 "이면(behind the scenes)" 구현에는 trait과 타입 상속에 상당한 변화가 있었지만, 대부분 그 변화는 최종 사용자에게는 투명하게(보이지 않게) 처리되었습니다.

이것은 좋은 일인데, Scala 개발자들이 컬렉션의 최종 결과를 누리기 때문입니다. 따라서 여러분이 사용하는 클래스인 외부 API — `List`, `Vector`, `ArrayBuffer`, `Map`, `Set` 같은 것들 — 는 대체로 그대로 유지됩니다. 오히려 Scala 2.13과 Scala 3는 이러한 내부 표현을 단순화했기 때문에, 여러분의 코드와 타입은 그 어느 때보다 더 단순해졌습니다.

이 개편을 언급하는 이유는 Scala 3가 Scala 2.13의 바로 뒤를 빠르게 잇기 때문입니다. 그래서 Scala 3에서 상당히 업데이트된 튜플을 제외하면, 많은 컬렉션이 Scala 2.13에서와 똑같이 동작합니다. 다시 말하지만, 이것은 좋은 일입니다.

Scala 2.13의 세부 사항에 관심이 있다면, 그 변화의 이면 이야기를 들려주는 세 가지 훌륭한 자료가 있습니다.

- Scala 2.13의 컬렉션에 대한 Scala 3 페이지
- Scala 2.13 컬렉션의 아키텍처에 대한 Scala 3 페이지
- Scala 2.13 클래스 계층 구조에 대한 개관

### 컬렉션 계층 구조 이해하기 (Understanding the Collections Hierarchy)

컬렉션에 대해 가장 먼저 알아야 할 점은, 모든 컬렉션이 표 11-1에 표시된 패키지들 안에 담겨 있다는 것입니다. 일반적으로 `scala.collection`에 있는 컬렉션은 `scala.collection.immutable`과 `scala.collection.mutable`에 있는 컬렉션의 상위 클래스(superclasses) — 더 정확하게는 상위 타입(supertypes) — 입니다. 이것은 기본(base) 연산이 `scala.collection`의 타입에서 제공되고, 불변 및 가변 연산은 다른 두 패키지의 타입에 추가된다는 의미입니다.

*표 11-1. Scala 컬렉션을 담고 있는 패키지들*

| 문자열(패키지 경로) | 설명 |
| --- | --- |
| `scala.collection` | 이곳의 컬렉션은 불변일 수도, 가변일 수도 있습니다. |
| `scala.collection.immutable` | 불변 컬렉션. 생성된 후에는 절대 변경되지 않습니다. |
| `scala.collection.mutable` | 가변 컬렉션. 컬렉션의 요소를 변경할 수 있게 해 주는 메서드를 일부(또는 다수) 가지고 있습니다. |

#### 컬렉션은 깊고 넓습니다 (The Collections Are Deep and Wide)

Scala 컬렉션 계층 구조는 매우 풍부합니다 — 깊으면서도 넓습니다 — 그리고 그것이 어떻게 구성되어 있는지를 이해하면 문제를 해결할 컬렉션 클래스나 메서드를 선택할 때 도움이 될 수 있습니다.

그림 11-1은 `Vector` 클래스가 상속받는 trait들을 보여 주며, Scala 컬렉션 계층 구조의 복잡함을 일부 보여 줍니다. 다음은 `Vector` 클래스가 상속받는 선형 상위 타입(Linear Supertypes) 목록입니다.

<img width="576" height="246" alt="Image" src="https://github.com/user-attachments/assets/30e2bb10-7a9e-46a3-b205-25eac5e00879" />

(a) Scala 클래스는 trait으로부터 상속받을 수 있고 (b) 잘 설계된 trait은 세분화되어(granular) 있기 때문에, 클래스 계층 구조는 이처럼 보일 수 있습니다. 하지만 위 목록 때문에 겁먹지는 마십시오. `Vector`를 사용하기 위해 그 모든 trait을 알 필요는 없습니다. 실제로 `Vector`를 사용하는 것은 단순합니다.

```scala
val x = Vector(1, 2, 3)
x.sum                // 6
x.filter(_ > 1)      // Vector(2, 3)
x.map(_ * 2)         // Vector(2, 4, 6)
x.takeWhile(_ < 3)   // Vector(1, 2)
```

높은 수준에서 보면, Scala의 컬렉션 클래스는 `Iterable` trait에서 시작해서 세 가지 주요 범주인 시퀀스(`Seq`), 셋(`Set`), 맵(`Map`)으로 확장됩니다. 시퀀스는 다시 *인덱스를 가진(indexed)* 시퀀스와 *선형(linear)* 시퀀스로 갈라집니다. 이를 그림 11-2가 보여 줍니다. 즉, 다음과 같은 계층입니다.

<img width="569" height="244" alt="Image" src="https://github.com/user-attachments/assets/8f4a8939-e252-4744-9e0c-2545ba7d8f0f" />

`Iterable` trait은 *iterator(반복자)* 를 정의하는데, 이는 컬렉션의 요소를 한 번에 하나씩 순회할 수 있게 해 줍니다. 하지만 iterator를 사용할 때 컬렉션은 단 한 번만 순회될 수 있는데, 이는 반복 과정에서 각 요소가 소비되기 때문입니다.

#### 시퀀스 (Sequences)

*시퀀스(sequence)* 계층을 조금 더 깊이 파고들면, Scala에는 많은 수의 시퀀스 타입이 있습니다. 가장 흔한 *불변* 시퀀스는 그림 11-3에, 가장 흔한 *가변* 시퀀스는 그림 11-4에 표시되어 있습니다.

<img width="573" height="567" alt="Image" src="https://github.com/user-attachments/assets/ba3a6ff1-42aa-43df-9458-e0edda6a465b" />

(그림 11-4에서 `IndexedSeq`와 `Buffer`는 점선으로 연결되어 있는데, 이는 `ArrayDeque`와 `ArrayBuffer` 등이 두 갈래 모두와 관계를 맺고 있음을 나타냅니다.)

그림 11-3에서 보듯, 불변 시퀀스는 두 가지 주요 범주인 *인덱스를 가진 시퀀스(indexed sequences)* 와 *선형 시퀀스(linear sequences, 연결 리스트)* 로 갈라집니다. `IndexedSeq`는 요소에 대한 임의 접근(random access)이 효율적임을 나타내는데, 예를 들어 `xs(1_000_000)`처럼 `Vector`의 요소에 접근하는 경우가 그렇습니다. 기본적으로, Scala 3에서 `IndexedSeq`를 원한다고 지정하면 `Vector`가 생성됩니다.

```scala
scala> val x = IndexedSeq(1,2,3)
x: IndexedSeq[Int] = Vector(1, 2, 3)
```

`LinearSeq`는 컬렉션이 머리(head)와 꼬리(tail) 구성요소로 효율적으로 나뉠 수 있음을 의미하며, `head`, `tail`, `isEmpty` 메서드를 사용해 작업하는 것이 흔합니다. Scala 3에서 `LinearSeq`를 생성하면 단일 연결 리스트(singly linked list)인 `List`가 생성된다는 점에 유의하십시오.

```scala
scala> val xs = scala.collection.immutable.LinearSeq(1,2,3)
xs: scala.collection.immutable.LinearSeq[Int] = List(1, 2, 3)
```

그림 11-4에 표시된 가변 시퀀스 중에서 `ArrayBuffer`가 가장 흔히 사용되며, 가변 시퀀스가 필요할 때 권장됩니다. 다음은 `ArrayBuffer`를 사용하는 방법을 간단히 살펴본 것입니다.

```scala
scala> import scala.collection.mutable.ArrayBuffer

scala> val xs = ArrayBuffer(1,2,3)
val xs: ArrayBuffer[Int] = ArrayBuffer(1, 2, 3)

scala> xs.addOne(4)
val res0: ArrayBuffer[Int] = ArrayBuffer(1, 2, 3, 4)

scala> xs.addAll(List(5,6,7))
val res1: ArrayBuffer[Int] = ArrayBuffer(1, 2, 3, 4, 5, 6, 7)
```

위에서는 `addOne`과 `addAll` 메서드를 보여 주었는데, 이것들은 비교적 최근에 추가된 것입니다. 역사적으로는 이러한 목적으로 `+=`와 `++=`를 사용하는 것이 더 흔했습니다.

```scala
scala> xs += 8
val res2: ArrayBuffer[Int] = ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8)

scala> xs ++= List(9,10)
val res3: ArrayBuffer[Int] = ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

#### 맵 (Maps)

Java의 `Map`, Ruby의 `Hash`, Python의 dictionary와 마찬가지로, Scala의 `Map`은 키/값 쌍으로 이루어진 컬렉션이며, 모든 키는 유일해야 합니다. 가장 흔한 불변 및 가변 `Map` 클래스는 각각 그림 11-5와 그림 11-6에 표시되어 있습니다.

<img width="583" height="471" alt="Image" src="https://github.com/user-attachments/assets/812f4471-451b-43bf-b4fc-74d79e28f0d5" />

`Map` 타입은 레시피 14.1 "Creating and Using Maps"에서 다루지만, 간단한 소개로서, import 문 없이 단순한 *불변* 맵이 필요하다면 다음과 같이 만들 수 있습니다.

```scala
scala> val m = Map(1 -> "a", 2 -> "b")
val m: Map[Int, String] = Map(1 -> a, 2 -> b)
```

*가변* 맵은 기본적으로 스코프(scope) 안에 있지 않으므로, 사용하려면 import하거나 전체 경로를 지정해야 합니다.

```scala
scala> val mm = collection.mutable.Map(1 -> "a", 2 -> "b")
val mm: scala.collection.mutable.Map[Int, String] = HashMap(1 -> a, 2 -> b)
```

#### 셋 (Sets)

Java의 `Set`과 마찬가지로, Scala의 `Set`은 유일한(unique) 요소들의 컬렉션입니다. 흔한 불변 및 가변 `Set` 클래스는 각각 그림 11-7과 그림 11-8에 표시되어 있습니다.

<img width="585" height="475" alt="Image" src="https://github.com/user-attachments/assets/7b065d20-bdca-4117-9e35-38b7a53de38e" />

`Set` trait과 클래스는 레시피 15.3 "Creating a Set and Adding Elements to It"에서 다루지만, 빠른 미리보기로서, 불변 셋이 필요하다면 import 문 없이 다음처럼 만들 수 있습니다.

```scala
scala> val set = Set(1, 2, 3)
val set: Set[Int] = Set(1, 2, 3)
```

맵과 마찬가지로, 가변 셋을 사용하려면 import하거나 전체 경로를 지정해야 합니다.

```scala
scala> val mset = collection.mutable.Set(1, 2, 3)
val mset: scala.collection.mutable.Set[Int] = HashSet(1, 2, 3)
```

요약하면, 이것이 Scala 컬렉션 계층 구조에 대한 개관입니다.

## 11.1 Choosing a Collections Class

### Problem

특정 문제를 해결하기 위해 Scala 컬렉션 클래스를 선택하고자 합니다.

### Solution

선택할 수 있는 컬렉션 클래스에는 세 가지 주요 범주가 있습니다.

- 시퀀스(Sequence)
- 맵(Map)
- 셋(Set)

*시퀀스* 는 요소들의 선형 컬렉션이며, 인덱스를 가질 수도(indexed) 있고 선형(연결 리스트)일 수도 있습니다. *맵* 은 Java의 `Map`, Ruby의 `Hash`, Python의 dictionary처럼 유일한 키를 가진 키/값 쌍의 컬렉션을 담습니다. *셋* 은 중복 요소를 담지 않는 시퀀스입니다.

이 세 가지 주요 범주에 더해, `Range`, `Stack`, `Queue`를 포함한 다른 유용한 컬렉션 타입들도 있습니다. 그리고 몇몇 다른 클래스들은 컬렉션처럼 동작하는데, 여기에는 `Option`, `Try`, `Either` 에러 처리 클래스가 포함됩니다.

#### 시퀀스 선택하기 (Choosing a sequence)

*시퀀스* — 요소들의 순차적 컬렉션 — 를 선택할 때에는 두 가지 주요 결정을 내려야 합니다.

- 시퀀스가 인덱스를 가져서 어떤 요소든 빠르게 접근할 수 있어야 하는가, 아니면 연결 리스트로 구현되어야 하는가?
- 가변 컬렉션을 원하는가, 불변 컬렉션을 원하는가?

Scala 2.10부터 시작해서 Scala 3까지 이어지는, 가변/불변과 인덱스/선형의 조합에 대해 권장되는 범용(general-purpose) 순차 컬렉션은 표 11-2에 표시되어 있습니다.

*표 11-2. Scala가 권장하는 범용 순차 컬렉션*

| | 불변(Immutable) | 가변(Mutable) |
| --- | --- | --- |
| 인덱스를 가짐(Indexed) | `Vector` | `ArrayBuffer` |
| 선형(연결 리스트)(Linear) | `List` | `ListBuffer` |

이 표를 읽는 예를 들면, 불변이면서 인덱스를 가진 컬렉션을 원한다면 일반적으로 `Vector`를 사용해야 하고, 가변이면서 인덱스를 가진 컬렉션을 원한다면 `ArrayBuffer`를 사용해야 한다는 식입니다.

이것들이 범용 권장 사항이긴 하지만, 시퀀스에는 그 밖에도 더 많은 대안이 있습니다. 가장 흔한 *불변* 시퀀스 선택지는 표 11-3에 표시되어 있습니다.

*표 11-3. 주요 불변 시퀀스 선택지*

| 클래스 | IndexedSeq | LinearSeq | 설명 |
| --- | --- | --- | --- |
| `LazyList` | | ✓ | `List`와 비슷하지만, 게으르며(lazy) 요소가 메모이즈(memoize)됩니다. 크거나 무한한 시퀀스에 좋습니다. (Scala 2의 `Stream` 클래스를 대체합니다.) |
| `List` | | ✓ | 권장되는 불변 선형 시퀀스로, 단일 연결 리스트입니다. 요소를 앞에 붙이는(prepending) 데, 그리고 리스트의 head와 tail을 다루며 동작하는 재귀 알고리즘에 적합합니다. |
| `Queue` | | ✓ | 선입선출(first-in, first-out) 자료 구조입니다. 불변 버전과 가변 버전이 모두 있습니다. |
| `Range` | ✓ | | 일정한 간격으로 떨어진 정수 또는 문자(character)의 범위입니다. |
| `Vector` | ✓ | | 권장되는 불변 인덱스 시퀀스입니다. Scaladoc에서는 다음과 같이 말합니다: "사실상 상수 시간(effectively constant time)에 임의 접근과 업데이트를 제공하며, 매우 빠른 append와 prepend를 제공합니다." |

가장 흔한 *가변* 시퀀스 선택지는 표 11-4에 표시되어 있습니다. `Queue`와 `Stack`이 이 표에도 들어 있는 까닭은 이 클래스들에 불변 버전과 가변 버전이 모두 있기 때문입니다. 설명에 인용된 모든 문구는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 11-4. 주요 가변 시퀀스 선택지*

| 클래스 | IndexedSeq | LinearSeq | Buffer | 설명 |
| --- | --- | --- | --- | --- |
| `Array` | ✓ | | | Java 배열로 뒷받침되며(backed by), 요소는 가변이지만 크기는 변경할 수 없습니다. |
| `ArrayBuffer` | ✓ | | ✓ | 권장되는 가변 인덱스 시퀀스입니다. "내부적으로 배열을 사용합니다. append, update, 임의 접근은 상수 시간(상환 시간, amortized time)을 가집니다. prepend와 remove는 버퍼 크기에 선형입니다." |
| `ArrayDeque` | ✓ | | | 양방향 큐(double-ended queue)로, 가변 `Stack`과 `Queue` 클래스의 상위 클래스입니다. "append, prepend, removeFirst, removeLast, 임의 접근은 상환 상수 시간(amortized constant time)을 가집니다." |
| `ListBuffer` | | ✓ | ✓ | `ArrayBuffer`와 비슷하지만 리스트로 뒷받침됩니다. 문서에서는 "버퍼를 리스트로 변환할 계획이라면 `ArrayBuffer` 대신 `ListBuffer`를 사용하라"고 말합니다. 상수 시간의 prepend와 append를 제공하며, 대부분의 다른 연산은 선형입니다. |
| `Queue` | ✓ | | | 선입선출 자료 구조입니다. |
| `Stack` | ✓ | | | 후입선출(last-in, first-out) 자료 구조입니다. |
| `StringBuilder` | ✓ | | | "가변 문자열을 위한 빌더입니다. `java.lang.StringBuilder`와 대부분 호환되는 API를 제공합니다." |

`ArrayBuffer`와 `ListBuffer`를 두 개의 열에 걸쳐 표시했다는 점에 유의하십시오. 그 이유는 둘 다 `Buffer`의 자손인데 — `Buffer`는 커지고 줄어들 수 있는 `Seq`입니다 — `ArrayBuffer`는 `IndexedSeq`처럼 동작하고 `ListBuffer`는 `LinearSeq`처럼 동작하기 때문입니다.

이 표들에 표시된 정보 외에도, 성능(performance)이 고려 사항이 될 수 있습니다. 선택 과정에서 성능이 중요하다면 레시피 11.2를 참고하십시오.

라이브러리를 위한 API를 만들 때에는, 시퀀스를 그 상위 클래스(superclass)의 관점에서 가리키고 싶을 수 있습니다. 표 11-5는 API에서 컬렉션을 일반적으로(generically) 가리킬 때 자주 사용되는 trait들을 보여 줍니다. 설명에 인용된 모든 문구는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 11-5. 라이브러리 API에서 흔히 사용되는 trait들*

| Trait | 설명 |
| --- | --- |
| `IndexedSeq` | 요소에 대한 임의 접근이 효율적임을 암시하는 시퀀스입니다. "효율적인 `apply`와 `length`를 가집니다." |
| `LinearSeq` | 요소에 대한 선형 접근이 효율적임을 암시하는 시퀀스입니다. "효율적인 `head`와 `tail` 연산을 가집니다." |
| `Seq` | 순차 컬렉션을 위한 기본(base) trait입니다. 시퀀스가 인덱스를 가졌는지 선형인지를 나타내는 것이 중요하지 않을 때 사용합니다. |
| `Iterable` | 가장 높은 컬렉션 수준입니다. 반환되는 타입에 대해 매우 일반적이고 싶을 때 사용합니다. (대략 Java 메서드가 `Collection`을 반환한다고 선언하는 것에 해당합니다.) |

#### 맵 선택하기 (Choosing a Map)

기본 불변 또는 가변 `Map` 클래스를 사용해도 되는 경우가 많지만, 여러분이 쓸 수 있는 더 많은 타입이 있습니다. `Map` 클래스 선택지는 표 11-6에 표시되어 있습니다. 설명에 인용된 모든 문구는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 11-6. 흔한 Map 선택지*

| 클래스 | 불변 | 가변 | 설명 |
| --- | --- | --- | --- |
| `CollisionProofHashMap` | | ✓ | "버킷에 레드-블랙 트리(red-black tree)를 사용하는 해시테이블로 가변 맵을 구현하여, 해시 충돌에 대해 좋은 최악의 경우(worst-case) 성능을 제공합니다." |
| `HashMap` | ✓ | ✓ | 불변 버전은 "해시 트라이(hash trie)를 사용해 맵을 구현"하고, 가변 버전은 "해시테이블을 사용해 맵을 구현"합니다. |
| `LinkedHashMap` | | ✓ | "해시테이블을 사용해 가변 맵을 구현합니다." 요소를 삽입된 순서대로 반환합니다. |
| `ListMap` | ✓ | ✓ | 리스트 자료 구조를 사용해 구현된 맵입니다. 요소가 삽입된 순서의 역순으로 반환하는데, 각 요소가 맵의 맨 앞에 삽입되는 것처럼 동작하기 때문입니다. |
| `Map` | ✓ | ✓ | 기본 맵으로, 가변 구현과 불변 구현이 모두 있습니다. |
| `SeqMap` | | ✓ | "순서가 있는 가변 맵을 위한 일반(generic) trait입니다." |
| `SortedMap` | ✓ | ✓ | 키를 정렬된 순서로 저장하는 기본 trait입니다. |
| `TreeMap` | ✓ | ✓ | 레드-블랙 트리로 구현된 정렬 맵입니다. 여러 성능 관련 사항은 Scaladoc을 참고하십시오. |
| `TreeSeqMap` | ✓ | | 순서를 보존합니다. 기본적으로 삽입 순서를 사용하지만, 수정 순서(modification order)도 사용할 수 있습니다. |
| `VectorMap` | ✓ | | 벡터/맵 기반 자료 구조를 사용하며, 삽입 순서를 보존합니다. "상환 사실상 상수 시간(amortized effectively constant time)을 가지지만, 추가 메모리를 사용하는 비용이 들고 다른 연산에서는 대체로 성능이 더 낮습니다." |
| `WeakHashMap` | | ✓ | 약한 참조(weak references)를 가진 해시 맵으로, `java.util.WeakHashMap`을 감싼 것입니다. |

기본 `Map` 클래스에 대한 자세한 내용은 레시피 14.1 "Creating and Using Maps"를, `Map` 클래스를 선택하는 것에 대한 더 많은 정보는 레시피 14.2 "Choosing a Map Implementation"를, `Map` 클래스를 정렬하는 것에 대한 자세한 내용은 레시피 14.10 "Sorting an Existing Map by Key or Value"를 참고하십시오.

#### 셋 선택하기 (Choosing a Set)

셋을 선택할 때에는 기본 가변 및 불변 셋 클래스가 있고, 키를 기준으로 정렬된 순서로 요소를 반환하는 `SortedSet`, 요소를 삽입 순서로 저장하는 `LinkedHashSet`, 그리고 특수한 목적을 위한 다른 셋들이 있습니다. 흔한 클래스는 표 11-7에 표시되어 있습니다. 설명에 인용된 모든 문구는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 11-7. 흔한 Set 선택지*

| 클래스 | 불변 | 가변 | 설명 |
| --- | --- | --- | --- |
| `BitSet` | ✓ | ✓ | "64비트 워드(word)로 묶인, 가변 크기의 비트 배열로 표현된 음이 아닌 정수(non-negative integers)"의 셋입니다. 작은 정수들로 이루어진 셋을 가질 때 메모리를 절약하는 데 사용됩니다. |
| `HashSet` | ✓ | ✓ | 불변 버전은 "해시 트라이를 사용해 셋을 구현"하고, 가변 버전은 "해시테이블을 사용해 셋을 구현"합니다. |
| `LinkedHashSet` | | ✓ | 해시테이블을 사용해 구현된 가변 셋입니다. 요소를 삽입된 순서로 반환합니다. |
| `ListSet` | ✓ | | 리스트 구조를 사용해 구현된 셋입니다. "적은 수의 요소에만 적합합니다." |
| `TreeSet` | ✓ | ✓ | 불변 버전은 "트리를 사용해 불변 정렬 셋을 구현"합니다. 가변 버전은 "가변 레드-블랙 트리를 사용해 구현"됩니다. |
| `Set` | ✓ | ✓ | 일반 기본 trait으로, 가변 구현과 불변 구현이 모두 있습니다. |
| `SortedSet` | ✓ | ✓ | 정렬 셋을 위한 기본 trait입니다. |

기본 `Set` 클래스에 대한 자세한 내용은 레시피 15.3 "Creating a Set and Adding Elements to It"를, 정렬 가능한 셋에 대한 자세한 내용은 레시피 15.5 "Storing Values in a Set in Sorted Order"를 참고하십시오.

#### 컬렉션처럼 동작하는 타입들 (Types that act like collections)

Scala는 다른 컬렉션 타입들도 제공하며, 또한 `map`, `filter` 등의 메서드를 가지고 있어서 컬렉션처럼 동작하는 타입들도 있습니다. 표 11-8은 컬렉션은 아니지만 어느 정도 컬렉션처럼 동작하는 여러 타입에 대한 설명을 제공합니다.

*표 11-8. 다른 컬렉션 클래스들 (그리고 컬렉션처럼 동작하는 타입들)*

| 클래스/Trait | 설명 |
| --- | --- |
| `Iterator` | 컬렉션은 아니지만, 컬렉션의 요소에 접근하는 방법을 제공합니다. 다만 `foreach`, `map`, `flatMap` 등 일반 컬렉션 클래스에서 볼 수 있는 메서드를 많이 정의합니다. 필요하다면 iterator를 컬렉션으로 변환할 수 있습니다. |
| `Option` | 0개 또는 1개의 요소를 담는 컬렉션처럼 동작합니다. `Some` 클래스와 `None` object는 `Option`을 확장합니다. `Some`은 하나의 요소를 담는 컨테이너이고, `None`은 0개의 요소를 담습니다. |
| `Tuple` | 이질적인(heterogeneous) 요소들의 컬렉션을 지원합니다. `drop`, `head`, `map`, `size`, `tail`, `take`, `splitAt`, `toList` 등 일부 컬렉션 같은 메서드를 가지고 있습니다. |

표 11-8에서 `Option`을 언급했으니, `Either`/`Left`/`Right`와 `Try`/`Success`/`Failure` 클래스도 `flatten`과 `map` 같은 컬렉션 같은 메서드를 몇 개 가지고 있다는 점을 짚어 둘 만합니다. 다만 `Option`이 제공하는 것만큼 많지는 않습니다.

#### 엄격한(strict) 컬렉션과 게으른(lazy) 컬렉션 (Strict and lazy collections)

컬렉션을 바라보는 또 다른 방법은, 그것이 엄격한(strict) 것인지 비엄격한(nonstrict, 또는 *게으른(lazy)*) 것인지입니다. 엄격한 컬렉션과 게으른 컬렉션을 이해하려면 먼저 *변환 메서드(transformer method)* 의 개념을 이해하는 것이 도움이 됩니다.

*변환 메서드(transformer method)* 는 기존 컬렉션으로부터 새로운 컬렉션을 구성합니다. 여기에는 `map`, `filter`, `reverse` 등 — 입력 컬렉션을 새로운 출력 컬렉션으로 변환하는 모든 메서드 — 가 포함됩니다. 새로운 컬렉션을 반환하지 않는 다른 메서드들 — `foreach`, `size`, `head` 등 — 은 변환 메서드가 아닙니다.

그 정의에 따라, 컬렉션은 엄격한지 게으른지의 관점에서도 생각할 수 있습니다. *엄격한* 컬렉션에서는 요소를 위한 메모리가 즉시 할당되고, 변환 메서드가 호출될 때 모든 요소가 즉시 평가됩니다. 반대로, *게으른* 컬렉션에서는 요소를 위한 메모리가 즉시 할당되지 않으며, 변환 메서드는 새로운 요소가 요청되기(demanded) 전까지는 그 요소를 구성하지 않습니다.

Scala에서 모든 컬렉션은 다음 두 가지 상황을 제외하면 엄격합니다.

- `LazyList`는 `List`의 게으른 버전입니다.
- 컬렉션에 *뷰(view)* 를 만들 때 — 예를 들어 `Vector(1,2,3).view`를 호출할 때 — 그 결과로 생긴 뷰의 변환 메서드는 게으릅니다.

뷰에 대한 자세한 내용은 레시피 11.4 "Creating a Lazy View on a Collection"를 참고하십시오.

## 11.2 Understanding the Performance of Collections

### Problem

성능이 중요한 애플리케이션을 위해 컬렉션을 선택할 때, 알고리즘에 알맞은 컬렉션을 고르고자 합니다.

### Solution

많은 경우, 컬렉션의 기본 구조를 이해함으로써 그 성능을 추론할 수 있습니다. 예를 들어, `List`는 단일 연결 리스트이고 인덱스를 가지고 있지 않기 때문에, `list(1_000_000)` 같은 요소에 접근해야 한다면 백만 개의 요소를 순회해야 합니다. 따라서 인덱스를 가진 `Vector`의 백만 번째 요소에 접근하는 것보다 훨씬 느립니다.

다른 경우에는 표를 살펴보는 것이 도움이 될 수 있습니다. 예를 들어, 표 11-10은 `Vector`의 `append` 연산이 eC, 즉 *사실상 상수 시간(effectively constant time)* 임을 보여 줍니다. 그 결과, 저는 제 컴퓨터의 REPL에서 다음처럼 1초 이내에 큰 `Vector`를 만들 수 있습니다.

```scala
var a = Vector[Int]()
for i <- 1 to 50_000 do a = a :+ i
```

하지만 표가 보여 주듯, `List`의 `append` 연산은 선형 시간을 요구하므로, 같은 크기의 `List`를 만들려고 하면 훨씬 더 오래 걸립니다 — 15초 이상입니다.

이 두 가지 접근법 모두 실제 코드에는 권장되지 않는다는 점에 유의하십시오. 저는 단지 `append` 연산에 대해 `Vector`와 `List`의 성능 차이를 보여 주기 위해 사용했을 뿐입니다.

#### 성능 특성 키 (Performance characteristic keys)

성능 표를 살펴보기 전에, 표 11-9는 뒤따르는 표들에서 사용되는 성능 특성 키(keys)를 보여 줍니다.

*표 11-9. 뒤따르는 표들에서 사용되는 성능 특성 키*

| 키 | 설명 |
| --- | --- |
| Con | 연산이 (빠른) 상수 시간(constant time)을 가집니다. |
| eC | 연산이 사실상 상수 시간을 가지지만, 벡터의 최대 길이나 해시 키의 분포 같은 일부 가정에 따라 달라질 수 있습니다. |
| aC | 연산이 상환 상수 시간(amortized constant time)을 가집니다. 일부 호출은 더 오래 걸릴 수 있지만, 많은 연산이 수행되면 평균적으로 호출당 상수 시간만 걸립니다. |
| Log | 연산이 컬렉션 크기의 로그(logarithm)에 비례하는 시간을 가집니다. |
| Lin | 연산이 선형(linear)이어서, 시간이 컬렉션 크기에 비례합니다. |
| - | 연산이 지원되지 않습니다. |

#### 순차 컬렉션의 성능 특성 (Performance characteristics for sequential collections)

표 11-10은 불변 및 가변 순차 컬렉션의 연산에 대한 성능 특성을 보여 줍니다.

*표 11-10. 순차 컬렉션의 성능 특성*

| | head | tail | apply | update | prepend | append | insert |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **불변(Immutable)** | | | | | | | |
| `List` | Con | Con | Lin | Lin | Con | Lin | - |
| `LazyList` | Con | Con | Lin | Lin | Con | Lin | - |
| `ArraySeq` | Con | Lin | Con | Lin | Lin | Lin | - |
| `Vector` | eC | eC | eC | eC | eC | eC | - |
| `Queue` | aC | aC | Lin | Lin | Lin | Con | - |
| `Range` | Con | Con | Con | - | - | - | - |
| `String` | Con | Lin | Con | Lin | Lin | Lin | - |
| **가변(Mutable)** | | | | | | | |
| `ArrayBuffer` | Con | Lin | Con | Con | Lin | aC | Lin |
| `ListBuffer` | Con | Lin | Lin | Lin | Con | Con | Lin |
| `StringBuilder` | Con | Lin | Con | Con | Lin | aC | Lin |
| `Queue` | Con | Lin | Lin | Lin | Con | Con | Lin |
| `ArraySeq` | Con | Lin | Con | Con | - | - | - |
| `Stack` | Con | Lin | Lin | Lin | Con | Lin | Lin |
| `Array` | Con | Lin | Con | Con | - | - | - |
| `ArrayDeque` | Con | Lin | Con | Con | aC | aC | Lin |

표 11-11은 표 11-10에서 사용된 열 제목(column heading)을 설명합니다.

*표 11-11. 표 11-10에서 사용된 열 제목 설명*

| 연산 | 설명 |
| --- | --- |
| head | 시퀀스의 첫 번째 요소를 선택합니다. |
| tail | 첫 번째 요소를 제외한 모든 요소로 이루어진 새 시퀀스를 만듭니다. |
| apply | 인덱싱(indexing)합니다. |
| update | 불변 시퀀스에 대해서는 함수형 업데이트(functional update)를, 가변 시퀀스에 대해서는 부수 효과를 동반한 업데이트(side-effecting update)를 합니다. |
| prepend | 시퀀스의 앞에 요소를 추가합니다. 불변 시퀀스의 경우 새 시퀀스를 만들고, 가변 시퀀스의 경우 기존 시퀀스를 수정합니다. |
| append | 시퀀스의 끝에 요소를 추가합니다. 불변 시퀀스의 경우 새 시퀀스를 만들고, 가변 시퀀스의 경우 기존 시퀀스를 수정합니다. |
| insert | 시퀀스의 임의 위치에 요소를 삽입합니다. 이것은 가변 시퀀스에서만 직접 지원됩니다. |

#### 맵과 셋의 성능 특성 (Map and Set performance characteristics)

표 11-12는 Scala의 흔한 맵 및 셋 타입에 대한 성능 특성을 보여 주며, 표 11-9의 키를 사용합니다.

*표 11-12. 맵과 셋의 성능 특성*

| | lookup | add | remove | min |
| --- | --- | --- | --- | --- |
| **불변(Immutable)** | | | | |
| `HashSet`/`HashMap` | eC | eC | eC | Lin |
| `TreeSet`/`TreeMap` | Log | Log | Log | Log |
| `BitSet` | Con | Lin | Lin | eC |
| `VectorMap` | eC | eC | aC | Lin |
| `ListMap` | Lin | Lin | Lin | Lin |
| **가변(Mutable)** | | | | |
| `HashSet`/`HashMap` | eC | eC | eC | Lin |
| `WeakHashMap` | eC | eC | eC | Lin |
| `BitSet` | Con | aC | Con | eC |
| `TreeSet` | Log | Log | Log | Log |

표 11-13은 표 11-12에서 사용된 열 제목(연산)에 대한 설명을 제공합니다.

*표 11-13. 표 11-12에서 사용된 열 제목 설명*

| 연산 | 설명 |
| --- | --- |
| lookup | 어떤 요소가 셋에 포함되어 있는지 검사하거나, 맵 키에 연결된 값을 선택합니다. |
| add | 셋에 새 요소를 추가하거나, 맵에 키/값 쌍을 추가합니다. |
| remove | 셋에서 요소를 제거하거나, 맵에서 키를 제거합니다. |
| min | 셋의 가장 작은 요소, 또는 맵의 가장 작은 키입니다. |

### Discussion

표 11-9의 키 설명에서 알 수 있듯이, 컬렉션을 선택할 때에는 일반적으로 최고의 성능을 찾기 위해 Con, eC, aC 키를 살펴보고 싶을 것입니다.

예를 들어, `List`는 단일 연결 리스트이기 때문에 head와 tail 요소에 접근하는 것은 빠른 연산이며, 요소를 앞에 붙이는(prepending) 과정도 마찬가지입니다. 그래서 이러한 연산들은 표 11-10에서 Con 키로 표시되어 있습니다. 하지만 `List`에 요소를 추가하는(appending) 것은 매우 느린 연산입니다 — `List`의 크기에 비례해 선형입니다 — 그래서 append 연산은 Lin 키로 표시되어 있습니다.

## 11.3 Understanding Mutable Variables with Immutable Collections

### Problem

가변 변수(`var`)를 불변 컬렉션과 섞어 쓰면, 마치 그 컬렉션이 어떤 식으로든 가변인 것처럼 보일 수 있다는 사실을 보았을 수 있습니다. 예를 들어, 불변 `Vector`로 `var` 필드를 만들면 마치 `Vector`에 새로운 요소를 추가할 수 있는 것처럼 보입니다.

```scala
var x = Vector(1)    // x: Vector(1)
x = x :+ 2           // x: Vector(1, 2)
x = x ++ List(3, 4)  // x: Vector(1, 2, 3, 4)
```

어떻게 이런 일이 가능할까요?

### Solution

위 예제에서 마치 불변 컬렉션을 변경(mutating)하고 있는 것처럼 보이지만, 실제로 일어나는 일은 요소를 추가할 때마다 변수 `x`가 새로운 시퀀스를 가리키게 된다는 것입니다. 변수 `x`는 *가변(mutable)* — Java의 비-`final` 필드와 같습니다 — 이므로, 각 단계에서 새로운 시퀀스로 재할당되고 있는 것입니다. 최종 결과는 다음 코드와 비슷합니다.

```scala
var x = Vector(1)
x = Vector(1, 2)        // reassign x
x = Vector(1, 2, 3, 4)  // reassign x again
```

두 번째와 세 번째 줄에서, `x` 참조가 새로운 시퀀스를 가리키도록 바뀝니다.

벡터 자체가 불변임을 증명할 수 있습니다. 변수를 재할당하는 것이 아니라 그 요소 중 하나를 변경하려고 시도하면 에러가 발생합니다.

```scala
scala> x(0) = 100
1 |x(0) = 100
  |^
  |value update is not a member of Vector[Int] - did you mean
  |Vector[Int].updated?
```

### Discussion

이 레시피가 첫 번째 컬렉션 관련 레시피들 가운데 포함된 까닭은, Scala로 작업을 시작할 때 가변 변수가 불변 컬렉션과 함께 동작하는 방식이 놀라울 수 있기 때문입니다. *변수(variables)* 에 대해 분명히 정리하면 다음과 같습니다.

- 가변 변수(`var`)는 새로운 데이터를 가리키도록 재할당될 수 있습니다.
- 불변 변수(`val`)는 Java의 `final` 변수와 같습니다. 절대 재할당될 수 없습니다.

*컬렉션(collections)* 에 대해 분명히 정리하면 다음과 같습니다.

- 가변 컬렉션(예: `ArrayBuffer`)의 요소는 변경될 수 있습니다.
- 불변 컬렉션(예: `Vector`나 `List`)의 요소는 변경될 수 없습니다.

순수 함수형 프로그래밍에서는 불변 변수를 불변 컬렉션과 조합해서 사용하게 되지만, 덜 엄격한 프로그래밍 스타일에서는 다른 조합을 사용할 수 있습니다. 예를 들어, 다음 두 조합도 흔합니다.

- 불변 변수와 가변 컬렉션 (예: `ArrayBuffer`와 함께 쓰는 `val`)
- 가변 변수와 불변 컬렉션 (예: `Vector`와 함께 쓰는 `var`)

이 컬렉션 장들의 많은 레시피와 도메인 모델링 장들이 이러한 기법들을 시연합니다.

## 11.4 Creating a Lazy View on a Collection

### Problem

큰 컬렉션을 다루고 있는데, 필요할 때에만 결과를 계산하고 반환하도록 그것의 게으른(lazy) 버전을 만들고자 합니다.

### Solution

컬렉션의 `view` 메서드를 호출해서 그 컬렉션에 대한 *뷰(view)* 를 만드십시오. 그러면 *변환 메서드(transformer methods)* 가 비엄격한(nonstrict), 즉 게으른 방식으로 구현된 새로운 컬렉션이 만들어집니다. 예를 들어, 큰 리스트가 주어졌다고 합시다.

```scala
val xs = List.range(0, 3_000_000)   // 0부터 2,999,999까지의 리스트
```

이 리스트에 `map`과 `filter` 같은 여러 변환 메서드를 호출하고 싶다고 상상해 봅시다. 다음은 인위적인 예제이긴 하지만 한 가지 문제를 보여 줍니다.

```scala
val ys = xs.map(_ + 1)
            .map(_ * 10)
            .filter(_ > 1_000)
            .filter(_ < 10_000)
```

이 예제를 REPL에서 실행하려고 하면, 다음과 같은 치명적인 "out of memory" 에러를 보게 될 것입니다.

```scala
scala> val ys = xs.map(_ + 1)
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

반대로, 다음 예제는 거의 즉시 반환되고 에러를 던지지 않습니다. 이 코드가 하는 일이라고는 뷰 하나와 네 개의 게으른 변환 메서드를 만드는 것뿐이기 때문입니다.

```scala
val ys = xs.view
            .map(_ + 1)
            .map(_ * 10)
            .filter(_ > 1_000)
            .filter(_ < 10_000)

// result: ys: scala.collection.View[Int] = View(<not computed>)
```

이제 메모리 부족 없이 `ys`로 작업할 수 있습니다.

```scala
scala> ys.take(3).foreach(println)
1010
1020
1030
```

컬렉션에 `view`를 호출하면 그 결과로 생긴 컬렉션이 게으르게 됩니다. 이제 뷰에 변환 메서드를 호출하면, 요소들은 엄격한 컬렉션에서 보통 그러하듯 "즉시(eagerly)" 계산되는 것이 아니라, 접근될 때에만 계산됩니다.

### Discussion

Scala 문서는 뷰가 "결과 컬렉션을 위한 프록시(proxy)만을 구성하며, 그 요소는 요청될 때에만 구성된다… 뷰는 어떤 기반(base) 컬렉션을 나타내지만 모든 변환 메서드를 게으르게 구현하는 특별한 종류의 컬렉션"이라고 말합니다.

*변환 메서드(transformer)* 는 하나 이상의 기존 컬렉션으로부터 새로운 컬렉션을 구성하는 메서드입니다. 여기에는 `map`, `filter`, `take` 등 많은 메서드가 포함됩니다.

> **변환 메서드에 대한 공식적인 설명 (An Official Description of Transformer Methods)**
>
> `filter` 같은 메서드가 변환 메서드인지에 대해서는 다소 논쟁이 있지만, 책 *Programming in Scala*는 다음과 같이 말합니다: "우리는 이런 메서드들을 변환 메서드(transformers)라고 부르는데, 그것들은 적어도 하나의 컬렉션을 수신 객체(receiver object)로 받아 그 결과로 또 다른 컬렉션을 만들어 내기 때문이다." 이 문장에서 저자들은 구체적으로 `map`, `filter`, `++` 메서드를 가리키고 있습니다.

`foreach`처럼 컬렉션을 변환하지 않는 다른 메서드들은 즉시(eagerly) 평가됩니다. 이것이 다음과 같은 변환 메서드들이 뷰를 반환하는 이유를 설명해 줍니다.

```scala
val a = List.range(0, 1_000_000)
val b = a.view.map(_ + 1)   // SeqView[Int] = SeqView(<not computed>)
val c = b.take(3)           // SeqView[Int] = SeqView(<not computed>)
```

그리고 `foreach`가 동작(action)을 일으키는 이유도 설명해 줍니다.

```scala
scala> c.foreach(println)
1
2
3
```

#### 뷰의 사용 사례 (The use case for views)

뷰를 사용하는 주된 사용 사례는 성능 — 속도, 메모리, 또는 둘 다 — 입니다.

성능과 관련하여, Solution의 예제는 먼저 (a) 메모리 부족을 일으키는 엄격한 접근법을 보여 주고, 그다음 (b) 같은 데이터셋으로 작업하게 해 주는 게으른 접근법을 보여 줍니다. 첫 번째 해법의 문제는, 변환 메서드가 호출될 때마다 새로운 중간(intermediate) 컬렉션을 만들려고 시도한다는 점입니다.

```scala
val b = a.map(_ + 1)        // 1st copy of the data
          .map(_ * 10)      // 2nd copy of the data
          .filter(_ > 1_000)   // 3rd copy of the data
          .filter(_ < 10_000)  // 4th copy of the data
```

만약 초기 컬렉션 `a`가 10억 개의 요소를 가지고 있다면, 첫 번째 `map` 호출은 또 다른 10억 개의 요소를 가진 새 중간 컬렉션을 만듭니다. 두 번째 `map` 호출은 또 다른 컬렉션을 만들기 때문에, 이제 우리는 30억 개의 요소를 메모리에 담으려고 시도하게 되고, 이런 식으로 계속됩니다.

이 점을 확실히 짚자면, 그 접근법은 다음과 같이 작성한 것과 같습니다.

```scala
val a = List.range(0, 1_000_000_000)   // 1B elements in RAM
val b = a.map(_ + 1)        // 1st copy of the data (2B elements in RAM)
val c = b.map(_ * 10)       // 2nd copy of the data (3B elements in RAM)
val d = c.filter(_ > 1_000)    // 3rd copy of the data (~4B total)
val e = d.filter(_ < 10_000)   // 4th copy of the data (~4B total)
```

반대로, 컬렉션에 즉시 뷰를 만들면, 그 이후의 모든 것은 본질적으로 그저 iterator 하나를 만들 뿐입니다.

```scala
val ys = a.view
          .map ...   // this DOES NOT create another one billion elements
```

성능과 관련된 모든 것이 늘 그렇듯, 여러분의 애플리케이션에서 뷰를 사용하는 경우와 사용하지 않는 경우를 반드시 테스트해서 무엇이 가장 잘 동작하는지 확인하십시오.

뷰를 이해하는 또 다른 성능 관련 이유는, 큰 데이터셋을 스트리밍(streaming) 방식으로 다루는 것이 매우 흔해졌고, 뷰가 스트림(streams)과 매우 비슷하게 동작하기 때문입니다. 큰 데이터셋과 스트림을 다루는 예제는 이 책의 Spark 레시피, 예를 들어 레시피 20.1 "Getting Started with Spark"를 참고하십시오.
