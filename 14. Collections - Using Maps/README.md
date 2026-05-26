# Chapter 14. Collections: Using Maps

## 들어가며

Scala의 `Map` 타입은 Java의 `Map`, Ruby의 `Hash`, 또는 Python의 딕셔너리(dictionary)와 같습니다. 이들은 모두 키/값(key/value) 쌍으로 구성되며, 키 값은 반드시 고유해야 한다는 공통점이 있습니다. 14.1절은 불변(immutable) 맵과 가변(mutable) 맵을 생성하고 사용하는 기초를 소개합니다.

맵을 소개한 이후, 14.2절은 특별한 맵 기능을 사용해야 할 때 적절한 맵 구현(implementation)을 선택할 수 있도록 도와줍니다. 그 다음으로 14.3절과 14.4절은 각각 불변 맵과 가변 맵에서 요소를 추가하고, 갱신하고, 제거하는 과정을 다룹니다.

Java에서 Scala로 넘어온 경우, 맵과 관련해 한 가지 큰 차이점은 Scala의 기본 `Map`이 불변(immutable)이라는 점입니다. 따라서 불변 컬렉션을 다루는 데 익숙하지 않다면, 맵의 요소를 추가하거나 삭제하거나 변경하려고 할 때 크게 놀랄 수 있습니다.

맵 요소를 추가하고, 제거하고, 교체하는 것 외에도, 흔히 수행하는 맵 작업으로는 키와 값을 다루는 것(14.5절부터 14.8절까지에서 다룸), 그리고 순회(traversing, 14.9절), 정렬(sorting, 14.10절), 필터링(filtering, 14.11절) 등이 있습니다.

---

## 14.1 Creating and Using Maps (맵 생성 및 사용)

### 문제

Scala 애플리케이션에서 `Map`을 생성하고 사용하고 싶습니다. 즉, Java 맵, Python 딕셔너리, 또는 Ruby 해시처럼 키/값 쌍을 담는 자료구조가 필요합니다.

### 해결책

키/값 쌍 자료구조가 필요할 때, Scala에서는 불변(immutable) `Map` 타입과 가변(mutable) `Map` 타입을 모두 생성할 수 있습니다.

#### 불변 맵 (Immutable map)

불변 맵을 생성할 때는 `import` 문이 필요 없으며, 그냥 `Map`을 생성하면 됩니다.

```scala
val a = Map(
    "AL" -> "Alabama",
    "AK" -> "Alaska"
)
```

이 예시는 타입이 `[String, String]`인 불변 `Map`을 생성합니다. 이는 *키(key)* 와 *값(value)* 이 모두 `String` 타입임을 의미합니다. 첫 번째 요소에서 문자열 `AL`은 키이고, `Alabama`는 값입니다.

불변 맵을 한번 가지게 되면, 요소를 추가하거나, 갱신하거나, 제거하도록 지정하면서 그 결과로 생긴 `Map`을 새로운 변수에 할당할 수 있습니다. 다음 예시들이 이를 보여줍니다.

```scala
// create a map
val a = Map(1 -> "a")

// adding elements
val b = a + (2 -> "b")
val c = b ++ Map(3 -> "c", 4 -> "d")
val d = c ++ List(5 -> "e", 6 -> "f")

// current result:
d: Map[Int, String] = HashMap(5 -> e, 1 -> a, 6 -> f, 2 -> b, 3 -> c, 4 -> d)

// update where the key is 1
val e = d + (1 -> "AA")
    // e: HashMap(5 -> e, 1 -> AA, 6 -> f, 2 -> b, 3 -> c, 4 -> d)

// update multiple elements at one time
val f = e ++ Map(2 -> "BB", 3 -> "CC")
val g = f ++ List(2 -> "BB", 3 -> "CC")
    // g: HashMap(5 -> e, 1 -> AA, 6 -> f, 2 -> BB, 3 -> CC, 4 -> d)

// remove elements by specifying the keys to remove
val h = g - 1
val i = h -- List(1, 2, 3)
    // i: HashMap(5 -> e, 6 -> f, 4 -> d)
```

불변 맵을 다룰 때, 변수를 `var` 필드로 생성하면 서로 다른 변수 이름을 계속 사용할 필요가 없습니다.

```scala
// reassign each update to the 'map' variable
var map = Map(1 -> "a")
map = map + (2 -> "b")   // map: Map(1 -> a, 2 -> b)
map = map + (3 -> "c")   // map: Map(1 -> a, 2 -> b, 3 -> c)
```

#### 가변 맵 (Mutable map)

*가변(mutable)* 맵을 생성하려면, `import` 문을 사용해 스코프(scope)로 가져오거나, 인스턴스를 생성할 때 `scala.collection.mutable.Map` 클래스의 전체 경로를 지정합니다. 가변 맵을 스코프에 가지게 되면, 다음 레시피들에서 설명하는 방식대로 사용할 수 있습니다. 다음은 몇 가지 기본 기능에 대한 간단한 시연입니다.

```scala
// create an empty, mutable map
val m = scala.collection.mutable.Map[Int, String]()

// adding by assignment
m(1) = "a"                      // m: HashMap(1 -> a)

// adding with += and ++=
m += (2 -> "b")                 // m: HashMap(1 -> a, 2 -> b)
m ++= Map(3 -> "c", 4 -> "d")   // m: HashMap(1 -> a, 2 -> b, 3 -> c, 4 -> d)
m ++= List(5 -> "e", 6 -> "f")  // m: HashMap(1 -> a, 2 -> b, 3 -> c, 4 -> d,
                                //            5 -> e, 6 -> f)

// remove elements by specifying the keys to remove
m -= 1            // m: HashMap(2 -> b, 3 -> c, 4 -> d, 5 -> e, 6 -> f)
m --= List(2,3)   // m: HashMap(4 -> d, 5 -> e, 6 -> f)

// updating
m(4) = "DD"       // m: HashMap(4 -> DD, 5 -> e, 6 -> f)
```

### 논의

다른 프로그래밍 언어의 맵과 마찬가지로, Scala의 맵은 키/값 쌍의 컬렉션입니다. Java의 맵, Python의 딕셔너리, 또는 Ruby의 해시를 사용해 본 적이 있다면, Scala의 맵은 직관적으로 이해될 것입니다. 단지 몇 가지 새로운 점만 알면 되는데, 맵 클래스에서 사용할 수 있는 메서드들과, 정렬된 맵(sorted maps)을 사용하는 경우처럼 특정 상황에서 유용할 수 있는 특수 맵들이 그것입니다.

맵 안에서 괄호 안에 사용되는 문법은 튜플(tuple)을 생성한다는 점에 유의하세요.

```scala
"AL" -> "Alabama"
```

튜플-2(tuple-2)는 `("AL", "Alabama")`처럼 선언할 수도 있기 때문에, 맵이 다음과 같이 생성되는 것을 볼 수도 있습니다.

```scala
val states = Map(
    ("AL", "Alabama"),
    ("AK", "Alaska")
)
```

가변 맵을 사용하고 있다는 점을 명확히 드러내고 싶다면, 한 가지 방법은 `import`할 때 가변 `Map` 클래스에 별칭(alias)을 부여하고, 그 별칭으로 참조하는 것입니다.

