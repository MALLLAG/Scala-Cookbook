# Chapter 10. Functional Programming

## Recipes

- 10.1 Using Function Literals (Anonymous Functions)
- 10.2 Passing Functions Around as Variables
- 10.3 Defining a Method That Accepts a Simple Function Parameter
- 10.4 Declaring More Complex Higher-Order Functions
- 10.5 Using Partially Applied Functions
- 10.6 Creating a Method That Returns a Function
- 10.7 Creating Partial Functions
- 10.8 Implementing Functional Error Handling
- 10.9 Real-World Example: Passing Functions Around in an Algorithm
- 10.10 Real-World Example: Functional Domain Modeling

---

## 들어가며

Scala는 객체지향 프로그래밍(object-oriented programming) 스타일과 함수형 프로그래밍(functional programming) 스타일을 모두 지원합니다. 실제로, 2018년의 한 발표에서 Scala 언어의 창시자인 마틴 오더스키(Martin Odersky)는 Scala의 본질이 "타입이 있는 환경에서의 함수형 프로그래밍과 객체지향 프로그래밍의 융합(fusion)"이며, 여기서 "로직을 위한 함수(functions for the logic)"와 "모듈성을 위한 오브젝트(objects for the modularity)"를 사용한다고 말했습니다. 이 책의 많은 레시피들이 이 융합을 보여주지만, 이 장에서는 오직 함수형 프로그래밍에만 초점을 맞추며, 이를 이 장에서는 *Scala/FP* 라고 부르겠습니다.

함수형 프로그래밍(FP)은 큰 주제이며, 저자는 자신의 책 *Functional Programming, Simplified* 에서 이에 대해 700페이지 이상을 썼습니다. 이 장에서 그 모든 내용을 다룰 수는 없지만, 주요 개념 몇 가지를 다루도록 하겠습니다. 처음 몇 개의 레시피는 다음과 같은 방법을 보여줍니다.

- 함수 리터럴(function literals)을 작성하고 이해하기
- 함수 리터럴(익명 함수, anonymous functions라고도 함)을 메서드에 전달하기
- 함수를 변수처럼 받는 메서드를 작성하기

그 이후에는 몇 가지 매우 구체적인 함수형 프로그래밍 기법들을 보게 됩니다.

- 부분 적용 함수(partially applied functions)
- 함수를 반환하는 메서드 작성하기
- 부분 함수(partial functions)

이 장은 이러한 기법들을 보여주는 데 도움이 되는 두 개의 예제로 마무리됩니다.

FP에 익숙하지 않다면 처음에는 당혹스러울 수 있으므로, 그 목표와 동기를 이해하는 것이 분명히 도움이 됩니다. 따라서 다음 몇 페이지에 걸쳐 함수형 프로그래밍에 대해 저자가 제공할 수 있는 최고의 입문 설명을 제공하려고 합니다. *Functional Programming, Simplified* 는 130개의 짧은 장으로 구성되어 있으며, 이 입문 설명은 그 책의 처음 21개 장을 극도로 압축한 버전입니다.

### 함수형 프로그래밍이란 무엇인가? (What Is Functional Programming?)

FP에 대한 일관된 정의를 찾는 것은 놀라울 정도로 어렵지만, 저자는 그 책을 쓰는 과정에서 다음과 같은 정의에 도달했습니다.

> 함수형 프로그래밍은 오직 순수 함수(pure functions)와 불변 값(immutable values)만을 사용하여 소프트웨어 애플리케이션을 작성하는 방식이다.

이 장에서 보게 되겠지만, 순수 함수는 마치 대수 방정식(algebraic equations)을 쓰는 것과 같은 수학적 함수입니다.

또 다른 멋진 정의는 메리 로즈 쿡(Mary Rose Cook)에게서 나온 것으로, 그녀는 이렇게 말합니다.

> 함수형 코드는 한 가지로 특징지어진다. 바로 *부수 효과(side effects)의 부재* 다. (순수 함수는) 현재 함수 바깥의 데이터에 의존하지 않으며, 현재 함수 바깥에 존재하는 데이터를 변경하지 않는다. 다른 모든 "함수형"이라는 것들은 이 속성으로부터 파생될 수 있다.

저자는 *Functional Programming, Simplified* 에서 이러한 정의들을 매우 상세하게 확장하지만, 이 장의 목적에서는 이 정의들이 견고한 출발점을 제공해 줍니다.

### 순수 함수 (Pure Functions)

위의 정의들을 이해하려면 순수 함수가 무엇인지도 이해해야 합니다. 저자의 세계에서 *순수 함수(pure function)* 란 다음과 같은 함수입니다.

- 그 알고리즘과 출력이 *오직* (a) 함수의 입력 파라미터와 (b) 다른 순수 함수의 호출에만 의존하는 함수
- 자신에게 주어진 파라미터를 변경(mutate)하지 않는 함수
- 애플리케이션의 다른 어떤 곳(즉, 어떤 종류의 전역 상태(global state))도 변경하지 않는 함수
- 파일, 데이터베이스, 네트워크, 또는 사용자와의 상호작용처럼 바깥 세계(outside world)와 상호작용하지 않는 함수

이러한 기준 때문에 순수 함수에 대해 다음과 같은 진술도 할 수 있습니다.

- 그 내부 알고리즘은 날짜, 시간, 난수(랜덤한 *어떤 것이든*) 함수처럼 시간에 따라 응답이 달라지는 다른 함수들을 호출하지 않습니다.
- 같은 입력으로 몇 번을 호출하든, 순수 함수는 항상 같은 값을 반환합니다.

수학 함수는 순수 함수의 좋은 예시이며, min, max, sum, sin, 코사인(cosine), 탄젠트(tangent) 등과 같은 알고리즘을 포함합니다. filter, map, 그리고 기존 리스트로부터 정렬된 리스트를 반환하는 것과 같은 리스트 관련 함수들도 좋은 예시입니다. 같은 입력으로 몇 번을 호출하든 항상 같은 결과를 반환합니다.

반대로, *비순수(impure)* 함수의 예시는 다음과 같습니다.

- 모든 종류의 입출력(I/O) 함수 (사용자로부터의 입력, 사용자에게의 출력, 파일·데이터베이스·네트워크에 대한 읽기와 쓰기를 포함)
- 서로 다른 시점에 서로 다른 결과를 반환하는 함수 (날짜, 시간, 난수 함수)
- 애플리케이션의 다른 어딘가에 있는 가변 상태(예: 클래스 안의 가변 필드)를 수정하는 함수
- `Array`나 `ArrayBuffer`와 같은 가변 타입을 받아서 그 요소들을 수정하는 함수

순수 함수는 주어진 입력 집합으로 호출했을 때 *항상* 정확히 같은 답을 돌려받게 된다는 안정감을 줍니다. 예를 들면 다음과 같습니다.

```scala
"zeus".length    // will always be `4`
sum(2,2)         // will always be `4`
List(4,5,6).max  // will always be `6`
```

### 부수 효과 (Side Effects)

순수하게 함수형인 프로그램은 부수 효과가 없다고 말합니다. 그렇다면 *부수 효과(side effect)* 란 무엇일까요?

부수 효과를 가진 함수는 상태를 수정하거나, 변수를 변경하거나, 그리고/또는 바깥 세계와 상호작용합니다. 여기에는 다음이 포함됩니다.

- 파일, 데이터베이스, 또는 웹 서비스에 데이터를 쓰는(또는 그것들로부터 읽는) 행위
- 입력으로 주어진 변수의 상태를 변경하거나, 자료 구조의 데이터를 변경하거나, 오브젝트의 가변 필드 값을 수정하는 행위
- 예외(exception)를 던지거나, 오류가 발생했을 때 애플리케이션을 멈추는 행위
- 부수 효과를 가진 다른 함수들을 호출하는 행위

순수 함수는 테스트하기가 훨씬 쉽습니다. `+`와 같은 덧셈 함수를 작성한다고 상상해 봅시다. 두 숫자 1과 2가 주어지면 결과는 항상 3이 됩니다. 이와 같은 순수 함수는 (a) 불변 데이터가 들어오고 (b) 결과가 나오는 단순한 문제이며, 그 외에는 아무 일도 일어나지 않습니다. 이런 함수는 부수 효과가 없고 자신의 스코프 바깥 어딘가에 있는 가변 상태에 의존하지 않기 때문에, 테스트하기가 간단합니다.

### FP로 사고하기 (Thinking in FP)

순수 함수를 작성하는 것은 비교적 간단하며, 실제로 작성하는 즐거움이 있는 경향이 있습니다. 왜냐하면 순수 함수를 작성할 때는 애플리케이션의 전체 상태를 생각할 필요가 없기 때문입니다. 무엇이 들어오고 무엇이 나가는지만 생각하면 됩니다.

FP에서 더 어려운 부분은 (a) I/O를 다루는 것과 (b) 순수 함수들을 서로 이어 붙이는(gluing) 것과 관련이 있습니다. FP에서 성공하려면, 코드를 수학으로 바라보고자 하는 강한 욕구를 가져야 한다는 것을 저자는 알게 되었습니다. 각 함수를 대수 방정식으로 — 데이터가 들어가고, 데이터가 나오며, 부수 효과가 없고, 아무것도 잘못될 수 없는 것으로 — 바라보고자 하는 불타는 욕구를 가져야 합니다.

실제로 일어나는 일은, 하나의 순수 함수를 작성하고, 그다음 또 하나, 그다음 또 하나를 작성하는 것입니다. 함수들이 완성되면, 마치 수학자가 칠판에 일련의 방정식을 쓰는 것처럼, 순수 함수들 — 대수 방정식들 — 을 조합하여 애플리케이션을 만들어 냅니다. 저자는 이렇게 코드를 대수처럼 쓰고자 하는 욕구의 중요성을 아무리 강조해도 지나치지 않다고 말합니다. 코드를 대수처럼 쓰고 *싶어 해야* 합니다.

예를 들어, 수학에서는 다음과 같은 함수를 가질 수 있습니다.

```
f(x) = x^2 + 2x + 1
```

Scala에서는 그 함수가 다음과 같이 작성됩니다.

```scala
def f(x: Int): Int = x*x + 2*x + 1
```

이 함수에 대해 몇 가지를 주목하세요.

- 함수의 결과는 오직 `x`의 값과 함수의 알고리즘에만 의존합니다.
- 이 함수는 오직 `*`와 `+` 연산자에만 의존하는데, 이는 다른 순수 함수를 호출하는 것으로 생각할 수 있습니다.
- 이 함수는 `x`를 변경하지 않습니다.

게다가,

- 이 함수는 세상의 다른 어떤 것도 변경하지 않습니다.
  - 그 스코프는 입력 파라미터 `x`에 알고리즘을 적용하는 일만 다루며, 그 스코프 바깥의 어떤 변수도 변경하지 않습니다.
  - 그것은 세상의 다른 어떤 것으로부터 읽거나 쓰지 않습니다. 사용자 입력도, 파일도, 데이터베이스도, 네트워크도 없습니다.
- 같은 입력(예: 2)으로 이 함수를 무한히 여러 번 호출하더라도, 항상 같은 값(예: 9)을 반환합니다.

이 함수는 출력이 오직 입력에만 의존하는 순수 함수입니다. FP란 모든 함수를 이처럼 작성한 다음, 그것들을 서로 조합하여 완전한 애플리케이션을 만드는 것에 관한 것입니다.

### 참조 투명성과 치환 (Referential Transparency and Substitution)

FP에서 또 하나의 중요한 개념은 *참조 투명성(referential transparency, RT)* 으로, 이는 어떤 표현식을 프로그램의 동작을 바꾸지 않고 그것이 평가된 결과 값으로 대체할 수 있다는(그리고 그 반대도 가능하다는) 속성입니다. 이 역시 대수를 사용하여 살펴볼 수 있습니다. 예를 들어, 다음의 모든 기호가 불변 값을 나타낸다면,

```
a = b + c
d = e + f + b
x = a + d
```

*치환 규칙(substitution rules)* 을 수행하여 `x`의 값을 결정할 수 있습니다.

```
x = a + d
x = (b + c) + d           // substitute for 'a'
x = (b + c) + (e + f + b) // substitute for 'd'
x = b + c + e + f + b     // remove unneeded parentheses
x = 2b + c + e + f        // can't reduce the expression any more
```

함수형 프로그래머들이 프로그램이 "결과로 평가된다(evaluates to a result)"고 말하거나, 치환 규칙을 수행하여 프로그램을 실행한다고 말할 때, 의미하는 바가 바로 이것입니다. 여러분과 컴파일러 모두 이러한 치환을 수행할 수 있습니다. 반대로, `b`와 같은 값이 호출될 때마다 난수나 사용자 입력 값을 반환한다면, 이 방정식들을 축약할 수 없습니다.

저 예제는 대수 기호를 사용했지만, Scala 코드로도 똑같은 것을 할 수 있습니다. 예를 들어, Scala/FP에서는 다음과 같은 코드를 작성합니다.

```scala
val a = f(x)
val b = g(a)
val c = h(y)
val d = i(b, c)
```

`f`, `g`, `h`, `i`가 순수 함수라고 가정하고 — 그리고 모든 필드가 `val` 필드라고 가정하면 — 이처럼 단순한 표현식을 작성할 때, 여러분과 컴파일러 모두 코드를 자유롭게 재배열할 수 있습니다. 예를 들어, 첫 번째와 세 번째 표현식은 어떤 순서로든 일어날 수 있으며, 심지어 병렬로 실행될 수도 있습니다. 유일한 요구사항은 `i`가 호출되기 전에 처음 세 개의 표현식이 평가되어야 한다는 것뿐입니다.

또한, 값 `a`는 항상 `f(x)`와 *정확히* 동일하므로, `f(x)`는 항상 `a`로 대체될 수 있고, 그 반대도 가능합니다. `b`, `c`, `d`에 대해서도 마찬가지입니다.

예를 들어, 다음 방정식은

