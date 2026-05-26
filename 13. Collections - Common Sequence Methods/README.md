# Chapter 13. Collections: Common Sequence Methods

## Recipes

- 13.1 Choosing a Collection Method to Solve a Problem
- 13.2 Looping Over a Collection with foreach
- 13.3 Using Iterators
- 13.4 Using zipWithIndex or zip to Create Loop Counters
- 13.5 Transforming One Collection to Another with map
- 13.6 Flattening a List of Lists with flatten
- 13.7 Using filter to Filter a Collection
- 13.8 Extracting a Sequence of Elements from a Collection
- 13.9 Splitting Sequences into Subsets
- 13.10 Walking Through a Collection with the reduce and fold Methods
- 13.11 Finding the Unique Elements in a Sequence
- 13.12 Merging Sequential Collections
- 13.13 Randomizing a Sequence
- 13.14 Sorting a Collection
- 13.15 Converting a Collection to a String with mkString and addString

---

## 들어가며

앞선 두 장(chapter)이 주로 시퀀스 **클래스(classes)** 에 초점을 맞췄다면, 이 장은 시퀀스 **메서드(methods)**, 그중에서도 가장 흔하게 사용되는 시퀀스 메서드들에 초점을 맞춥니다. 하지만 이 레시피들을 본격적으로 살펴보기 전에, 컬렉션 클래스의 메서드를 다룰 때 알아두어야 할 몇 가지 중요한 개념이 있습니다.

- 술어(Predicates)
- 익명 함수(Anonymous functions)
- 암묵적 루프(Implied loops)

### 술어(Predicate)

**술어(predicate)** 란 단순히 하나 이상의 입력 파라미터를 받아 `Boolean` 값을 반환하는 메서드, 함수, 혹은 익명 함수를 말합니다. 예를 들어, 다음 메서드는 `true` 또는 `false`를 반환하므로 술어입니다.

```scala
def isEven(i: Int): Boolean =
    i % 2 == 0
```

술어는 단순한 개념이지만, 컬렉션 메서드를 다룰 때 이 용어를 너무 자주 듣게 되므로 짚고 넘어가는 것이 중요합니다.

### 익명 함수(Anonymous Functions)

**익명 함수(anonymous function)** 라는 개념 또한 중요합니다. 이 개념은 레시피 10.1 "함수 리터럴(익명 함수) 사용하기"에서 깊이 있게 설명되지만, 간단한 예로서 다음 코드는 `isEven` 메서드와 동일한 일을 하는 익명 함수의 긴 형식(long form)을 보여줍니다.

```scala
(i: Int) => i % 2 == 0
```

같은 함수의 짧은 형식(short form)은 다음과 같습니다.

```scala
_ % 2 == 0
```

이것만 놓고 보면 별것 아닌 것처럼 보이지만, 컬렉션의 `filter` 메서드와 결합되면 매우 적은 양의 코드로 많은 일을 해낼 수 있습니다.

```scala
scala> val list = List.range(1, 10)
list: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> val events = list.filter(_ % 2 == 0)
events: List[Int] = List(2, 4, 6, 8)
```

### 암묵적 루프(Implied Loops)

`filter` 메서드는 세 번째 주제인 **암묵적 루프(implied loops)** 로 자연스럽게 이어집니다. 위 예시에서 보듯이, `filter`는 컬렉션의 모든 요소에 여러분의 함수를 적용하고 새로운 컬렉션을 반환하는 루프를 내부에 포함하고 있습니다. `filter` 메서드 없이도 다음과 같이 동등한 코드를 작성할 수 있습니다.

```scala
for
    e <- list
    if e % 2 == 0
yield
    e
```

하지만 `filter`를 사용하는 방식이 더 간결하고 읽기 쉽다는 데 동의하실 것입니다.

`filter`, `foreach`, `map`, `reduceLeft` 등 많은 컬렉션 메서드는 그 알고리즘 안에 루프를 내장하고 있습니다. 이러한 내장 메서드들 덕분에, 다른 많은 언어들에 비해 Scala 코드를 작성할 때 직접 작성하는 `for` 루프의 수가 훨씬 적어집니다.

### 이 장의 레시피들

시퀀스 클래스에는 100개가 넘는 내장 메서드가 있지만, 이 장의 레시피들은 가장 흔하게 사용되는 메서드에 초점을 맞춥니다. 여기에는 다음이 포함됩니다.

- `filter`: 주어진 술어로 컬렉션을 필터링할 수 있게 해줍니다.
- `map`: 컬렉션의 각 요소에 변환 함수를 적용할 수 있게 해줍니다.
- 기존 시퀀스에서 시퀀스와 서브셋(subset)을 추출하는 메서드들
- 시퀀스에서 고유한(unique) 요소를 찾는 메서드들
- 시퀀스를 병합(merge)하고 zip하는 메서드들
- 시퀀스를 무작위로 섞고(randomize) 정렬(sort)하는 메서드들
- 시퀀스를 문자열로 변환하는 두 가지 메서드

이러한 기능들과 그 외 몇 가지가 다음 레시피들에서 시연됩니다.

---

## 13.1 Choosing a Collection Method to Solve a Problem

### 문제(Problem)

Scala 컬렉션 클래스에는 사용할 수 있는 메서드가 매우 많은데, 문제를 해결하기 위해 어떤 메서드를 골라야 할지 결정해야 합니다.

### 해결책(Solution)

Scala 컬렉션 클래스는 데이터를 다루는 데 사용할 수 있는 풍부한 메서드를 제공합니다. 대부분의 메서드는 함수(function) 또는 술어(predicate)를 인자로 받습니다.

이 레시피에서는 사용 가능한 메서드들을 두 가지 방식으로 나열합니다. 먼저 몇 개 단락에 걸쳐 메서드들을 카테고리별로 분류하여 필요한 것을 찾기 쉽게 합니다. 그다음 이어지는 표(table)들에서 각 메서드의 간단한 설명과 메서드 시그니처(signature)를 제공합니다.

#### 카테고리별로 정리한 메서드들(Methods organized by category)

**필터링 메서드(Filtering methods)**

컬렉션을 필터링하는 데 사용할 수 있는 메서드에는 `collect`, `diff`, `distinct`, `drop`, `dropRight`, `dropWhile`, `filter`, `filterNot`, `filterInPlace`, `find`, `foldLeft`, `foldRight`, `head`, `headOption`, `init`, `intersect`, `last`, `lastOption`, `slice`, `tail`, `take`, `takeRight`, `takeWhile`, 그리고 `union`이 있습니다.

**변환 메서드(Transformer methods)**

변환 메서드는 적어도 하나의 입력 컬렉션을 받아 새로운 출력 컬렉션을 생성하며, 일반적으로 여러분이 제공하는 알고리즘을 사용합니다. 여기에는 `+`, `++`, `+:`, `++:`, `appended`, `appendedAll`, `diff`, `distinct`, `collect`, `concat`, `flatMap`, `flatten`, `inits`, `map`, `mapInPlace`, `patch`, `reverse`, `sorted`, `sortBy`, `sortWith`, `sortInPlace`, `sortInPlaceWith`, `sortInPlaceBy`, `tails`, `takeWhile`, `updated`, `zip`, 그리고 `zipWithIndex`가 있습니다.

**그룹화 메서드(Grouping methods)**

이 메서드들은 기존 컬렉션을 받아 그 하나의 컬렉션으로부터 여러 개의 그룹을 생성하게 해줍니다. 여기에는 `groupBy`, `grouped`, `groupMap`, `partition`, `sliding`, `span`, `splitAt`, 그리고 `unzip`이 있습니다.

**정보성 및 수학적 메서드(Informational and mathematical methods)**

이 메서드들은 컬렉션에 대한 정보를 제공하며, `canEqual`, `contains`, `containsSlice`, `count`, `endsWith`, `exists`, `find`, `findLast`, `forAll`, `indexOf`, `indexOfSlice`, `indexWhere`, `isDefinedAt`, `isEmpty`, `last`, `lastOption`, `lastIndexOf`, `lastIndexOfSlice`, `lastIndexWhere`, `length`, `lengthIs`, `max`, `maxBy`, `maxByOption`, `min`, `minBy`, `minOption`, `minByOption`, `nonEmpty`, `product`, `segmentLength`, `size`, `sizeIs`, `startsWith`, 그리고 `sum`이 있습니다. `foldLeft`, `foldRight`, `reduceLeft`, `reduceRight` 메서드 또한 여러분이 제공하는 함수와 함께 사용하여 컬렉션에 대한 정보를 얻을 수 있습니다.

**기타(Others)**

분류하기 어려운 몇 가지 메서드들이 있는데, `view`, `foreach`, `addString`, 그리고 `mkString`이 그렇습니다. `view`는 컬렉션에 대한 지연(lazy) 뷰를 생성합니다(레시피 11.4 "컬렉션에 지연 뷰 생성하기" 참고). `foreach`는 `for` 루프처럼 컬렉션의 요소들을 순회하면서 각 요소에 대해 부수 효과(side effect)를 수행하게 해줍니다. `addString`과 `mkString`은 컬렉션으로부터 `String`을 만들게 해줍니다.

여기에 나열된 것 외에도 더 많은 메서드가 있습니다. 예를 들어, 현재 컬렉션(예: `List`)을 다른 컬렉션 타입으로 변환하게 해주는 `to*` 메서드들의 모음(`toArray`, `toBuffer`, `toVector` 등)이 있습니다. 더 많은 내장 메서드를 찾으려면 여러분의 컬렉션 클래스에 대한 Scaladoc을 확인하세요.

#### 공통 컬렉션 메서드(Common collection methods)

다음 표들은 가장 흔한 컬렉션 메서드들을 나열합니다. 설명에 나오는 모든 인용구는 각 클래스의 Scaladoc에서 가져온 것입니다.

표 13-1은 `Iterable`을 통해 모든 컬렉션에 공통적인 메서드들을 나열합니다. 표의 첫 번째 열에서는 다음 기호들이 사용됩니다.

- `c`는 시퀀스 컬렉션(sequential collection)을 의미합니다.
- `f`는 함수(function)를 의미합니다.
- `p`는 술어(predicate)를 의미합니다.
- `n`은 숫자(number)를 의미합니다.

가변(mutable) 컬렉션과 불변(immutable) 컬렉션을 위한 추가 메서드는 각각 표 13-2와 표 13-3에 나열되어 있습니다.

**표 13-1. Iterable 컬렉션에 공통인 메서드 (scala.collection.Iterable)**