```scala
import scala.collection.mutable.{Map => MMap}

// MMap is really scala.collection.mutable.Map
val m = MMap(1 -> 'a')   // m: Map[Int, Char] = HashMap(1 -> a)
m += (2 -> 'b')          // m: HashMap(1 -> a, 2 -> b)
```

이러한 별칭 기법은 9.3절 "Renaming Members on Import"에서 더 자세히 설명됩니다.

---

## 14.2 Choosing a Map Implementation (맵 구현 선택하기)

### 문제

특정 문제에 맞는 `Map` 클래스를 선택해야 합니다.

### 해결책

Scala에는 선택할 수 있는 풍부한 `Map` 타입들이 있으며, 심지어 Java `Map` 클래스를 사용할 수도 있습니다. 논의(Discussion)에서 대부분의 클래스에 대한 포괄적인 목록을 제공하지만, 가장 널리 쓰이는 것들은 다음과 같습니다.

**불변 `Map` (The immutable Map)**
: 기본적인 불변 맵으로, 키가 반환되는 순서에 대한 보장이 없습니다.

**가변 `Map` (The mutable Map)**
: 기본적인 가변 맵으로, 키가 반환되는 순서에 대한 보장이 없습니다.

**`SortedMap`**
: 요소들을 키 기준으로 정렬된 순서로 반환합니다.

**`LinkedHashMap`**
: 요소들을 삽입된 순서대로 반환합니다.

**`VectorMap`**
: 벡터/맵 기반 자료구조에 요소를 저장하여 삽입 순서를 보존합니다.

**`WeakHashMap`**
: 해당 Scaladoc에 따르면, "키가 더 이상 강하게 참조되지 않으면(strongly referenced) 맵 항목이 제거됩니다."

기본적인 불변 맵과 가변 맵은 다른 여러 레시피에서 자세히 다루므로 여기서는 다루지 않습니다. `SortedMap`과 `LinkedHashMap`은 14.10절에서 다루므로 여기서는 간단히만 짚고 넘어갑니다.

#### 기본적인 불변 맵과 가변 맵

정렬이나 삽입 순서가 중요하지 않은 기본 `Map` 클래스를 찾고 있다면, 기본 불변 `Map`을 선택하거나, 14.1절에서 보여준 것처럼 가변 `Map`을 `import`할 수 있습니다.

#### `SortedMap`

요소를 키 기준 정렬된 순서로 반환하는 `Map`을 원한다면, `SortedMap`을 사용합니다.

```scala
import scala.collection.SortedMap
val x = SortedMap(
    2 -> "b",
    4 -> "d",
    3 -> "c",
    1 -> "a"
)
```

이 코드를 REPL에 붙여넣으면 다음과 같은 결과를 볼 수 있습니다.

```scala
val x: scala.collection.SortedMap[Int, String]
    = TreeMap(1 -> a, 2 -> b, 3 -> c, 4 -> d)
```

#### `LinkedHashMap`, `VectorMap`, `ListMap`으로 삽입 순서 기억하기

요소의 삽입 순서를 기억하는 `Map`을 원한다면, `LinkedHashMap`, `VectorMap`, 또는 경우에 따라 `ListMap`을 사용합니다. `LinkedHashMap`은 가변 형태로만 제공되므로 먼저 다음과 같이 `import`해야 합니다.

```scala
import collection.mutable.LinkedHashMap
```

이제 `LinkedHashMap`을 생성하면, 요소들이 삽입된 순서대로 저장됩니다.

```scala
val x = LinkedHashMap(    // x: LinkedHashMap(1 -> a, 2 -> b)
    1 -> "a",
    2 -> "b"
)
x += (3 -> "c")           // x: LinkedHashMap(1 -> a, 2 -> b, 3 -> c)
x += (4 -> "d")           // x: LinkedHashMap(1 -> a, 2 -> b, 3 -> c, 4 -> d)
```

해당 Scaladoc에 따르면, `VectorMap`은 "벡터/맵 기반 자료구조를 사용하여 불변 맵을 구현하며, 이는 삽입 순서를 보존합니다…`VectorMap`은 추가 메모리를 사용하는 대가로 분할 상환(amortized) 시 사실상 상수 시간의 조회(lookup)를 제공하지만, 다른 연산들에 대해서는 일반적으로 더 낮은 성능을 보입니다." `VectorMap`은 기본 불변 맵처럼 동작하지만 삽입 순서를 보존합니다.

```scala
import collection.immutable.VectorMap

val a = VectorMap(            // a: VectorMap(10 -> a)
    10 -> "a"
)
val b = a ++ Map(7 -> "b")   // b: VectorMap(10 -> a, 7 -> b)
val c = b ++ Map(3 -> "c")   // c: VectorMap(10 -> a, 7 -> b, 3 -> c)
```

불변 `ListMap`을 사용할 수도 있지만, 이는 다소 특이한 존재입니다. 내부적으로 요소들이 `List`처럼 저장되며, 가장 최근의 요소가 헤드(head) 위치에 저장됩니다. 그러나 `ListMap`의 Scaladoc에 따르면, "리스트 맵의 이터레이터와 모든 순회 메서드는 키/값 쌍을 *처음 삽입된 순서대로* 방문합니다." 즉, 꼬리에서 머리(tail-to-head) 방향으로 순회하는 것입니다. 이 때문에 성능이 좋지 않을 수 있으므로, `ListMap`은 작은 컬렉션에만 적합합니다. 성능에 대한 자세한 내용은 불변 `ListMap`의 Scaladoc을 참고하세요.

### 논의

표 14-1은 기본적인 Scala 맵 클래스와 트레이트를 요약하여 각각에 대한 간략한 설명을 제공합니다. 인용 부호 안의 텍스트는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 14-1. 기본 맵 클래스와 트레이트*

| 클래스 또는 트레이트 | 설명 |
| --- | --- |
| `collection.immutable.Map` | 아무것도 `import`하지 않으면 얻게 되는 기본 불변 맵입니다. |
| `collection.mutable.Map` | 기본 맵의 가변 버전입니다. |
| `collection.immutable.VectorMap` | "벡터/맵 기반 자료구조로, 삽입 순서를 보존합니다. 추가 메모리를 사용하는 대가로 분할 상환 시 사실상 상수 시간의 조회를 제공하지만, 다른 연산들에 대해서는 일반적으로 더 낮은 성능을 보입니다." |
| `collection.mutable.LinkedHashMap` | "이터레이터와 모든 순회 메서드는 요소들을 삽입된 순서대로 방문합니다." |
| `collection.immutable.SortedMap` | 맵의 키들이 정렬된 순서로 반환됩니다. "키/값 쌍은 키에 대한 `scala.math.Ordering`에 따라 정렬됩니다." |

이것들이 가장 흔히 사용되는 맵들이지만, Scala는 더 많은 맵 타입을 제공합니다. 이들은 표 14-2에 요약되어 있으며, 인용 부호 안의 텍스트는 각 클래스의 Scaladoc에서 가져온 것입니다.

*표 14-2. 추가적인 맵 클래스와 트레이트*