```scala
val b = g(a)
```

다음 방정식과 정확히 동일합니다.

```scala
val b = g(f(x))
```

모든 필드가 불변이고 함수들이 순수하기 때문에, 여러분과 컴파일러 모두 방정식들을 계속 이리저리 옮기고 치환을 수행할 수 있으며, 그 결과 다음의 모든 표현식이 동등해집니다.

```scala
val d = i(b, c)
val d = i(g(a), h(y))
val d = i(g(f(x)), h(y))
```

앞서 언급했듯이, 함수형 프로그래밍의 한 가지 큰 이점은 순수 함수가 부수 효과를 가진 함수보다 테스트하기 쉽다는 것이며, 이제 두 번째 이점도 볼 수 있습니다. 이처럼 참조 투명한 코드에서는 `g(a)`와 `h(y)`를 별도의 스레드(thread)(또는 더 임의적인 단위인 파이버(fiber))에서 실행하여 여러 코어를 활용할 수 있습니다. 모든 필드가 불변이고 함수가 순수하기 때문에, 이러한 대수적 치환을 안전하게 수행할 수 있습니다. 하지만 필드가 가변(`var` 필드)이거나 함수가 비순수하다면, 이 조각들을 안전하게 이리저리 옮길 수 없습니다.

Lisp — 원래 이름은 *LISP* 로, LISt Processor의 약자입니다 — 은 1958년에 처음 명세된 프로그래밍 언어로, 고수준 프로그래밍 언어의 많은 중요한 개념들을 개척했으며, 여기에는 고차 함수(higher-order functions)가 포함됩니다. 대수적/함수형 스타일로 코드를 작성하면, 자연스럽게 콘래드 바스키(Conrad Barski)의 책 *Land of Lisp* (No Starch Press)에 설명된 사고방식으로 이어집니다.

> 일부 숙련된 Lisp 사용자들은 누군가 함수가 "값을 반환한다(returns a value)"고 말하면 움찔합니다. 람다 대수(lambda calculus)에서는 시작 프로그램에 치환 규칙을 수행하여 함수의 결과를 결정하는 방식으로 프로그램을 "실행"합니다. 따라서, 함수 집합의 결과는 치환을 수행함으로써 그저 마법처럼 나타나는 것이며, 함수가 의식적으로 값을 반환하기로 "결정"하는 일은 결코 없습니다. 이 때문에 Lisp 순수주의자들은 함수가 "결과로 평가된다(evaluates to a result)"고 말하기를 선호합니다.

앞의 예제들이 이 인용문의 의미를 잘 보여줍니다.

### FP는 표현식 지향 프로그래밍의 상위 집합이다 (FP Is a Superset of Expression-Oriented Programming)

어떤 언어가 FP를 지원하려면, 먼저 *표현식 지향 프로그래밍(expression-oriented programming, EOP)* 을 지원해야 합니다. EOP에서는 모든 코드 줄이 문장(statement)이 아니라 표현식(expression)입니다. *표현식(expression)* 은 결과를 반환하고 부수 효과를 갖지 않는 코드 줄입니다. 반대로, *문장(statement)* 은 `println`을 호출하는 것과 같습니다. 즉, 결과를 반환하지 않고 오직 그 부수 효과를 위해서만 호출됩니다. (엄밀히 말하면, 문장도 결과를 반환하지만, 그것은 `Unit` 결과입니다.)

Scala를 그토록 훌륭한 FP 언어로 만드는 기능 중 하나는, 모든 코드를 표현식으로 작성할 수 있다는 점입니다. 여기에는 `if` 표현식이 포함되고,

```scala
val a = 1
val b = 2
val max = if a > b then a else b
```

`match` 표현식,

```scala
val evenOrOdd: String = i match
    case 1 | 3 | 5 | 7 | 9 => "odd"
    case 2 | 4 | 6 | 8 | 10 => "even"
```

`for` 표현식,

```scala
val xs = List(1, 2, 3, 4, 5)
val ys = for
    x <- xs
    if x > 2
yield
    x * 10
```

그리고 심지어 `try/catch` 블록도 값을 반환합니다.

```scala
def makeInt(s: String): Int =
    try
        s.toInt
    catch
        case _ : Throwable => 0
```

### Scala에서의 함수형 프로그래밍을 위한 나의 규칙들 (My Rules for Functional Programming in Scala)

올바른 FP 사고방식을 채택하는 데 도움이 되도록, 저자는 자신의 책 *Functional Programming, Simplified* 에서 Scala/FP 코드를 작성하기 위한 다음 규칙들을 만들었습니다.

- `null` 값을 절대로 사용하지 마세요. Scala에 `null` 키워드가 있다는 사실조차 잊으세요.
- 오직 순수 함수만 작성하세요.
- 모든 필드에 오직 불변 값(`val`)만 사용하세요.
- 모든 코드 줄은 대수 표현식이어야 합니다. `if`를 사용할 때마다 항상 `else`도 함께 사용해야 합니다.
- 순수 함수는 절대로 예외를 던져서는 안 됩니다. 대신, `Option`, `Try`, `Either`와 같은 값을 산출(yield)합니다.
- 데이터와 동작을 캡슐화하는 OOP "클래스"를 만들지 마세요. 대신, `case` 클래스를 사용하여 불변 자료 구조를 만든 다음, 그 자료 구조에 대해 작동하는 순수 함수를 작성하세요.

이 단순한 규칙들을 채택하면 다음과 같은 것을 알게 됩니다.

- 여러분의 뇌가 시스템과 싸우기 위한 지름길을 찾는 것을 멈추게 됩니다. (가끔씩 `var` 필드나 비순수 함수를 끼워 넣는 것은 학습 과정을 늦출 뿐입니다.)
- 여러분의 코드가 대수처럼 됩니다.
- 시간이 지나면서 Scala/FP의 사고 과정을 이해하게 되고, 하나의 개념이 논리적으로 또 다른 개념으로 이어진다는 것을 발견하게 됩니다.

마지막 요점의 예로, 오직 불변 필드만 사용하는 것이 자연스럽게 재귀 알고리즘(recursive algorithms)으로 이어진다는 것을 보게 됩니다. 그다음에는, 불변 Scala 컬렉션 클래스들에 내장된 모든 함수형 메서드들 덕분에, 재귀를 그렇게 자주 사용할 필요가 없다는 것을 보게 됩니다.

### 그렇습니다, FP 코드도 I/O를 사용합니다 (Yes, FP Code Uses I/O)

입출력(I/O)을 다루는 데에는 여러 가지 접근 방식이 있지만, 당연히 FP 코드도 I/O를 사용합니다. 여기에는 사용자 I/O를 다루는 것과, 파일·데이터베이스·네트워크에 대한 읽기와 쓰기가 포함됩니다. I/O 없이는 어떤 애플리케이션도 유용할 수 없으므로, Scala/FP(그리고 다른 모든 함수형 언어들)는 "함수형" 방식으로 I/O를 다루기 위한 기능들을 갖추고 있습니다.

예를 들어, 명령줄(command-line) I/O를 함수형 방식으로 다루는 Scala 코드는 다음과 같은 모습인 경향이 있습니다.

```scala
def mainLoop: IO[Unit] =
    for
        _   <- putStr(prompt)
        cmd <- getLine.map(Command.parse _)
        _   <- if cmd == Quit then
                   IO.unit
               else
                   processCommand(cmd) >> mainLoop
    yield
        ()

mainLoop.unsafeRunSync()
```

이 코드에서 `putStr`는 `println`을 함수형으로 대체한 것이고, `getLine`은 사용자 입력을 읽게 해주는 함수형 메서드입니다. 또한, `mainLoop`가 자기 자신을 재귀적으로 호출하는 것에 주목하세요. 이것이 바로 불변 변수로 루프를 만드는 방법입니다.

안타깝게도, 이러한 I/O 함수들의 배경에 있는 기법과 철학을 설명하려면 — 여러분의 배경 지식에 따라 잠재적으로 100페이지 이상이 — 꽤 시간이 걸리지만, 저자는 자신의 책 *Functional Programming, Simplified* 에서 이를 상세히 설명합니다.

> **함수형 케이크와 명령형 아이싱 (The Functional Cake and Imperative Icing)**
>
> 저자가 이 책의 1판에서 썼듯이, Cats, ZIO, Monix와 같은 FP 라이브러리를 사용하기 전까지, 함수형 프로그래밍을 처음 접하는 사람들에게 저자가 줄 수 있는 최선의 조언은 애플리케이션의 핵심부를 순수 함수로 작성하라는 것입니다. 이 순수 함수형 핵심부는 "케이크"로 생각할 수 있고, 바깥 세계와 상호작용하는 I/O 함수들은 그 핵심부 주위를 둘러싼 "아이싱(icing)"으로 생각할 수 있습니다. 애플리케이션에 따라, 80%의 케이크(순수 함수)와 20%의 아이싱(I/O 함수)이 될 수도 있고, 그 반대가 될 수도 있습니다. 일부 개발자들은 이 기법을 "함수형 핵심부와 명령형 셸(functional core and imperative shell)"을 갖는 것이라고 설명합니다.

---

## 10.1 함수 리터럴 사용하기 (익명 함수, Anonymous Functions)

### 문제

여러분은 익명 함수(anonymous function) — *함수 리터럴(function literal)* 이라고도 함 — 를 사용하여, 함수를 받는 메서드에 전달하거나 변수에 할당하고자 합니다.

### 해법

다음 `List`가 주어졌을 때,

```scala
scala> val x = List.range(1, 10)
val x: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)
```

리스트의 `filter` 메서드에 익명 함수를 전달하여 짝수만 포함하는 새로운 `List`를 만들 수 있습니다.

```scala
val evens = x.filter((i: Int) => i % 2 == 0)
//                   ---------------------- (이 부분이 익명 함수입니다)
```

REPL은 이 표현식이 짝수로 이루어진 새 `List`를 산출함을 보여줍니다.

```scala
scala> val evens = x.filter((i: Int) => i % 2 == 0)
evens: List[Int] = List(2, 4, 6, 8)
```

이 해법에서, 다음 코드는 함수 리터럴이며, 이처럼 메서드에 전달될 때는 *익명 함수(anonymous function)* — 일부 프로그래밍 언어에서 *람다(lambda)* 라고도 부르는 것 — 라고도 알려져 있습니다.

```scala
(i: Int) => i % 2 == 0
```

이 코드가 동작하긴 하지만, 함수 리터럴을 정의하는 가장 명시적인 형태를 보여주는 것입니다. 여러 가지 Scala의 단축 표기 덕분에, 이 표현식은 다음과 같이 단순화될 수 있습니다.

```scala
val evens = x.filter(_ % 2 == 0)
```

REPL은 이것이 같은 결과를 반환함을 보여줍니다.

```scala
scala> val evens = x.filter(_ % 2 == 0)
evens: List[Int] = List(2, 4, 6, 8)
```

### 논의

이 레시피의 첫 번째 예제는 다음 함수 리터럴을 사용합니다.

```scala
(i: Int) => i % 2 == 0
```

이 코드를 볼 때, `=>` 기호를 *변환기(transformer)* 로 생각하면 도움이 됩니다. 왜냐하면 이 표현식은 기호의 왼쪽에 있는 파라미터 목록(`i`라는 이름의 `Int`)을, 기호의 오른쪽에 있는 알고리즘(이 경우, `Boolean`을 결과로 내는 나머지 연산 테스트)을 사용하여 새로운 결과로 변환하기 때문입니다.

앞서 언급했듯이, 이 예제는 익명 함수를 정의하는 긴 형태를 보여주며, 이는 여러 방식으로 단순화될 수 있습니다. 첫 번째 예제는 가장 명시적인 형태를 보여줍니다.

```scala
val evens = x.filter((i: Int) => i % 2 == 0)
```

그리고 Scala는 리스트가 정수 값을 포함하고 있다는 것을 알 수 있으므로, `i`에 대한 타입 선언은 필요하지 않습니다.

```scala
val evens = x.filter((i) => i % 2 == 0)
```

익명 함수가 파라미터를 하나만 가질 때는, 괄호가 필요하지 않습니다.

```scala
val evens = x.filter(i => i % 2 == 0)
```

Scala는 함수 안에서 파라미터가 한 번만 나타날 때 변수 이름 대신 `_` 기호를 사용할 수 있게 해주므로, 이 코드는 더욱 단순화될 수 있습니다.

```scala
val evens = x.filter(_ % 2 == 0)
```

다른 상황에서는 익명 함수를 더 단순화할 수 있습니다. 예를 들어, 가장 명시적인 형태에서 시작하여, `foreach` 메서드와 함께 이 익명 함수를 사용해 리스트의 각 요소를 출력할 수 있습니다.

```scala
x.foreach((i: Int) => println(i))
```

앞서와 마찬가지로, `Int` 선언은 필요하지 않습니다.

```scala
x.foreach((i) => println(i))
```

인자가 하나뿐이므로, `i` 입력 파라미터를 둘러싼 괄호는 필요하지 않습니다.

```scala
x.foreach(i => println(i))
```

`i`가 함수 본문에서 한 번만 사용되므로, 표현식은 `_` 와일드카드를 사용해 더 단순화될 수 있습니다.

```scala
x.foreach(println(_))
```

마지막으로, 함수 리터럴이 단일 인자를 받는 하나의 문장으로 이루어져 있다면, 인자를 명시적으로 이름 짓고 지정할 필요가 없으므로, 문장을 다음과 같이 줄일 수 있습니다.

```scala
x.foreach(println)
```

#### 여러 파라미터를 갖는 익명 함수 (Anonymous functions that have multiple parameters)

`Map`은 여러 파라미터를 받는 익명 함수의 좋은 예를 제공합니다. 예를 들어, 다음 `Map`이 주어졌을 때,

```scala
val map = Map(1 -> 10, 2 -> 20, 3 -> 30)
```