| Method | Description |
| --- | --- |
| `c collect f` | 함수가 정의된 컬렉션의 모든 요소에 부분 함수(partial function)를 적용하여 새로운 컬렉션을 만듭니다. |
| `c count p` | 컬렉션에서 술어를 만족하는 요소의 개수를 셉니다. |
| `c drop n` | 처음 `n`개의 요소를 제외한 컬렉션의 모든 요소를 반환합니다. |
| `c dropWhile p` | "술어를 만족하는 요소들로 이루어진 가장 긴 접두부(longest prefix)"를 포함하는 컬렉션을 반환합니다. |
| `c exists p` | 컬렉션의 어떤 요소에 대해서라도 술어가 참이면 `true`를 반환합니다. |
| `c filter p` | 컬렉션에서 술어가 `true`인 요소들을 반환합니다. |
| `c filterNot p` | 컬렉션에서 술어가 `false`인 요소들을 반환합니다. |
| `c find p` | 술어와 일치하는 첫 번째 요소를 `Option[A]`로 반환합니다. |
| `c flatMap f` | 컬렉션 `c`의 모든 요소에 함수를 적용하고(`map`처럼) 결과로 나온 컬렉션들을 평탄화(flatten)하여 새로운 컬렉션을 반환합니다. |
| `c flatten` | 컬렉션들의 컬렉션(예: 리스트의 리스트)을 단일 컬렉션(단일 리스트)으로 변환합니다. |
| `c foldLeft(s)(f)` | 시드(seed) 값 `s`에서 시작하여, 연산 `f`를 왼쪽에서 오른쪽으로(좌결합, left associative) 연속된 요소들에 적용합니다. |
| `c foldRight(s)(f)` | 시드 값 `s`에서 시작하여, 연산 `f`를 오른쪽에서 왼쪽으로(우결합, right associative) 연속된 요소들에 적용합니다. |
| `c forAll p` | 모든 요소에 대해 술어가 참이면 `true`를, 그렇지 않으면 `false`를 반환합니다. |
| `c foreach f` | 컬렉션의 모든 요소에 함수 `f`를 적용합니다(여기서 `f`는 일반적으로 부수 효과를 갖는 함수입니다). |
| `c groupBy f` | 함수에 따라 컬렉션을 컬렉션들의 `Map`으로 분할(partition)합니다. |
| `c head` | 컬렉션의 첫 번째 요소를 반환합니다. 컬렉션이 비어 있으면 `NoSuchElementException`을 던집니다. |
| `c headOption` | 컬렉션의 첫 번째 요소를 `Some[A]`로 반환하거나, 요소가 존재하지 않으면(컬렉션이 비어 있으면) `None`을 반환합니다. |
| `c init` | 마지막 요소를 제외한 컬렉션의 모든 요소를 선택합니다. 컬렉션이 비어 있으면 `UnsupportedOperationException`을 던집니다. |
| `c inits` | "이 iterable 컬렉션의 inits(앞부분들)를 순회합니다." |
| `c isEmpty` | 컬렉션이 비어 있으면 `true`를, 그렇지 않으면 `false`를 반환합니다. |
| `c knownSize` | "컬렉션의 요소 개수를 저렴하게 계산할 수 있으면 그 개수를, 그렇지 않으면 -1을 반환합니다. 저렴하다는 것은 보통 컬렉션 순회를 요구하지 않음을 의미합니다." |
| `c last` | 컬렉션의 마지막 요소를 반환합니다. 컬렉션이 비어 있으면 `NoSuchElementException`을 던집니다. |
| `c lastOption` | 컬렉션의 마지막 요소를 `Some[A]`로 반환하거나, 요소가 존재하지 않으면(컬렉션이 비어 있으면) `None`을 반환합니다. |
| `c1 lazyZip c2` | `zip` 메서드의 지연(lazy) 버전입니다. |
| `c map f` | 컬렉션의 모든 요소에 함수를 적용하여 새로운 컬렉션을 생성합니다. |
| `c max` | 컬렉션에서 가장 큰 요소를 반환합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c maxOption` | 컬렉션에서 가장 큰 요소를 `Option`으로 반환합니다. |
| `c maxBy f` | 함수 `f`로 측정했을 때 가장 큰 요소를 반환합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c maxByOption` | 함수 `f`로 측정했을 때 가장 큰 요소를 `Option`으로 반환합니다. |
| `c min` | 컬렉션에서 가장 작은 요소를 반환합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c minOption` | 컬렉션에서 가장 작은 요소를 `Option`으로 반환합니다. |
| `c minBy` | 함수 `f`로 측정했을 때 가장 작은 요소를 반환합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c minByOption` | 함수 `f`로 측정했을 때 가장 작은 요소를 `Option`으로 반환합니다. |
| `c mkString` | 시퀀스를 문자열로 변환하는 여러 옵션을 제공합니다. |
| `c nonEmpty` | 컬렉션이 적어도 하나의 요소를 포함하면 `true`를, 그렇지 않으면 `false`를 반환합니다. |
| `c partition p` | 술어 알고리즘에 따라 두 개의 컬렉션을 반환합니다. |
| `c product` | 컬렉션의 모든 요소를 곱한 값을 반환합니다. |
| `c reduceLeft op` | `foldLeft`와 동일하지만, 컬렉션의 첫 번째 요소에서 시작합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c reduceRight op` | `foldRight`와 동일하지만, 컬렉션의 마지막 요소에서 시작합니다. `java.lang.UnsupportedOperationException`을 던질 수 있습니다. |
| `c scanLeft op` | `reduceLeft`와 유사하지만, `Iterable`을 반환합니다. |
| `c scanRight op` | `reduceRight`와 유사하지만, `Iterable`을 반환합니다. |
| `c size` | 컬렉션의 크기를 반환합니다. |
| `c1 sizeCompare(c2)` | `c1`의 크기를 `c2`의 크기와 비교합니다. `c1`이 더 작으면 <0, 같으면 0, `c1`이 더 크면 >0을 반환합니다. |
| `c sizeIs n` | 컬렉션의 크기를 정수 `n`과 비교하되, 가능한 한 적은 요소만 순회합니다. |
| `c slice(from, to)` | 요소 `from`에서 시작하여 요소 `to`에서 끝나는 요소들의 구간(interval)을 반환합니다. |
| `c sliding(size,step)` | `size`로 주어진 길이의 시퀀스들을 반환하면서, 시퀀스 위로 슬라이딩 윈도우(sliding window)를 통과시키는 방식으로 동작합니다. `step` 파라미터는 요소들을 건너뛸 수 있게 해줍니다. |
| `c span p` | 두 컬렉션으로 이루어진 컬렉션을 반환합니다. 첫 번째는 `c.takeWhile(p)`로, 두 번째는 `c.dropWhile(p)`로 생성됩니다. |
| `c splitAt n` | 요소 `n`에서 컬렉션 `c`를 나누어 두 컬렉션으로 이루어진 컬렉션을 반환합니다. |
| `c sum` | 컬렉션의 모든 요소의 합을 반환합니다. |
| `c tail` | 첫 번째 요소를 제외한 컬렉션의 모든 요소를 반환합니다. |
| `c tails` | 시퀀스의 tails(뒷부분들)를 순회합니다. |
| `c take n` | 컬렉션의 처음 `n`개 요소를 반환합니다. |
| `c takeWhile p` | 술어가 `true`인 동안 컬렉션의 요소들을 반환합니다. 술어가 `false`가 되면 멈춥니다. |
| `c tapEach f` | 컬렉션 `c`의 각 요소에 부수 효과를 갖는 함수 `f`를 적용하면서 `c`를 그대로 반환합니다. 로깅처럼 메서드 호출 체인 안에 부수 효과를 삽입할 수 있게 해줍니다. |
| `c unzip` | `zip`의 반대로, `Tuple2` 요소들의 컬렉션을 분해하는 것처럼, 각 요소를 두 조각으로 나누어 컬렉션을 분리합니다. |
| `c view` | 컬렉션에 대한 비엄격(nonstrict, lazy) 뷰를 반환합니다. |
| `c1 zip c2` | `c1`의 0번째 요소와 `c2`의 0번째 요소, `c1`의 1번째 요소와 `c2`의 1번째 요소를 짝지어 쌍(pairs)들의 컬렉션을 생성합니다. |
| `c zipWithIndex` | 컬렉션을 그 인덱스들과 zip합니다. |

여기에 나온 것 외에도 추가 메서드들이 있지만, 이것들이 가장 흔한 것들입니다. 더 많은 메서드를 원하시면 여러분이 작업 중인 컬렉션의 Scaladoc을 참고하세요.

#### 가변 컬렉션 메서드(Mutable collection methods)

표 13-2는 가변(mutable) 컬렉션에 흔히 사용 가능한 메서드들을 보여줍니다. (이것들은 모두 메서드이지만, 일부는 내장 연산자(operator)처럼 보입니다.)

**표 13-2. 가변 컬렉션에 쓰이는 공통 연산자(메서드)**

| Method | Description |
| --- | --- |
| `c += x` | 요소 `x`를 컬렉션 `c`에 추가합니다. `addOne`의 별칭(alias)입니다. |
| `c1 ++= c2` | 컬렉션 `c2`의 요소들을 컬렉션 `c1`에 추가합니다. `addAll`의 별칭입니다. |
| `c -= x` | 요소 `x`를 컬렉션 `c`에서 제거합니다. `subtractOne`의 별칭입니다. |
| `c -= (x,y,z)` | 요소 `x`, `y`, `z`를 컬렉션 `c`에서 제거합니다. |
| `c1 --= c2` | 컬렉션 `c2`의 요소들을 컬렉션 `c1`에서 제거합니다. `subtractAll`의 별칭입니다. |
| `c(n) = x` | 요소 `c(n)`에 값 `x`를 할당합니다. |
| `c append x` | 요소 `x`를 컬렉션 `c`에 덧붙입니다(append). |
| `c1 appendAll c2` | `c2`의 요소들을 컬렉션 `c1`에 덧붙입니다. |
| `c clear` | 컬렉션에서 모든 요소를 제거합니다. |
| `c filterInPlace p` | 술어가 `true`인 컬렉션의 요소들을 유지합니다. |
| `c flatMapInPlace f` | `c`가 리스트의 리스트라고 가정할 때, 함수 `f`를 요소들에 적용하여 모든 요소를 갱신합니다. `map` 후 `flatten`처럼 동작합니다. |
| `c mapInPlace f` | 함수를 요소들에 적용하여 컬렉션의 모든 요소를 갱신합니다. |
| `c1.patchInPlace(i,c2,n)` | 인덱스 `i`에서 시작하여, 시퀀스 `c2`를 패치(patch)하면서 `n`개의 요소를 대체합니다. 인덱스 `i`에 새 시퀀스를 삽입하려면 `n`을 0으로 설정하세요. |
| `c prepend x` | 요소 `x`를 컬렉션 `c`의 앞에 붙입니다(prepend). |
| `c1 prependAll c2` | `c2`의 요소들을 컬렉션 `c1`의 앞에 붙입니다. |
| `c sortInPlace` | `Ordering`에 따라 컬렉션을 제자리(in place)에서 정렬합니다. |
| `c sortInPlaceBy f` | 변환 함수 `f`와 함께 암묵적으로 주어진 `Ordering`에 따라 컬렉션을 제자리에서 정렬합니다. |
| `c sortInPlaceWith f` | 비교 함수 `f`에 따라 컬렉션을 제자리에서 정렬합니다. |
| `c remove i` | 인덱스 `i`의 요소를 제거합니다. |
| `c.remove(i, len)` | 인덱스 `i`에서 시작하여 길이 `len`만큼 이어지는 요소들을 제거합니다. |
| `c.update(i,e)` | 인덱스 `i`의 요소를 새 값 `e`로 갱신합니다. |

`+=`나 `-=` 같은 기호 메서드 이름들은 이제 이름이 붙은 메서드들의 별칭이라는 점에 유의하세요. 예를 들어 `+=`는 `addOne`의 별칭입니다. 더 많은 메서드는 여러분이 작업 중인 가변 컬렉션의 Scaladoc을 참고하세요.

#### 불변 컬렉션 메서드(Immutable collection methods)

표 13-3은 불변(immutable) 컬렉션을 다룰 때의 공통 메서드들을 보여줍니다. 불변 컬렉션은 수정될 수 없으므로, 첫 번째 열에 있는 각 표현식의 결과는 반드시 새로운 변수에 할당되어야 한다는 점에 유의하세요. (또한 불변 컬렉션에서 가변 변수를 사용하는 방법에 대한 자세한 내용은 레시피 11.3 "불변 컬렉션으로 가변 변수 이해하기"를 참고하세요.)

**표 13-3. 불변 컬렉션에 고유한 메서드**

| Method | Description |
| --- | --- |
| `c1 ++ c2` | 컬렉션 `c2`의 요소들을 컬렉션 `c1`에 덧붙여 새로운 컬렉션을 생성합니다. `concat`의 별칭입니다. |
| `c :+ e` | 요소 `e`가 컬렉션 `c`에 덧붙여진 새로운 컬렉션을 반환합니다. `appended`의 별칭입니다. |
| `c1 :++ c2` | `c2`의 요소들이 `c1`의 요소들에 덧붙여진 새로운 컬렉션을 반환합니다. `appendedAll`의 별칭입니다. |
| `e +: c` | 요소 `e`가 컬렉션 `c`의 앞에 붙은 새로운 컬렉션을 반환합니다. `prepended`의 별칭입니다. |
| `c1 ++: c2` | `c1`의 요소들이 `c2`의 앞에 붙은 새로운 컬렉션을 반환합니다. `prependedAll`의 별칭입니다. |
| `e :: list` | 요소 `e`가 `list`라는 이름의 `List` 앞에 붙은 `List`를 반환합니다. (`::`는 `List`에서만 동작합니다.) |
| `list1 ::: list2` | `list1`의 요소들이 `list2`의 요소들 앞에 붙은 `List`를 반환합니다. (`:::`는 `List`에서만 동작합니다.) |
| `c updated(i,e)` | 인덱스 `i`의 요소가 `e`로 대체된 `c`의 복사본을 반환합니다. |

`++`나 `++=` 같은 기호 메서드 이름들은 이제 이름이 붙은 메서드들의 별칭이라는 점에 유의하세요. 예를 들어 `++`는 `concat`의 별칭입니다. 또한 `-`와 `--` 두 메서드는 대부분의 시퀀스에서 여러 버전 이전에 폐기 예정(deprecated)되었으며, 현재는 셋(set)에서만 사용 가능하다는 점에 유의하세요. 따라서 원하는 요소가 제거된 새 컬렉션을 반환하려면 표 13-1에 나열된 필터링 메서드들을 사용하세요.

이 표는 불변 컬렉션에 사용 가능한 가장 흔한 메서드들만 나열합니다. 불변 `Set`에서 사용 가능한 `-`와 `--` 메서드처럼 다른 메서드들도 있습니다. 더 많은 메서드는 여러분의 현재 컬렉션의 Scaladoc을 참고하세요.

#### 맵(Maps)

맵(Map)에는 추가 메서드들이 있으며, 표 13-4에 나와 있습니다. 이 표에서 첫 번째 열에는 다음 기호들이 사용됩니다.

- `m`, `m1`, `m2`는 맵을 의미합니다.
- `mm`은 가변 맵(mutable map)을 의미합니다.
- `k`, `k1`, `k2`는 맵의 키(keys)를 의미합니다.
- `p`는 술어(`true` 또는 `false`를 반환하는 함수)를 의미합니다.
- `v`, `v1`, `v2`는 맵의 값(values)을 의미합니다.
- `c`는 컬렉션을 의미합니다.

**표 13-4. 불변 맵과 가변 맵에 쓰이는 공통 메서드**

| Map method | Description |
| --- | --- |
| **불변 맵을 위한 메서드** | |
| `m + (k->v)` | 새 키/값 쌍이 추가된 맵을 반환합니다. 키 `k`를 가진 키/값 쌍을 갱신하는 데에도 사용할 수 있습니다. `updated`의 별칭입니다. |
| `m1 ++ m2` | 맵 `m1`과 `m2`의 결합을 반환합니다. 키/값 쌍을 갱신하는 데에도 사용할 수 있습니다. `concat`의 별칭입니다. |
| `m ++ Seq(k1->v1, k2->v2)` | 맵 `m`과 `Seq`의 요소들의 결합을 반환합니다. 키/값 쌍을 갱신하는 데에도 사용할 수 있습니다. `concat`의 별칭입니다. |
| `m - k` | 키 `k`(와 그에 대응하는 값)가 제거된 맵을 반환합니다. `removed`의 별칭입니다. |
| `m - Seq(k1, k2, k3)` | 키 `k1`, `k2`, `k3`가 제거된 맵을 반환합니다. `removed`의 별칭입니다. |
| `m -- k`<br>`m -- Seq(k1,k2)` | 키(들)가 제거된 맵을 반환합니다. `Seq`로 표시되었지만 어떤 `IterableOnce`든 될 수 있습니다. `removedAll`의 별칭입니다. |
| **가변 맵을 위한 메서드** | |
| `mm(k) = v` | 키 `k`에 값 `v`를 할당합니다. |
| `mm += (k -> v)` | 가변 맵 `mm`에 키/값 쌍(들)을 추가합니다. `addOne`의 별칭입니다. |
| `mm ++= Map(k1 -> v1, k2 -> v2)` | 가변 맵 `mm`에 키/값 쌍(들)을 추가합니다. `addAll`의 별칭입니다. |
| `mm ++= List(k1 -> v1, k2 -> v2)` | 컬렉션 `c`의 요소들을 가변 맵 `mm`에 추가합니다. `addAll`의 별칭입니다. |
| `mm -= k` | 주어진 키를 기준으로 가변 맵 `mm`에서 맵 항목을 제거합니다. `subtractOne`의 별칭입니다. |
| `mm --= Seq(k1, k2, k3)` | 주어진 키(들)를 기준으로 가변 맵 `mm`에서 맵 항목들을 제거합니다. `subtractAll`의 별칭입니다. |
| **가변 맵과 불변 맵 모두를 위한 메서드** | |
| `m(k)` | 키 `k`와 연관된 값을 반환합니다. |
| `m contains k` | 맵 `m`이 키 `k`를 포함하면 `true`를 반환합니다. |
| `m filter p` | 키와 값이 술어 `p`의 조건과 일치하는 맵을 반환합니다. |
| `m get k` | 키 `k`가 발견되면 그 값을 `Some[A]`로, 그렇지 않으면 `None`을 반환합니다. |
| `m getOrElse(k, d)` | 키 `k`가 발견되면 그 값을, 그렇지 않으면 기본값 `d`를 반환합니다. |
| `m isDefinedAt k` | 맵이 키 `k`를 포함하면 `true`를 반환합니다. |
| `m keys` | 맵의 키들을 `Iterable`로 반환합니다. |
| `m keyIterator` | 맵의 키들을 `Iterator`로 반환합니다. |
| `m keySet` | 맵의 키들을 `Set`으로 반환합니다. |
| `m values` | 맵의 값들을 `Iterable`로 반환합니다. |
| `m valuesIterator` | 맵의 값들을 `Iterator`로 반환합니다. |

또한 `updatedWith`와 `updateWith` 메서드로 `Map`의 값을 갱신할 수 있는데, 각각 불변 맵과 가변 맵에서 사용 가능합니다.

추가 메서드는 가변 맵 클래스 Scaladoc과 불변 맵 클래스 Scaladoc을 참고하세요.

### 토론(Discussion)

보시다시피, Scala 컬렉션 클래스는 풍부한 메서드들(그리고 연산자처럼 보이는 메서드들)을 포함하고 있습니다. 이 메서드들을 이해하면 생산성이 높아지는데, 이를 이해할수록 더 적은 코드와 더 적은 루프를 작성하게 되고, 대신 이 메서드들과 함께 동작하는 짧은 함수와 술어를 작성하게 되기 때문입니다.

---

## 13.2 Looping Over a Collection with foreach

### 문제(Problem)

`foreach` 메서드로 컬렉션의 요소들을 순회하고자 합니다.

### 해결책(Solution)

`foreach` 메서드가 찾고 있는 시그니처와 일치하는 함수, 익명 함수, 또는 메서드를 `foreach`에 제공하면서 문제를 해결하세요.

Scala 시퀀스의 `foreach` 메서드는 다음 시그니처를 가집니다.

```scala
def foreach[U](f: (A) => U): Unit
```

이는 함수를 유일한 파라미터로 받으며, 그 함수는 제네릭 타입 `A`를 받고 아무것도(`Unit`) 반환하지 않음을 의미합니다. 실질적으로 `A`는 여러분의 컬렉션에 담긴 타입(예: `Int`나 `String`)입니다.

`foreach`가 동작하는 방식은, 첫 번째 요소에서 시작하여 마지막 요소에서 끝나면서 컬렉션의 요소를 한 번에 하나씩 여러분의 함수에 전달하는 것입니다. 여러분이 제공하는 함수는 각 요소에 대해 원하는 일을 무엇이든 하지만, 그 함수는 아무것도 반환할 수 없습니다. (무언가를 반환하고 싶다면 `map` 메서드를 참고하세요.)

예를 들어, `foreach`의 흔한 용도는 정보를 출력하는 것입니다. `Vector[Int]`가 주어졌을 때,

```scala
val nums = Vector(1, 2, 3)
```

`Int` 파라미터를 받고 아무것도 반환하지 않는 함수를 작성할 수 있습니다.

```scala
def printAnInt(i: Int): Unit = println(i)
```

`printAnInt`가 `foreach`가 요구하는 시그니처와 일치하므로, 이를 `nums`와 `foreach`와 함께 사용할 수 있습니다.

```scala
scala> nums.foreach(i => printAnInt(i))
1
2
3
```

이 표현식을 다음과 같이도 작성할 수 있습니다.

```scala
nums.foreach(printAnInt(_))
nums.foreach(printAnInt)     // 가장 흔함
```

마지막 예시가 가장 흔하게 사용되는 형식을 보여줍니다.

마찬가지로, `foreach`에 전달하는 익명 함수를 작성하여 이 문제를 해결할 수도 있습니다. 다음 예시들은 모두 `printAnInt` 함수를 사용하는 것과 동일합니다.

```scala
nums.foreach(i => println(i))
nums.foreach(println(_))
nums.foreach(println)        // 가장 흔함
```

#### 맵에서의 foreach(foreach on Maps)

`foreach`는 `Map` 클래스에서도 사용 가능합니다. `Map` 구현의 `foreach`는 다음 시그니처를 가집니다.

```scala
def foreach[U](f: ((K, V)) => U): Unit
```

이는 *키(key)* 와 *값(value)* 을 의미하는 두 파라미터(`K`와 `V`)를 기대하고 `Unit`을 의미하는 `U`를 반환하는 함수를 받음을 의미합니다. 따라서 `foreach`는 여러분의 함수에 두 개의 파라미터를 전달합니다. 이 파라미터들을 튜플(tuple)로 다룰 수 있습니다.

```scala
val m = Map("first_name" -> "Nick", "last_name" -> "Miller")