| 클래스 또는 트레이트 | 설명 |
| --- | --- |
| `collection.immutable.HashMap` | "압축된 해시-배열 매핑된 접두사 트리(Compressed Hash-Array Mapped Prefix-tree)를 사용하여 불변 맵을 구현합니다." |
| `collection.immutable.TreeMap` | "이 클래스는 범위 질의(range queries)가 수행되거나, 어떤 순서대로의 순회가 필요할 때 최적입니다." |
| `collection.mutable.WeakHashMap` | `java.util.WeakHashMap`을 감싼 래퍼(wrapper)로, "키가 더 이상 강하게 참조되지 않으면 맵 항목이 제거됩니다." |
| `collection.immutable.ListMap` | "리스트 기반 자료구조를 사용하여 불변 맵을 구현합니다." 여러 연산이 `O(n)`이므로 "이 컬렉션은 적은 수의 요소에만 적합합니다." |

`scala-collection-contrib` 라이브러리에는 몇 가지 맵 클래스가 더 있으며, 이 라이브러리는 "Scala 2.13 표준 컬렉션에 대한 다양한 추가 기능을 제공합니다." 이 라이브러리의 `MultiDict` 클래스는 기존의 `MultiMap` 클래스를 대체하며, "주어진 키에 값들의 집합(set)을 연결할 수 있습니다." 또한 `MultiDict`의 정렬된 버전인 `SortedMultiDict`도 있습니다.

#### 병렬 맵 클래스 (Parallel map classes)

Scala 2.13부터 병렬 컬렉션 라이브러리(parallel collections library)는 별도의 JAR 파일로 분리되었으며, 현재 GitHub에서 유지보수되고 있습니다. 이 라이브러리에는 `collection.parallel.immutable.ParMap`, `collection.parallel.immutable.ParHashMap`, `collection.parallel.mutable.ParHashMap` 등과 같은 병렬/동시성(parallel/concurrent) 맵 구현들이 포함되어 있습니다. 자세한 내용은 해당 프로젝트를 참고하세요.

---

## 14.3 Adding, Updating, and Removing Immutable Map Elements (불변 맵 요소 추가, 갱신, 제거)

### 문제

*불변(immutable)* 맵을 다룰 때 요소를 추가하거나, 갱신하거나, 삭제하고 싶습니다.

### 해결책

불변 `Map`은 제자리에서(in place) 갱신할 수 없습니다. 따라서 다음과 같이 합니다.

- 각 목적에 맞는 함수형 메서드(functional method)를 적용합니다.
- 그 결과를 새로운 변수에 할당하는 것을 잊지 마세요.

이 접근 방식을 명확히 하기 위해, 다음 예시들은 일련의 `val` 변수와 함께 불변 맵을 사용합니다. 먼저, 불변 맵을 `val`로 생성합니다.

```scala
val a = Map(1 -> "a")     // a: Map[Int, String] = Map(1 -> a)
```

#### 요소 추가하기 (Adding elements)

하나의 키/값 쌍을 추가할 때는 `+` 메서드를 사용하며, 그 과정에서 결과를 새로운 변수에 할당합니다.

```scala
// add one element
val b = a + (2 -> "b")    // b: Map(1 -> a, 2 -> b)
```

둘 이상의 키/값 쌍을 추가할 때는 `++`와 컬렉션을 사용합니다.

```scala
// add multiple elements
val c = b ++ Map(3 -> "c", 4 -> "d")
    // c: Map(1 -> a, 2 -> b, 3 -> c, 4 -> d)

val d = c ++ List(5 -> "e", 6 -> "f")
    // d: HashMap(5 -> e, 1 -> a, 6 -> f, 2 -> b, 3 -> c, 4 -> d)
```

#### 요소 갱신하기 (Updating elements)

불변 맵에서 하나의 키/값 쌍을 갱신하려면, `+` 메서드를 사용하면서 키와 값을 다시 할당하면, 새 값이 기존 값을 대체합니다.

```scala
val e = d + (1 -> "AA")
    // e: HashMap(5 -> e, 1 -> AA, 6 -> f, 2 -> b, 3 -> c, 4 -> d)
```

여러 개의 키/값 쌍을 갱신하려면, 새 값들을 맵 또는 튜플의 시퀀스로 공급합니다.

```scala
// update multiple elements at once with a Map
val e = d ++ Map(2 -> "BB", 3 -> "CC")
    // e: HashMap(5 -> e, 1 -> a, 6 -> f, 2 -> BB, 3 -> CC, 4 -> d)

// update multiple elements at once with a List
val e = d ++ List(2 -> "BB", 3 -> "CC")
    // e: HashMap(5 -> e, 1 -> a, 6 -> f, 2 -> BB, 3 -> CC, 4 -> d)
```

#### 요소 제거하기 (Removing elements)

하나의 요소를 제거하려면, `-` 메서드를 사용하여 제거할 키를 지정합니다.

```scala
val e = d - 1     // e: HashMap(5 -> e, 6 -> f, 2 -> b, 3 -> c, 4 -> d)
```

여러 개의 요소를 제거하려면, `--` 메서드를 사용합니다.

```scala
val e = d -- List(1, 2, 3)    // e: HashMap(5 -> e, 6 -> f, 4 -> d)
```

### 논의

불변 맵을 `var`로 선언한 다음, 각 연산의 결과를 같은 변수에 다시 할당할 수도 있습니다. `var`를 사용하면 앞의 예시들이 다음과 같은 모습이 됩니다.

```scala
// declare the map variable as a `var`
var a = Map(1 -> "a")

// add one element
a = a + (2 -> "b")

// add multiple elements
a = a ++ Map(3 -> "c", 4 -> "d")
a = a ++ List(5 -> "e", 6 -> "f")

// update where the key is 1
a = a + (1 -> "AA")

// update multiple elements at one time
a = a ++ Map(2 -> "BB", 3 -> "CC")
a = a ++ List(4 -> "DD", 5 -> "EE")

// remove one element by specifying its key
a = a - 1

// remove multiple elements
a = a -- List(2, 3)
```

이 예시들에서는 `a`가 `var`로 정의되어 있기 때문에, 각 단계마다 다시 할당되고 있습니다.

---

## 14.4 Adding, Updating, and Removing Elements in Mutable Maps (가변 맵의 요소 추가, 갱신, 제거)

### 문제

가변(mutable) 맵에서 요소를 추가하거나, 제거하거나, 갱신하고 싶습니다.

### 해결책

가변 맵에 요소를 추가하는 방법은 다음과 같습니다.

- 다음 문법으로 할당합니다: `map(key) = value`
- `+=`를 사용합니다.
- `++=`를 사용합니다.

요소를 제거하는 방법은 다음과 같습니다.

- `-=`를 사용합니다.
- `--=`를 사용합니다.

요소를 갱신하려면 키를 새 값에 다시 할당합니다. 논의(Discussion)에서는 `put`, `filterInPlace`, `remove`, `clear`를 포함하여 사용할 수 있는 추가 메서드들을 보여줍니다.

새로운 가변 `Map`이 주어졌을 때,

```scala
val m = scala.collection.mutable.Map[Int, String]()
```

키를 값에 할당하여 맵에 요소를 추가할 수 있습니다.

```scala
m(1) = "a"        // m: HashMap(1 -> a)
```

`+=` 메서드로 개별 요소를 추가할 수도 있습니다.

```scala
m += (2 -> "b")   // m: HashMap(1 -> a, 2 -> b)
```

`++=`를 사용하여 다른 컬렉션으로부터 여러 요소를 추가합니다.