이 예제는 불변 `Map` 인스턴스에 대해 `transform` 메서드와 함께 익명 함수를 사용하는 문법을 보여주는데, 여기서 각 요소의 키(key)와 값(value)이 익명 함수에 전달됩니다.

```scala
val newMap = map.transform((k,v) => k + v)
```

REPL은 이것이 어떻게 동작하는지 보여줍니다.

```scala
scala> val map = Map(1 -> 10, 2 -> 20, 3 -> 30)
val map: Map[Int, Int] = Map(1 -> 10, 2 -> 20, 3 -> 30)

scala> val newMap = map.transform((k,v) => k + v)
val newMap: Map[Int, Int] = Map(1 -> 11, 2 -> 22, 3 -> 33)
```

이것이 특별히 유용한 알고리즘은 아니지만, 중요한 부분은 익명 함수가 모든 `Map` 항목으로부터 받는 키/값 쌍을 다루는 문법을 보여준다는 것입니다.

```scala
(k,v) => k + v
```

> **Map 요소를 튜플로 다룰 수도 있습니다 (Can Also Treat Map Elements as a Tuple)**
>
> 필요에 따라, 또 다른 잠재적인 접근 방식은 각 `Map` 요소를 두 개의 요소로 이루어진 튜플(tuple)로 다루는 것입니다.
>
> ```scala
> scala> map.foreach(x => println(s"${x._1} --> ${x._2}"))
> 1 --> 10
> 2 --> 20
> 3 --> 30
> ```

---

## 10.2 함수를 변수로서 주고받기 (Passing Functions Around as Variables)

### 문제

객체지향 프로그래밍 언어에서 `String`, `Int`, 그리고 다른 변수들을 주고받는 것처럼, 여러분은 함수를 변수로 만들어 주고받고자 합니다.

### 해법

Recipe 10.1에서 보여준 문법을 사용하여 함수 리터럴을 정의한 다음, 그 리터럴을 변수에 할당하세요. 예를 들어, 다음 코드는 `Int` 파라미터를 받아 전달된 `Int`의 두 배에 해당하는 값을 반환하는 함수 리터럴을 정의합니다.

```scala
(i: Int) => i * 2
```

Recipe 10.1에서 언급했듯이, `=>` 기호를 *변환기(transformer)* 로 생각할 수 있습니다. 이 경우, 함수는 `Int` 값 `i`를 `i`의 두 배인 `Int` 값으로 변환합니다.

이제 그 함수 리터럴을 변수에 할당할 수 있습니다.

```scala
val double = (i: Int) => i * 2
```

이 코드를 REPL에 붙여넣으면, Scala가 `double`을 `Int`를 또 다른 `Int`로 변환하는 함수로 인식하는 것을 다음의 밑줄 친 코드에서 볼 수 있습니다.

```scala
scala> val double = (i: Int) => i * 2
val double: Int => Int = Lambda ...
//          ----------
```

이 시점에서 변수 `double`은, `String`, `Int`, 또는 다른 타입의 인스턴스와 마찬가지로 하나의 변수 인스턴스이지만, 이 경우에는 함수의 인스턴스이며 이를 *함수 값(function value)* 이라고 부릅니다. 이제 메서드를 호출하듯이 `double`을 호출할 수 있습니다.

```scala
double(2)   // 4
double(3)   // 6
```

이처럼 `double`을 그냥 호출하는 것을 넘어서, 그 시그니처와 일치하는 함수 파라미터를 받는 어떤 메서드에도 그것을 전달할 수 있습니다. 예를 들어, `List`와 같은 시퀀스 클래스의 `map` 메서드는 그 시그니처가 보여주듯이 타입 A를 타입 B로 변환하는 함수 파라미터를 받습니다.

```scala
def map[B](f: (A) => B): List[B]
//         ----------
```

이 때문에, `List[Int]`를 다룰 때 `map`에게 `Int`를 `Int`로 변환하는 `double` 함수를 줄 수 있습니다.

```scala
scala> val list = List.range(1, 5)
list: List[Int] = List(1, 2, 3, 4)

scala> list.map(double)
res0: List[Int] = List(2, 4, 6, 8)
```

이 예제에서 제네릭 타입 A는 `Int`이고 제네릭 타입 B도 마침 `Int`이지만, 더 복잡한 예제에서는 다른 타입일 수도 있습니다. 예를 들어, `String`을 `Int`로 변환하는 함수를 만들 수 있습니다.

```scala
val length = (s: String) => s.length
```

그런 다음 그 String-to-Int 함수를 문자열 리스트에 대한 `map` 메서드와 함께 사용할 수 있습니다.

```scala
scala> val lengths = List("Mercedes", "Hannah", "Emily").map(length)
val lengths: List[Int] = List(8, 6, 5)
```

함수형 프로그래밍의 세계에 오신 것을 환영합니다.

> **함수와 메서드는 일반적으로 서로 바꿔 쓸 수 있습니다 (Functions and Methods are Generally Interchangeable)**
>
> 첫 번째 예제는 `val` 변수로 생성된 `double` *함수* 를 보여주지만, `def`를 사용하여 *메서드* 를 정의하고 일반적으로 같은 방식으로 사용할 수도 있습니다. 자세한 내용은 논의(Discussion)를 참고하세요.

### 논의

함수 리터럴은 적어도 두 가지 다른 방식으로 선언할 수 있습니다. 다음의 나머지 연산 함수 값은 — `i`가 짝수이면 `true`를 반환합니다 — 함수 리터럴의 반환 타입이 `Boolean`임을 추론합니다.

```scala
val f = (i: Int) => { i % 2 == 0 }  // 괄호가 있으면 때때로 읽기 더 쉽습니다
val f = (i: Int) => i % 2 == 0      // 괄호가 없는 동일한 함수
```

이 경우, Scala 컴파일러는 함수의 본문을 보고 그것이 `Boolean` 값을 반환한다고 판단할 만큼 똑똑합니다.

하지만 함수 리터럴의 반환 타입을 명시적으로 선언하고 싶거나, 함수가 더 복잡해서 그렇게 하고 싶다면, 다음 예제들은 이 `isEven` 함수가 `Boolean`을 반환한다는 것을 명시적으로 선언하기 위해 사용할 수 있는 여러 형태를 보여줍니다.

```scala
val isEven: (Int) => Boolean = i => { i % 2 == 0 }
val isEven: Int => Boolean = i => { i % 2 == 0 }
val isEven: Int => Boolean = i => i % 2 == 0
val isEven: Int => Boolean = _ % 2 == 0
```

두 번째 예제는 이 접근 방식들의 차이를 보여주는 데 도움이 됩니다. 이 함수들은 모두 두 개의 `Int` 파라미터를 받아 두 입력 값의 합인 하나의 `Int` 값을 반환합니다.

```scala
// 암시적(implicit) 접근 방식
val add = (x: Int, y: Int) => { x + y }
val add = (x: Int, y: Int) => x + y

// 명시적(explicit) 접근 방식
val add: (Int, Int) => Int = (x,y) => { x + y }
val add: (Int, Int) => Int = (x,y) => x + y
```

이 예제들 중 일부에서 메서드 본문을 둘러싼 중괄호(curly braces)를 보여주는 이유는, 특히 함수 문법을 처음 접하는 경우라면 코드가 더 읽기 쉽다고 느끼기 때문입니다. 이왕 이 주제를 다루는 김에, 괄호가 없는 여러 줄(multiline) 함수의 예를 소개합니다.

```scala
val addThenDouble: (Int, Int) => Int = (x,y) =>
    val a = x + y
    2 * a
```

#### def 메서드를 val 함수처럼 사용하기 (Using a def method like a val function)

Scala는 매우 유연하며, *에타 확장(Eta Expansion)* 이라고 알려진 기술 덕분에, 함수를 정의하여 `val`에 할당할 수 있는 것처럼 `def`를 사용하여 메서드를 정의한 다음 그것을 변수로 주고받을 수도 있습니다. 다시 나머지 연산 알고리즘을 사용하여, `def` 메서드를 다음 방식들 중 어느 것으로든 정의할 수 있습니다.

```scala
def isEvenMethod(i: Int) = i % 2 == 0
def isEvenMethod(i: Int) = { i % 2 == 0 }
def isEvenMethod(i: Int): Boolean = i % 2 == 0
def isEvenMethod(i: Int): Boolean = { i % 2 == 0 }
```

메서드가 함수 파라미터를 기대하는 다른 메서드에 전달될 때, 에타 확장은 그 메서드를 투명하게(transparently) 함수로 변환합니다. 따라서, 이 `isEven` 메서드들 중 어느 것이든 `Int`를 받아 `Boolean`을 반환하도록 정의된 함수 파라미터를 받는 다른 메서드에 전달될 수 있습니다. 예를 들어, 시퀀스 클래스의 `filter` 메서드는 제네릭 타입 A를 `Boolean`으로 변환하는 함수를 받도록 정의되어 있습니다.

```scala
def filter(p: (A) => Boolean): List[A]
//            ----------------
```

다음 예제에서는 `List[Int]`를 가지고 있으므로, `filter`에게 `Int`를 `Boolean`으로 변환하는 `isEvenMethod`를 전달할 수 있습니다.

```scala
val list = List.range(1, 10)
list.filter(isEvenMethod)
```

REPL에서는 다음과 같은 모습입니다.

```scala
scala> def isEvenMethod(i: Int) = i % 2 == 0
def isEvenMethod(i: Int): Boolean

scala> val list = List.range(1, 10)
val list: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)

scala> list.filter(isEvenMethod)
val res0: List[Int] = List(2, 4, 6, 8)
```

앞서 언급했듯이, 이것은 함수 리터럴을 정의하여 변수에 할당하는 과정과 유사합니다. 예를 들어, 여기서 `isEvenFunction`이 `isEvenMethod`와 똑같이 동작하는 것을 볼 수 있습니다.

```scala
val isEvenFunction = (i: Int) => i % 2 == 0
list.filter(isEvenFunction)   // List(2, 4, 6, 8)
```

프로그래밍의 관점에서 보면, 명백한 차이는 `isEvenMethod`는 *메서드* 인 반면 `isEvenFunction`은 변수에 할당된 *함수* 라는 것입니다. 하지만 프로그래머로서, 두 접근 방식이 모두 동작한다는 것은 멋진 일입니다.

#### 기존 함수/메서드를 함수 변수에 할당하기 (Assigning an existing function/method to a function variable)

이 탐구를 계속해서, 기존 메서드나 함수를 함수 변수에 할당할 수도 있습니다. 예를 들어, `scala.math.cos` 메서드로부터 `c`라는 이름의 새 함수를 다음과 같이 만들 수 있습니다.

```scala
scala> val c = scala.math.cos
val c: Double => Double = Lambda ...
```

그 결과로 생긴 함수 값 `c`는 *부분 적용 함수(partially applied function)* 라고 불립니다. `cos` 메서드는 하나의 인자를 필요로 하는데 아직 그것을 제공하지 않았기 때문에 부분적으로 적용된 것입니다.

이제 `c`를 함수 값으로 가지게 되었으니, `cos`를 사용하듯이 그것을 사용할 수 있습니다.

```scala
scala> c(0)
res0: Double = 1.0
```

> **Scala 3에서 개선된 에타 확장 (Improved Eta Expansion in Scala 3)**
>
> Scala 2에서는 이 예제에 밑줄(underscore)이 필요했습니다.
>
> ```scala
> val c = scala.math.cos _
> ```
>
> 하지만 Scala 3의 개선된 에타 확장 기술 덕분에, 이것은 더 이상 필요하지 않습니다.

다음 예제는 이 기법을 사용하여 `scala.math`의 `pow` 메서드로부터 `square` 함수를 만드는 방법을 보여줍니다. `pow`는 두 개의 파라미터를 받으며, 두 번째 파라미터는 첫 번째 파라미터에 적용되어야 할 거듭제곱(power)이라는 점에 주목하세요.

```scala
scala> val square = scala.math.pow(_, 2)
val square: Double => Double = Lambda ...
```

다시 말하지만, `square`는 부분 적용 함수입니다. 거듭제곱 파라미터는 제공했지만 값(value) 파라미터는 제공하지 않았으므로, 이제 `square`는 제곱될 값이라는 하나의 추가 파라미터를 받기를 기다리고 있습니다.

```scala
scala> square(3)
val res0: Double = 9.0

scala> square(4)
val res1: Double = 16.0
```

이 예제는 이 기법을 사용하는 전형적인 방식을 보여줍니다. 즉, 더 일반적인 메서드(`pow`)로부터 더 구체적인 함수(`square`)를 만듭니다. 더 자세한 내용은 Recipe 10.5를 참고하세요.