m.foreach(t => println(s"${t._1} -> ${t._2}"))   // 튜플 구문
```

다음 방식도 사용할 수 있습니다.

```scala
m.foreach {
    (fname, lname) => println(s"$fname -> $lname")
}
```

맵을 순회하는 다른 방법들은 레시피 14.9 "맵 순회하기"를 참고하세요.

> **부수 효과(Side Effects)**
>
> 보여드린 대로, `foreach`는 컬렉션의 각 요소에 여러분의 함수를 적용하지만, 여러분의 함수가 값을 반환하지 않아야 한다는 것을 요구하며, `foreach` 자체도 값을 반환하지 않습니다. `foreach`가 아무것도 반환하지 않으므로, 논리적으로 출력을 인쇄하거나 다른 변수를 변경(mutate)하는 등 어떤 다른 이유로 사용되어야 합니다. 따라서 `foreach`—그리고 `Unit`을 반환하는 다른 모든 메서드—는 그 부수 효과를 위해 사용되어야 한다고 말합니다. 그러므로 `foreach`는 표현식(expression)이 아니라 문장(statement)입니다. 문장과 표현식에 대한 자세한 내용은 저자의 블로그 글 "A Note About Expression-Oriented Programming"을 참고하세요.

### 토론(Discussion)

`foreach`를 여러 줄짜리 함수와 함께 사용하려면, 함수를 중괄호(`{}`)로 감싼 블록으로 전달하세요.

```scala
val longWords = StringBuilder()

"Hello world it's Al".split(" ").foreach { e =>
    if e.length > 4 then longWords.append(s" $e")
    else println("Not added: " + e)
}
```

이 코드를 REPL에서 실행하면 다음 출력을 생성합니다.

```
Not added: it's
Not added: Al
val longWords: StringBuilder = Hello world
```

---

## 13.3 Using Iterators

### 문제(Problem)

Scala 애플리케이션에서 이터레이터(iterator)를 다루고자 하거나 다룰 필요가 있습니다.

### 해결책(Solution)

Scala에서 이터레이터를 다룰 때 알아야 할 몇 가지 중요한 점이 있습니다.

- Java의 `while` 루프와 달리, Scala 개발자들은 일반적으로 `Iterator`의 `hasNext`와 `next` 메서드를 직접 사용하지 않습니다.
- 큰 파일을 읽는 것처럼 성능상의 이유로 이터레이터를 사용하는 것이 합당할 때가 있습니다.
- 이터레이터는 사용한 후에는 소진됩니다(exhausted).
- 이터레이터는 컬렉션이 아니지만, 일반적인 컬렉션 메서드를 가지고 있습니다.
- 이터레이터의 변환(transformer) 메서드는 지연(lazy)됩니다.
- `BufferedIterator`라는 `Iterator`의 하위 클래스는 `head`와 `headOption` 메서드를 제공하여 다음 요소의 값을 미리 엿볼(peek) 수 있게 해줍니다.

이 점들(과 해결책)은 다음 섹션들에서 다룹니다.

#### Scala 개발자들은 hasNext와 next를 직접 사용하지 않습니다

`hasNext`와 `next()`로 이터레이터를 사용하는 것이 Java에서는 역사적으로 컬렉션을 순회하는 흔한 방법이었지만, Scala 개발자들은 보통 그 메서드들에 직접 접근하지 않습니다. 대신 `map`, `filter`, `foreach` 같은 컬렉션 메서드나 `for` 루프를 사용하여 컬렉션을 순회합니다. 명확히 말하자면, Scala에서 이터레이터를 가질 때 저자는 다음과 같은 코드를 직접 작성한 적이 없습니다.

```scala
val it = Iterator(1, 2, 3)

// 이렇게 하지 않습니다
val it = collection.iterator
while (it.hasNext) ...
```

반대로, 저자는 다음과 같은 코드는 작성합니다.

```scala
val a = it.map(_ * 2)        // a: Iterator[Int] = <iterator>
val b = it.filter(_ > 2)     // b: Iterator[Int] = <iterator>
val c = for e <- it yield e*2     // c: Iterator[Int] = <iterator>
```

#### 성능상의 이유로 이터레이터를 사용하는 것이 합당합니다

`hasNext`와 `next()`를 직접 호출하지는 않지만, 이터레이터는 Scala에서 중요한 개념입니다. 예를 들어, `io.Source.fromFile` 메서드로 파일을 읽으면 파일에서 한 번에 한 줄씩 읽을 수 있게 해주는 이터레이터를 반환합니다. 큰 데이터 파일을 메모리에 통째로 읽어 들이는 것은 실용적이지 않으므로 이는 합당합니다.

이터레이터는 또한 지연(lazy)인 *뷰(views)* 의 변환 메서드에서도 사용됩니다. 예를 들어, 책 *Programming in Scala* 는 `lazyMap` 함수가 이터레이터로 구현될 수 있음을 보여줍니다.

```scala
def lazyMap[T, U](coll: Iterable[T], f: T => U) =
    new Iterable[U] {
        def iterator = coll.iterator map f
    }
```

레시피 11.4 "컬렉션에 지연 뷰 생성하기"에서 보여주듯이, 큰 컬렉션에 뷰를 사용하는 것은 성능을 개선하는 중요한 팁이 될 수 있습니다.

#### 이터레이터는 사용한 후에는 소진됩니다

이터레이터 사용에서 중요한 부분은 사용한 후에는 소진된다는(비어 있게 된다는) 것을 아는 것입니다. 각 요소에 접근하면서, 이터레이터를 변경(mutate)하고 이전 요소는 버려집니다(자세한 내용은 토론 참고). 예를 들어, `foreach`로 이터레이터의 요소들을 출력하면 첫 번째 호출은 동작합니다.

```scala
scala> val it = Iterator(1,2,3)
it: Iterator[Int] = nonempty iterator

scala> it.foreach(print)
123
```

하지만 같은 호출을 두 번째로 시도하면, 이터레이터가 소진되었기 때문에 아무 출력도 얻지 못합니다.

```scala
scala> it.foreach(print)
(여기엔 출력이 없습니다)
```

#### 이터레이터는 컬렉션처럼 동작합니다

기술적으로 이터레이터는 컬렉션이 아닙니다. 대신, 컬렉션의 요소들에 한 번에 하나씩 접근하는 방법을 제공합니다. 하지만 이터레이터는 `foreach`, `map`, `filter` 등 일반적인 컬렉션 클래스에서 볼 수 있는 많은 메서드를 정의하고 있습니다. 필요할 때 이터레이터를 컬렉션으로 변환할 수도 있습니다.

```scala
val i = Iterator(1,2,3)   // i: Iterator[Int] = <iterator>
val a = i.toVector        // a: Vector[Int] = Vector(1, 2, 3)

val i = Iterator(1,2,3)   // i: Iterator[Int] = <iterator>
val b = i.toList          // b: List[Int] = List(1, 2, 3)
```

#### 이터레이터는 지연됩니다(Iterators are lazy)

또 다른 중요한 점은 이터레이터가 지연된다는 것인데, 이는 그 변환 메서드들이 비엄격(nonstrict, lazy) 방식으로 평가됨을 의미합니다. 예를 들어, 다음 `for` 루프와 `map`, `filter` 메서드는 구체적인 결과를 반환하지 않고 단지 이터레이터를 반환한다는 것에 주목하세요.

```scala
val i = Iterator(1,2,3)           // i: Iterator[Int] = <iterator>

val a = for e <- i yield e*2      // a: Iterator[Int] = <iterator>
val b = i.map(_ * 2)              // b: Iterator[Int] = <iterator>
val c = i.filter(_ > 2)           // c: Iterator[Int] = <iterator>
```

다른 지연 메서드들과 마찬가지로, `foreach` 같은 엄격(strict) 메서드를 호출하여 강제로 평가될 때에만 평가됩니다.

```scala
scala> i.map(_ + 10).foreach(println)
11
12
13
```

#### BufferedIterator는 미리 엿볼 수 있게 해줍니다

버퍼링된 이터레이터(buffered iterator)는 이터레이터를 앞으로 이동시키지 않고도 다음 요소를 엿볼 수 있게 해주는 이터레이터입니다. `Iterator`의 `buffered` 메서드를 호출하여 `Iterator`로부터 `BufferedIterator`를 생성할 수 있습니다.

```scala
val it = Iterator(1,2)   // it: Iterator[Int] = <iterator>
val bi = it.buffered     // bi: BufferedIterator[Int] = <iterator>
```

그 후에는 `BufferedIterator`에서 `head` 메서드를 호출할 수 있는데, 이는 이터레이터에 영향을 주지 않습니다.

```scala
// 'head'를 원하는 만큼 여러 번 호출
bi.head   // 1
bi.head   // 1
bi.head   // 1
```

반면에, `Iterator`나 `BufferedIterator`에서 `next`를 호출하면 어떤 일이 일어나는지 주목하세요.

```scala
// 'next'는 이터레이터를 전진시킵니다
bi.next   // 1
bi.next   // 2
bi.next   // java.util.NoSuchElementException: next on empty iterator
```

> **head 호출에 주의하세요(Beware Calling Head)**
>
> 레시피 13.1에서 논의했듯이, 일반적으로 `head` 대신 `headOption`을 호출하는 것이 좋습니다. 빈 리스트에서, 또는 리스트의 끝에서 `head`를 호출하면 예외를 던지기 때문입니다.
>
> ```scala
> // 요소가 하나뿐인 BufferedIterator 생성
> val bi = Iterator(1).buffered
>     // result: BufferedIterator[Int] = <iterator>
>
> // 'head'는 잘 동작합니다
> bi.head         // 1
>
> // 이터레이터를 전진시킵니다
> bi.next         // 1
> bi.headOption   // None  (headOption은 의도대로 동작합니다)
>
> // 'head'는 터집니다
> bi.head
>     // result: java.util.NoSuchElementException:
>     //         next on empty iterator
> ```

### 토론(Discussion)

개념적으로, 이터레이터는 포인터와 같습니다. `List`에 이터레이터를 생성하면, 처음에는 리스트의 첫 번째 요소를 가리킵니다.

```scala
val x = 1 :: 2 :: Nil
        ^
```

그다음 이터레이터의 `next` 메서드를 호출하면, 컬렉션의 다음 요소를 가리킵니다.

```scala
val x = 1 :: 2 :: Nil
             ^
```

마지막으로, 이터레이터가 컬렉션의 끝에 도달하면 소진된 것으로 간주됩니다. 첫 번째 요소를 가리키도록 되돌아가지 않습니다.

```scala
val x = 1 :: 2 :: Nil
                  ^
```

해결책에서 보여준 대로, 이 시점에서 `next`나 `head`를 호출하려고 하면 `java.util.NoSuchElementException`을 던집니다.

---

## 13.4 Using zipWithIndex or zip to Create Loop Counters

### 문제(Problem)

`for` 루프나 `foreach` 메서드로 시퀀스를 순회할 때, 카운터를 수동으로 만들지 않고도 루프 안에서 카운터에 접근하고자 합니다.

### 해결책(Solution)

카운터를 만들려면 `zipWithIndex`나 `zip` 메서드를 사용하세요. 예를 들어, 문자(character)들의 리스트가 있다고 가정합니다.

```scala
val chars = List('a', 'b', 'c')
```

리스트의 요소들을 카운터와 함께 출력하는 한 가지 방법은 `zipWithIndex`, `foreach`, 그리고 중괄호 안의 `case` 문을 사용하는 것입니다.

```scala
chars.zipWithIndex.foreach {
    case (c, i) => println(s"character '$c' has index $i")
}