```scala
m ++= Map(3 -> "c", 4 -> "d")
    // m: HashMap(1 -> a, 2 -> b, 3 -> c, 4 -> d)

m ++= List(5 -> "e", 6 -> "f")
    // m: HashMap(1 -> a, 2 -> b, 3 -> c, 4 -> d, 5 -> e, 6 -> f)
```

`-=` 메서드로 키를 지정하여 맵에서 하나의 요소를 제거합니다.

```scala
m -= 1            // m: HashMap(2 -> b, 3 -> c, 4 -> d, 5 -> e, 6 -> f)
```

`--=` 메서드로 키를 지정하여 여러 요소를 제거합니다.

```scala
m --= List(2,3)   // m: HashMap(4 -> d, 5 -> e, 6 -> f)
```

요소의 키를 새 값에 다시 할당하여 요소를 갱신합니다.

```scala
m(4) = "DD"       // m: HashMap(4 -> DD, 5 -> e, 6 -> f)
```

논의에서는 가변 맵을 수정하는 더 많은 방법을 보여줍니다.

### 논의

해결책에서 보여준 메서드들은 가장 흔한 접근 방식들을 보여줍니다. 다음의 메서드들도 사용할 수 있습니다.

- `put`: 키/값 쌍을 추가하거나, 기존 값을 교체합니다.
- `filterInPlace`: 여러분이 공급하는 술어(predicate)에 일치하는 요소들만 맵에 남깁니다.
- `remove`: 키 값으로 요소를 제거합니다.
- `clear`: 맵의 모든 요소를 삭제합니다.

예를 들어, 다음과 같은 가변 `Map`이 주어졌을 때,

```scala
val m = collection.mutable.Map(
    "AK" -> "Alaska",
    "IL" -> "Illinois",
    "KY" -> "Kentucky"
)
```

이 메서드들은 다음 예시들에서 보여집니다.

```scala
// returns None if the key WAS NOT in the map
val x = m.put("CO", "Colorado")
    // x: Option[String] = None
    // m: HashMap(AK -> Alaska, IL -> Illinois, CO -> Colorado, KY -> Kentucky)

// returns Some if the key WAS in the map
val x = m.put("CO", "Colorado")
    // x: Option[String] = Some(Colorado)
    // m: HashMap(AK -> Alaska, IL -> Illinois, CO -> Colorado, KY -> Kentucky)

m.filterInPlace((k,v) => k == "AK")
    // m: HashMap(AK -> Alaska)

// `remove` returns a Some if the key WAS in the map
val x = m.remove("AK")
    // x: Option[String] = Some(Alaska)
    // m: collection.mutable.Map[String, String] = HashMap()

// `remove` returns a None if the key WAS NOT in the map
val x = m.remove("FOO")
    // x: Option[String] = None
    // m: collection.mutable.Map[String, String] = HashMap()

m.clear   // m: HashMap()
```

예시 안의 주석들은 `put`이나 `remove` 같은 메서드들이 언제 `Some`과 `None` 값을 반환하는지 설명합니다.

---

## 14.5 Accessing Map Values (Without Exceptions) (예외 없이 맵 값 접근하기)

### 문제

예외를 던지지 않으면서 맵에 저장된 개별 값에 접근하고 싶습니다. 예를 들어, 다음과 같은 작은 맵이 주어졌을 때,

```scala
val states = Map(
    "AL" -> "Alabama",
)
```

배열 요소에 접근하듯이 키에 연관된 값에 접근할 수 있습니다.

```scala
val s = states("AL")   // s: Alabama
```

그러나 맵에 요청한 키가 없으면 `java.util.NoSuchElementException` 예외가 던져집니다.

```scala
val s = states("YO")   // java.util.NoSuchElementException: key not found: YO
```

### 해결책

예외를 피하려면 다음을 사용합니다.

- 맵을 생성할 때 `withDefaultValue`
- 요소에 접근할 때 `getOrElse`
- 맵 값을 `Some` 또는 `None`으로 반환하는 `get`

예를 들어, 다음과 같은 맵이 주어졌을 때,

```scala
val states = Map(
    "AL" -> "Alabama",
    "AK" -> "Alaska",
    "AZ" -> "Arizona"
)
```

이 문제를 피하는 한 가지 방법은 `withDefaultValue` 메서드로 맵을 생성하는 것입니다.

```scala
val states = Map(
    "AL" -> "Alabama"
).withDefaultValue("Not found")
```

이름이 암시하듯, 이는 키를 찾을 수 없을 때 맵이 반환할 기본값을 생성합니다.

```scala
val x = states("AL")   // x: Alabama
val x = states("yo")   // x: Not found
```

또 다른 해결책은 값을 찾으려 할 때 `getOrElse` 메서드를 사용하는 것입니다. 이 메서드는 키를 찾을 수 없으면 여러분이 지정한 기본값을 반환합니다.

```scala
val s = states.getOrElse("yo", "No such state")   // s: No such state
```

또 다른 해결책은 `get` 메서드를 사용하는 것으로, 이는 `Option`을 반환합니다.

```scala
val x = states.get("AZ")   // x: Some(Arizona)
val x = states.get("yo")   // x: None
```

이 세 가지 옵션이 있다는 것은 좋은 일인데, 여러분이 선호하는 프로그래밍 스타일에 따라 서로 다른 방식으로 작업할 수 있기 때문입니다.

---

## 14.6 Testing for the Existence of a Key or Value in a Map (맵에 키 또는 값이 존재하는지 검사하기)

### 문제

맵이 주어진 키 또는 값을 포함하는지 검사하고 싶습니다.

### 해결책

맵에 키가 존재하는지 검사하려면 `contains` 또는 `get` 메서드를 사용합니다. 맵에 값이 존재하는지 검사하려면 `valuesIterator` 메서드를 사용합니다.

#### 키 검사하기 (Testing for keys)

맵에 *키(key)* 가 존재하는지 검사할 때는 `contains` 메서드를 사용하는 것이 직관적인 해결책입니다.

```scala
val states = Map(
    "AK" -> "Alaska",
    "IL" -> "Illinois",
    "KY" -> "Kentucky"
)

states.contains("FOO")   // false
states.contains("AK")    // true
```

필요에 따라, 맵에 `get`을 호출하여 `None`을 반환하는지 `Some`을 반환하는지 확인할 수도 있습니다.

```scala
states.get("FOO")        // None
states.get("AK")         // Some(Alaska)
```

예를 들어, 이 접근 방식은 `match` 표현식에서 사용할 수 있습니다.

```scala
states.get("AK") match
    case Some(state) => println(s"state = $state")
    case None => println("state not found")
```

이전 레시피에서 보여준 것처럼, 맵에 없는 키를 사용하려고 하면 예외가 던져진다는 점에 유의하세요.

```scala
states("AL")             // java.util.NoSuchElementException: key not found: AL
```

#### 값 검사하기 (Testing for values)

맵에 *값(value)* 이 존재하는지 검사하려면, `valuesIterator` 메서드를 사용하여 `contains`로 값을 검색합니다.

```scala
states.valuesIterator.contains("Alaska")     // true
states.valuesIterator.contains("Kentucky")   // true
states.valuesIterator.contains("ucky")       // false
```

이것이 동작하는 이유는 (a) `valuesIterator` 메서드가 `Iterator`를 반환하고,

```scala
states.valuesIterator   // Iterator[String] = <iterator>
```