> **square의 REPL 출력 읽기 (Reading square's REPL Output)**
>
> `square`를 만들 때, 그것이 여전히 파라미터를 필요로 한다는 것을 알 수 있는데, REPL 출력이 그 타입 시그니처를 보여주기 때문입니다.
>
> ```scala
> val square: Double => Double ...
> //          ----------------
> ```
>
> 이는 그것이 `Double` 파라미터를 받아 `Double` 값을 반환하는 함수라는 의미입니다.

요약하면, 함수 변수에 대한 몇 가지 참고 사항은 다음과 같습니다.

- `=>` 기호를 변환기로 생각하세요. 그것은 오른쪽의 알고리즘을 사용하여 왼쪽의 입력 데이터를 어떤 새로운 출력 데이터로 변환합니다.
- 메서드를 정의하려면 `def`를, 함수를 만들려면 `val`을 사용하세요.
- 함수를 변수에 할당할 때, *함수 리터럴* 은 표현식의 오른쪽에 있는 코드입니다.

#### 함수를 Map에 저장하기 (Storing functions in a Map)

함수가 변수라고 말할 때, 저자가 의미하는 바는 함수가 모든 면에서 `String`이나 `Int` 변수처럼 사용될 수 있다는 것입니다. 함수는 이 장의 여러 레시피에서 보여주듯이 함수 파라미터로 사용될 수 있습니다. 그리고 이 예제가 보여주듯이, 함수(또는 메서드)를 `Map`에 저장할 수도 있습니다.

```scala
def add(i: Int, j: Int) = i + j
def multiply(i: Int, j: Int) = i * j

// 함수들을 Map에 저장합니다
val functions = Map(
    "add" -> add,
    "multiply" -> multiply
)

// Map에서 함수를 꺼내어 사용합니다
val f = functions("add")
f(2, 2)   // 4
```

함수와 메서드는 단어의 모든 의미에서 진정으로 변수입니다.

---

## 10.3 단순한 함수 파라미터를 받는 메서드 정의하기 (Defining a Method That Accepts a Simple Function Parameter)

### 문제

여러분은 단순한 함수를 메서드 파라미터로 받는 메서드를 만들고자 합니다.

### 해법

이 해법은 3단계 과정을 따릅니다.

1. 파라미터로 받고자 하는 함수의 시그니처를 포함하여, 메서드를 정의합니다.
2. 이 시그니처와 일치하는 하나 이상의 함수를 정의합니다.
3. 나중에 어느 시점에, 그 함수(들)를 여러분의 메서드에 파라미터로 전달합니다.

이를 시연하기 위해, 함수를 파라미터로 받는 `executeFunction`이라는 메서드를 정의합니다. 이 메서드는 `callback`이라는 이름의 파라미터 하나를 받는데, 이는 함수입니다. 그 함수는 입력 파라미터를 갖지 않아야 하며 아무것도 반환하지 않아야 합니다.

```scala
def executeFunction(callback:() => Unit) =
    callback()
```

이 코드에 대한 두 가지 참고 사항입니다.

- `callback:()` 문법은 입력 파라미터가 없는 함수를 지정합니다. 만약 함수에 파라미터가 있다면, 그 타입들은 괄호 안에 나열될 것입니다.
- 코드의 `=> Unit` 부분은 이 `callback` 함수가 아무것도 반환하지 않음을 나타냅니다.

이 문법은 잠시 후에 논의하겠습니다.

다음으로, 이 시그니처와 일치하는 함수 또는 메서드를 정의합니다. 예를 들어, `sayHelloF`와 `sayHelloM`은 모두 입력 파라미터를 받지 않으며 아무것도 반환하지 않습니다.

```scala
val sayHelloF = () => println("Hello")    // 함수(function)
def sayHelloM(): Unit = println("Hello")  // 메서드(method)
```

레시피의 마지막 단계에서, `sayHelloF`와 `sayHelloM`이 모두 `callback`의 타입 시그니처와 일치하므로, 그것들을 `executeFunction` 메서드에 전달할 수 있습니다.

```scala
executeFunction(sayHelloF)
executeFunction(sayHelloM)
```

REPL은 이것이 어떻게 동작하는지 보여줍니다.

```scala
scala> val sayHelloF = () => println("Hello")
val sayHelloF: () => Unit = Lambda ...

scala> def sayHelloM(): Unit = println("Hello")
def sayHelloM(): Unit

scala> executeFunction(sayHelloF)
Hello

scala> executeFunction(sayHelloM)
Hello
```

### 논의

이 레시피에서 저자는 단순한 함수를 받는 메서드를 만들어서, 파라미터를 받지 않고 아무것도 반환하지 않는(`Unit`) 함수로 이 과정이 어떻게 동작하는지 볼 수 있게 했습니다. 다음 레시피에서는 더 복잡한 함수 시그니처의 예제들을 보게 됩니다.

이 예제에서 사용된 `callback`이라는 이름에 특별한 것은 없습니다. 저자가 함수를 메서드에 전달하는 방법을 처음 배웠을 때는 `callback`이라는 이름을 선호했는데, 의미를 명확하게 해주었기 때문입니다. 하지만 그것은 그저 메서드 파라미터의 이름일 뿐입니다. 요즘은 `Int` 파라미터를 `i`로 이름 짓는 것처럼, 함수 파라미터를 `f`로 이름 짓습니다.

```scala
def runAFunction(f:() => Unit) = f()
```

특별한 부분은, 메서드에 전달하는 어떤 함수든 여러분이 정의한 함수 파라미터 시그니처와 일치해야 한다는 점입니다. 이 경우, 전달되는 함수는 인자를 받지 않아야 하고 아무것도 반환하지 않아야 한다고 선언합니다.

```scala
f:() => Unit
```

더 일반적으로, 함수를 메서드 파라미터로 정의하는 문법은 다음과 같습니다.

```scala
parameterName: (parameterType(s)) => returnType
```

`runAFunction` 예제에서, `parameterName`은 `f`이고, 함수가 어떤 파라미터도 받지 않으므로 `parameterType` 영역은 비어 있으며, 함수가 아무것도 반환하지 않으므로 반환 타입은 `Unit`입니다.

```scala
runAFunction(f:() => Unit)
```

또 다른 예로, `String`을 받아 `Int`를 반환하는 함수 파라미터를 정의하려면, 다음 두 시그니처 중 하나를 사용하세요.

```scala
executeFunction(f: String => Int)
executeFunction(f: (String) => Int)
```

더 복잡한 함수 시그니처 예제들은 다음 레시피를 참고하세요.

> **Unit에 대한 참고 (A Note About Unit)**
>
> 이 예제들에서 보여준 Scala의 `Unit`은 Java의 `Void`나 Python의 `None`과 유사합니다. 함수가 아무것도 반환하지 않음을 나타내기 위해 이와 같은 상황에서 사용됩니다.

---

## 10.4 더 복잡한 고차 함수 선언하기 (Declaring More Complex Higher-Order Functions)

### 문제

여러분은 함수를 파라미터로 받는 메서드를 정의하고자 하며, 그 함수는 하나 이상의 입력 파라미터를 가질 수 있고 `Unit`이 아닌 값을 반환할 수도 있습니다. 또한 여러분의 메서드는 추가적인 파라미터를 가질 수도 있습니다.

### 해법

Recipe 10.3에서 설명한 접근 방식을 따라, 함수를 파라미터로 받는 메서드를 정의하세요. 받기를 기대하는 함수 시그니처를 지정한 다음, 메서드 본문 안에서 그 함수를 실행하세요.

다음 예제는 `exec`라는 메서드를 정의하는데, 이 메서드는 함수를 입력 파라미터로 받습니다. 그 함수는 하나의 `Int`를 입력 파라미터로 받아야 하고 아무것도 반환하지 않아야 합니다.

```scala
def exec(callback: Int => Unit) =
    // 우리가 받은 함수를 호출하면서, 그것에게 Int 파라미터를 줍니다
    callback(1)
```

다음으로, 기대하는 시그니처와 일치하는 함수를 정의합니다. 이 함수와 이 메서드는 모두 `Int` 인자를 받고 아무것도 반환하지 않으므로 `callback`의 시그니처와 일치합니다.

```scala
val plusOne = (i: Int) => println(i+1)
def plusOne(i: Int) = println(i+1)
```

이제 `plusOne`의 두 버전 중 어느 것이든 `exec` 함수에 전달할 수 있습니다.

```scala
exec(plusOne)
```

`plusOne`이 메서드 안에서 호출되므로, 이 코드는 숫자 2를 출력합니다.

이 시그니처와 일치하는 어떤 함수든 `exec` 메서드에 전달될 수 있습니다. 예를 들어, 마찬가지로 `Int`를 받고 아무것도 반환하지 않는 `plusTen`이라는 새 함수(또는 메서드)를 정의합니다.

```scala
val plusTen = (i: Int) => println(i+10)
def plusTen(i: Int) = println(i+10)
```

이제 그것을 `exec` 함수에 전달할 수 있고, 마찬가지로 동작하는 것을 볼 수 있습니다.

```scala
exec(plusTen)   // 11을 출력합니다
```

이 예제들은 단순하지만, 이 기법의 위력을 볼 수 있습니다. 즉, 서로 바꿔 쓸 수 있는 알고리즘들을 손쉽게 교체해 넣을 수 있습니다. 전달되는 함수나 메서드의 시그니처가 여러분의 메서드가 기대하는 것과 일치하기만 하면, 여러분의 알고리즘은 무엇이든 할 수 있습니다. 이것은 OOP의 전략(Strategy) 디자인 패턴에서 알고리즘을 교체하는 것에 비견할 만합니다.

### 논의

`given`(Recipe 23.8 참고)과 같은 다른 기능들을 포함하지 않는다면, 함수를 메서드 파라미터로 기술하는 일반적인 문법은 다음과 같습니다.

```scala
parameterName: (parameterType(s)) => returnType
```

따라서, `String`을 받아 `Int`를 반환하는 함수를 정의하려면, 다음 두 시그니처 중 하나를 사용하세요.

```scala
exec(f: (String) => Int)
exec(f: String => Int)
```

두 번째 예제가 동작하는 이유는, 함수가 입력 파라미터를 하나만 갖도록 선언될 때 괄호가 선택 사항이기 때문입니다. 더 복잡한 것의 예로, 다음은 두 개의 `Int` 값을 받아 `Boolean`을 반환하는 함수의 시그니처입니다.

```scala
exec(f: (Int, Int) => Boolean)
```

마지막으로, 이 `exec` 메서드는 `String`, `Int`, `Double` 파라미터를 받아 `Seq[String]`을 반환하는 함수를 기대합니다.

```scala
exec(f: (String, Int, Double) => Seq[String])
```

#### 다른 파라미터들과 함께 함수를 전달하기 (Passing in a function with other parameters)

함수 파라미터는 다른 어떤 메서드 파라미터와도 똑같으므로, 메서드는 함수에 더해 다른 파라미터들도 받을 수 있으며, 실제로 이런 경우가 흔합니다.

다음 코드가 이를 시연합니다. 먼저, 함수와 `Int`라는 두 개의 파라미터를 받는 `executeXTimes`라는 메서드를 정의합니다.

```scala
def executeXTimes(callback:() => Unit, numTimes: Int): Unit =
    for i <- 1 to numTimes do callback()
```

그 이름이 암시하듯이, `executeXTimes`는 `callback` 함수를 `numTimes`번 호출하므로, 3을 전달하면 `callback`이 세 번 호출됩니다.

다음으로, `callback`의 시그니처와 일치하는 함수 또는 메서드를 정의합니다.

```scala
val sayHello = () => println("Hello")
def sayHello() = println("Hello")
```

이제 `sayHello`의 두 버전 중 어느 것과 `Int`를 `executeXTimes`에 전달합니다.

```scala
scala> executeXTimes(sayHello, 3)
Hello
Hello
Hello
```

이는 이 기법을 사용하여 변수들을 메서드에 전달할 수 있고, 그 변수들이 메서드 본문 안의 함수에 의해 사용될 수 있음을 보여줍니다.

또 다른 예로, 함수와 두 개의 `Int` 파라미터를 받는 `executeAndPrint`라는 메서드를 만듭니다.

```scala
def executeAndPrint(f:(Int, Int) => Int, x: Int, y: Int): Unit =
    val result = f(x, y)
    println(result)
```

이 경우, 함수 `f`는 두 개의 `Int` 파라미터를 받아 `Int`를 반환합니다. 이 `executeAndPrint` 메서드는 이전 예제보다 더 흥미로운데, 왜냐하면 자신이 받은 두 개의 `Int` 파라미터를, 다음 코드 줄에서 자신이 받은 함수 파라미터에게 전달하기 때문입니다.

```scala
val result = f(x, y)
```

이를 시연하기 위해, `executeAndPrint`가 기대하는 함수의 시그니처와 일치하는 두 함수, `sum` 함수와 `multiply` 함수를 만듭니다.

```scala
val sum = (x: Int, y: Int) => x + y
def sum(x: Int, y: Int) = x + y

val multiply = (x: Int, y: Int) => x * y
def multiply(x: Int, y: Int) = x * y
```

이제 다음과 같이 서로 다른 함수들을 두 개의 `Int` 파라미터와 함께 전달하면서 `executeAndPrint`를 호출할 수 있습니다.

```scala
executeAndPrint(sum, 2, 9)       // 11을 출력합니다
executeAndPrint(multiply, 3, 9)  // 27을 출력합니다
```

이것이 멋진 이유는, `executeAndPrint` 메서드가 실제로 어떤 알고리즘이 실행되는지 모르기 때문입니다. 그것이 아는 것이라고는, 파라미터 `x`와 `y`를 자신이 받은 함수에 전달하고 그 함수로부터 나온 결과를 출력한다는 것뿐입니다. 이것은 OOP에서 인터페이스를 정의한 다음 그 인터페이스의 구체적인 구현을 제공하는 것과 약간 비슷합니다.

이 3단계 과정의 또 다른 예를 하나 더 소개합니다.

```scala
// 1 - 메서드를 정의합니다
def exec(callback: (Any, Any) => Unit, x: Any, y: Any): Unit =
    callback(x, y)

// 2 - 전달할 함수를 정의합니다
def printTwoThings(a: Any, b: Any): Unit =
    println(a)
    println(b)

// 3 - 함수와 일부 파라미터를 메서드에 전달합니다
case class Person(name: String)
exec(printTwoThings, "Hello", Person("Dave"))
```

마지막 코드 줄의 출력은 REPL에서 다음과 같은 모습입니다.

```scala
scala> exec(printTwoThings, "Hello", Person("Dave"))
Hello
Person(Dave)
```

---

## 10.5 부분 적용 함수 사용하기 (Using Partially Applied Functions)

### 문제

여러분은 (a) 공통 변수들을 함수에 전달하여 (b) 그 값들이 미리 적재된(preloaded) 새 함수를 만들고, 그다음 (c) 그 새 함수에게 필요한 고유한 변수들만 전달하여 사용함으로써, 변수들을 함수에 반복적으로 전달하는 일을 없애고자 합니다.

### 해법

부분 적용 함수의 고전적인 예제는 `sum` 함수에서 시작합니다.

```scala
val sum = (a: Int, b: Int, c: Int) => a + b + c
```

`sum`에 특별한 것은 없으며, 그저 세 개의 `Int` 값을 더하는 함수일 뿐입니다. 하지만 `sum`을 호출할 때 파라미터 중 두 개를 제공하면서 세 번째 파라미터를 제공하지 않으면 흥미로운 일이 벌어집니다.

```scala
val addTo3 = sum(1, 2, _)
```

세 번째 파라미터에 대한 값을 제공하지 않았기 때문에, 그 결과로 생긴 변수 `addTo3`는 부분 적용 함수입니다. REPL에서 이를 볼 수 있습니다. 먼저, `sum` 함수를 붙여넣습니다.

```scala
scala> val sum = (a: Int, b: Int, c: Int) => a + b + c
val sum: (Int, Int, Int) => Int = Lambda ...
```

REPL 결과는 다음 출력을 보여줍니다.

```scala
val sum: (Int, Int, Int) => Int = Lambda ...
```

이 출력은 `sum`이 세 개의 `Int` 입력 파라미터를 받아 `Int`를 반환하는 함수임을 확인해 줍니다. 다음으로, `sum`이 원하는 세 개의 입력 파라미터 중 두 개만 주면서, 그 결과를 `addTo3`에 할당합니다.

```scala
scala> val addTo3 = sum(1, 2, _)
val addTo3: Int => Int = Lambda ...
//          ----------
```

REPL 결과의 밑줄 친 부분은 `addTo3`가 단일 `Int` 입력 파라미터를 출력 `Int` 파라미터로 변환하는 함수임을 보여줍니다. `addTo3`는 `sum`에게 입력 파라미터 1과 2를 줌으로써 만들어지며, 이제 `addTo3`는 하나의 입력 파라미터를 더 받을 수 있는 함수입니다. 그래서 이제 `addTo3`에게 숫자 10과 같은 `Int`를 주면, 두 함수에 전달된 세 숫자의 합을 마법처럼 얻게 됩니다.

```scala
scala> addTo3(10)
res0: Int = 13
```

방금 일어난 일을 요약하면 다음과 같습니다.

- 처음 두 숫자(1과 2)가 원래의 `sum` 함수에 전달되었습니다.
- 그 과정이 부분 적용 함수인 `addTo3`라는 새 함수를 만듭니다.
- 코드의 나중 어느 시점에, 세 번째 숫자(10)가 `addTo3`에 전달됩니다.

이 예제에서 저자는 `sum`을 `val` 함수로 만들었지만, `def` 메서드로도 정의할 수 있으며, 그래도 정확히 똑같이 동작한다는 점에 주목하세요.

```scala
scala> def sum(a: Int, b: Int, c: Int) = a + b + c
def sum(a: Int, b: Int, c: Int): Int

scala> val addTo3 = sum(1, 2, _)
val addTo3: Int => Int = Lambda ...

scala> addTo3(10)
val res0: Int = 13
```

### 논의

함수형 프로그래밍 언어에서, 파라미터를 가진 함수를 호출할 때, 여러분은 함수를 파라미터에 *적용한다(applying)* 고 말합니다. 모든 파라미터가 함수에 전달될 때 — Java와 같은 언어에서는 항상 그렇게 합니다 — 여러분은 함수를 모든 파라미터에 *완전히 적용한(fully applied)* 것입니다. 하지만 파라미터의 일부분만 함수에 줄 때, 그 표현식의 결과는 부분 적용 함수입니다.

예제에서 시연했듯이, 그 결과로 생긴 부분 적용 함수는 여러분이 주고받을 수 있는 변수입니다. 예를 들어, 다음 코드는 부분 적용 함수 `addTo3`가 웜홀(wormhole) 안으로 전달되고, 웜홀을 통과하여, 실행되기 전에 반대편으로 빠져나올 수 있는 방법을 보여줍니다.

```scala
def sum(a: Int, b: Int, c: Int) = a + b + c
val addTo3 = sum(1, 2, _)

def intoTheWormhole(f: Int => Int) = throughTheWormhole(f)
def throughTheWormhole(f: Int => Int) = otherSideOfWormhole(f)

// 받은 함수가 무엇이든 그것에 10을 공급합니다:
def otherSideOfWormhole(f: Int => Int) = f(10)

intoTheWormhole(addTo3)   // 13
```

마지막 줄의 주석이 보여주듯이, `addTo3`가 마침내 `otherSideOfWormhole`에 의해 실행될 때 `intoTheWormhole`을 호출한 결과는 13이 됩니다.

함수 변수는 *함수 값(function values)* 이라고도 불리며, 보여준 것처럼 나중에 함수 값을 *완전히 적용하는* 데 필요한 모든 파라미터를 제공하면 결과가 산출됩니다.

#### 실전 활용 (Real-world use)

이 기법의 한 가지 용도는 일반적인 함수의 더 구체적인 버전을 만드는 것입니다. 예를 들어, HTML을 다룰 때, HTML 조각에 접두사(prefix)와 접미사(suffix)를 추가하는 메서드를 가질 수 있습니다.

```scala
def wrap(prefix: String, html: String, suffix: String) =
    prefix + html + suffix
```

코드의 어느 시점에서 서로 다른 HTML 문자열들에 항상 같은 접두사와 접미사를 추가하고 싶다는 것을 안다면, `html` 파라미터는 적용하지 않은 채 그 두 파라미터를 메서드에 적용할 수 있습니다.

```scala
val wrapWithDiv = wrap("<div>", _, "</div>")
```

이제 그 결과로 생긴 `wrapWithDiv` 함수를, 감싸고 싶은 HTML만 전달하면서 호출할 수 있습니다.

```scala
scala> wrapWithDiv("<p>Hello, world</p>")
res0: String = <div><p>Hello, world</p></div>

scala> wrapWithDiv("""<img src="/images/foo.png" />""")
res1: String = <div><img src="/images/foo.png" /></div>
```

`wrapWithDiv` 함수는 여러분이 적용한 `<div>`와 `</div>` 태그가 미리 적재되어 있으므로, 감싸고 싶은 HTML이라는 단 하나의 인자만으로 호출할 수 있습니다.

좋은 점으로, 원한다면 원래의 `wrap` 함수도 여전히 호출할 수 있습니다.

```scala
wrap("<pre>", "val x = 1", "</pre>")
```

일반적으로, 부분 적용 함수를 사용하여 기존 메서드(또는 함수)에 일부 인자를 묶어 두고 나머지는 채워지도록 남겨 둠으로써 프로그래밍을 더 쉽게 만들 수 있습니다.

> **Scala 3에서 개선된 타입 추론 (Improved Type Inference in Scala 3)**
>
> Scala 2에서는 생략된 파라미터의 타입을 지정하는 것이 종종 필요했는데, 예를 들어 앞선 예제에서 `String` 타입을 지정하는 것이었습니다.
>
> ```scala
> val wrapWithDiv = wrap("<div>", _: String, "</div>")
> //                                ---------
> ```
>
> 하지만 지금까지 Scala 3에서는 그것이 필요하지 않았습니다. 예제에서 보여준 것처럼, 그저 빠진 필드를 `_` 문자로 선언하면 됩니다.
>
> ```scala
> val wrapWithDiv = wrap("<div>", _, "</div>")
> ```

---

## 10.6 함수를 반환하는 메서드 만들기 (Creating a Method That Returns a Function)

### 문제

여러분은 함수(알고리즘)를 함수나 메서드로부터 반환하고자 합니다.

### 해법

익명 함수를 정의하고, 그것을 여러분의 메서드로부터 반환하세요. 그런 다음 그것을 함수 변수에 할당하고, 나중에 필요할 때 그 함수 변수를 호출하세요.

예를 들어, 현재 스코프에 `prefix`라는 변수가 존재한다고 가정하면, 이 코드는 `str`이라는 `String` 인자를 받아 그 문자열 앞에 `prefix`를 붙여 반환하는 익명 함수를 선언합니다.

```scala
(str: String) => s"$prefix $str"
```

이제 그 익명 함수를 다른 함수의 본문으로부터 다음과 같이 반환할 수 있습니다.

```scala
// 한 줄(single line) 문법
def saySomething(prefix: String) = (str: String) => s"$prefix $str"

// 여러 줄(multiline) 문법, 읽기가 더 쉬울 수 있습니다
def saySomething(prefix: String) = (str: String) =>
    s"$prefix $str"
```

그 예제는 `saySomething`의 반환 타입을 보여주지 않지만, 원한다면 그것을 `(String => String)`으로 선언할 수 있습니다.

```scala
def saySomething(prefix: String): (String => String) = (str: String) =>
    s"$prefix $str"
```

`saySomething`은 하나의 `String`을 또 다른 `String`으로 변환하는 함수를 반환하므로, 그 결과로 생긴 함수를 변수에 할당할 수 있습니다. `saySomething`은 또한 `prefix`라는 `String` 파라미터를 받으므로, `sayHello`라는 새 함수를 만들면서 그 파라미터를 줍니다.

```scala
val sayHello = saySomething("Hello")
```

이 코드를 REPL에 붙여넣으면, `sayHello`가 `String`을 `String`으로 변환하는 함수임을 볼 수 있습니다.

```scala
scala> val sayHello = saySomething("Hello")
val sayHello: String => String = Lambda ...
//            ----------------
```

`sayHello`는 본질적으로 `saySomething`과 같지만, `prefix`가 `"Hello"` 값으로 미리 적재되어 있습니다. 익명 함수를 다시 보면, 그것이 `String` 파라미터 `s`를 받아 `String`을 반환하는 것을 알 수 있으므로, 그것에게 `String`을 전달합니다.

```scala
sayHello("Al")
```

REPL에서 이 단계들은 다음과 같은 모습입니다.

```scala
scala> def saySomething(prefix: String) = (str: String) =>
     |     s"$prefix $str"
def saySomething(prefix: String): String => String

// "Hello"를 prefix에 할당합니다
scala> val sayHello = saySomething("Hello")
val sayHello: String => String = Lambda ...

// "Al"을 str에 할당합니다
scala> sayHello("Al")
res0: String = Hello Al
```

### 논의

알고리즘을 메서드 안에 캡슐화하고 싶을 때면 언제든 이 접근 방식을 사용할 수 있습니다. 객체지향의 팩토리(Factory)나 전략(Strategy) 패턴과 약간 비슷하게, 여러분의 메서드가 반환하는 함수는 그것이 받는 입력 파라미터에 기반할 수 있습니다. 예를 들어, 지정된 언어에 기반하여 적절한 인사말을 반환하는 `greeting` 메서드를 만듭니다.

```scala
def greeting(language: String) = (name: String) =>
    language match
        case "english" => s"Hello, $name"
        case "spanish" => s"Buenos dias, $name"
```

`greeting`이 `String => String` 함수를 반환한다는 것이 명확해 보이지 않는다면, (a) 메서드 반환 타입을 지정하고 (b) 메서드 안에서 함수 값들을 만듦으로써 코드를 더 명시적으로 만들 수 있습니다.

```scala
// [a] 'String => String' 반환 타입을 선언합니다
def greeting(language: String): (String => String) = (name: String) =>
    // [b] 여기서 함수 값들을 만든 다음, match 표현식으로부터
    // 그것들을 반환합니다
    val englishFunc = () => s"Hello, $name"
    val spanishFunc = () => s"Buenos dias, $name"
    language match
        case "english" => println("returning the english function")
                          englishFunc()
        case "spanish" => println("returning the spanish function")
                          spanishFunc()
```

REPL에서 호출될 때 이 두 번째 메서드는 다음과 같은 모습입니다.

```scala
scala> val hello = greeting("english")
val hello: String => String = Lambda ...

scala> val buenosDias = greeting("spanish")
val buenosDias: String => String = Lambda ...

scala> hello("Al")
returning english function
val res0: String = Hello, Al

scala> buenosDias("Lorenzo")
returning spanish function
val res1: String = Buenos dias, Lorenzo
```

하나 이상의 함수를 메서드 뒤에 캡슐화하고 싶을 때면 언제든 이 레시피를 사용할 수 있습니다.

#### 메서드에서 메서드를 반환하기 (Returning methods from a method)

또한, 원한다면 Scala의 에타 확장 기술 덕분에, 메서드 안에서 *메서드* 를 선언하고 반환할 수도 있습니다.

```scala
def greeting(language: String): (String => String) = (name: String) =>
    def englishMethod = s"Hello, $name"
    def spanishMethod = s"Buenos dias, $name"
    language match
        case "english" => println("returning the english method")
                          englishMethod
        case "spanish" => println("returning the spanish method")
                          spanishMethod
```

`greeting`에 대한 유일한 변경은, 이 버전이 `englishMethod`와 `spanishMethod`를 선언하고 반환한다는 것입니다. 저자는 메서드 문법이 읽기 더 쉽다고 느껴서 이 접근 방식을 선호하며, `greeting`의 호출자 입장에서는 그 외의 모든 것이 정확히 똑같아 보입니다.

> **같은 기법, 다른 용도 (Same Technique, Different Uses)**
>
> 첫 번째 예제에서는 `prefix`가 결과 함수에 값을 미리 적재하는 데 사용되었습니다. 두 번째 예제에서는 `language` 파라미터가 어떤 알고리즘을 반환할지 선택하는 데 사용되었는데, 이는 객체지향 프로그래밍의 전략(Strategy)이나 템플릿(Template) 패턴과 비슷한 것입니다.

---

## 10.7 부분 함수 만들기 (Creating Partial Functions)

### 문제

여러분은 가능한 입력 값의 일부분에 대해서만 동작하는 함수를 정의하고자 하거나, 입력 값의 일부분에 대해서만 동작하는 일련의 함수들을 정의한 다음 그 함수들을 조합하여 문제를 완전히 해결하고자 합니다.

### 해법

*부분 함수(partial function)* 는 자신에게 주어질 수 있는 모든 가능한 입력 값에 대해 답을 제공하지 않는 함수입니다. 그것은 가능한 데이터의 일부분에 대해서만 답을 제공하며, 자신이 처리할 수 있는 데이터를 정의합니다. Scala에서, 부분 함수는 특정 값을 처리할 수 있는지 알아보기 위해 질의(query)될 수도 있습니다.

예를 들어, 한 숫자를 다른 숫자로 나누는 일반적인 함수를 상상해 봅시다.

```scala
val divide = (x: Int) => 42 / x
```

이렇게 정의되면, 이 함수는 입력 파라미터가 0일 때 터져 버립니다(blows up).

```scala
scala> divide(0)
java.lang.ArithmeticException: / by zero
```

이 특정 상황을 예외를 잡고 던지는 것으로 처리할 수도 있겠지만, Scala는 `divide` 함수를 `PartialFunction`으로 정의할 수 있게 해줍니다. 그렇게 할 때, 입력 파라미터가 0이 아닐 때 이 함수가 정의된다(defined)는 것도 명시적으로 진술합니다.

```scala
val divide = new PartialFunction[Int, Int] {
    def apply(x: Int) = 42 / x
    def isDefinedAt(x: Int) = x != 0
}
```

이 접근 방식에서, `apply` 메서드는 함수 시그니처와 본문을 정의합니다. 이제 몇 가지 좋은 것들을 할 수 있습니다. 한 가지는 함수를 사용하려고 시도하기 전에 함수를 테스트하는 것입니다.

```scala
scala> divide.isDefinedAt(0)
res0: Boolean = false

scala> divide.isDefinedAt(1)
res1: Boolean = true

scala> val x = if divide.isDefinedAt(1) then Some(divide(1)) else None
val x: Option[Int] = Some(42)
```

이에 더해, 잠시 후에 다른 코드가 부분 함수를 활용하여 우아하고 간결한 해법을 제공하는 것을 보게 됩니다.

저 `divide` 함수는 자신이 처리하는 데이터에 대해 명시적인 반면, 부분 함수는 `case` 문을 사용하여 작성될 수도 있습니다.

```scala
val divide2: PartialFunction[Int, Int] =
    case d if d != 0 => 42 / d
```

이 접근 방식에서, Scala는 코드의 다음 부분에 기반하여 `divide2`가 `Int` 입력 파라미터를 받는다는 것을 추론할 수 있습니다.

```scala
PartialFunction[Int, Int]
//              ---
```

이 코드는 `isDefinedAt` 메서드를 명시적으로 구현하지 않지만, 이전의 `divide` 함수 정의와 똑같이 동작합니다.

```scala
scala> divide2.isDefinedAt(0)
res0: Boolean = false

scala> divide2.isDefinedAt(1)
res1: Boolean = true
```

### 논의

`PartialFunction` Scaladoc은 부분 함수를 다음과 같이 설명합니다.

> `PartialFunction[A, B]` 타입의 부분 함수는, 정의역(domain)이 반드시 타입 A의 모든 값을 포함하지는 않는 단항 함수(unary function)다. 함수 `isDefinedAt`은 어떤 값이 그 함수의 정의역 안에 있는지를 동적으로 테스트할 수 있게 해준다.

이는 `case` 문을 사용한 마지막 예제가 왜 동작하는지 설명하는 데 도움이 됩니다. 즉, `isDefinedAt` 메서드는 주어진 값이 함수의 정의역 안에 있는지 — 다시 말해, 그것이 처리되는지 또는 고려되는지 — 를 동적으로 테스트합니다. `PartialFunction` 트레이트의 시그니처는 다음과 같은 모습입니다.

```scala
trait PartialFunction[-A, +B] extends (A) => B
```

다른 레시피들에서 논의했듯이, `=>` 기호는 변환기로 생각할 수 있으며, 이 경우 `(A) => B`는 타입 A를 결과 타입 B로 변환하는 함수로 해석될 수 있습니다.

`divide2` 메서드는 입력 `Int`를 출력 `Int`로 변환하므로, 그 시그니처는 다음과 같은 모습입니다.

```scala
val divide2: PartialFunction[Int, Int] = ...
//                           --------
```

하지만 만약 그것이 대신 `String`을 반환한다면, 다음과 같이 선언될 것입니다.

```scala
val divide2: PartialFunction[Int, String] = ...
//                           -----------
```

예를 들어, 다음 메서드는 이 시그니처를 사용합니다.

```scala
// 1을 "one"으로, 5까지 변환합니다
val convertLowNumToString = new PartialFunction[Int, String] {
    val nums = Array("one", "two", "three", "four", "five")
    def apply(i: Int) = nums(i-1)
    def isDefinedAt(i: Int) = i > 0 && i < 6
}
```

#### orElse와 andThen으로 부분 함수 연결하기 (Chaining partial functions with orElse and andThen)

부분 함수의 멋진 기능은 그것들을 서로 연결(chain)할 수 있다는 것입니다. 예를 들어, 한 메서드는 짝수만 처리하고 또 다른 메서드는 홀수만 처리할 수 있는데, 함께 사용하면 모든 정수 문제를 해결할 수 있습니다.

이 접근 방식을 시연하기 위해, 다음 예제는 각각 소수의 `Int` 입력을 처리하여 `String` 결과로 변환할 수 있는 두 함수를 보여줍니다.

```scala
// 1을 "one"으로, 5까지 변환합니다
val convert1to5 = new PartialFunction[Int, String] {
    val nums = Array("one", "two", "three", "four", "five")
    def apply(i: Int) = nums(i-1)
    def isDefinedAt(i: Int) = i > 0 && i < 6
}

// 6을 "six"로, 10까지 변환합니다
val convert6to10 = new PartialFunction[Int, String] {
    val nums = Array("six", "seven", "eight", "nine", "ten")
    def apply(i: Int) = nums(i-6)
    def isDefinedAt(i: Int) = i > 5 && i < 11
}
```

따로따로 보면, 각각은 다섯 개의 숫자만 처리할 수 있습니다. 하지만 `orElse`로 결합하면, 그 결과로 생긴 함수는 10개를 처리할 수 있습니다.

```scala
scala> val handle1to10 = convert1to5 orElse convert6to10
handle1to10: PartialFunction[Int,String] = <function1>

scala> handle1to10(3)
res0: String = three

scala> handle1to10(8)
res1: String = eight
```

`orElse` 메서드는 `PartialFunction` 트레이트에서 나오는데, 이 트레이트는 부분 함수들을 서로 연결하는 것을 더 돕기 위한 `andThen` 메서드도 포함합니다.

#### 컬렉션 클래스에서의 부분 함수 (Partial functions in the collections classes)

부분 함수에 대해 아는 것은 단지 도구 상자에 또 하나의 도구를 갖는 것 때문만이 아니라, 그것들이 Scala 컬렉션 라이브러리를 포함한 일부 라이브러리의 API에서 사용되기 때문에도 중요합니다.

부분 함수를 마주치게 되는 한 가지 예는 컬렉션 클래스의 `collect` 메서드입니다. `collect` 메서드는 부분 함수를 입력으로 받으며, 그 Scaladoc이 설명하듯이 `collect`는 "이 리스트의 모든 요소들 중 함수가 정의된 요소들에 부분 함수를 적용하여 새 컬렉션을 만듭니다."

예를 들어, 앞서 보여준 `divide` 함수는 `Int` 값 0에서 정의되지 않은 부분 함수입니다. 그 함수를 다시 보겠습니다.

```scala
val divide: PartialFunction[Int, Int] =
    case d: Int if d != 0 => 42 / d
```

이 부분 함수를 `map` 메서드와 0을 포함하는 리스트에 사용하려고 시도하면, `MatchError`로 터져 버립니다.

```scala
scala> List(0,1,2).map(divide)
scala.MatchError: 0 (of class java.lang.Integer)
stack trace continues ...
```

하지만 같은 함수를 `collect` 메서드와 함께 사용하면, 예외를 던지지 않습니다.

```scala
scala> List(0,1,2).collect(divide)
res0: List[Int] = List(42, 21)
```

이는 `collect` 메서드가 자신에게 주어진 각 요소에 대해 `isDefinedAt` 메서드를 테스트하도록 작성되었기 때문입니다. 개념적으로, 이는 다음과 유사합니다.

```scala
List(0,1,2).filter(divide.isDefinedAt(_))
           .map(divide)
```

그 결과, `collect`는 입력 값이 0일 때는 `divide` 알고리즘을 실행하지 않지만, 다른 모든 요소에 대해서는 그것을 실행합니다.

`collect` 메서드가 다른 상황에서 동작하는 것도 볼 수 있습니다. 예를 들어, 여러 데이터 타입이 섞여 있는 `List`에, `Int` 값에 대해서만 동작하는 함수를 함께 전달하는 경우입니다.

```scala
scala> List(42, "cat").collect { case i: Int => i + 1 }
res0: List[Int] = List(43)
```

내부적으로 `isDefinedAt` 메서드를 확인하기 때문에, `collect`는 여러분의 익명 함수가 `String`을 입력으로 다룰 수 없다는 사실을 처리할 수 있습니다.

`collect`의 또 다른 용도는, 리스트가 일련의 `Some`과 `None` 값을 포함하고 있고 모든 `Some` 값을 추출하고 싶을 때입니다.

```scala
scala> val possibleNums = List(Some(1), None, Some(3), None)
val possibleNums: List[Option[Int]] = List(Some(1), None, Some(3), None)

scala> possibleNums.collect{case Some(i) => i}
val res1: List[Int] = List(1, 3)
```

> **또는 flatten을 사용하세요 (Or Use flatten)**
>
> `Seq[Option]`을 그 `Some` 요소 안의 값들만으로 줄이는 또 다른 접근 방식은, 리스트에 `flatten`을 호출하는 것입니다.
>
> ```scala
> scala> possibleNums.flatten
> val res0: List[Int] = List(1, 3)
> ```
>
> 이것이 동작하는 이유는 `Option`이 0개 또는 1개의 값을 포함하는 리스트와 같고, `flatten`의 존재 목적이 "리스트들의 리스트(list of lists)"를 하나의 리스트로 변환하는 것이기 때문입니다. 더 자세한 내용은 Recipe 13.6을 참고하세요.

---

## 10.8 함수형 오류 처리 구현하기 (Implementing Functional Error Handling)

### 문제

여러분은 함수형 프로그래밍 스타일로 코드를 작성하기 시작했지만, 순수 함수를 작성할 때 예외와 그 밖의 오류를 어떻게 처리해야 할지 확신이 서지 않습니다.

### 해법

함수형 코드를 작성하는 것은 대수 방정식을 쓰는 것과 같고 — 그리고 대수 방정식은 항상 값을 반환하며 결코 예외를 던지지 않기 때문에 — 여러분의 순수 함수는 예외를 던지지 않습니다. 대신, Scala의 *오류 처리 타입(error handling types)* 으로 오류를 처리합니다.

- Option/Some/None
- Try/Success/Failure
- Either/Left/Right

이에 대한 저자의 표준적인 예제는 `makeInt` 메서드를 작성하는 것입니다. 잠시 Scala가 `String`에 `makeInt` 메서드를 포함하고 있지 않아서 여러분이 직접 메서드를 작성하고 싶다고 상상해 봅시다. 올바른 해법은 다음과 같은 모습입니다.

```scala
def makeInt(s: String): Option[Int] =
    try
        Some(Integer.parseInt(s))
    catch
        case e: NumberFormatException => None
```

이 코드는 `makeInt`가 `String`을 `Int`로 변환할 수 있으면 `Some[Int]`를 반환하고, 그렇지 않으면 `None`을 반환합니다. 이 메서드의 호출자는 다음과 같이 사용합니다.

```scala
makeInt("1")   // Option[Int] = Some(1)
makeInt("a")   // Option[Int] = None

makeInt(aString) match
    case Some(i) => println(s"i = $i")
    case None => println("Could not create an Int")
```

정수로 변환될 수도 있고 안 될 수도 있는 문자열 리스트 `listOfStrings`가 주어졌을 때, `makeInt`를 다음과 같이 사용할 수도 있습니다.

```scala
val optionalListOfInts: Seq[Option[Int]] =
    for s <- listOfStrings yield makeInt(s)
```

이것이 훌륭한 이유는, `makeInt`가 예외를 던져 그 `for` 표현식을 터뜨리지 않기 때문입니다. 대신, `for` 표현식은 `Option[Int]` 값들을 포함하는 `Seq`를 반환합니다. 예를 들어, `listOfStrings`가 다음 값들을 포함한다면,

```scala
val listOfStrings = List("a", "1", "b", "2")
```

`optionalListOfInts`는 다음 값들을 포함하게 됩니다.

```scala
List(None, Some(1), None, Some(2))
```

성공적으로 정수로 변환된 값들만 포함하는 리스트를 만들려면, 그 리스트를 다음과 같이 `flatten` 하기만 하면 됩니다.

```scala
val ints = optionalListOfInts.flatten   // List(1, 2)
```

이 해법에서 `Option` 타입을 사용하는 것에 더해, `Try`와 `Either` 타입도 사용할 수 있습니다. 이 세 가지 오류 처리 타입을 사용하는 `makeInt` 메서드의 훨씬 짧은 버전은 다음과 같은 모습입니다.

```scala
import scala.util.control.Exception.*
import scala.util.{Try, Success, Failure}

def makeInt(s: String): Option[Int] = allCatch.opt(Integer.parseInt(s))
def makeInt(s: String): Try[Int] = Try(Integer.parseInt(s))
def makeInt(s: String): Either[Throwable, Int] =
    allCatch.either(Integer.parseInt(s))
```

이 예제들은 그 세 가지 접근 방식의 성공 경우와 오류 경우를 보여줍니다.

```scala
// Option
makeInt("1")   // Some(1)
makeInt("a")   // None

// Try
makeInt("1")   // util.Try[Int] = Success(1)
makeInt("a")   // util.Try[Int] = Failure(java.lang.NumberFormatException:
               //                 For input string: "a")

// Either
makeInt("1")   // Either[Throwable, Int] = Right(1)
makeInt("a")   // Either[Throwable, Int] = Left(java.lang.NumberFormatException:
               //                          For input string: "a")
```

이 모든 접근 방식의 핵심은 예외를 던지지 않는다는 것입니다. 대신, 이러한 오류 처리 타입을 반환합니다.

> **Either를 사용하면 ZIO 같은 FP 라이브러리를 위한 준비가 됩니다 (Using Either Gets You Ready for FP Libraries Like ZIO)**
>
> 이 책의 리뷰어 중 한 명인 헤르만 휴크(Hermann Hueck)는 `Either`를 사용하는 두 가지 이점을 지적했습니다. (a) 오류 타입을 제어할 수 있기 때문에 `Try`보다 더 유연하며, (b) `Either`와 유사한 접근 방식을 광범위하게 사용하는 ZIO 같은 FP 라이브러리를 사용할 준비가 된다는 것입니다.

### 논의

이 문제에 대한 나쁜(비-FP) 접근 방식은, 메서드를 다음과 같이 작성하여 예외를 던지는 것입니다.

```scala
// 이런 식으로 코드를 작성하지 마세요!
@throws(classOf[NumberFormatException])
def makeInt(s: String): Int =
    try
        Integer.parseInt(s)
    catch
        case e: NumberFormatException => throw e
```

FP에서는 이런 식으로 코드를 작성하지 않습니다. 왜냐하면 다른 사람들이 여러분의 메서드를 사용할 때, 예외가 발생하면 그들의 방정식이 터져 버리기 때문입니다. 예를 들어, 누군가 이 버전의 `makeInt`를 사용하는 다음 `for` 표현식을 작성한다고 상상해 봅시다.

```scala
val possibleListOfInts: Seq[Int] =
    for s <- listOfStrings yield makeInt(s)
```

`listOfStrings`가 해법에서 보여준 것과 같은 값들을 포함한다면,

```scala
val listOfStrings = List("a", "1", "b", "2")
```

대수 방정식이기를 바라는 그들의 `for` 표현식은 리스트의 첫 번째 요소인 `"a"`에서 터져 버립니다.

다시 말하지만, 대수 방정식이 예외를 던지지 않기 때문에, 순수 함수도 예외를 던지지 않습니다.

---

## 10.9 실전 예제: 알고리즘에서 함수 주고받기 (Real-World Example: Passing Functions Around in an Algorithm)

다소 실전적인 예제로, 이 강의에서는 저자가 항공우주 공학자로 일하던 시절에 사용했던 알고리즘의 일부로서 메서드와 함수를 주고받는 방법을 보여드리겠습니다.

*뉴턴의 방법(Newton's Method)* 은 방정식의 근(roots)을 구하는 데 사용할 수 있는 수학적 방법입니다. 예를 들어, 이 예제는 다음 방정식에 대한 `x`의 가능한 값을 찾습니다.

```
3x + sin(x) - E^x = 0
```

다음 코드에서 볼 수 있듯이, `newtonsMethod`라는 메서드는 함수들을 처음 두 개의 파라미터로 받습니다. 또한 두 개의 다른 `Double` 파라미터를 받고 `Double`을 반환합니다.

```scala
/**
 * Newton's Method for solving equations.
 * @param fx The equation to solve.
 * @param fxPrime The derivative of `fx`.
 * @param x An initial "guess" for the value of `x`.
 * @param tolerance Stop iterating when the iteration values are
 *        within this tolerance.
 * @todo Check that `f(xNext)` is greater than a second tolerance value.
 * @todo Check that `f'(x) != 0`
 */
def newtonsMethod(
    fx: Double => Double,
    fxPrime: Double => Double,
    x: Double,
    tolerance: Double
): Double =
    /**
     * most FP approaches don't use a `var` field,
     * but some people believe that `var` fields are acceptable
     * when they are contained within the scope of a method/function.
     */
    var x1 = x
    var xNext = newtonsMethodHelper(fx, fxPrime, x1)
    while math.abs(xNext - x1) > tolerance do
        x1 = xNext
        println(xNext) // debugging (intermediate values)
        xNext = newtonsMethodHelper(fx, fxPrime, x1)
    end while

    // return xNext:
    xNext

end newtonsMethod

/**
 * This is the `x2 = x1 - f(x1)/f'(x1)` calculation.
 */
def newtonsMethodHelper(
    fx: Double => Double,
    fxPrime: Double => Double,
    x: Double
): Double =
    x - fx(x) / fxPrime(x)
```

`newtonsMethod`에 전달되는 두 함수는 원래의 방정식(`fx`)과 그 방정식의 도함수(derivative)(`fxPrime`)여야 합니다. 두 함수 *내부* 의 세부 사항에 대해서는 너무 걱정하지 마세요. 저자는 오직 이와 같은 실전 알고리즘에서 함수가 어떻게 주고받아지는지에 초점을 맞추는 데에만 관심이 있습니다.

`newtonsMethodHelper` 메서드 또한 두 함수를 파라미터로 받으므로, 함수들이 `newtonsMethod`에서 `newtonsMethodHelper`로 어떻게 전달되는지 볼 수 있습니다.

다음은 뉴턴의 방법을 사용하여 `fx` 방정식의 근을 찾는 방법을 보여주는 `@main` 드라이버(driver) 메서드입니다.

```scala
/**
 * A "driver" function to test Newton's method. Start with:
 *   - the desired `f(x)` and `f'(x)` equations
 *   - an initial guess, and
 *   - a tolerance value
 */
@main def driver =
    // The `f(x)` and `f'(x)` functions. Both functions take a `Double`
    // parameter named `x` and return a `Double`.
    def fx(x: Double): Double = 3*x + math.sin(x) - math.pow(math.E, x)
    def fxPrime(x: Double): Double = 3 + math.cos(x) - math.pow(math.E, x)

    val initialGuess = 0.0
    val tolerance = 0.00005

    // pass `f(x)` and `f'(x)` to the Newton's Method function, along with
    // the initial guess and tolerance.
    val answer = newtonsMethod(fx, fxPrime, initialGuess, tolerance)

    // note: this is not an FP approach to printing output
    println(answer)
```

이 예제의 출력은 다음과 같습니다.

```
0.3333333333333333
0.3601707135776337
0.36042168047601975
0.3604217029603242
```

보다시피, 이 코드의 대부분은 메서드와 함수를 정의하고, 함수를 메서드에 전달하고, 그다음 메서드 안에서 함수를 호출하는 일과 관련됩니다. 이는 특히 이와 같은 알고리즘을 위한 코드를 작성할 때 FP가 어떻게 동작하는지에 대한 감을 줍니다.

`newtonsMethod`라는 메서드는, 구현되지 않은 `@todo` 항목들의 한계 내에서, `fxPrime`이 `fx`의 도함수인 어떤 두 함수 `fx`와 `fxPrime`에 대해서든 동작합니다. 이 예제를 가지고 실험해 보려면, 함수 `fx`와 `fxPrime`을 바꿔 보거나, `@todo` 항목들을 구현해 보세요.

> **알고리즘의 출처 (Source of the Algorithm)**
>
> 여기서 보여준 알고리즘은 커티스 제럴드(Curtis Gerald)와 패트릭 휘틀리(Patrick Wheatley)의 대학 교재 *Applied Numerical Analysis* (Pearson)의 1980년대 판에서 나온 것으로, 그 책에서는 이 접근 방식이 의사코드(pseudocode)로 시연되었습니다.

---

## 10.10 실전 예제: 함수형 도메인 모델링 (Real-World Example: Functional Domain Modeling)

다소 실전적인 예제로, 피자 가게를 위한 FP 스타일의 주문 입력(order-entry) 애플리케이션을 어떻게 구성하는지 살펴보겠습니다. 이 예제의 코드는 오직 피자에만 초점을 맞추며 — 브레드스틱, 치즈스틱, 청량음료, 샐러드는 없습니다 — 그러나 고객(customers), 주소(addresses), 주문(orders), 그리고 그 데이터 타입들에 대한 연산(순수 함수)을 모델링할 것입니다.

### 데이터 모델 (The Data Model)

시작하기 위해, 피자 클래스에 필요할 몇 가지 열거형(enums)이 있습니다. 우리가 무엇을 하는지 명확히 하기 위해, 이 코드를 *Nouns.scala* 라는 이름의 파일에 넣으세요.

```scala
enum Topping:
    case Cheese, Pepperoni, Sausage, Mushrooms, Onions

enum CrustSize:
    case Small, Medium, Large

enum CrustType:
    case Regular, Thin, Thick
```

다음으로, 이 열거형들은 `Pizza` 클래스를 정의하는 데 사용됩니다. 이 케이스 클래스를 *Nouns.scala* 에 추가하세요.

```scala
case class Pizza(
    crustSize: CrustSize,
    crustType: CrustType,
    toppings: Seq[Topping]
)
```

마지막으로, 이 클래스들은 고객과 주문이라는 개념을 모델링하는 데 사용됩니다.

```scala
case class Customer(
    name: String,
    phone: String,
    address: Address
)

case class Address(
    street1: String,
    street2: Option[String],
    city: String,
    state: String,
    postalCode: String
)

case class Order(
    pizzas: Seq[Pizza],
    customer: Customer
)
```

그 코드도 *Nouns.scala* 에 추가하세요.

데이터 모델은 그게 전부입니다. 클래스들이 단순하고 불변인 자료 구조이며, 열거형과 케이스 클래스로 정의되었다는 점에 주목하세요. OOP 클래스와 달리, 동작(behaviors, 즉 메서드)을 클래스 안에 캡슐화하지 않습니다. 그 결과, 이 접근 방식은 데이터베이스 스키마를 정의하는 것과 상당히 비슷한 느낌이 듭니다.

> **마른 도메인 오브젝트 (Skinny Domain Objects)**
>
> 데바시시 고시(Debasish Ghosh)는 자신의 책 *Functional and Reactive Domain Modeling* (Manning)에서, OOP 실무자들이 자신의 클래스를 데이터와 동작을 캡슐화하는 "풍부한 도메인 모델(rich domain models)"이라고 묘사하는 반면, FP 데이터 모델은 "마른 도메인 오브젝트(skinny domain objects)"로 생각할 수 있다고 말합니다. 이 강의가 보여주듯이, 데이터 모델은 속성(attributes)은 갖지만 동작은 갖지 않는 열거형과 케이스 클래스를 사용하여 정의되기 때문입니다.

### 그 모델에 대해 작동하는 함수들 (Functions That Operate on That Model)

이제 해야 할 일은, 그 불변 자료 구조들에 대해 작동하는 일련의 순수 함수를 만드는 것뿐입니다. 이를 위한 좋은 방법은, 먼저 하나 이상의 트레이트(traits)를 사용하여 원하는 인터페이스를 스케치하는 것입니다. `Pizza`에 대해 작동하는 함수들을 위해 저자는 하나의 트레이트를 정의하겠습니다. 이 코드를 *Verbs.scala* 라는 이름의 파일에 넣으세요.

```scala
trait PizzaServiceInterface:
    def addTopping(p: Pizza, t: Topping): Pizza
    def removeTopping(p: Pizza, t: Topping): Pizza
    def removeAllToppings(p: Pizza): Pizza

    def updateCrustSize(p: Pizza, cs: CrustSize): Pizza
    def updateCrustType(p: Pizza, ct: CrustType): Pizza
```

그 트레이트의 구체적인 구현을 만들고 나면 — 잠시 후에 하게 됩니다 — 다음과 같은 코드를 작성할 수 있습니다.

```scala
import Topping.*, CrustSize.*, CrustType.*

@main def pizzaServiceMain =
    // PizzaService는 PizzaServiceInterface를 확장(extend)하는 트레이트입니다
    import PizzaService.*
    object PizzaService extends PizzaService

    // 초기 피자
    val p = Pizza(Medium, Regular, Seq(Cheese))

    // PizzaService 함수들을 시연합니다
    val p1 = addTopping(p, Pepperoni)
    val p2 = addTopping(p1, Mushrooms)
    val p3 = updateCrustType(p2, Thick)
    val p4 = updateCrustSize(p3, Large)

    // 이것은 출력을 출력하는 함수형 접근 방식이 *아닙니다*.
    // 결과:
    // Pizza(LargeCrustSize,ThickCrustType,List(Cheese, Pepperoni, Mushrooms))
    println(p4)
```

그 코드를 *Driver.scala* 라는 이름의 파일에 넣으세요.

그 API의 모습이 만족스러우므로, 그다음 `PizzaServiceInterface` 트레이트의 구체적인 구현을 만듭니다. 그렇게 하려면, 이 코드를 *Verbs.scala* 파일에 추가하세요.

```scala
import ListUtils.dropFirstMatch

trait PizzaService extends PizzaServiceInterface:
    def addTopping(p: Pizza, t: Topping): Pizza =
        val newToppings = p.toppings :+ t
        p.copy(toppings = newToppings)

    def removeTopping(p: Pizza, t: Topping): Pizza =
        val newToppings = dropFirstMatch(p.toppings, t)
        p.copy(toppings = newToppings)

    def removeAllToppings(p: Pizza): Pizza =
        val newToppings = Seq[Topping]()
        p.copy(toppings = newToppings)

    def updateCrustSize(p: Pizza, cs: CrustSize): Pizza =
        p.copy(crustSize = cs)

    def updateCrustType(p: Pizza, ct: CrustType): Pizza =
        p.copy(crustType = ct)

end PizzaService
```

그 코드는 리스트에서 첫 번째로 일치하는 요소를 제거하는 `dropFirstMatch`라는 메서드를 필요로 하는데, 저자는 그것을 `ListUtils` 오브젝트에 넣었습니다.

```scala
object ListUtils:

    /**
     * Drops the first matching element in a list, as in this example:
     * {{{
     * val xs = list(1,2,3,1)
     * dropFirstMatch(xs, 1) == List(2,3,1)
     * }}}
     */
    def dropFirstMatch[A](xs: Seq[A], value: A): Seq[A] =
        val idx = xs.indexOf(value)
        for
            (x, i) <- xs.zipWithIndex
            if i != idx
        yield
            x
```

우리의 목적상, 이 메서드는 토핑 리스트에서 발견된 `Topping`의 첫 번째 출현(occurrence)을 제거하도록 동작합니다.

```scala
val a = List(Pepperoni, Mushrooms, Pepperoni)
val b = dropFirstMatch(a, Pepperoni)
// 결과: b == List(Mushrooms, Pepperoni)   // 첫 번째 Pepperoni가 제거됩니다
```

`PizzaServiceInterface`와 `PizzaService`로 보여준 것처럼, 함수들(즉, 동사들(verbs))의 구현은 종종 두 단계 과정입니다. 첫 번째 단계에서는, 여러분의 API의 계약(contract)을 인터페이스로 스케치합니다. 두 번째 단계에서는 그 인터페이스의 구체적인 *구현(implementation)* 을 만듭니다. 이는 기반 인터페이스의 여러 구체적인 구현을 만들 수 있는 유연성을 줍니다.

이 시점에서 여러분은 완전하고 동작하는 작은 애플리케이션을 갖게 됩니다. 이 애플리케이션을 가지고 실험하면서 API가 현재 모습 그대로 마음에 드는지, 아니면 수정하고 싶은지 살펴보세요.

> **명사와 동사 (Nouns and Verbs)**
>
> 저자는 이 FP 코드 작성 접근 방식을 강조하기 위해 구체적으로 *Nouns.scala* 와 *Verbs.scala* 라는 파일명을 사용합니다. 보여준 것처럼, 여러분의 데이터 모델은 애플리케이션의 명사들(nouns)로만 구성되고, 함수들은 동작이 일어나는 곳, 즉 동사들(verbs)입니다.

### 트레이트를 사용하여 의존성 주입 프레임워크 만들기 (Use Traits To Create a Dependency Injection Framework)

트레이트를 인터페이스로 사용한 다음 그것을 또 다른 트레이트(또는 오브젝트)에서 구현하는 것의 한 가지 이점은, 이 설계를 사용하여 *의존성 주입 프레임워크(dependency injection framework)* 를 만들 수 있다는 것입니다. 예를 들어, 피자의 가격을 계산하는 코드를 작성하고 싶은데, 그 코드가 데이터베이스에 대한 접근을 필요로 한다고 상상해 봅시다. 이 문제에 대한 한 가지 접근 방식은 다음과 같습니다.

1. `PizzaDaoInterface`라는 데이터 접근 오브젝트(data access object, DAO) 인터페이스를 만듭니다.
2. 개발(Development), 테스트(Test), 운영(Production) 환경을 위한 그 DAO 인터페이스의 서로 다른 구현들을 만듭니다.
3. `PizzaDaoInterface`를 참조하는 "피자 가격 책정기(pizza pricer)" 트레이트를 만듭니다.
4. 개발, 테스트, 운영 환경을 위한 구체적인 피자 가격 책정기 구현들을 만듭니다.
5. 코드를 테스트하기 위한 단위 테스트(unit tests)를 만듭니다(또는 우리의 경우, 해법을 시연하기 위한 `@main` 드라이버 애플리케이션).

이 단계들은 다음 예제에 나와 있습니다.

#### 1. DAO 인터페이스 만들기 (Create a DAO interface)

먼저, *Dao.scala* 라는 이름의 파일을 만든 다음, DAO가 어떤 모습이기를 원하는지 선언하기 위해 이 인터페이스를 그 파일에 넣으세요.

```scala
trait PizzaDaoInterface:
    def getToppingPrices(): Map[Topping, BigDecimal]
    def getCrustSizePrices(): Map[CrustSize, BigDecimal]
    def getCrustTypePrices(): Map[CrustType, BigDecimal]
```

실제 세계에서는, 데이터베이스 접근이 실패할 수 있기 때문에, 이 메서드들은 각 `Map`을 감싸는 `Option`, `Try`, 또는 `Either` 같은 타입을 반환할 것입니다. 하지만 코드를 조금 더 단순하게 만들기 위해 그것들은 생략합니다.

#### 2. 그 DAO의 여러 구현 만들기 (Create multiple implementations of that DAO)

다음으로, 개발, 테스트, 운영 환경을 위한 그 DAO 인터페이스의 구체적인 구현들을 만듭니다. 일반적으로는 각 환경마다 하나의 구체적인 구현을 만들지만, 우리의 목적상 저자는 개발 환경을 위한 하나의 "목(mock)" DAO인 `DevPizzaDao`만 만들겠습니다. 이것의 목적은 로컬 개발(및 테스트) 과정 동안 빠르고 모의된(simulated) 데이터베이스를 갖는 것입니다. 이를 위해, 이 코드도 *Dao.scala* 에 넣으세요.

```scala
object DevPizzaDao extends PizzaDaoInterface:

    def getToppingPrices(): Map[Topping, BigDecimal] =
        Map(
            Cheese    -> BigDecimal(1),   // 각각 $1을 모의합니다
            Pepperoni -> BigDecimal(1),
            Sausage   -> BigDecimal(1),
            Mushrooms -> BigDecimal(1)
        )

    def getCrustSizePrices(): Map[CrustSize, BigDecimal] =
        Map(
            Small  -> BigDecimal(0),
            Medium -> BigDecimal(1),
            Large  -> BigDecimal(2)
        )

    def getCrustTypePrices(): Map[CrustType, BigDecimal] =
        Map(
            Regular -> BigDecimal(0),
            Thick   -> BigDecimal(1),
            Thin    -> BigDecimal(1)
        )

end DevPizzaDao
```

실제 세계에서는 이 DAO를 개발과 테스트에 사용하고, 운영 환경에는 `ProductionPizzaDao`를 사용할 수도 있습니다. 또는 테스트 환경을 위한 별도의 DAO를 사용하거나, 테스트 데이터베이스에 대해 코드를 테스트하는 어디에서든 사용할 수도 있습니다.

#### 3. "피자 가격 책정기" 트레이트 만들기 (Create a "pizza pricer" trait)

다음으로, *Verbs.scala* 파일로 돌아가서, `PizzaDaoInterface`를 참조하는 "피자 가격 책정기" 트레이트를 만듭니다.

```scala
trait PizzaPricerTrait:

    // 이 기반 트레이트는 DAO 인터페이스를 참조합니다
    def pizzaDao: PizzaDaoInterface

    def calculatePizzaPrice(p: Pizza): BigDecimal =
        // 여기서 핵심은 `pizzaDao`의 사용입니다
        val crustSizePrice: BigDecimal =
            pizzaDao.getCrustSizePrices()(p.crustSize)
        val crustTypePrice: BigDecimal =
            pizzaDao.getCrustTypePrices()(p.crustType)
        val toppingPrices: Seq[BigDecimal] =
            for
                topping <- p.toppings
                toppingPrice = pizzaDao.getToppingPrices()(topping)
            yield
                toppingPrice
        val totalToppingPrice: BigDecimal = toppingPrices.reduce(_ + _) //sum
        val totalPrice: BigDecimal =
            crustSizePrice + crustTypePrice + totalToppingPrice
        totalPrice

    // 그 밖의 가격 관련 함수들 ...

end PizzaPricerTrait
```

이 부분의 해법에서 두 가지 핵심은 다음과 같습니다.

- `pizzaDao` 참조를 `PizzaDaoInterface` 타입의 `def`로 선언합니다. 그것은 다음에 개발하게 될 `val` 필드들로 대체될 것입니다.
- 보여준 것처럼, `calculatePizzaPrice`는 이 기반 트레이트에서 구현될 수 있습니다. 이렇게 하면 그것을 한 곳에서만 구현하면 되고, `PizzaPricerTrait`를 확장하는 구체적인 오브젝트들에서 그것을 사용할 수 있게 됩니다.

#### 4. 환경별 가격 책정기 만들기 (Create specific pricers for your environments)

다음으로, 레시피의 마지막 단계에서, 개발, 테스트, 운영 환경을 위한 구체적인 피자 가격 책정기 오브젝트들을 만듭니다.

```scala
object DevPizzaPricerService extends PizzaPricerTrait:
    val pizzaDao = DevPizzaDao        // 개발 환경

object TestPizzaPricerService extends PizzaPricerTrait:
    val pizzaDao = TestPizzaDao       // 테스트 환경

object ProductionPizzaPricerService extends PizzaPricerTrait:
    val pizzaDao = ProductionPizzaDao // 운영 환경
```

`PizzaPricerTrait`가 자신의 `calculatePizzaPrice` 메서드를 완전히 구현하기 때문에, 이 오브젝트들이 해야 할 일이라고는 보여준 것처럼 각자의 데이터 접근 오브젝트에 연결하는 것뿐입니다.

이 프로젝트의 소스 코드에서, 저자가 *Verbs.scala* 파일에 `DevPizzaPricer`를 만든 것을 보게 됩니다. (이 예제에서, `DevPizzaPricerService`는 저자가 `DevPizzaDao`를 구현했기 때문에 컴파일되고 실행되지만, `TestPizzaPricerService`와 `ProductionPizzaPricerService`는 저자가 `TestPizzaDao`와 `ProductionPizzaDao`를 구현하지 않았기 때문에 컴파일되지 않는다는 점에 주목하세요.)

> **균일 접근 원칙 (The Uniform Access Principle)**
>
> `PizzaPricerTrait`에서, `pizzaDao`는 `def`로 정의됩니다.
>
> ```scala
> def pizzaDao: PizzaDaoInterface
> ```
>
> 하지만 구체적인 `DevPizzaPricerService` 오브젝트에서 저자는 그 필드를 `val`로 정의합니다.
>
> ```scala
> val pizzaDao = DevPizzaDao
> ```
>
> 이것이 동작하는 이유는 Scala의 *균일 접근 원칙(Uniform Access Principle, UAP)* 구현 덕분입니다. 그 출처는 Scala 용어집(Scala Glossary)으로, 다음과 같이 말합니다. "균일 접근 원칙은 변수와 파라미터가 없는 함수(parameterless functions)가 같은 문법을 사용하여 접근되어야 한다고 명시한다. Scala는 파라미터가 없는 함수의 호출 지점(call sites)에 괄호를 둘 수 없게 함으로써 이 원칙을 지원한다. 그 결과, 파라미터가 없는 함수 정의는 클라이언트 코드에 영향을 주지 않고 `val`로 바뀔 수 있으며, 그 반대도 가능하다."

#### 5. 단위 테스트 또는 드라이버 앱 만들기 (Create unit tests or a driver app)

실제 세계에서는 코드를 테스트하기 위해 단위 테스트를 만들겠지만, 여기서는 우리의 목적상 해법을 시연하기 위해 `@main` 애플리케이션을 만들겠습니다. 이 `pizzaPricingMain` 애플리케이션은 `DevPizzaPricerService`(이것은 다시 `DevPizzaDao`를 사용합니다)를 사용하여 개발 환경에서 피자 가격 책정기 알고리즘에 접근하고 사용하는 방법을 보여줍니다.

```scala
@main def pizzaPricingMain =

    object PizzaService extends PizzaService
    import PizzaService.*
    import DevPizzaPricerService.*

    // 피자를 만듭니다
    val p = Pizza(
        Medium,
        Regular,
        Seq(Cheese, Pepperoni, Mushrooms)
    )

    // 피자 가격을 결정합니다
    val pizzaPrice = calculatePizzaPrice(p)

    // 피자와 그 가격을 출력합니다 (비함수형 방식으로)
    println(s"Pizza: $p")
    println(s"Price: $pizzaPrice")
```

그 코드를 실행하면 다음 출력을 보게 됩니다.

```
Pizza: Pizza(Medium,Regular,List(Cheese, Pepperoni, Mushrooms))
Price: 11.0
```

이 "배선(wiring)" 기법은 Recipe 6.11 "Using Traits to Create Modules"에서 설명한 Scala의 *모듈(modules)* 개념을 사용합니다. 이 접근 방식의 중요한 이점은 개발 환경에서는 `DevPizzaPricerService`를, 테스트에서는 `TestPizzaPricerService`를, 운영에서는 `ProductionPizzaPricerService`를 사용할 수 있다는 것입니다. 이 기법은 Scala 언어의 기능들을 사용하여 여러분만의 의존성 주입 프레임워크를 만듭니다.

> **불투명 타입으로 이 코드 개선하기 (Improve This Code with Opaque Types)**
>
> 이 코드는 적어도 두 곳에서 *불투명 타입(opaque types)* 을 사용하여 개선될 수 있습니다.
>
> - `postalCode` 필드에 `String`을 사용하는 곳
> - 통화(currency)를 나타내는 필드에 `BigDecimal`을 사용하는 곳
>
> 불투명 타입을 사용하면, 그 필드들을 다음과 같이 대신 참조할 수 있습니다.
>
> ```scala
> case class Address(
>     street1: String,
>     street2: Option[String],
>     city: String,
>     state: String,
>     postalCode: PostalCode   // String 대신 PostalCode를 사용합니다
> )
>
> // PizzaDaoInterface 안에서:
> def getToppingPrices(): Map[Topping, Money]
> ```
>
> 이와 같은 커스텀 타입을 만드는 것의 장점은, (a) 모두가 그 필드들이 무엇인지 더 쉽게 알 수 있고, (b) 우편번호 필드를 검증하는 것과 같이 그 타입들을 다루기 위한 커스텀 메서드와 확장 메서드를 추가할 수 있다는 것입니다. 이 레시피를 비교적 단순하게 유지하기 위해 저자는 이러한 변경을 하지 않았지만, 더 의미 있는 타입을 만들기 위해 이 기법을 사용하는 데 관심이 있다면 Recipe 23.7 "Creating Meaningful Type Names with Opaque Types"를 참고하세요.