// 출력:
character 'a' has index 0
character 'b' has index 1
character 'c' has index 2
```

토론에서 보겠지만, 이 해결책이 동작하는 이유는 `zipWithIndex`가 두 요소짜리 튜플(tuple-2)들의 시퀀스를 다음과 같이 반환하기 때문입니다.

```scala
List((a,0), (b,1), ...
```

또한 코드 블록 안의 `case` 문이 tuple-2와 일치하기 때문에 동작합니다. `foreach`가 여러분의 알고리즘에 tuple-2를 전달하므로, 코드를 다음과 같이도 작성할 수 있습니다.

```scala
chars.zipWithIndex.foreach { t =>
    println(s"character '${t._1}' has index ${t._2}")
}
```

마지막으로, `for` 루프를 사용할 수도 있습니다.

```scala
for
    (c, i) <- chars.zipWithIndex
do
    println(s"character '$c' has index $i")
```

모든 루프는 동일한 출력을 냅니다.

#### zip으로 시작 값 제어하기(Control the starting value with zip)

`zipWithIndex`를 사용하면 카운터가 항상 `0`에서 시작합니다. 시작 값을 제어하고 싶다면, 대신 `zip`을 사용하세요.

```scala
for (c, i) <- chars.zip(LazyList from 1) do
    println(s"${c} is #${i}")
```

그 루프의 출력은 다음과 같습니다.

```
a is #1
b is #2
c is #3
```

### 토론(Discussion)

시퀀스에서 호출되면, `zipWithIndex`는 tuple-2 요소들의 시퀀스를 반환합니다. 예를 들어, `List[Char]`가 주어지면 `zipWithIndex`가 tuple-2 값들의 리스트를 생성하는 것을 볼 수 있습니다.

```scala
scala> val chars = List('a', 'b', 'c')
val chars: List[Char] = List(a, b, c)

scala> val zwi = chars.zipWithIndex
val zwi: List[(Char, Int)] = List((a,0), (b,1), (c,2))
```

#### 중괄호 안에 case 문 사용하기(Using a case statement in curly braces)

해결책에서 보여준 대로, `foreach`와 함께 중괄호 안에 `case` 문을 사용할 수 있습니다.

```scala
chars.zipWithIndex.foreach {
    case (c, i) => println(s"character '$c' has index $i")
}
```

이 접근법은 함수 리터럴이 기대되는 곳이라면 어디든 사용할 수 있습니다. 다른 상황에서는 필요한 만큼 많은 `case` 대안(alternatives)을 사용할 수 있습니다.

Scala 2.13 이전에는, 그 예시를 `case` 키워드로만 작성할 수 있었지만, Scala 2.13 이후로는 그 코드 줄을 다음 방식들 중 어느 것으로도 작성할 수 있습니다.

```scala
case(c, i) => println(s"character '$c' has index $i")     // 앞서 보여준 방식
case(c -> i) => println(s"character '$c' has index $i")   // 대안
(c, i) => println(s"character '$c' has index $i")         // 'case' 없이
```

#### 지연 뷰 사용하기(Using a lazy view)

`zipWithIndex`는 기존 시퀀스로부터 새로운 시퀀스를 생성하므로, 특히 큰 시퀀스를 다룰 때는 `zipWithIndex`를 호출하기 전에 `view`를 호출하는 것이 좋을 수 있습니다.

```scala
scala> val zwi2 = chars.view.zipWithIndex
zwi2: scala.collection.View[(Char, Int)] = View(<not computed>)
```

레시피 11.4 "컬렉션에 지연 뷰 생성하기"에서 논의했듯이, 이는 `chars`에 대한 지연 뷰를 생성하는데, 이는 다음을 의미합니다.

- 중간 시퀀스(intermediate sequence)가 생성되지 *않습니다*.
- 튜플 요소들은 필요해질 때까지 생성되지 않습니다. 다만 루프의 경우, 알고리즘에 `break`나 예외가 포함되지 않는 한 일반적으로 항상 필요하게 됩니다.

`view`를 사용하면 중간 컬렉션이 생성되는 것을 방지하므로, `zipWithIndex`를 호출하기 전에 `view`를 호출하는 것은 큰 컬렉션을 순회할 때 도움이 될 수 있습니다. 늘 그렇듯, 성능이 우려된다면 뷰를 사용했을 때와 사용하지 않았을 때를 모두 테스트하세요.

---

## 13.5 Transforming One Collection to Another with map

### 문제(Problem)

이전 레시피와 마찬가지로, 원본 컬렉션의 모든 요소에 알고리즘을 적용하여 한 컬렉션을 다른 컬렉션으로 변환하고자 합니다.

### 해결책(Solution)

레시피 4.4 "for/yield로 기존 컬렉션에서 새 컬렉션 만들기"에서 보여준 `for/yield` 조합을 사용하는 대신, 컬렉션에서 `map` 메서드를 호출하여 함수, 익명 함수, 또는 메서드를 전달해 각 요소를 변환하세요. 다음 예시들은 익명 함수를 사용하는 방법을 보여줍니다.

```scala
val a = Vector(1,2,3)

// 각 요소에 1을 더합니다
val b = a.map(_ + 1)        // b: Vector(2, 3, 4)
val b = a.map(e => e + 1)   // b: Vector(2, 3, 4)

// 각 요소를 두 배로 만듭니다
val b = a.map(_ * 2)        // b: Vector(2, 4, 6)
val b = a.map(e => e * 2)   // b: Vector(2, 4, 6)
```

다음 예시들은 함수(또는 메서드)를 사용하는 방법을 보여줍니다.

```scala
def plusOne(i: Int) = i + 1
val a = Vector(1,2,3)

// map과 함께 plusOne을 사용하는 세 가지 방법
val b = a.map(plusOne)         // b: Vector(2, 3, 4)
val b = a.map(plusOne(_))      // b: Vector(2, 3, 4)
val b = a.map(e => plusOne(e)) // b: Vector(2, 3, 4)
```

#### map과 함께 동작하는 메서드 작성하기(Writing a method to work with map)

`map`과 함께 동작하는 메서드를 작성할 때는 다음을 따르세요.

- 메서드가 컬렉션과 같은 타입의 단일 입력 파라미터를 받도록 정의하세요.
- 메서드의 반환 타입은 필요한 어떤 타입이든 될 수 있습니다.

예를 들어, 잠재적으로 정수로 변환될 수 있는 문자열 리스트가 있다고 상상해 보세요.

```scala
val strings = List("1", "2", "hi mom", "4", "yo")
```

`map`을 사용하여 그 문자열 리스트를 정수 리스트로 변환할 수 있습니다. 먼저 필요한 것은 (a) `String`을 받고 (b) `Int`를 반환하는 메서드입니다. 예를 들어, 접근법을 보여주는 첫 단계로서, 리스트의 각 문자열 길이를 알아내고 싶다면 이 `lengthOf` 메서드가 잘 동작합니다.

```scala
def lengthOf(s: String): Int = s.length
val x = strings.map(lengthOf)   // x: List(1, 1, 6, 1, 2)
```

하지만 저자는 각 문자열을 정수로 변환하고 싶고—일부 문자열은 제대로 변환되지 않을 것이므로—실제로 필요한 것은 `Option[Int]`를 반환하는 함수입니다.

```scala
import scala.util.Try
def makeInt(s: String): Option[Int] = Try(Integer.parseInt(s)).toOption
```

`"1"` 문자열이 주어지면 그 메서드는 `Some(1)`을 반환하고, `"yo"` 같은 문자열이 주어지면 `None`을 반환합니다.

이제 `makeInt`를 `map`과 함께 사용하여 리스트의 각 문자열을 정수로 변환하기 시작할 수 있습니다. 첫 번째 시도는 `List[Option[Int]]` 타입, 즉 `Option[Int]`의 리스트를 반환합니다.

```scala
scala> val intOptions = strings.map(makeInt)
val intOptions: List[Option[Int]] = List(Some(1), Some(2), None, Some(4), None)
```

하지만 사용 가능한 컬렉션 메서드들을 알게 되면, 그 `List[Option[Int]]`를 `List[Int]`로 평탄화할 수 있음을 알게 됩니다.

```scala
scala> val ints = strings.map(makeInt).flatten
val ints: List[Int] = List(1, 2, 4)
```

### 토론(Discussion)

저자가 처음 Scala에 입문했을 때 배경이 Java였기에, 처음에는 `for/yield` 루프를 작성했습니다. 그것들은 더 익숙했던 명령형(imperative) 해결책이었습니다. 하지만 결국 `map`이 가드(guard)가 없고 단 하나의 생성기(generator)만 있는 `for/yield` 표현식과 동일하다는 것을 깨달았습니다.

```scala
val list = List("a", "b", "c")                    // list: List(a, b, c)

// map
val caps1 = list.map(_.capitalize)                // caps1: List(A, B, C)

// for/yield
val caps2 = for e <- list yield e.capitalize      // caps2: List(A, B, C)
```

이것을 이해하고 나서, `map`을 사용하기 시작했습니다.

이는 Scala 컬렉션 클래스에서 발견하게 될 수십 개의 함수형 메서드에 대한 핵심 개념입니다. `map`, `filter`, `take` 등의 메서드는 모두 직접 작성하는 `for` 루프를 대체합니다. 이 내장 함수형 메서드들을 사용하는 데에는 많은 이점이 있지만, 두 가지 중요한 이점은 다음과 같습니다.

- 직접 `for` 루프를 작성하지 않아도 됩니다.
- 다른 개발자들이 작성한 직접 만든 `for` 루프를 읽지 않아도 됩니다.

이 줄들을 비꼬는 의도로 한 것은 아닙니다. 오히려 `for` 루프가 그 안의 직접 만든 알고리즘—즉 *의도(intent)*—를 찾기 위해 읽어야 할 상용구(boilerplate) 코드를 많이 요구한다는 점을 말하고자 하는 것입니다. 반대로, Scala의 내장 컬렉션 메서드를 사용하면 그 의도를 훨씬 쉽게 알아볼 수 있습니다.

작은 예로서, 다음과 같은 리스트가 주어졌을 때,

```scala
val fruits = List("banana", "peach", "lime", "pear", "cherry")
```

(a) 두 글자보다 길고 (b) 여섯 글자보다 짧은 모든 문자열을 찾은 다음, (c) 그 남은 문자열들을 대문자화하는 명령형 해결책은 다음과 같습니다.

```scala
val newFruits = for
    f <- fruits
    if f.length < 6
    if f.startsWith("p")
yield f.capitalize
```

Scala의 구문 덕분에 읽기에 그리 어렵지는 않지만, 적어도 두 가지에 주목하세요.

- `f <- fruits`, 즉 "fruits의 각 fruit에 대해"를 명시적으로 작성해야 합니다.
- 알고리즘의 일부는 `for` 표현식 안에 있고, 다른 일부는 `yield` 키워드 뒤에 옵니다.

반대로, 같은 문제에 대한 관용적인(idiomatic) Scala 해결책은 다음과 같습니다.

```scala
val newFruits = fruits.filter(_.length > 2)
                      .filter(_.startsWith("p"))
                      .map(_.capitalize)
```

이런 작은 예에서도, (a) 무엇을 원하는지를 작성하는 것과 (b) 원하는 것을 얻기 위한 단계별 명령형 알고리즘을 작성하는 것의 차이를 볼 수 있습니다. Scala 컬렉션 메서드 사용법을 이해하고 나면, 직접 만든 `for` 루프의 세부 사항보다는 의도에 더 집중하게 되고, 코드는 더 간결하면서도 여전히 매우 읽기 쉬워집니다—이것이 우리가 말하는 *표현력 있는(expressive)* 코드입니다.

> **map을 transform으로 생각하기(Think of map as transform)**
>
> 저자가 막 Scala와 `map` 메서드를 사용하기 시작했을 때, 처음에는 `map`을 입력할 때마다 *transform* 이라고 말하는 것이 도움이 되었습니다. 즉, 메서드 이름이 `map` 대신 `transform`이었으면 했습니다.
>
> ```scala
> fruits.map(_.capitalize)         // 실제 이름
> fruits.transform(_.capitalize)   // 저자가 바랐던 이름
> ```
>
> 이는 `map`이 초기 리스트의 각 요소에 여러분의 함수를 적용하고, 그 요소들을 새 리스트로 변환(transform)하기 때문입니다.
>
> (저자의 블로그 글 "The 'Great FP Terminology Barrier'"에서 설명하듯이, `map`이라는 이름은 수학 분야에서 유래했습니다.)

---

## 13.6 Flattening a List of Lists with flatten

### 문제(Problem)

리스트의 리스트—더 일반적으로는 시퀀스의 시퀀스—가 있고, 그것들로부터 하나의 리스트(시퀀스)를 만들고자 합니다.

### 해결책(Solution)

`flatten` 메서드를 사용하여 리스트의 리스트를 단일 리스트로 변환하세요. 이를 시연하기 위해, 먼저 리스트의 리스트를 만듭니다.

```scala
val lol = List(List(1,2), List(3,4))
```

이 리스트의 리스트에서 `flatten` 메서드를 호출하면 새로운 리스트 하나가 생성됩니다.

```scala
val x = lol.flatten   // x: List(1, 2, 3, 4)
```

보시다시피, `flatten`은 그 이름이 의미하는 일을 합니다. 외부 리스트 안에 담긴 두 리스트를 평탄화하여 하나의 결과 리스트로 만듭니다.

여기서는 *list* 라는 용어를 사용하지만, `flatten` 메서드는 `List`에만 국한되지 않습니다. 다른 시퀀스(`Array`, `ArrayBuffer`, `Vector` 등)와도 동작합니다.

```scala
val a = Vector(Vector(1,2), Vector(3,4))
val b = a.flatten   // b: Vector(1, 2, 3, 4)
```

### 토론(Discussion)

소셜 네트워킹 애플리케이션에서, 여러분의 친구들과 그들의 친구들의 리스트로 같은 일을 할 수 있습니다.

```scala
val myFriends = List("Adam", "David", "Frank")
val adamsFriends = List("Nick K", "Bill M")
val davidsFriends = List("Becca G", "Kenny D", "Bill M")
val franksFriends = List[String] = Nil
val friendsOfFriends = List(adamsFriends, davidsFriends, franksFriends)
```

`friendsOfFriends`가 리스트의 리스트이기 때문에,

```scala
List(
    List("Nick K", "Bill M"),
    List("Becca G", "Kenny D", "Bill M"),
    List()
)
```

`flatten`을 사용하여 친구들의 친구들의 고유한 리스트를 만드는 것 같은 여러 작업을 수행할 수 있습니다.

```scala
scala> val uniqueFriendsOfFriends = friendsOfFriends.flatten.distinct
uniqueFriendsOfFriends: List[String] = List(Nick K, Bill M, Becca G, Kenny D)
```

#### Seq[Option]과 함께 쓰는 flatten(flatten with Seq[Option])

`flatten`은 `Option` 값들의 리스트가 있을 때 특히 유용합니다. `Option`은 0개 또는 1개의 요소를 담는 컨테이너로 생각할 수 있으므로, `flatten`은 `Some`과 `None` 요소들의 시퀀스에 매우 유용한 효과를 가집니다. `Some`은 요소가 하나인 리스트와 같고 `None`은 요소가 없는 리스트와 같기 때문에, `flatten`은 새 리스트를 만들면서 `Some` 요소들에서 값을 추출하고 `None` 요소들을 버립니다.

```scala
val x = Vector(Some(1), None, Some(3), None)   // x: Vector[Option[Int]]
val y = x.flatten                              // y: Vector(1, 3)
```

`Option`/`Some`/`None` 값에 익숙하지 않다면, `Some`과 `None` 값들의 리스트를 각 리스트가 한 개 또는 0개의 항목을 담는 리스트의 리스트와 비슷한 것으로 생각하면 도움이 됩니다.

```scala
List( Some(1), None,   Some(2) ).flatten   // List(1, 2)
List( List(1), List(), List(2) ).flatten   // List(1, 2)
```

`Option` 값을 다루는 더 많은 정보는 레시피 24.6 "Scala의 오류 처리 타입(Option, Try, Either) 사용하기"를 참고하세요.

#### map과 flatten을 결합한 flatMap(Combining map and flatten with flatMap)

시퀀스에 `map`을 호출한 다음 `flatten`을 호출해야 할 일이 생기면, 대신 `flatMap`을 사용할 수 있습니다. 예를 들어, 이 `nums` 리스트와 `Option`을 반환하는 `makeInt` 메서드가 주어졌을 때,

```scala
val nums = List("1", "2", "three", "4", "one hundred")

import scala.util.{Try,Success,Failure}
def makeInt(s: String): Option[Int] = Try(Integer.parseInt(s.trim)).toOption
```

그 리스트에서 제대로 정수로 변환되는 문자열들의 합을 `map` 다음에 `flatten`을 사용하여 계산할 수 있습니다.

```scala
nums.map(makeInt).flatten   // List(1, 2, 4)
```

하지만 이와 같은 리스트를 다룰 때는 언제든 대신 `flatMap`을 사용할 수 있습니다.

```scala
nums.flatMap(makeInt)   // List(1, 2, 4)
```

이는 이 메서드가 "map flat"이라고 불려야 한다고 생각하게 만들지만, `flatMap`이라는 이름은 오랫동안 사용되어 왔으며, 이것은 그 한 가지 가능한 사용 사례일 뿐입니다.

---

## 13.7 Using filter to Filter a Collection

### 문제(Problem)

컬렉션의 항목들을 필터링하여, 여러분의 필터링 기준과 일치하는 요소들만 담은 새 컬렉션을 만들고자 합니다.

### 해결책(Solution)

시퀀스를 필터링하려면 다음을 따르세요.

- 불변(immutable) 컬렉션에서는 `filter` 메서드를 사용하세요.
- 가변(mutable) 컬렉션에서는 `filterInPlace`를 사용하세요.

필요에 따라, 레시피 13.1에서 설명한 다른 함수형 메서드들을 사용하여 컬렉션을 필터링할 수도 있습니다.

#### 불변 컬렉션에서 filter 사용하기(Use filter on immutable collections)

다음은 `Seq`의 `filter` 메서드 시그니처입니다.

```scala
def filter(p: (A) => Boolean): Seq[A]    // 일반적인 경우
```

따라서 구체적인 예로서, `Seq[Int]`가 있을 때 그 시그니처는 다음과 같습니다.

```scala
def filter(p: (Int) => Boolean): Seq[Int]   // Seq[Int]에 대한 구체적인 경우
```

이는 `filter`가 *술어(predicate)*—`true` 또는 `false`를 반환하는 함수—를 받아 시퀀스를 반환함을 의미합니다. 여러분이 제공하는 술어는 시퀀스에 담긴 타입(예: `Int`)의 입력 파라미터를 받아 `Boolean`을 반환해야 합니다. 여러분의 함수는 새 컬렉션에 유지하고 싶은 요소들에 대해서는 `true`를, 버리고 싶은 요소들에 대해서는 `false`를 반환해야 합니다. 필터링 연산의 결과를 새 변수에 할당하는 것을 잊지 마세요.

예를 들어, 다음 예시들은 정수 리스트와 두 가지 다른 알고리즘으로 `filter`를 사용하는 방법을 보여줍니다.

```scala
val a = List.range(1, 10)        // a: List(1, 2, 3, 4, 5, 6, 7, 8, 9)

// 5보다 작은 모든 요소로 새 리스트를 만듭니다
val b = a.filter(_ < 5)          // b: List(1, 2, 3, 4)
val b = a.filter(e => e < 5)     // b: List(1, 2, 3, 4)

// 리스트의 모든 짝수로 리스트를 만듭니다
val evens = x.filter(_ % 2 == 0)   // evens: List(2, 4, 6, 8)
```

보시다시피, `filter`는 여러분의 함수/술어가 호출될 때 `true`를 반환하는 시퀀스의 모든 요소를 반환합니다. 여러분의 함수가 `false`를 반환하는 리스트의 모든 요소를 반환하는 `filterNot` 메서드도 있습니다.

#### 가변 컬렉션에서 filterInPlace 사용하기(Use filterInPlace on mutable collections)

`ArrayBuffer` 같은 가변 컬렉션이 있을 때는, `filter` 대신 `filterInPlace`를 사용하세요.

```scala
import scala.collection.mutable.ArrayBuffer
val a = ArrayBuffer.range(1,10)   // a: ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8, 9)

a.filterInPlace(_ < 5)   // a: ArrayBuffer(1, 2, 3, 4)
a.filterInPlace(_ > 2)   // a: ArrayBuffer(3, 4)
```

`ArrayBuffer`는 가변이므로, `filterInPlace`의 결과를 다른 변수에 할당할 필요가 없습니다. 변수 `a`의 내용이 직접 변경됩니다.

### 토론(Discussion)

컬렉션을 필터링하는 데 사용할 수 있는 주요 메서드들은 레시피 13.1에 나열되어 있으며, 편의를 위해 여기에 반복합니다: `collect`, `diff`, `distinct`, `drop`, `dropRight`, `dropWhile`, `filter`, `filterNot`, `filterInPlace`, `find`, `foldLeft`, `foldRight`, `head`, `headOption`, `init`, `intersect`, `last`, `lastOption`, `slice`, `tail`, `take`, `takeRight`, `takeWhile`, 그리고 `union`.

다른 메서드들과 비교했을 때 `filter`(와 `filterInPlace`)의 고유한 특징은 다음과 같습니다.

- `filter`는 컬렉션의 *모든* 요소를 순회합니다. 다른 일부 메서드들은 컬렉션의 끝에 도달하기 전에 멈춥니다.
- `filter`는 요소를 필터링하기 위한 *술어(predicate)* 를 제공하게 해줍니다.

#### 여러분의 술어가 필터링을 제어합니다(Your predicate controls the filtering)

컬렉션의 요소들을 어떻게 필터링하는지는 전적으로 여러분의 알고리즘에 달려 있습니다. 불변 컬렉션과 `filter`를 사용하여, 다음 예시들은 문자열 리스트를 필터링하는 몇 가지 방법을 보여줍니다.

```scala
val fruits = List("orange", "peach", "apple", "banana")
val x = fruits.filter(f => f.startsWith("a"))   // List(apple)
val x = fruits.filter(_.startsWith("a"))        // List(apple)
val x = fruits.filter(_.length > 5)             // List(orange, banana)
```

#### collect 메서드로 컬렉션 필터링하기(Using the collect method to filter a collection)

`collect` 메서드는 흥미로운 필터링 메서드입니다. `collect` 메서드는 `IterableOnceOps` 트레이트에 정의되어 있으며, `IterableOnceOps` Scaladoc에 따르면 `collect`는 "함수가 정의된 이 리스트의 모든 요소에 부분 함수(partial function)를 적용하여" 새 리스트를 만듭니다. 함수를 적용하기 때문에, 다음 REPL 예시들에서 보여주듯이 단일 `case` 문으로 리스트를 필터링하는 좋은 방법이 될 수 있습니다.

```scala
scala> val x = List(0,1,2)
val x: List[Int] = List(0, 1, 2)

scala> val y = x.collect{ case i: Int if i > 0 => i }
val y: List[Int] = List(1, 2)

scala> val x = List(Some(1), None, Some(3))
val x: List[Option[Int]] = List(Some(1), None, Some(3))

scala> val y = x.collect{ case Some(i) => i}
val y: List[Int] = List(1, 3)
```

이 예시들이 동작하는 이유는 (a) 레시피 13.4에서 논의했듯이 `case` 표현식이 익명 함수를 생성하고 (b) `collect` 메서드가 부분 함수와 함께 동작하기 때문입니다. `collect`가 어떻게 동작하는지를 보여주지만, `List[Option]` 값들을 사용한 두 번째 예시는 `flatten`으로 더 쉽게 줄일 수 있다는 점에 유의하세요.

```scala
scala> x.flatten
val res0: List[Int] = List(1, 3)
```

관련하여, 일치하는 첫 번째 요소를 `Option`으로 반환하는 `collectFirst` 메서드도 있습니다.

```scala
scala> val firstTen = (1 to 10).toList
val firstTen: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

scala> firstTen.collectFirst{case x if x > 1 => x}
val res0: Option[Int] = Some(2)

scala> firstTen.collectFirst{case x if x > 99 => x}
val res1: Option[Int] = None
```

---

## 13.8 Extracting a Sequence of Elements from a Collection

### 문제(Problem)

시작 위치와 길이, 또는 함수를 지정하여 컬렉션에서 연속된(contiguous) 요소들의 시퀀스를 추출하고자 합니다.

### 해결책(Solution)

시퀀스에서 연속된 요소들의 리스트를 추출하는 데 사용할 수 있는 컬렉션 메서드가 꽤 많은데, `drop`, `dropWhile`, `head`, `headOption`, `init`, `last`, `lastOption`, `slice`, `tail`, `take`, 그리고 `takeWhile`이 있습니다.

다음 `Vector`가 주어졌을 때,

```scala
val x = (1 to 10).toVector
```

`drop` 메서드는 지정한 개수의 요소를 시퀀스의 처음부터 버립니다.

```scala
val y = x.drop(3)          // y: Vector(4, 5, 6, 7, 8, 9, 10)
```

`dropRight`는 `drop`처럼 동작하지만, 컬렉션의 끝에서 시작하여 앞쪽으로 진행하면서 시퀀스의 끝에서 요소들을 버립니다.

```scala
val y = x.dropRight(4)     // y: Vector(1, 2, 3, 4, 5, 6)
```

`dropWhile`은 여러분이 제공하는 술어가 `true`를 반환하는 동안 요소들을 버립니다.

```scala
val y = x.dropWhile(_ < 6)   // y: Vector(6, 7, 8, 9, 10)
```

`take`는 시퀀스에서 처음 N개의 요소를 추출합니다.

```scala
val y = x.take(3)          // y: Vector(1, 2, 3)
```

`takeRight`는 `take`와 같은 방식으로 동작하지만, 시퀀스의 끝에서 요소들을 가져옵니다.

```scala
val y = x.takeRight(3)     // y: Vector(8, 9, 10)
```

`takeWhile`은 여러분이 제공하는 술어가 `true`를 반환하는 동안 요소들을 반환합니다.

```scala
val y = x.takeWhile(_ < 5)   // y: Vector(1, 2, 3, 4)
```

> **성능 참고(Performance Note)**
>
> `List` 클래스가 구성되는 방식 때문에, `dropRight`와 `takeRight` 같은 메서드는 `List` 같은 선형(linear) 시퀀스에서 느리게 동작합니다. 큰 시퀀스에서 이 메서드들을 사용해야 한다면, 대신 `Vector` 같은 인덱스 기반(indexed) 시퀀스를 사용하세요.

`slice(from, until)`은 인덱스 `from`에서 시작하여 인덱스 `until`까지(단, `until`은 포함하지 않음)의 시퀀스를 반환하며, 0 기반(zero-based) 인덱스를 가정합니다.

```scala
val chars = Vector('a', 'b', 'c', 'd')

chars.slice(0,1)   // Vector(a)
chars.slice(0,2)   // Vector(a, b)

chars.slice(1,1)   // Vector()
chars.slice(1,2)   // Vector(b)
chars.slice(1,3)   // Vector(b, c)

chars.slice(2,3)   // Vector(c)
chars.slice(2,4)   // Vector(c, d)
```

이 모든 메서드는 시퀀스를 필터링하는 또 다른 방법을 제공하며, 그 구별되는 특징은 연속된 요소들의 시퀀스를 반환한다는 것입니다.

### 토론(Discussion)

사용할 수 있는 메서드가 더 있습니다. 다음 리스트가 주어졌을 때,

```scala
val nums = Vector(1, 2, 3, 4, 5)
```

다음 표현식 뒤의 주석은 각 메서드가 반환하는 값을 보여줍니다.

```scala
nums.head          // 1
nums.headOption    // Some(1)
nums.init          // Vector(1, 2, 3, 4)

nums.tail          // Vector(2, 3, 4, 5)
nums.last          // 5
nums.lastOption    // Some(5)
```

일반적으로 이 메서드들이 어떻게 동작하는지는 이름에서 분명하지만, 약간의 설명이 필요할 수 있는 두 가지는 `init`과 `tail`입니다. `init` 메서드는 마지막 요소를 제외한 시퀀스의 모든 요소를 반환합니다. `tail` 메서드는 첫 번째 요소를 제외한 모든 요소를 반환합니다.

주의 사항으로, `head`, `init`, `tail`, `last`는 빈 시퀀스에서 `java.lang.UnsupportedOperationException`을 던진다는 점을 알아두세요. 함수형 프로그래밍에서, 순수 함수는 *전체(total)*—모든 입력에 대해 정의되어 있음—이며 예외를 던지지 않습니다. 이 때문에 순수 함수형 프로그래머들은 일반적으로 이 메서드들을 사용하지 않습니다. 사용할 때는 시퀀스가 비어 있지 않은지 신중하게 확인합니다.

---

## 13.9 Splitting Sequences into Subsets

### 문제(Problem)

여러분이 정의하는 알고리즘이나 위치를 기준으로, 시퀀스를 원본 시퀀스의 서브셋(subset)인 둘 이상의 시퀀스로 분할(partition)하고자 합니다.

### 해결책(Solution)

`groupBy`, `splitAt`, `partition`, 또는 `span` 메서드를 사용하여 시퀀스를 하위 시퀀스(subsequence)들로 분할하세요. 이 메서드들은 다음 예시들에서 시연됩니다.

#### groupBy

`groupBy` 메서드는 여러분이 제공하는 술어 함수에 따라 시퀀스를 서브셋들로 나누게 해줍니다.

```scala
val xs = List(15, 10, 5, 8, 20, 12)
val groups = xs.groupBy(_ > 10)
    // Map(false -> List(10, 5, 8), true -> List(15, 20, 12))
```

`groupBy`는 여러분의 함수에 따라 컬렉션을 N개의 시퀀스로 이루어진 `Map`으로 분할합니다. 이 예시에서는 두 개의 결과 시퀀스가 있지만, 여러분의 알고리즘에 따라 맵 안에 몇 개의 시퀀스든 만들 수 있습니다.

이 특정 예시에서, `true` 키는 여러분의 술어가 `true`를 반환하는 요소들을 가리키고, `false` 키는 `false`를 반환하는 요소들을 가리킵니다. `groupBy`가 생성하는 `Map`의 시퀀스들은 다음과 같이 접근할 수 있습니다.

```scala
val listOfTrues = groups(true)     // List(15, 20, 12)
val listOfFalses = groups(false)   // List(10, 5, 8)
```

다음은 `groupBy`로 얼마나 많은 리스트가 생성될 수 있는지 보여주는 또 다른 알고리즘입니다.

```scala
val xs = List(1, 3, 11, 12, 101, 102)

// 이 알고리즘에 'scala.math.log10'을 사용할 수도 있습니다
def groupBy10s(i: Int): Int =
    assert(i > 0)
    if i < 10 then 1
    else if i < 100 then 10
    else 100

xs.groupBy(groupBy10s)   // result: HashMap(
    //     1 -> List(1, 3),
    //     10 -> List(11, 12),
    //     100 -> List(101, 102)
    // )
```

#### splitAt, partition, span

`splitAt`은 원본 시퀀스를 나눌 인덱스를 제공하여 초기 시퀀스로부터 두 개의 하위 시퀀스를 만들게 해줍니다.

```scala
val xs = List(15, 5, 20, 10)

val ys = xs.splitAt(1)   // ys: (List(15), List(5, 20, 10))
val ys = xs.splitAt(2)   // ys: (List(15, 5), List(20, 10))
```

`partition`과 `span`은 술어 함수에 따라 시퀀스를 tuple-2에 담긴 두 개의 시퀀스로 나누게 해줍니다.

```scala
val xs = List(15, 5, 25, 20, 10)

val ys = xs.partition(_ > 10)   // ys: (List(15, 25, 20), List(5, 10))
val ys = xs.partition(_ < 25)   // ys: (List(15, 5, 20, 10), List(25))

val ys = xs.span(_ < 20)        // ys: (List(15, 5), List(25, 20, 10))
val ys = xs.span(_ > 10)        // ys: (List(15), List(5, 25, 20, 10))
```

`splitAt`, `partition`, `span` 메서드는 원본 컬렉션과 같은 타입의 시퀀스들로 이루어진 tuple-2를 만듭니다. 이들이 동작하는 방식은 다음과 같습니다.

- `splitAt`은 여러분이 제공한 요소 인덱스 값에 따라 원본 리스트를 나눕니다.
- `partition`은 두 개의 리스트를 만드는데, 하나는 여러분의 술어가 `true`를 반환하는 값들을 담고, 다른 하나는 `false`를 반환하는 요소들을 담습니다.
- `span`은 여러분의 술어 `p`를 기준으로 tuple-2를 반환하는데, 이는 "이 리스트의 모든 요소가 `p`를 만족하는 가장 긴 접두부(longest prefix)와, 이 리스트의 나머지"로 구성됩니다.

시퀀스들의 tuple-2가 반환되면, 그 두 시퀀스는 다음과 같이 접근할 수 있습니다.

```scala
scala> val (a,b) = xs.partition(_ > 10)
val a: List[Int] = List(15, 20)
val b: List[Int] = List(5, 10)
```

### 토론(Discussion)

이것들이 저자가 가장 흔하게 사용하는 그룹화 메서드들이지만, `sliding`과 `unzip`을 포함한 다른 메서드들도 있습니다.

#### sliding

`sliding(size,step)` 메서드는 시퀀스를 여러 그룹으로 나누는 데 사용할 수 있는 흥미로운 메서드입니다. `size`만으로, 또는 `size`와 `step` 둘 다로 호출할 수 있습니다.

```scala
val a = Vector(1,2,3,4,5)

// size = 2
val b = a.sliding(2).toList     // b: List(Array(1, 2), Array(2, 3),
                                //         Array(3, 4), Array(4, 5))

// size = 2, step = 2
val b = a.sliding(2,2).toList    // b: List(Vector(1, 2), Vector(3, 4),
                                 //         Vector(5))

// size = 2, step = 3
val b = a.sliding(2,3).toList    // b: List(Vector(1, 2), Vector(4, 5))
```

보시다시피, `sliding`은 원본 시퀀스 위로 "슬라이딩 윈도우(sliding window)"를 통과시키면서, `size`로 주어진 길이의 시퀀스들을 반환하는 방식으로 동작합니다. `step` 파라미터는 마지막 두 예시에서 보여주듯이 요소들을 건너뛸 수 있게 해줍니다.

#### unzip

`unzip` 메서드 또한 흥미롭습니다. tuple-2 값들의 시퀀스를 받아 두 개의 결과 리스트를 만드는 데 사용할 수 있습니다. 하나는 각 튜플의 첫 번째 요소를 담고, 다른 하나는 각 튜플의 두 번째 요소를 담습니다.

```scala
val listOfTuple2s = List((1,2), ('a','b'))
val x = listOfTuple2s.unzip       // (List(1, a),List(2, b))
```

예를 들어, 커플(couple) 리스트가 주어졌을 때, `unzip`으로 그 리스트를 여성 리스트와 남성 리스트로 만들 수 있습니다.

```scala
val couples = List(("Wilma", "Fred"), ("Betty", "Barney"))
val (women, men) = couples.unzip   // val men: List(Fred, Barney)
                                   // val women: List(Wilma, Betty)
```

그 이름에서 짐작할 수 있듯이, `unzip`은 `zip`의 반대입니다.

```scala
val couples = women zip men        // List((Wilma,Fred), (Betty,Barney))
```

어떤 시퀀스(`List`, `Vector` 등)든 더 많은 메서드는 Scaladoc을 참고하세요.

---

## 13.10 Walking Through a Collection with the reduce and fold Methods

### 문제(Problem)

시퀀스의 모든 요소를 순회하면서, 인접한 모든 요소에 알고리즘을 적용하여 합, 곱, 최댓값, 최솟값 등 연산으로부터 단일 스칼라(scalar) 결과를 얻고자 합니다.

### 해결책(Solution)

`reduceLeft`, `foldLeft`, `reduceRight`, `foldRight` 메서드를 사용하여 시퀀스의 요소들을 순회하면서 여러분의 사용자 정의 알고리즘을 요소들에 적용해 단일 스칼라 결과를 얻으세요. `reduceLeft`는 여기 해결책에서 시연되고, 나머지 메서드들은 토론에서(`scanLeft`, `scanRight` 같은 관련 메서드들과 함께) 보여집니다. 이 관련 메서드들은 시퀀스를 결과로 반환합니다.

예를 들어, `reduceLeft`를 사용하여 시퀀스를 왼쪽에서 오른쪽으로, 첫 번째 요소에서 마지막 요소까지 순회합니다. 이 메서드들이 정의된 `IterableOnceOps` 트레이트 Scaladoc에 따르면, 여러분은 *결합적 이항 연산자(associative binary operator)* 를 전달하고, 그러면 `reduceLeft`는 시퀀스의 처음 두 요소에 여러분의 연산자(알고리즘)를 적용하여 중간 결과를 만드는 것으로 시작합니다. 다음으로, 여러분의 알고리즘이 그 중간 결과와 세 번째 요소에 적용되고, 그 적용이 새 결과를 만듭니다. 이 과정은 시퀀스의 끝에 도달할 때까지 계속됩니다.

이 메서드들을 한 번도 사용해 본 적이 없다면, 놀라울 정도로 많은 능력을 제공한다는 것을 알게 될 것입니다. 이를 보여주는 가장 좋은 방법은 몇 가지 예시입니다. 먼저, 실험할 샘플 시퀀스를 만듭니다.

```scala
val xs = Vector(12, 6, 15, 2, 20, 9)
```

그 시퀀스가 주어지면, `reduceLeft`를 사용하여 시퀀스에 대한 다양한 속성을 결정합니다. 시퀀스 요소들의 합을 계산하는 방법은 다음과 같습니다.

```scala
val sum = xs.reduceLeft(_ + _)   // 64
```

밑줄(`_`)이 혼란스럽게 하지 않도록 하세요. 그것들은 단지 `reduceLeft`가 여러분의 함수에 전달하는 두 파라미터를 나타냅니다. 원한다면 익명 함수를 다음과 같이 작성할 수도 있습니다.

```scala
xs.reduceLeft((x,y) => x + y)
```

다음 예시들은 `reduceLeft`를 사용하여 시퀀스 모든 요소의 곱, 시퀀스의 최솟값, 그리고 최댓값을 얻는 방법을 보여줍니다.

```scala
xs.reduceLeft(_ * _)     // 388800
xs.reduceLeft(_ min _)   // 2
xs.reduceLeft(_ max _)   // 20
```

`reduceLeft`는 요소가 하나뿐인 경우에도 동작한다는 점에 유의하세요.

```scala
List(1).reduceLeft(_ * _)     // 1
List(1).reduceLeft(_ min _)   // 1
List(1).reduceLeft(_ max _)   // 1
```

하지만 주어진 시퀀스가 비어 있으면 예외를 던집니다.

```scala
val emptyVector = Vector[Int]()
val sum = emptyVector.reduceLeft(_ + _)
// result: java.lang.UnsupportedOperationException: empty.reduceLeft
```

이 예외 동작 때문에, `reduceLeft`를 사용하기 전에 컬렉션 크기를 확인하거나, 첫 번째 파라미터 그룹에 초기 시드(seed) 값을 제공할 수 있게 해주어 이 문제를 피하게 해주는 `foldLeft`를 사용하는 것이 좋습니다.

```scala
val emptyVector = Vector[Int]()
emptyVector.foldLeft(0)(_ + _)   // 0
emptyVector.foldLeft(1)(_ * _)   // 1
```

### 토론(Discussion)

`reduceLeft`가 어떻게 동작하는지를 보여주기 위해 그것과 함께 동작하는 메서드를 만들 수 있습니다. 다음 메서드는 마지막 예시처럼 최대 비교를 하지만, `reduceLeft`가 시퀀스를 순회하는 과정을 볼 수 있도록 추가 디버깅 코드를 가지고 있습니다. 함수는 다음과 같습니다.

```scala
def findMax(x: Int, y: Int): Int =
    val winner = x max y
    println(s"compared $x to $y, $winner was larger")
    winner
```

이제 시퀀스에서 다시 `reduceLeft`를 호출하되, 이번에는 `findMax` 메서드를 줍니다.

```scala
scala> xs.reduceLeft(findMax)
compared 12 to 6, 12 was larger
compared 12 to 15, 15 was larger
compared 15 to 2, 15 was larger
compared 15 to 20, 20 was larger
compared 20 to 9, 20 was larger
res0: Int = 20
```

이 출력은 `reduceLeft`가 시퀀스의 요소들을 어떻게 순회하는지, 그리고 각 단계에서 메서드를 어떻게 호출하는지 보여줍니다. 과정이 동작하는 방식은 다음과 같습니다.

- `reduceLeft`는 시퀀스의 처음 두 요소인 12와 6을 테스트하기 위해 `findMax`를 호출하는 것으로 시작합니다. 12가 6보다 크므로 `findMax`는 12를 반환합니다.
- `reduceLeft`는 그 결과(12)를 취해 `findMax(12, 15)`를 호출합니다. 12는 첫 번째 비교의 결과이고, 15는 컬렉션의 다음 요소입니다. 15가 더 크므로 새 결과가 됩니다.
- `reduceLeft`는 메서드로부터 받은 결과를 계속 취해 시퀀스의 다음 요소와 비교하며, 시퀀스의 모든 요소를 순회할 때까지 진행하여 결과 20으로 끝납니다.

#### 뺄셈 알고리즘(A subtraction algorithm)

이 레시피에서 "left" 메서드들(`reduceLeft`와 `foldLeft`)은 컬렉션의 처음부터 끝으로 이동하고, "right" 메서드들은 컬렉션의 끝에서 처음으로 이동한다고 언급했습니다. 이를 잘 보여주는 방법은 뺄셈 알고리즘인데, 이는 교환 법칙(commutative)이 성립하지 않으므로 리스트를 왼쪽에서 오른쪽으로 순회할 때와 오른쪽에서 왼쪽으로 순회할 때 다른 결과를 냅니다.

예를 들어, 이 리스트와 `subtract` 알고리즘이 주어졌을 때,

```scala
val xs = List(1, 2, 3)

def subtract(a: Int, b: Int): Int =
    println(s"a: $a, b: $b")
    val result = a - b
    println(s"result: $result\n")
    result
```

REPL은 `reduceLeft`와 `reduceRight`가 어떻게 동작하는지 보여줍니다.

```scala
scala> xs.reduceLeft(subtract)
a: 1, b: 2
result: -1

a: -1, b: 3
result: -4

val res0: Int = -4

scala> xs.reduceRight(subtract)
a: 2, b: 3
result: -1

a: 1, b: -1
result: 2

val res1: Int = 2
```

보시다시피, `reduceLeft`와 `reduceRight`는 `subtract` 알고리즘에 대해 다른 결과를 냅니다. 첫 번째는 리스트를 왼쪽에서 오른쪽으로 순회하고, 두 번째는 리스트를 오른쪽에서 왼쪽으로 순회하기 때문입니다.

> **Reduce 알고리즘 작성하기(Writing Reduce Algorithms)**
>
> reduce 메서드에 대해 미묘하지만 중요한 한 가지 참고 사항: 여러분이 제공하는 사용자 정의 함수는 컬렉션에 저장된 것과 같은 데이터 타입을 반환해야 합니다. 이는 reduce 알고리즘이 여러분 함수의 결과를 컬렉션의 다음 요소와 비교할 수 있도록 하기 위해 필요합니다.

#### foldLeft, reduceRight, foldRight

`foldLeft` 메서드는 `reduceLeft`와 똑같이 동작하지만, 첫 번째 요소에 사용될 시드 값을 설정하게 해줍니다. 다음 예시들은 합 알고리즘을 먼저 `reduceLeft`로, 그다음 `foldLeft`로 시연하여 차이를 보여줍니다.

```scala
val xs = List(1, 2, 3)

xs.reduceLeft(_ + _)       // 6
xs.foldLeft(20)(_ + _)     // 26
xs.foldLeft(100)(_ + _)    // 106
```

마지막 두 예시에서, `foldLeft`는 첫 번째 요소로 20과 100을 사용하며, 이는 보여준 대로 결과 합에 영향을 줍니다.

이런 구문을 본 적이 없다면, `foldLeft`는 두 개의 파라미터 리스트를 받습니다. 첫 번째 파라미터 리스트는 하나의 필드, 즉 시드 값을 받습니다. 두 번째 파라미터 리스트는 여러분이 실행하고 싶은 코드 블록(여러분의 알고리즘)입니다. 여러 파라미터 리스트의 사용에 대한 자세한 정보는 레시피 4.17 "직접 제어 구조 만들기"를 참고하세요.

`reduceRight`와 `foldRight` 메서드는 각각 `reduceLeft`와 `foldLeft` 메서드와 유사하게 동작하지만, 컬렉션의 끝에서 시작하여 오른쪽에서 왼쪽으로, 즉 컬렉션의 끝에서 처음으로 진행합니다.

#### fold 알고리즘과 항등값(fold algorithms and identity values)

fold 알고리즘은 리스트의 타입과 그 리스트에서 수행하는 연산에 대한 *항등값(identity value)* 으로 알려진 것과 관련이 있습니다. 예를 들어,

- `Seq[Int]`에서 연산할 때, 값 `0`은 덧셈이나 합 알고리즘에 아무것도 더하지 않습니다.
- 마찬가지로, 값 `1`은 곱 알고리즘에 아무것도 더하지 않습니다.
- `Seq[String]`에서 연산할 때, 빈 문자열—`""`—은 덧셈이나 곱 알고리즘에 아무것도 더하지 않습니다.

따라서 fold 연산이 다음과 같이 작성된 것을 볼 수 있습니다.

```scala
listOfInts.foldLeft(0)(_ + _)
listOfInts.foldLeft(1)(_ * _)

// 문자열 리스트를 연결합니다
listOfStrings.foldLeft("")(_ + _)
```

이 값들을 사용하면 빈 리스트와 함께 fold 메서드를 사용할 수 있습니다. 저자는 "Tail-Recursive Algorithms in Scala"에서 항등값에 대해 더 많이 다룹니다.

#### fold와 reduce 메서드가 동작하는 방식 요약(Summary of how the fold and reduce methods work)

fold와 reduce 메서드에 대해 알아야 할 핵심 사항은 다음과 같습니다.

- 전체 시퀀스를 순회합니다.
- max와 min 알고리즘에서 보여주듯이, 시퀀스의 인접한 요소들을 비교합니다.
- sum과 product 알고리즘에서 보여주듯이, 시퀀스를 순회하면서 누산기(accumulator)를 만듭니다.
- fold와 reduce 알고리즘은 일반적으로 (다른 시퀀스가 아니라) 마지막에 단일 스칼라 값을 산출하는 데 사용됩니다.
- 여러분의 사용자 정의 함수는 컬렉션에 담긴 타입의 두 파라미터를 받아야 하며, 그 같은 타입을 반환해야 합니다.

#### scanLeft와 scanRight

`scanLeft`와 `scanRight`라는 두 메서드는 `reduceLeft`와 `reduceRight`처럼 시퀀스를 순회하지만, 단일 값 대신 시퀀스를 반환합니다. 예를 들어, Scaladoc에 따르면 `scanLeft`는 "연산자를 왼쪽에서 오른쪽으로 적용한 누적(cumulative) 결과를 담은 컬렉션을 생성"합니다. 어떻게 동작하는지 이해하기 위해, 약간의 디버그 코드가 들어 있는 또 다른 함수를 만듭니다.

```scala
def product(x: Int, y: Int): Int =
    val result = x * y
    println(s"multiplied $x by $y to yield $result")
    result
```

다음은 그 함수와 시드 값을 사용했을 때 `scanLeft`가 어떻게 보이는지입니다.

```scala
scala> val xs = Vector(1, 2, 3)
xs: Vector[Int] = Vector(1, 2, 3)

scala> xs.scanLeft(10)(product)
multiplied 10 by 1 to yield 10
multiplied 10 by 2 to yield 20
multiplied 20 by 3 to yield 60
res0: Vector[Int] = Vector(10, 10, 20, 60)
```

보시다시피, `map` 메서드처럼 `scanLeft`는 단일 값이 아니라 새 시퀀스를 반환합니다. `scanRight` 메서드는 같은 방식으로 동작하지만 컬렉션을 오른쪽에서 왼쪽으로 순회합니다.

여러분의 시퀀스에 대한 Scaladoc에서 `reduce`, `reduceLeftOption`, `reduceRightOption`을 포함한 몇 가지 관련 메서드를 더 참고하세요.

---

## 13.11 Finding the Unique Elements in a Sequence

### 문제(Problem)

중복 요소를 담고 있는 시퀀스가 있고, 중복을 제거하여 고유한(unique) 요소들만 남기고자 합니다.

### 해결책(Solution)

시퀀스에서 `distinct` 메서드를 호출하거나 `toSet`을 호출하세요.

```scala
val x = Vector(1, 1, 2, 3, 3, 4)
val y = x.distinct   // Vector(1, 2, 3, 4)
val z = x.toSet      // Set(1, 2, 3, 4)
```

두 접근법 모두 중복 값이 제거된 새 시퀀스를 반환하지만, `distinct`는 시작했던 것과 같은 시퀀스 타입을 반환하는 반면, `toSet`은 `Set`을 반환합니다.

### 토론(Discussion)

이 접근법들을 여러분만의 클래스와 함께 사용하려면, `equals`와 `hashCode` 메서드를 구현해야 합니다. 예를 들어, 케이스 클래스(case class)는 이 메서드들을 자동으로 구현해 주므로, 다음 `Person` 클래스를 `distinct`와 함께 사용할 수 있습니다.

```scala
case class Person(firstName: String, lastName: String)

val dale1 = Person("Dale", "Cooper")
val dale2 = Person("Dale", "Cooper")
val ed = Person("Ed", "Hurley")
val list = List(dale1, dale2, ed)

// 올바른 해결책: 이 결과에는 Dale Cooper가 단 한 명만 나타납니다:
val uniques = list.distinct   // List(Person(Dale,Cooper), Person(Ed,Hurley))
val uniques = list.toSet      // Set(Person(Dale,Cooper), Person(Ed,Hurley))
```

케이스 클래스를 사용하고 싶지 않다면, `equals`와 `hashCode` 메서드를 구현하는 방법에 대한 논의는 레시피 5.9 "equals 메서드 정의하기(객체 동등성)"를 참고하세요.

---

## 13.12 Merging Sequential Collections

### 문제(Problem)

두 시퀀스를 하나의 시퀀스로 결합하고자 하는데, 모든 원본 요소를 유지하거나, 두 컬렉션에 공통인 요소를 찾거나, 두 시퀀스 사이의 차이(difference)를 찾고자 합니다.

### 해결책(Solution)

필요에 따라 이 문제에 대한 다양한 해결책이 있습니다.

- `++` 또는 `concat` 메서드를 사용하여 두 가변 또는 불변 시퀀스의 모든 요소를 새 시퀀스로 병합하세요.
- `++=` 메서드를 사용하여 시퀀스의 요소들을 기존 가변 시퀀스로 병합하세요.
- `:::`를 사용하여 두 `List`를 새 `List`로 병합하세요.
- `intersect` 메서드를 사용하여 두 시퀀스의 교집합(intersection)을 찾으세요.
- `diff` 메서드를 사용하여 두 시퀀스의 차이를 찾으세요.

다음 예시들에서 보여주듯이, `distinct`와 `toSet`을 사용하여 시퀀스를 고유한 요소들로 줄일 수도 있습니다.

#### ++ 또는 concat 메서드(The ++ or concat methods)

`++` 메서드는 `concat` 메서드의 별칭이므로, 둘 중 하나를 사용하여 두 가변 또는 불변 시퀀스를 병합하면서 결과를 새 변수에 할당하세요.

```scala
val a = List(1,2,3)
val b = Vector(4,5,6)

val c = a ++ b          // c: List[Int] = List(1, 2, 3, 4, 5, 6)
val c = a.concat(b)     // c: List[Int] = List(1, 2, 3, 4, 5, 6)

val d = b ++ a          // d: Vector[Int] = Vector(4, 5, 6, 1, 2, 3)
val d = b.concat(a)     // d: Vector[Int] = Vector(4, 5, 6, 1, 2, 3)
```

#### ++= 메서드(The ++= method)

`++=` 메서드를 사용하여 시퀀스를 `ArrayBuffer` 같은 기존 가변 시퀀스로 병합하세요.

```scala
import collection.mutable.ArrayBuffer

// 시퀀스들을 ArrayBuffer로 병합합니다
val a = ArrayBuffer(1,2,3)
a ++= Seq(4,5,6)   // a: ArrayBuffer(1, 2, 3, 4, 5, 6)
a ++= List(7,8)    // a: ArrayBuffer(1, 2, 3, 4, 5, 6, 7, 8)
```

#### :::로 두 List 병합하기(Use ::: to merge two Lists)

`List`를 다루고 있다면, `:::` 메서드를 사용하여 한 리스트의 요소들을 다른 리스트의 앞에 붙이면서 결과를 새 변수에 할당하세요.

```scala
val a = List(1,2,3,4)
val b = List(4,5,6,7)
val c = a ::: b   // c: List(1, 2, 3, 4, 4, 5, 6, 7)
```

#### intersect와 diff

`intersect` 메서드는 두 시퀀스의 교집합—두 시퀀스에 공통인 요소들—을 찾습니다.

```scala
val a = Vector(1,2,3,4,5)
val b = Vector(4,5,6,7,8)

val c = a.intersect(b)   // c: Vector(4, 5)
val c = b.intersect(a)   // c: Vector(4, 5)
```

`diff` 메서드는 두 시퀀스의 차이—첫 번째 시퀀스에는 있지만 두 번째 시퀀스에는 없는 요소들—를 제공합니다. 이 정의 때문에, 그 결과는 어느 시퀀스에서 호출되는지에 따라 달라집니다.

```scala
val a = List(1,2,3,4)
val b = List(3,4,5,6)
val c = a.diff(b)   // c: List(1, 2)
val c = b.diff(a)   // c: List(5, 6)

val a = List(1,2,3,4,1,2,3,4)
val b = List(3,4,5,6,3,4,5,6)
val c = a.diff(b)   // c: List(1, 2, 1, 2)
val c = b.diff(a)   // c: List(5, 6, 5, 6)
```

`diff` 메서드의 Scaladoc은 이것이 "이 리스트의 모든 요소를 담되, `that`에도 나타나는 요소들의 [일부 출현]은 제외한 새 리스트를 반환한다. 만약 요소 값 `x`가 `that`에 `n`번 나타나면, `x`의 처음 `n`번 출현은 결과의 일부가 되지 않지만, 그 이후의 출현은 포함된다"고 명시합니다. 이 동작 때문에, 고유한 요소들의 리스트를 만들려면 `distinct` 메서드를 사용해야 할 수도 있습니다.

```scala
val a = List(1,2,3,4,1,2,3,4)
val b = List(3,4,5,6,3,4,5,6)

val c = a.diff(b)            // c: List(1, 2, 1, 2)
val c = b.diff(a)            // c: List(5, 6, 5, 6)

val c = a.diff(b).distinct   // c: List(1, 2)
val c = b.diff(a).distinct   // c: List(5, 6)
```

### 토론(Discussion)

`diff`를 사용하여 두 집합의 *상대 여집합(relative complement)* 을 얻을 수 있습니다. 집합 A의 집합 B에 대한 상대 여집합은 B에는 있지만 A에는 없는 요소들의 집합입니다.

최근 프로젝트에서 저자는 한 시퀀스에는 있지만 *다른* 시퀀스에는 없는 고유한 요소들의 리스트를 찾아야 했습니다. 두 시퀀스에 먼저 `distinct`를 호출한 다음 `diff`를 사용하여 비교함으로써 이를 해결했습니다. 예를 들어, 다음 두 벡터가 주어졌을 때,

```scala
val a = Vector(1,2,3,11,4,12,4,4,5)
val b = Vector(6,7,4,4,5)
```

각 벡터의 상대 여집합을 찾는 한 가지 방법은 먼저 그 벡터에 `distinct`를 호출한 다음, `diff`로 다른 벡터와 비교하는 것입니다.

```scala
// b에는 없는 a의 요소들
val uniqToA = a.distinct.diff(b)   // Vector(1, 2, 3, 11, 12)

// a에는 없는 b의 요소들
val uniqToB = b.distinct.diff(a)   // Vector(6, 7)
```

원한다면, 그 결과들을 합하여 첫 번째 집합이나 두 번째 집합에는 있지만 둘 다에는 없는 요소들의 리스트를 얻을 수 있습니다.

```scala
val uniqs = uniqToA ++ uniqToB     // Vector(1, 2, 3, 11, 12, 6, 7)
```

이 문제를 살펴보는 김에, 같은 결과를 얻는 또 다른 방법이 있습니다.

```scala
// 두 리스트에 공통인 고유한 요소들의 리스트를 만듭니다
val i = a.intersect(b).toSet       // Set(4, 5)

// 원본 리스트들에서 그 요소들을 뺍니다
val uniqToA = a.toSet -- i         // HashSet(1, 2, 12, 3, 11)
val uniqToB = b.toSet -- i         // Set(6, 7)

val uniqs = uniqToA ++ uniqToB     // HashSet(1, 6, 2, 12, 7, 3, 11)
```

이 해결책에서 `toSet`을 사용하는 이유는 시퀀스에서 고유한 요소들의 리스트를 만드는 또 다른 방법이기 때문입니다.

---

## 13.13 Randomizing a Sequence

### 문제(Problem)

기존 시퀀스를 무작위로 섞거나(shuffle), 시퀀스에서 무작위 요소를 얻고자 합니다.

### 해결책(Solution)

시퀀스를 무작위로 섞으려면, `scala.util.Random`을 임포트한 다음 그 `shuffle` 메서드를 기존 시퀀스에 적용하면서 결과를 새 시퀀스에 할당하세요.

```scala
import scala.util.Random

// List
val xs = List(1,2,3,4,5)
val ys = Random.shuffle(xs)     // 'ys'는 섞입니다, 예: List(4,1,3,2,5)

// 다른 시퀀스에서도 동작합니다
val x = Random.shuffle(Vector(1,2,3,4,5))   // x: Vector(5,3,4,1,2)
val x = Random.shuffle(Array(1,2,3,4,5))    // x: mutable.ArraySeq(1,3,2,4,5)

import scala.collection.mutable.ArrayBuffer
val x = Random.shuffle(ArrayBuffer(1,2,3,4,5))   // x: ArrayBuffer(4,2,3,1,5)
```

함수형 메서드에서 늘 그렇듯, 이 해결책의 핵심은 `shuffle`이 주어진 리스트를 무작위로 섞지 않는다는 것을 아는 것입니다. 대신, 무작위로 섞인 새 리스트를 반환합니다.

### 토론(Discussion)

그 해결책은 전체 리스트를 무작위로 섞고 싶을 때 잘 동작합니다. 하지만 리스트에서 무작위 요소 하나만 얻고 싶다면, 다음과 같은 메서드가 훨씬 효율적입니다.

```scala
import scala.util.Random

// 'seq'가 비어 있으면 IllegalArgumentException을 던집니다
def getRandomElement[A](seq: Seq[A]): A =
    seq(Random.nextInt(seq.length))
```

전달하는 시퀀스가 적어도 하나의 요소를 포함하는 한, 그 함수는 원하는 대로 동작합니다. `nextInt`가 `0`(포함)과 `seq.length`(미포함) 사이의 값을 반환하기 때문에 동작합니다. 따라서 시퀀스가 100개의 요소를 포함하면, `nextInt`는 0과 99 사이의 값을 반환하며, 이는 가능한 인덱스들과 일치합니다.

이제 시퀀스가 있을 때마다, 이 함수를 사용하여 시퀀스에서 무작위 요소를 얻을 수 있습니다.

```scala
val randomNumber = getRandomElement(List(1,2,3))
val randomString = getRandomElement(List("a", "b", "c"))
```

---

## 13.14 Sorting a Collection

### 문제(Problem)

(a) 시퀀스 컬렉션을 정렬하거나, (b) 사용자 정의 클래스에서 `Ordered` 트레이트를 구현하여 `sorted` 메서드나 `<`, `<=`, `>`, `>=` 같은 연산자로 여러분 클래스의 인스턴스를 비교할 수 있게 하거나, (c) 정렬할 때 암묵적(implicit) 또는 명시적(explicit) `Ordering`을 사용하고자 합니다.

### 해결책(Solution)

`Array`를 정렬하는 방법에 대한 정보는 레시피 12.11 "배열 정렬하기"를 참고하세요. 그 외에는, *불변(immutable)* 시퀀스를 정렬하려면 `sorted`, `sortWith`, 또는 `sortBy` 메서드를 사용하고, *가변(mutable)* 시퀀스를 정렬하려면 `sortInPlace`, `sortInPlaceWith`, `sortInPlaceBy`를 사용하세요. 필요에 따라 `Ordered`나 `Ordering` 트레이트를 구현하세요.

#### 불변 시퀀스에서 sorted, sortWith, sortBy 사용하기(Using sorted, sortWith, and sortBy with immutable sequences)

시퀀스의 `sorted` 메서드는 `Double`, `Int`, `String` 타입, 그리고 암묵적 `scala.math.Ordering`을 가진 다른 모든 타입의 컬렉션을 정렬할 수 있습니다.

```scala
List(10, 5, 8, 1, 7).sorted          // List(1, 5, 7, 8, 10)
List("dog", "mouse", "cat").sorted   // List(cat, dog, mouse)
```

`sortWith` 메서드는 여러분만의 정렬 알고리즘을 제공하게 해줍니다. 다음 예시들은 `String` 또는 `Int` 시퀀스를 양방향으로 정렬하는 방법을 보여줍니다.

```scala
// 짧은 형식: 정렬 알고리즘이 '_' 참조를 사용합니다
Vector("dog", "mouse", "cat").sortWith(_ < _)   // Vector(cat, dog, mouse)
Vector("dog", "mouse", "cat").sortWith(_ > _)   // Vector(mouse, dog, cat)

// 긴 형식: 정렬 알고리즘이 tuple-2를 사용합니다
Vector(10, 5, 8, 1, 7).sortWith((a,b) => a < b)   // Vector(1, 5, 7, 8, 10)
Vector(10, 5, 8, 1, 7).sortWith((a,b) => a > b)   // Vector(10, 8, 7, 5, 1)
```

여러분의 정렬 함수는 두 파라미터를 받아야 하며, 필요한 만큼 단순하거나 복잡할 수 있습니다. 정렬 함수가 길어지면, 먼저 메서드로 선언하세요.

```scala
def sortByLength(s1: String, s2: String): Boolean =
    println(s"comparing $s1 & $s2")
    s1.length > s2.length
```

그런 다음 `sortWith` 메서드에 전달하여 사용하세요.

```scala
scala> val a = List("dog", "mouse", "cat").sortWith(sortByLength)
comparing mouse & dog
comparing cat & mouse
comparing mouse & cat
comparing cat & dog
comparing dog & cat
a: List[String] = List(mouse, dog, cat)
```

Scaladoc에 따르면, `sortBy` 메서드는 "암묵적으로 주어진 `Ordering`을 변환 함수로 변환한 결과에서 나오는 `Ordering`에 따라 시퀀스를 정렬"합니다. `List`에 대한 `sortBy` 시그니처는 다음과 같습니다.

```scala
def sortBy[B](f: (A) => B)(implicit ord: Ordering[B]): List[A]
```

다음은 `sortBy`를 사용하는 몇 가지 예시입니다.

```scala
val a = List("peach", "apple", "pear", "fig")
val b = a.sortBy(s => s.length)              // b: List(fig, pear, peach, apple)

// Scaladoc은 이런 예시를 보여주는데, "scala.Ordering이 암묵적으로
// Ordering[Tuple2[Int, Char]]를 제공하기 때문에" 동작합니다
val b = a.sortBy(s => (s.length, s.head))    // b: List(fig, pear, apple, peach)

// 가장 긴 문자열에서 가장 짧은 문자열로, 그다음 문자열로 정렬하는 방법
val a = List("fin", "fit", "fig", "pear", "peas", "peach", "peat")
val b = a.sortBy(s => (-s.length, s))
    // b: List(peach, pear, peas, peat, fig, fin, fit)
```

#### 가변 시퀀스에서 sortInPlace, sortInPlaceWith, sortInPlaceBy 사용하기(Using sortInPlace, sortInPlaceWith, and sortInPlaceBy with mutable sequences)

`ArrayBuffer` 같은 가변 시퀀스에서는 `sortInPlace`, `sortInPlaceWith`, `sortInPlaceBy` 메서드를 사용할 수 있습니다. 시퀀스의 데이터 타입이 암묵적 `Ordering`으로 또는 `Ordered`를 구현하여 정렬을 지원하면, `sortInPlace`가 직접적인 해결책입니다.

```scala
import scala.collection.mutable.ArrayBuffer

val a = ArrayBuffer(3,5,1)
a.sortInPlace    // a: ArrayBuffer(1, 3, 5)

val b = ArrayBuffer("Mercedes", "Hannah", "Emily")
b.sortInPlace    // b: ArrayBuffer(Emily, Hannah, Mercedes)
```

`sortInPlaceWith`는 `sortWith`와 유사합니다.

```scala
import scala.collection.mutable.ArrayBuffer
val a = ArrayBuffer(3,5,1)
a.sortInPlaceWith(_ < _)    // a: ArrayBuffer(1, 3, 5)
a.sortInPlaceWith(_ > _)    // a: ArrayBuffer(5, 3, 1)
```

`sortInPlaceBy`는 `sortBy`처럼 동작하며, 정렬할 함수를 지정하게 해줍니다.

```scala
import scala.collection.mutable.ArrayBuffer
val a = ArrayBuffer("kiwi", "apple", "fig")
a.sortInPlaceBy(_.length)   // a: ArrayBuffer(fig, kiwi, apple)
```

### 토론(Discussion)

다음 토론은 *불변* 시퀀스를 사용하여 `Ordering`과 `Ordered`를 사용하는 방법을 시연하지만, 이 토론은 *가변* 시퀀스에도 적용됩니다.

#### 암묵적 Ordering 갖기(Having an implicit Ordering)

시퀀스가 담고 있는 타입에 암묵적 `Ordering`이 없으면, `sorted`로 정렬할 수 없습니다. 예를 들어, 이 `Person` 클래스와 `List[Person]`이 주어졌을 때,

```scala
class Person(val name: String):
    override def toString = name

val dudes = List(
    Person("Bill"),
    Person("Al"),
    Person("Adam")
)
```

REPL에서 이 리스트를 정렬하려고 하면, `Person` 클래스에 암묵적 `Ordering`이 없다는 오류를 보게 됩니다.

```scala
scala> dudes.sorted
1 |dudes.sorted
  |            ^
  |        No implicit Ordering defined for B
  |        where:    B is a type variable with constraint >: Person
  |        I found:
  |            scala.math.Ordering.ordered[A](/* missing
  |            */summon[scala.math.Ordering.AsComparable[B]])
  |        But no implicit values were found that match type
  |        scala.math.Ordering.AsComparable[B].
```

작성된 그대로의 `Person` 클래스로는 `sorted`를 사용할 수 없으므로, 한 가지 해결책은 `sortWith`를 사용하여 `name` 필드로 `Person` 요소들을 정렬하는 익명 함수를 작성하는 것입니다.

```scala
dudes.sortWith(_.name < _.name)   // List(Adam, Al, Bill)
dudes.sortWith(_.name > _.name)   // List(Bill, Al, Adam)
```

#### sorted와 함께 명시적 Ordering 제공하기(Providing an explicit Ordering with sorted)

여러분의 클래스에 암묵적 `Ordering`이 없을 때, 한 가지 해결책은 명시적 `Ordering`을 제공하는 것입니다. 예를 들어, 기본적으로 이 `Person` 클래스는 정렬이 어떻게 동작해야 하는지에 대한 어떤 정보도 제공하지 않습니다.

```scala
class Person(val firstName: String, val lastName: String):
    override def toString = s"$firstName $lastName"
```

그 때문에, 방금 보여준 것처럼 `sorted`로 `Person` 인스턴스 리스트를 정렬하려고 하면 동작하지 않습니다.

```scala
val peeps = List(
    Person("Jessica", "Day"),
    Person("Nick", "Miller"),
    Person("Winston", "Bishop")
)

scala> peeps.sorted
1 |peeps.sorted
  |          ^
  |No implicit Ordering defined for B ... (긴 오류 메시지) ...
```

이 문제에 대한 한 가지 해결책은 `Person`과 함께 동작하는 명시적 `Ordering`을 만드는 것입니다.

```scala
object LastNameOrdering extends Ordering[Person]:
    def compare(a: Person, b: Person) = a.lastName compare b.lastName
```

이제 `LastNameOrdering`을 `sorted`와 함께 사용하면, 정렬이 원하는 대로 동작합니다.

```scala
scala> val sortedPeeps = peeps.sorted(LastNameOrdering)
val sortedPeeps: List[Person] = List(Winston Bishop, Jessica Day, Nick Miller)
```

이 해결책이 동작하는 이유는 (a) `sorted`가 암묵적 `Ordering` 파라미터를 받도록 정의되어 있고,

```scala
def sorted[B >: A](implicit ord: Ordering[B]): List[A]
                   --------------------------
```

(b) 저자가 그 파라미터를 명시적으로 제공하기 때문입니다.

```scala
val sortedPeeps = peeps.sorted(LastNameOrdering)
                              ----------------
```

또 다른 접근법은 `LastNameOrdering`을 `implicit` 키워드로 선언한 다음, `sorted`를 호출하는 것입니다.

```scala
implicit object LastNameOrdering extends Ordering[Person]:
    def compare(a: Person, b: Person) = a.lastName compare b.lastName

val sortedPeeps = peeps.sorted
    // sortedPeeps: List(Winston Bishop, Jessica Day, Nick Miller)
```

이 해결책에서, `LastNameOrdering`이 `implicit` 키워드로 정의되어 있기 때문에, 마법처럼 끌려와서 `sorted`가 찾고 있는 암묵적 `Ordering` 파라미터로 사용됩니다.

#### sorted를 사용하기 위해 Ordered 트레이트 믹스인하기(Mix in the Ordered trait to use sorted)

`Person` 클래스를 `sorted` 메서드와 함께 사용하고 싶다면, 또 다른 해결책은 `Person`에 `Ordered` 트레이트를 믹스인(mix in)한 다음, `Ordered` 트레이트의 추상(abstract) `compare` 메서드를 구현하는 것입니다. 이 기법은 다음 코드에 나와 있습니다.

```scala
class Person(var name: String) extends Ordered[Person]:

    override def toString = name

    // 같으면 0, this < that이면 음수, this > that이면 양수를 반환합니다
    def compare(that: Person): Int =
        // String에 대한 '=='의 정의에 의존합니다
        if this.name == that.name then
            0
        else if this.name > that.name then
            1
        else
            -1
```

이제 이 새 `Person` 클래스를 `sorted`와 함께 사용할 수 있습니다.

```scala
val dudes = List(
    Person("Bill"),
    Person("Al"),
    Person("Adam")
)

val x = dudes.sorted   // x: List(Adam, Al, Bill)
```

`Ordered`의 `compare` 메서드는 추상이므로, 정렬 능력을 제공하기 위해 여러분의 클래스에 구현합니다. 주석에 나와 있듯이, `compare`는 다음과 같이 동작합니다.

- 두 객체가 같으면 `0`을 반환합니다(동등성을 원하는 대로 정의하되, 일반적으로 여러분 클래스의 `equals` 메서드를 사용합니다).
- `this`가 `that`보다 작으면 음수 값을 반환합니다.
- `this`가 `that`보다 크면 양수 값을 반환합니다.

한 인스턴스가 다른 인스턴스보다 큰지 여부를 어떻게 결정하는지는 전적으로 여러분의 `compare` 알고리즘에 달려 있습니다.

이 `compare` 알고리즘은 두 `String` 값만 비교하므로, 다음과 같이 작성될 수도 있었다는 점에 유의하세요.

```scala
def compare (that: Person) = this.name.compare(that.name)
```

하지만 저자는 접근법을 명확히 하기 위해 첫 번째 예시에서 보여준 대로 작성했습니다.

여러분의 클래스에 `Ordered` 트레이트를 믹스인하는 것의 추가 이점은, 코드에서 객체 인스턴스를 직접 비교할 수 있게 해준다는 것입니다.

```scala
val bill = Person("Bill")
val al = Person("Al")
val adam = Person("Adam")

if adam > bill then println(adam) else println(bill)
```

이것이 동작하는 이유는 `Ordered` 트레이트가 `<=`, `<`, `>`, `>=` 메서드를 구현하고, 그 메서드들이 그 비교를 하기 위해 여러분의 `compare` 메서드를 호출하기 때문입니다.

---

## 13.15 Converting a Collection to a String with mkString and addString

### 문제(Problem)

컬렉션의 요소들을 `String`으로 변환하고자 하는데, 어쩌면 필드 구분자(separator), 접두사(prefix), 접미사(suffix)를 추가하면서 변환하고자 합니다.

### 해결책(Solution)

`mkString`이나 `addString` 메서드를 사용하여 컬렉션을 `String`으로 출력하세요.

#### mkString

간단한 컬렉션이 주어졌을 때,

```scala
val x = Vector("apple", "banana", "cherry")
```

`mkString`을 사용하여 요소들을 `String`으로 변환할 수 있습니다.

```scala
x.mkString   // "applebananacherry"
```

그것은 그다지 유용해 보이지 않으므로, 구분자를 추가합니다.

```scala
x.mkString(" ")    // "apple banana cherry"
x.mkString("|")    // "apple|banana|cherry"
x.mkString(", ")   // "apple, banana, cherry"
```

`mkString`은 오버로드(overload)되어 있으므로, 문자열을 만들 때 접두사와 접미사를 추가할 수 있습니다.

```scala
x.mkString("[", ", ", "]")   // "[apple, banana, cherry]"
```

`Map` 클래스에도 `mkString` 메서드가 있습니다.

```scala
val a = Map(1 -> "one", 2 -> "two")
a.mkString                  // "1 -> one2 -> two"
a.mkString("|")             // "1 -> one|2 -> two"
a.mkString("| ", " | ", " |")   // "| 1 -> one | 2 -> two |"
```

#### addString

Scala 2.13부터, `mkString`과 유사한 새로운 `addString` 메서드가 있는데, 이는 가변 `StringBuilder`를 시퀀스의 내용으로 채우게 해줍니다. `mkString`처럼, `addString`을 그 자체로, 구분자와 함께, 그리고 시작·끝·구분자 문자열과 함께 사용할 수 있습니다.

```scala
val x = Vector("a", "b", "c")

val sb = StringBuilder()
val y = x.addString(sb)            // y: StringBuilder = abc

val sb = StringBuilder()
val y = x.addString(sb , ", ")     // y: StringBuilder = "a, b, c"

val sb = StringBuilder()
val y = x.addString(
    sb,      // StringBuilder
    "[",     // start
    ", ",    // separator
    "]"      // end
)

// 마지막 표현식의 결과:
y: StringBuilder = [a, b, c]
y(0)   // Char = '['
y(1)   // Char = 'a'
```

이 기법은 `String` 대신 `StringBuilder`를 사용하므로, 큰 데이터셋에서 더 빨라야 합니다. (늘 그렇듯, 성능 관련 우려가 있다면 항상 테스트하세요.)

### 토론(Discussion)

이 기법들로 만들어지는 문자열은 시퀀스에 있는 요소들의 문자열 표현(string representation), 즉 그 요소들의 `toString` 메서드를 호출하여 얻게 될 것을 기반으로 합니다. 따라서 이 방식은 문자열과 정수 같은 타입에서는 잘 동작하지만, `toString` 메서드를 구현하지 않은 이 `Person` 클래스 같은 간단한 클래스가 있고 그 `Person`을 `List`에 담으면, 결과 문자열은 그다지 유용하지 않을 것입니다.

```scala
class Person(val name: String)
val xs = List(Person("Schmidt"))
xs.mkString   // Person@1b17b5cb (유용하지 않은 결과)
```

그 문제를 해결하려면, 여러분의 클래스에 `toString` 메서드를 제대로 구현하세요.

```scala
class Person(val name: String):
    override def toString = name

List(Person("Schmidt")).mkString   // "Schmidt"
```

#### 반복된 문자로 문자열 만들기(Creating a string from a repeated character)

약간 관련된 이야기로, 다음 기법으로 시퀀스를 `Char`나 `String`으로 채울 수 있습니다.

```scala
val list = List.fill(5)('-')          // List(-, -, -, -, -)
```

그런 다음 그 리스트를 `String`으로 변환할 수 있습니다.

```scala
val list = List.fill(5)('-').mkString   // "-----"
```

저자는 이 접근법을 사용하여 문장에 밑줄을 만들었는데, 예를 들어 문장의 길이가 10글자임을 알 때 10개의 밑줄 문자를 만드는 식이었습니다. 하지만 더 간단한 기법은 원하는 문자열을 곱하여 결과 문자열을 만드는 것입니다.

```scala
"─" * 10   // String = ──────────
```

저자는 이 기법들에 대해 "Scala Functions to Repeat a Character n Times (Blank Padding)"에서 더 많이 다룹니다.