(b) `contains`가 여러분이 공급한 요소가 맵의 값이면 `true`를 반환하기 때문입니다. 맵의 값에 대해 더 많은 검색 연산을 수행해야 한다면, 다음 접근 방식들을 사용할 수도 있습니다.

```scala
states.valuesIterator.exists(_.contains("ucky"))   // true
states.valuesIterator.exists(_.matches("Ala.*"))   // true
```

`exists`는 함수를 받기 때문에, 더 복잡한 키 값과 여러분만의 알고리즘으로 이 기법을 사용할 수 있습니다.

### 논의

이런 식으로 메서드를 체이닝(chaining)할 때는, 특히 큰 컬렉션을 다룰 때 중간 결과(intermediate results)에 주의해야 합니다. 이 레시피에서 저자는 원래 맵에서 값을 가져오기 위해 `values` 메서드를 사용했지만, 이는 새로운 중간 컬렉션을 만들어 냅니다. 맵이 크다면 이는 많은 메모리를 소비할 수 있습니다. 반대로, `valuesIterator` 메서드는 가벼운 이터레이터를 반환합니다.

---

## 14.7 Getting the Keys or Values from a Map (맵에서 키 또는 값 가져오기)

### 문제

맵에서 모든 키 또는 값을 가져오고 싶습니다.

### 해결책

#### 키 (Keys)

키를 가져오려면 다음 메서드들을 사용합니다.

- `keySet`: 키들을 `Set`으로 가져옵니다.
- `keys`: 키들을 `Iterable`로 가져옵니다.
- `keysIterator`: 키들을 이터레이터로 가져옵니다.

이 메서드들은 다음 예시들에서 보여집니다.

```scala
val states = Map("AK" -> "Alaska", "AL" -> "Alabama", "AR" -> "Arkansas")
states.keySet         // Set[String] = Set(AK, AL, AR)
states.keys           // Iterable[String] = Set(AK, AL, AR)
states.keysIterator   // Iterator[String] = <iterator>
```

#### 값 (Values)

맵에서 값을 가져오려면 다음을 사용합니다.

- `values` 메서드: 값들을 `Iterable`로 가져옵니다.
- `valuesIterator`: 값들을 `Iterator`로 가져옵니다.

이 메서드들은 다음 예시들에서 보여집니다.

```scala
states.values         // Iterable[String] = Iterable(Alaska, Alabama, Arkansas)
states.valuesIterator // Iterator[String] = <iterator>
```

### 논의

이 예시들에서 보여주듯, `keysIterator`와 `valuesIterator`는 맵 데이터로부터 이터레이터를 반환합니다. 큰 맵을 가지고 있을 때는 일반적으로 이 메서드들을 사용하고 싶을 텐데, 새로운 컬렉션을 만들지 않고 기존 요소들을 순회할 이터레이터만 제공하기 때문입니다.

또한 맵의 값을 변환하고 싶다면, 14.9절에서 보여주는 것처럼 `view.mapValues` 또는 `transform` 메서드를 사용한다는 점에 유의하세요.

---

## 14.8 Finding the Largest (or Smallest) Key or Value in a Map (맵에서 가장 큰 (또는 가장 작은) 키 또는 값 찾기)

### 문제

맵에서 가장 큰 또는 가장 작은 키 또는 값을 찾고 싶습니다.

### 해결책

필요와 맵 데이터에 따라, 맵에 `max`와 `min` 메서드를 사용하거나, 맵의 `keysIterator` 또는 `valuesIterator`를 다른 접근 방식과 함께 사용합니다.

가장 큰 *키* 와 가장 작은 *키* 를 찾는 두 가지 접근 방식이 해결책에서 보여지며, 가장 큰 *값* 과 가장 작은 *값* 을 찾는 접근 방식은 논의에서 보여집니다.

맵 키를 다루는 접근 방식을 시연하기 위해, 먼저 샘플 `Map`을 생성합니다.

```scala
val grades = Map(
    "Al" -> 80,
    "Kim" -> 95,
    "Teri" -> 85,
    "Julia" -> 90
)
```

이 맵에서 키는 `String` 타입이므로, 어떤 키가 "가장 큰지"는 여러분의 정의에 따라 달라집니다. 맵에 `max`와 `min` 메서드를 호출하여 자연스러운 `String` 정렬 순서로 가장 큰 또는 가장 작은 키를 찾을 수 있습니다.

```scala
grades.max   // (Teri,85)
grades.min   // (Al,80)
```

`Teri`의 `T`가 이름들 중 알파벳에서 가장 뒤에 있기 때문에 최댓값(max)으로 반환됩니다. 같은 이유로 `Al`이 최솟값(min)으로 반환됩니다.

`keysIterator`를 호출하여—이는 `scala.collection.Iterator`를 반환합니다—맵 키에 대한 이터레이터를 얻은 뒤, 그 이터레이터에 `max`와 `min` 메서드를 호출할 수도 있습니다.

```scala
grades.keysIterator.max   // Teri
grades.keysIterator.min   // Al
```

추가로, `keysIterator`를 가져와 `reduceLeft`를 사용하여 동일한 max와 min 값을 찾을 수도 있습니다.

```scala
scala> grades.keysIterator.reduceLeft((x,y) => if x > y then x else y)
val res2: String = Teri

scala> grades.keysIterator.reduceLeft((x,y) => if x < y then x else y)
val res3: String = Al
```

이 접근 방식은 유연한데, '가장 큰 것'에 대한 여러분의 정의가 가장 긴 문자열이라면, 문자열 길이를 비교할 수도 있기 때문입니다.

```scala
scala> grades.keysIterator.reduceLeft((x,y) => if x.length > y.length then x else y)
res4: String = Julia
```

### 논의

맵에서 가장 큰 그리고 가장 작은 *값(values)* 을 찾아야 할 때는, `valuesIterator` 메서드를 사용하여 값들을 순회하면서, 이터레이터에 `max` 또는 `min` 메서드를 호출합니다.

```scala
grades.valuesIterator.max   // 95
grades.valuesIterator.min   // 80
```

이것이 동작하는 이유는 `Map`의 값들이 `Int` 타입이고, `Int`는 암시적(implicit) `Ordering`을 가지기 때문입니다. 암시적 변환(implicit conversions)에 대한 공식 문서에 따르면, "암시적 메서드 `Int => Ordered[Int]`가 `scala.Predef.intWrapper`를 통해 자동으로 제공됩니다."

반대로, `Map`에서 값으로 사용되는 타입이 `Ordering`을 제공하지 않으면, 이 접근 방식은 동작하지 않습니다. `scala.math.Ordered` 또는 `scala.math.Ordering` 트레이트를 구현하는 것에 대한 자세한 내용은 13.14절 "Sorting a Collection"과 12.11절 "Sorting Arrays"를 참고하세요.

마찬가지로, 선호한다면 `max`와 `min`을 `reduceLeft`와 함께 사용할 수도 있습니다.

```scala
grades.valuesIterator.reduceLeft(_ max _)   // 95
grades.valuesIterator.reduceLeft(_ min _)   // 80
```

`reduceLeft`를 사용하는 것의 이점은 *어떤* 타입의 값이든 여러분만의 커스텀 알고리즘으로 비교할 수 있다는 것이며, 이는 더 복잡한 데이터 타입에서 필요로 할 수 있는 작업을 대표적으로 보여줍니다. 다음 예시들은 약간 더 복잡한 알고리즘으로 `reduceLeft`를 사용하는 방법을 보여줍니다.

```scala
// max
scala> grades.valuesIterator.reduceLeft((x,y) => if x > y then x else y)
val res5: Int = 95

// min
scala> grades.valuesIterator.reduceLeft((x,y) => if x < y then x else y)
val res6: Int = 80
```

이제 이 `x`와 `y` 값에 어떻게 접근하는지 보았으니, 비교에 필요한 어떤 알고리즘이든 만들 수 있습니다.

---

## 14.9 Traversing a Map (맵 순회하기)

### 문제

맵 안의 요소들을 순회(iterate over)하고 싶습니다.

### 해결책

맵 안의 요소들을 순회하는 여러 가지 방법이 있습니다. 다음과 같은 샘플 맵이 주어졌을 때,

```scala
val ratings = Map(
    "Lady in the Water"-> 3.0,
    "Snakes on a Plane"-> 4.0,
    "You, Me and Dupree"-> 3.5
)
```

모든 맵 요소를 반복하는 좋은 방법은 다음과 같은 `for` 루프 문법입니다.

```scala
for (k,v) <- ratings do println(s"key: $k, value: $v")
```

익명 함수(anonymous function)와 함께 `foreach`를 사용하는 것도 매우 읽기 쉬운 접근 방식입니다.

```scala
ratings.foreach {
    case(movie, rating) => println(s"key: $movie, value: $rating")
}
```

이 접근 방식은 튜플 문법을 사용하여 키와 값 필드에 접근하는 방법을 보여줍니다.

```scala
ratings.foreach(x => println(s"key: ${x._1}, value: ${x._2}"))
```

참고: 이것들은 저자의 영화 평점이 아닙니다. 이들은 Toby Segaran의 책 *Programming Collective Intelligence* (O'Reilly)에서 가져온 것입니다.

### 논의

익명 함수와 함께 사용하는 `foreach` 메서드는 Scala 3에서 세 가지 다른 방식으로 작성할 수 있다는 점에 유의하세요.

```scala
ratings.foreach {
    (movie, rating) => println(s"key: $movie, value: $rating")
}

ratings.foreach {
    case movie -> rating => println(s"key: $movie, value: $rating")
}

// works with Scala 2
ratings.foreach {
    case(movie, rating) => println(s"key: $movie, value: $rating")
}
```

이 예시들 각각에서, 중괄호 안의 코드가 익명 함수 역할을 합니다.

맵을 어떻게 순회하고 싶은지에 따라, 맵의 키 또는 값에만 접근하고 싶을 수도 있습니다.

#### 키 (Keys)

맵의 키에만 접근하고 싶다면, `keysIterator`를 사용하여 모든 키를 가벼운 `Iterator`로 가져올 수 있습니다.

```scala
val i = ratings.keysIterator

// the iterator provides access to the map's keys
i.toList   // List(Lady in the Water, Snakes on a Plane, You, Me and Dupree)
```

`keys` 메서드는 `Iterable`을 반환하므로, 새로운 중간 컬렉션을 만드는 일이 포함되지만(맵이 크다면 성능 문제가 될 수 있음), 쉽게 동작합니다.

```scala
scala> ratings.keys.foreach((m) => println(s"$m rating is ${ratings(m)}"))
Lady in the Water rating is 3.0
Snakes on a Plane rating is 4.0
You, Me and Dupree rating is 3.5
```

#### 값 (Values)

맵을 순회하며 값에 대해 연산을 수행하고 싶다면, `MapView` 트레이트에 정의된 `mapValues` 메서드가 필요한 것일 수 있습니다. 먼저 맵에 `view`를 호출하여 `MapView`를 만든 다음, `mapValues`를 호출합니다. 이는 각 맵 값에 함수를 적용하고 수정된 맵을 반환하게 해줍니다.

```scala
val a = Map(1 -> "ay", 2 -> "bee")
    // a: Map[Int, String] = Map(1 -> ay, 2 -> bee)

val b = a.view.mapValues(_.toUpperCase).toMap   // Map(1 -> AY, 2 -> BEE)
```

뷰(view)는 변환 메서드(transformer methods)를 비엄격(nonstrict) 또는 지연(lazy) 방식으로 구현하기 때문에, `mapValues`를 호출하기 전에 뷰를 만드는 것은 가벼운 이터레이터만 생성합니다.

```scala
// `view` uses an iterator
a.view                  // MapView(<not computed>)
```

이 접근 방식은 맵이 매우 클 때 특히 유익할 수 있습니다. 뷰에 대한 자세한 내용은 11.4절 "Creating a Lazy View on a Collection"을 참고하세요.

반대로, `values` 메서드와 `map`을 사용하여 이 문제를 해결할 수 있습니다.

```scala
a.values.map(_.toUpperCase)   // List(AY, BEE)
```

그러나 이 과정의 첫 번째 단계가 중간 `Iterable`을 만든다는 점에 유의하세요.

```scala
a.values                      // Iterable(ay, bee)
```

따라서 큰 맵의 경우, 이 접근 방식은 추가적인 중간 컬렉션 때문에 성능이나 메모리 문제를 일으킬 수 있습니다.

#### 값을 변환해야 하는 경우 (If you need to transform the values)

맵을 순회하며 맵 값을 변환하고 싶다면, `transform` 메서드가 기존 맵으로부터 새로운 맵을 만드는 또 다른 방법을 제공합니다. `mapValues`와 달리, 키와 값 둘 다를 사용하여 값을 변환할 수 있습니다.

```scala
val map1 = Map(1 -> 10, 2 -> 20, 3 -> 30)

// use the map keys and values to create new values
val map2 = map1.transform((k,v) => k + v)
    // map2: Map(1 -> 11, 2 -> 22, 3 -> 33)
```

더 복잡한 상황에서는, 초기 `Map`에 뷰를 만든 다음 그 `MapView`에 `map` 메서드를 호출할 수도 있는데, 이는 `Map`의 키와 값에 모두 접근할 수 있게 해줍니다.

```scala
val map3 = map1.view.map((k,v) => (k, k + v)).toMap
```

---

## 14.10 Sorting an Existing Map by Key or Value (기존 맵을 키 또는 값으로 정렬하기)

### 문제

정렬되지 않은 맵이 있는데, 맵의 요소들을 키 또는 값으로 정렬하고 싶습니다.

### 해결책

다음과 같은 기본 불변 `Map`이 주어졌을 때,

```scala
val grades = Map(
    "Kim" -> 90,
    "Al" -> 85,
    "Melissa" -> 95,
    "Emily" -> 91,
    "Hannah" -> 92
)
```

`sortBy`를 사용하여 맵을 키 기준으로 낮은 값에서 높은 값으로 정렬한 다음, 그 결과를 가변 `LinkedHashMap` 또는 불변 `VectorMap`에 저장할 수 있습니다. 두 가지 해결책이 여기에 보여집니다.

```scala
import scala.collection.mutable.LinkedHashMap

// Version 1: sorts by key by accessing each tuple as '(k,v)'
val x = LinkedHashMap(grades.toSeq.sortBy((k,v) => k):_*)
    // x: LinkedHashMap(Al -> 85, Emily -> 91, Hannah -> 92, Kim -> 90,
    //                  Melissa -> 95)

// Version 2: sorts by key using the tuple '._1' syntax
val x = LinkedHashMap(grades.toSeq.sortBy(_._1):_*)
    // x: LinkedHashMap(Al -> 85, Emily -> 91, Hannah -> 92, Kim -> 90,
    //                  Melissa -> 95)
```

`sortWith`와 여러분만의 커스텀 알고리즘을 사용하여 키를 오름차순 또는 내림차순으로 정렬할 수도 있으며, 마찬가지로 결과를 `LinkedHashMap`에 저장합니다.

```scala
// sort by key, low to high
val x = LinkedHashMap(grades.toSeq.sortWith(_._1 < _._1):_*)
    // x: LinkedHashMap(Al -> 85, Emily -> 91, Hannah -> 92, Kim -> 90,
    //                  Melissa -> 95)

// sort by key, high to low
val x = LinkedHashMap(grades.toSeq.sortWith(_._1 > _._1):_*)
    // x: LinkedHashMap(Melissa -> 95, Kim -> 90, Hannah -> 92, Emily -> 91,
    //                  Al -> 85)
```

이 문법(`:_*`)은 논의에서 설명됩니다.

`sortBy`를 사용하여 맵을 값으로 정렬할 수 있습니다.

```scala
// value, low to high, accessing elements as `(k,v)`
val x = LinkedHashMap(grades.toSeq.sortBy((k,v) => v):_*)
    // x: LinkedHashMap(Al -> 85, Kim -> 90, Emily -> 91, Hannah -> 92,
    //                  Melissa -> 95)

// value, low to high, using the tuple '_' syntax
val x = LinkedHashMap(grades.toSeq.sortBy(_._2):_*)
    // x: LinkedHashMap(Al -> 85, Kim -> 90, Emily -> 91, Hannah -> 92,
    //                  Melissa -> 95)
```

`sortWith`를 사용하여 값을 오름차순 또는 내림차순으로 정렬할 수도 있습니다.

```scala
// sort by value, low to high
val x = LinkedHashMap(grades.toSeq.sortWith(_._2 < _._2):_*)
    // x: LinkedHashMap(Al -> 85, Kim -> 90, Emily -> 91, Hannah -> 92,
    //                  Melissa -> 95)

// sort by value, high to low
val x = LinkedHashMap(grades.toSeq.sortWith(_._2 > _._2):_*)
    // x: LinkedHashMap(Melissa -> 95, Hannah -> 92, Emily -> 91, Kim -> 90,
    //                  Al -> 85)
```

`LinkedHashMap`을 사용하는 것 외에, 결과를 불변 `VectorMap`에 저장할 수도 있습니다. 성능이 중요할 때는, 어느 맵 타입이 여러분의 상황에 가장 잘 맞는지 두 가지 모두를 반드시 테스트해 보세요.

### 논의

이 해결책들을 이해하기 위해서는, 이들을 더 작은 조각으로 나누어 보는 것이 도움이 됩니다. 먼저, 기본 불변 `Map`에서 시작합니다.

```scala
val grades = Map(
    "Kim" -> 90,
    "Al" -> 85,
    "Melissa" -> 95,
    "Emily" -> 91,
    "Hannah" -> 92
)
```

다음으로, `grades.toSeq`가 두 요소짜리 튜플 값들의 시퀀스, 즉 `Seq[(String, Int)]`를 생성한다는 것을 볼 수 있습니다.

```scala
val x = grades.toSeq
    // x: ArrayBuffer((Hannah,92), (Melissa,95), (Kim,90), (Emily,91), (Al,85))
```

`Seq`로 변환하는 이유는, `Seq`에는 사용할 수 있는 정렬 메서드들이 있기 때문입니다.

```scala
// sort by key
val x = grades.toSeq.sortBy(_._1)
    // x: Seq[(String, Int)] =
    // ArrayBuffer((Al,85), (Emily,91), (Hannah,92), (Kim,90), (Melissa,95))

// sort by key
val x = grades.toSeq.sortWith(_._1 < _._1)
    // x: Seq[(String, Int)] =
    // ArrayBuffer((Al,85), (Emily,91), (Hannah,92), (Kim,90), (Melissa,95))
```

이 예시들에서 `_._1` 문법은 각 튜플의 첫 번째 요소, 즉 *키* 를 가리킵니다. 마찬가지로, `_._2`는 *값* 을 가리킵니다.

맵 데이터를 원하는 대로 정렬했다면, 정렬된 순서를 유지하기 위해 `LinkedHashMap`, `VectorMap`, 또는 `ListMap`에 저장합니다.

```scala
val x = LinkedHashMap(grades.toSeq.sortBy(_._1):_*)
    // x: scala.collection.mutable.LinkedHashMap[String,Int] =
    // Map(Al -> 85, Emily -> 91, Hannah -> 92, Kim -> 90, Melissa -> 95)
```

`LinkedHashMap`은 가변 클래스로만 제공되며, `VectorMap`은 불변 맵입니다. 불변 `ListMap`도 있지만, 작은 맵에만 권장됩니다. 여러분의 상황에 가장 잘 맞는 것을 사용하세요.

#### `_*`에 대하여 (About that `_*`)

`_*` 부분은 익숙해지는 데 약간 시간이 걸립니다. 이는 데이터를 변환하여 `LinkedHashMap`(또는 `VectorMap`이나 `ListMap`)에 여러 개의 파라미터로 전달되도록 하는 특수 문법입니다. Unix 시스템에서 `xargs` 명령을 사용해 본 적이 있다면, 그것과 비슷하게 동작합니다. 즉, 요소들의 시퀀스를 입력으로 받아 한 번에 하나씩 다음 명령으로 전달합니다.

코드를 다시 별개의 줄들로 나누어 보면 이를 확인할 수 있습니다. `sortBy` 메서드는 `Seq[(String, Int)]`, 즉 튜플의 시퀀스를 반환합니다.

```scala
val seqOfTuples = grades.toSeq.sortBy(_._1)
    // seqOfTuples: Seq[(String, Int)] =
    // List((Al,85), (Emily,91), (Hannah,92), (Kim,90), (Melissa,95))
```

안타깝게도, 튜플의 시퀀스로는 `VectorMap`, `LinkedHashMap`, 또는 `ListMap`을 생성할 수 없습니다.

```scala
scala> VectorMap(seqOfTuples)
1 |VectorMap(seqOfTuples)
  |          ^^^^^
  |          Found:    (seqOfTuples : Seq[(String, Int)])
  |          Required: (Any, Any)
```

그러나 `VectorMap`의 컴패니언 오브젝트(companion object)에 있는 `apply` 메서드는 튜플-2의 가변 인자(varargs) 파라미터를 받기 때문에, `_*`를 사용하여 `seqOfTuples`가 동작하도록 조정할 수 있습니다. 즉, 튜플-2 요소들의 시퀀스를 개별 튜플-2 값들의 연속(series)으로 변환하는 것입니다. 이는 `VectorMap`의 `apply` 메서드에 그것이 원하는 형태를 제공합니다.

```scala
val x = VectorMap(seqOfTuples: _*)
    // x: scala.collection.immutable.VectorMap[String, Int] =
    // VectorMap(Al -> 85, Emily -> 91, Hannah -> 92, Kim -> 90, Melissa -> 95)
```

`_*`가 어떻게 동작하는지 보는 또 다른 방법은, 가변 인자 파라미터를 받는 여러분만의 메서드를 정의하는 것입니다. 다음의 `printAll` 메서드는 `String` 타입의 가변 인자 필드라는 하나의 파라미터를 받습니다.

```scala
def printAll(strings: String*): Unit = strings.foreach(println)
```

그런 다음 다음과 같이 `List`를 생성하면,

```scala
// a sequence of strings
val fruits = List("apple", "banana", "cherry")
```

그 `List`를 `printAll`에 전달할 수 없다는 것을 알게 됩니다. 이는 이전 예시처럼 실패합니다.

```scala
scala> printAll(fruits)
1 |printAll(fruits)
  |         ^^
  |         Found:    (fruits : List[String])
  |         Required: String
```

그러나 `_*`를 사용하여 `List`가 `printAll`과 동작하도록 조정할 수 있습니다.

```scala
// this works
scala> printAll(fruits: _*)
apple
banana
cherry
```

Unix 배경이 있다면, `_*`를 *splat* 연산자로 생각하는 것이 도움이 될 수 있습니다. 이 연산자는 컴파일러에게 시퀀스의 각 요소를 `printAll`에 별개의 인자로 전달하라고 지시합니다. `fruits`를 하나의 `List` 인자로 전달하는 대신 말입니다.

---

## 14.11 Filtering a Map (맵 필터링하기)

### 문제

맵에 담긴 요소들을 필터링하고 싶습니다. 가변 맵을 직접 수정하거나, 불변 맵에 필터링 알고리즘을 적용하여 새로운 맵을 만드는 방법으로 말입니다.

### 해결책

가변 맵을 사용할 때는 유지할 요소들을 정의하기 위해 `filterInPlace` 메서드를 사용하고, 가변 또는 불변 맵의 요소를 필터링할 때는 `filterKeys` 또는 `filter`를 사용하되, 결과를 새로운 변수에 할당하는 것을 잊지 마세요.

#### 가변 맵 (Mutable maps)

*가변(mutable)* 맵에서는 `filterInPlace` 메서드를 사용하여 어떤 요소들이 유지되어야 하는지를 지정함으로써 요소를 필터링할 수 있습니다.

```scala
val x = collection.mutable.Map(
    1 -> 100,
    2 -> 200,
    3 -> 300
)

x.filterInPlace((k,v) => k > 1)     // x: HashMap(2 -> b, 3 -> c)
x.filterInPlace((k,v) => v > 200)   // x: HashMap(3 -> 300)
```

보여주듯이, `filterInPlace`는 가변 맵을 제자리에서(in place) 수정합니다. 그 예시에서 사용된 익명 함수 시그니처가 암시하듯,

```scala
(k,v) => ...
```

여러분의 알고리즘은 각 요소의 키와 값 모두를 테스트하여 맵에 어떤 요소를 유지할지 결정할 수 있습니다.

"필터"에 대한 여러분의 정의에 따라, 14.3절에서 보여준 `remove`와 `clear` 같은 메서드를 사용하여 맵에서 요소를 제거할 수도 있습니다.

#### 가변 맵과 불변 맵 (Mutable and immutable maps)

가변 또는 불변 맵을 다룰 때, `filterKeys` 메서드와 함께 술어(predicate)를 사용하여 어떤 맵 요소를 유지할지 정의할 수 있습니다. 이 메서드를 사용하려면 먼저 맵에 `view` 메서드를 호출하여 `MapView`를 만들어야 합니다. 그런 다음 필터링된 결과를 새로운 변수에 할당하는 것을 잊지 마세요.

```scala
val x = Map(
    1 -> "a",
    2 -> "b",
    3 -> "c"
)

val y = x.view.filterKeys(_ > 2).toMap   // y: Map(3 -> c)
```

여러분이 공급하는 술어는 새로운 컬렉션에 유지하고 싶은 요소에 대해서는 `true`를, 원하지 않는 요소에 대해서는 `false`를 반환해야 합니다.

이 접근 방식에서 마지막에 `toMap`을 호출하지 않으면, REPL에서 다음과 같은 결과를 보게 된다는 점에 유의하세요.

```scala
scala> val y = x.view.filterKeys(_ > 2)
val y: scala.collection.MapView[Int, String] = MapView(<not computed>)
```

여기서 `MapView(<not computed>)`를 보게 되는 이유는, `view`를 호출하면 초기 맵에 대한 지연 뷰(lazy view)가 생성되고, `toMap` 같은 메서드를 호출하여 강제로 수행하기 전까지는 실제로 계산이 수행되지 않기 때문입니다. (뷰가 어떻게 동작하는지에 대한 자세한 내용은 11.4절 "Creating a Lazy View on a Collection"을 참고하세요.)

알고리즘이 더 길다면, 익명 함수를 사용하는 대신 함수(또는 메서드)를 정의한 다음 그것을 `filterKeys`에 전달할 수 있습니다. 예를 들어, 먼저 다음과 같이 다소 장황하지만(명확한) 메서드를 정의합니다. 이 메서드는 값이 1일 때 `true`를 반환합니다.

```scala
def only1(i: Int) = if i == 1 then true else false
```

그런 다음 그 메서드를 `filterKeys` 메서드에 전달합니다.

```scala
val x = Map(1 -> "a", 2 -> "b", 3 -> "c")
val y = x.view.filterKeys(only1).toMap   // y: Map(1 -> a)
```

`filterKeys`와 함께 `Set`을 사용하여 유지할 요소들을 정의할 수도 있습니다.

```scala
val x = Map(1 -> "a", 2 -> "b", 3 -> "c")
val y = x.view.filterKeys(Set(2,3)).toMap
    // y: Map[Int, String] = Map(2 -> b, 3 -> c)
```

논의에서는 값으로 `Map`을 필터링하는 방법 등 다른 필터링 메서드들을 시연합니다.

### 논의

13.7절 "Using filter to Filter a Collection"에서 보여준 모든 필터링 메서드를 사용할 수 있습니다. 예를 들어, `filter` 메서드의 맵 버전은 키, 값, 또는 둘 다로 맵 요소를 필터링할 수 있게 해줍니다. `filter` 메서드는 여러분의 술어에 튜플-2를 제공하므로, 다음 예시들에서 보여주듯 키와 값에 접근할 수 있습니다.

```scala
// an immutable map
val a = Map(1 -> "a", 2 -> "b", 3 -> "c")

// filter by the key
val b = a.filter((k,v) => k > 1)        // b: Map(2 -> b, 3 -> c)

// filter by the value
val c = a.filter((k,v) => v != "b")     // c: Map(1 -> a, 3 -> c)
```

선호한다면 필터 알고리즘에서 튜플을 사용할 수도 있습니다.

```scala
// filter by the key (t._1)
val b = a.filter((t) => t._1 > 1)       // b: Map(2 -> b, 3 -> c)

// filter by the value (t._2)
val b = a.filter((t) => t._2 != "b")    // b: Map(1 -> a, 3 -> c)
```

`take` 메서드는 맵에서 처음 N개의 요소를 "취할(take, 유지할)" 수 있게 해줍니다.

```scala
val b = a.take(2)                       // b: Map(1 -> a, 2 -> b)
```

사용할 수 있는 다른 메서드들의 예시(`takeWhile`, `drop`, `slice` 포함)는 13.7절 "Using filter to Filter a Collection"의 필터링 레시피들을 참고하세요.
