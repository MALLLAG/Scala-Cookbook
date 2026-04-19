# Chapter 1. Command-Line Tasks

## 챕터 개요

Scala 3 여정을 시작하면서 가장 처음 마주하게 되는 단계 중 하나는 command line에서 작업하는 것입니다. 예를 들어 Scala를 설치한 뒤에는 운영체제의 명령줄에서 `scala`라고 입력하여 **REPL(Read/Eval/Print/Loop)** 을 시작해 볼 수 있습니다. 또는 한 파일로 이루어진 작은 "Hello, world" 프로젝트를 만들어 컴파일하고 실행해 보고 싶을 수도 있습니다. 이런 명령줄 작업은 많은 사람들이 Scala를 처음 접할 때 거치는 관문이기 때문에, 본 챕터에서 가장 먼저 다루고 있습니다.

**REPL**은 명령줄 기반의 **shell** 입니다. Scala와 서드파티 라이브러리가 어떻게 동작하는지 작은 테스트로 확인해 볼 수 있는 playground 같은 공간입니다. Java의 JShell, Ruby의 irb, Python shell 또는 IPython, Haskell의 ghci 같은 환경을 사용해 보신 분이라면 Scala의 REPL도 매우 친숙하게 느껴지실 것입니다. 운영체제 명령줄에서 `scala`를 입력하기만 하면 REPL이 시작되며, Scala 표현식을 입력하면 셸에서 즉시 evaluate됩니다.

Scala 코드를 테스트하고 싶을 때마다 REPL은 훌륭한 놀이터 환경이 되어 줍니다. 본격적인 프로젝트를 만들 필요 없이 테스트 코드를 REPL에 입력해서 동작을 확인할 때까지 실험해 볼 수 있습니다. REPL은 매우 중요한 도구이기 때문에, 이 챕터의 첫 두 레시피에서 가장 중요한 기능들을 시연합니다.

### Ammonite REPL에 대하여

REPL이 훌륭하긴 하지만, 유일한 선택지는 아닙니다. **Ammonite REPL**은 원래 Scala 2를 위해 만들어졌으며, Scala 2 REPL 보다 훨씬 더 많은 기능을 제공합니다. 대표적인 기능은 다음과 같습니다.

- GitHub 및 Maven 저장소에서 코드를 가져오는 기능
- 세션을 저장하고 복원하는 기능
- 보기 좋게 pretty-printed 출력
- multiline 편집

이 책이 집필되는 시점에서는 Ammonite가 아직 Scala 3로 포팅 중이지만, 많은 주요 기능은 이미 동작합니다. 이러한 기능의 사용 예시는 Recipe 1.3에서 확인하실 수 있습니다.

### sbt, scalac, scala에 대하여

마지막으로, Scala 프로젝트를 빌드할 때에는 일반적으로 **sbt** 같은 빌드 도구를 사용하며, 이는 Chapter 17에서 다룹니다. 하지만 한두 개 파일만으로 이루어진 작은 Scala 애플리케이션을 컴파일해서 실행하고 싶다면, Java에서 `javac`와 `java`를 사용하듯이 `scalac` 명령으로 컴파일하고 `scala` 명령으로 실행할 수 있습니다. 이 과정은 Recipe 1.4에서 시연하며, 그 후 Recipe 1.6에서는 JAR 파일로 패키징한 애플리케이션을 `java`나 `scala` 명령으로 실행하는 방법을 보여 드립니다.

---

## 1.1 Getting Started with the Scala REPL

### Problem

Scala REPL의 사용을 시작하고, 기본 기능들을 활용해 보고자 합니다.

### Solution

Java, Python, Ruby, Haskell 같은 언어에서 REPL 환경을 사용해 보셨다면, Scala REPL도 매우 친숙하게 느껴지실 것입니다. REPL을 시작하려면 운영체제 명령줄에서 `scala`라고 입력하시면 됩니다. REPL이 시작되면 초기 메시지가 나타난 뒤 `scala>` 프롬프트가 표시됩니다.

```
$ scala
Welcome to Scala 3.0
Type in expressions for evaluation. Or try :help.

scala> _
```

이 프롬프트는 이제 Scala REPL을 사용 중임을 나타냅니다. REPL 환경 안에서는 온갖 종류의 실험과 표현식을 시도해 보실 수 있습니다.

```
scala> val x = 1
x: Int = 1

scala> val y = 2
y: Int = 2

scala> x + y
res0: Int = 3

scala> val x = List(1, 2, 3)
x: List[Int] = List(1, 2, 3)

scala> x.sum
res1: Int = 6
```

위 예시에서 보이듯이 다음과 같은 특성이 있습니다.

- 명령을 입력한 뒤, REPL 출력은 **데이터 타입 정보를 포함해서** 표현식의 결과를 보여 줍니다.
- 세 번째 예시처럼 **변수 이름을 지정하지 않으면**, REPL이 `res0`, `res1` 등으로 시작하는 자체 변수를 생성합니다. 여러분이 직접 만든 변수처럼 이 변수 이름을 사용하실 수 있습니다.

```
scala> res1.getClass
res2: Class[Int] = int

scala> res1 + 2
res3: Int = 8
```

초보자든 숙련된 개발자든 상관없이, Scala의 기능과 본인의 알고리즘이 어떻게 동작하는지를 빠르게 확인하기 위해 매일 REPL에서 코드를 작성합니다.

#### Tab completion

REPL을 더 효과적으로 사용하기 위한 몇 가지 간단한 비법이 있습니다. 한 가지 방법은 **tab completion** 을 사용하여 어떤 객체에서 호출 가능한 메서드들을 확인하는 것입니다. 탭 자동 완성이 어떻게 동작하는지 보시려면 숫자 `1`을 입력한 뒤 점(`.`)을 찍고 Tab 키를 누르시면 됩니다. REPL이 `Int` 인스턴스에서 사용 가능한 수십 개의 메서드를 응답으로 보여 줍니다.

```
scala> 1.
!=                  finalize            round
##                  floatValue          self
%                   floor               shortValue
&                   formatted           sign
*                   getClass            signum
many more here ...
```

메서드 이름의 앞부분을 입력한 뒤 Tab 키를 누르면, 표시되는 메서드 목록을 좁힐 수도 있습니다. 예를 들어 `List`에서 사용 가능한 모든 메서드를 보시려면 `List(1).`을 입력한 뒤 Tab 키를 눌러 보세요. 200개가 넘는 메서드가 나타납니다. 하지만 `List`에서 문자 `to`로 시작하는 메서드에만 관심이 있으시다면, `List(1).to`를 입력하고 Tab을 눌러 보세요. 출력 결과가 다음과 같이 축소됩니다.

```
scala> List(1).to
to             toIndexedSeq     toList     toSet         toTraversable
toArray        toIterable       toMap      toStream      toVector
toBuffer       toIterator       toSeq      toString
```

### Discussion

저는 REPL을 작은 실험을 많이 하는 데에 사용하며, Scala가 자동으로 수행하는 일부 타입 변환을 이해하는 데에도 도움이 됩니다. 예를 들어 제가 처음 Scala를 사용했을 때, 다음 코드를 REPL에 입력해 보고도 변수 `x`의 타입이 무엇인지 모르고 있었습니다.

```
scala> val x = (3, "Three", 3.0)
val x: (Int, String, Double) = (3,Three,3.0)
```

REPL을 사용하면 이와 같은 테스트를 실행한 뒤 변수에서 `getClass`를 호출하여 그 타입을 쉽게 확인할 수 있습니다.

```
scala> x.getClass
val res0: Class[? <: (Int, String, Double)] = class scala.Tuple3
```

Scala를 처음 사용할 때에는 결과 라인의 일부가 읽기 어려울 수 있지만, `=` 오른쪽의 텍스트를 보면 타입이 `Tuple3` 클래스임을 알 수 있습니다.

REPL의 `:type` 명령을 사용해도 비슷한 정보를 확인할 수 있습니다. 다만 현재는 `Tuple3`이라는 이름은 보여 주지 않습니다.

```
scala> :type x
(Int, String, Double)
```

하지만 다른 여러 상황에서는 일반적으로 유용합니다.

```
scala> :type 1 + 1.1
Double

scala> :type List(1,2,3).map(_ * 2.5)
List[Double]
```

비록 위 예시들은 간단하지만, 더 복잡한 코드나 익숙하지 않은 라이브러리로 작업하실 때 REPL이 매우 유용하다는 것을 느끼게 되실 것입니다.

#### sbt 내부에서 REPL 실행하기

sbt 셸 안에서도 Scala REPL 세션을 시작하실 수 있습니다. Recipe 17.5, "Understanding Other sbt Commands"에서 보이듯이, sbt 프로젝트 내부에서 sbt 셸을 시작하신 뒤 아래처럼 진행하면 됩니다.

```
$ sbt
MyProject> _
```

그런 다음 `console` 혹은 `consoleQuick` 명령을 사용하실 수 있습니다.

```
MyProject> console
scala> _
```

- `console` 명령은 프로젝트의 소스코드 파일들을 컴파일한 뒤 클래스패스에 올려놓고 REPL을 시작합니다.
- `consoleQuick` 명령은 프로젝트의 dependencies은 클래스패스에 포함하지만, 프로젝트 소스코드 파일은 컴파일하지 않고 REPL을 시작합니다. 이 두 번째 방법은 코드가 컴파일되지 않을 때나, 의존성(라이브러리)과 함께 테스트 코드를 실행해 보고 싶을 때 유용합니다.

---

## 1.2 Loading Source Code and JAR Files into the REPL

### Problem

소스 코드 파일에 Scala 코드가 있고, 이 코드를 REPL에서 사용하고 싶은 경우입니다.

### Solution

`:load` 명령을 사용하면 소스 코드 파일을 REPL 환경으로 읽어 들일 수 있습니다. 예를 들어 *models*라는 하위 디렉터리의 *Person.scala* 파일에 다음과 같은 코드가 있다고 가정해 봅시다.

```scala
class Person(val name: String):
    override def toString = name
```

다음과 같이 해당 소스 코드를 실행 중인 REPL 환경으로 로드하실 수 있습니다.

```
scala> :load models/Person.scala
// defined class Person
```

코드가 REPL에 로드된 후에는, 새 `Person` 인스턴스를 생성하실 수 있습니다.

```
scala> val p = Person("Kenny")
val p: Person = Kenny
```

다만, 소스 코드에 `package` 선언이 있는 경우에는 주의해야 합니다.

```scala
// Dog.scala file
package animals
class Dog(val name: String)
```

이 경우 `:load` 명령은 실패합니다.

```
scala> :load Dog.scala
1 |package foo
  |^^^
  |Illegal start of statement
```

REPL에서 소스 코드 파일은 `package`를 사용할 수 없으므로, 이런 경우에는 이들을 JAR 파일로 컴파일한 뒤 REPL을 시작할 때 클래스패스에 포함시켜야 합니다. 예를 들어, 저는 제 Simple Test 라이브러리의 0.2.0 버전을 Scala 3 REPL에서 다음과 같이 사용합니다.

```
// start the repl like this
$ scala -cp simpletest_3.0.0-0.2.0.jar

scala> import com.alvinalexander.simpletest.SimpleTest.*

scala> isTrue(1 == 1)
true
```

이 책이 집필되는 시점에서는, 이미 실행 중인 REPL 세션에는 JAR를 추가하실 수 없습니다만, 이 기능은 향후에 추가될 수 있습니다.

### Discussion

또 알아 두시면 좋은 사실은, **현재 디렉터리에 있는 컴파일된 class 파일은 자동으로 REPL에 로드된다는 점**입니다. 예를 들어 이 코드를 *Cat.scala* 파일에 넣고 `scalac`로 컴파일하시면, *Cat.class* 파일이 생성됩니다.

```scala
case class Cat(name: String)
```

해당 class 파일과 같은 디렉터리에서 REPL을 시작하시면, 새 `Cat` 인스턴스를 생성하실 수 있습니다.

```
scala> Cat("Morris")
val res0: Cat = Cat(Morris)
```

Unix 시스템에서는 이 기법을 사용하여 REPL 환경을 커스터마이즈할 수 있습니다. 방법은 다음과 같습니다.

1. 홈 디렉터리에 *repl*이라는 하위 디렉터리를 만듭니다. 제 경우에는 이 디렉터리를 */Users/al/repl*로 생성합니다. (원하는 이름을 사용하실 수 있습니다.)
2. 해당 디렉터리에 원하는 `*.class` 파일을 넣어 둡니다.
3. 그 디렉터리에서 REPL을 시작하기 위한 alias 또는 shell script를 작성합니다.

저의 경우, *~/repl* 디렉터리에 *Repl.scala*라는 파일을 두고 다음과 같은 내용을 담아 둡니다.

```scala
import sys.process.*

def clear = "clear".!
def cmd(cmd: String) = cmd.!!
def ls(dir: String) = println(cmd(s"ls -al $dir"))
def help =
    println("\n=== MY CONFIG ===")
    "cat /Users/Al/repl/Repl.scala".!

case class Person(name: String)
val nums = List(1, 2, 3)
```

그런 다음 이 코드를 `scalac`로 컴파일하여 그 디렉터리에 클래스 파일을 생성합니다. 이후 다음과 같이 REPL을 시작하는 alias를 만들어 사용합니다.

```
alias repl="cd ~/repl; scala; cd -"
```

이 alias는 저를 *~/repl* 디렉터리로 이동시키고, REPL을 시작하며, REPL을 종료했을 때 원래의 현재 디렉터리로 돌아오도록 합니다.

또 다른 접근법으로는 *repl*이라는 셸 스크립트를 만들어 실행 가능하게 한 뒤, `~/bin` 디렉터리(또는 `PATH` 상의 다른 곳)에 두는 방법이 있습니다.

```sh
#!/bin/sh

cd ~/repl
scala
```

셸 스크립트는 서브프로세스로 실행되기 때문에, REPL을 종료하시면 원래 디렉터리로 돌아오게 됩니다.

이 방식으로 설정하시면, REPL이 시작될 때 여러분의 커스텀 메서드들이 로드되므로 `scala` 셸 안에서 바로 사용하실 수 있습니다.

```
clear        // clear the screen
cmd("ps")    // run the 'ps' command
ls(".")      // run 'ls' in the current directory
help         // displays my Repl.scala file as a form of help
```

이 기법을 사용하여 REPL에서 활용하고 싶은 여러 커스텀 정의들을 미리 로드할 수 있습니다.

---

## 1.3 Getting Started with the Ammonite REPL

### Problem

Ammonite REPL을 사용하기 시작하고 그 기본 기능들을 이해하고자 합니다.

### Solution

**Ammonite REPL**은 기본 Scala REPL과 똑같이 동작합니다. 다운로드 및 설치 후 `amm` 명령으로 실행하시면 됩니다. 기본 Scala REPL과 마찬가지로, Scala 표현식을 평가하고, 변수 이름을 지정하지 않으면 자동으로 이름을 할당해 줍니다.

```
@ val x = 1 + 1
x: Int = 2

@ 2 + 2
res0: Int = 4
```

하지만 Ammonite는 훨씬 많은 추가 기능을 제공합니다. 다음 명령으로 셸 프롬프트를 변경하실 수 있습니다.

```
@ repl.prompt() = "yo: "

yo: _
```

그다음, *foo*라는 하위 디렉터리의 *Repl.scala*라는 파일에 이러한 Scala 표현식이 담겨 있다고 가정해 봅시다.

```scala
import sys.process.*

def clear = "clear".!
def cmd(cmd: String) = cmd.!!
def ls(dir: String) = println(cmd(s"ls -al $dir"))
```

이 내용은 다음 명령으로 Ammonite REPL에 import 하실 수 있습니다.

```
@ import $file.foo.Repl, Repl.*
```

그러면 Ammonite에서 이 메서드들을 사용하실 수 있습니다.

```
clear        // clear the screen
cmd("ps")    // run the 'ps' command
ls("/tmp")   // use 'ls' to list files in /tmp
```

마찬가지로, *foo*라는 하위 디렉터리의 *simpletest_3.0.0-0.2.0.jar*라는 JAR 파일을 Ammonite의 `$cp` 변수를 사용하여 `amm` REPL 세션에 import 할 수 있습니다.

```scala
// import the jar file
import $cp.foo.`simpletest_3.0.0-0.2.0.jar`

// use the library you imported
import com.alvinalexander.simpletest.SimpleTest.*
isTrue(1 == 1)
```

`import $ivy` 명령을 사용하면 Maven Central(그리고 기타 저장소)에서 의존성을 가져와 현재 셸에서 사용할 수 있습니다.

```
yo: import $ivy.`org.jsoup:jsoup:1.13.1`
import $ivy.$

yo: import org.jsoup.Jsoup, org.jsoup.nodes.{Document, Element}
import org.jsoup.Jsoup

yo: val html = "<p>Hi!</p>"
html: String = "<p>Hi!</p>"

yo: val doc: Document = Jsoup.parse(html)
doc: Document = <html> ...

yo: doc.body.text
res2: String = "Hi!"
```

Ammonite의 내장 `time` 명령을 사용하면 코드 실행에 걸리는 시간을 측정할 수 있습니다.

```
@ time(Thread.sleep(1_000))
res2: (Unit, FiniteDuration) = ((), 1003788992 nanoseconds)
```

Ammonite의 자동 완성 기능도 인상적입니다. 다음과 같은 표현식을 입력하신 뒤, 점(`.`)을 찍고 Tab을 눌러 보세요.

```
@ Seq("a").map(x => x.
```

그러면 Ammonite가 `x`에서 사용 가능한 많은 메서드 목록을 표시합니다. `x`는 `String`이므로 다음과 같은 메서드들이 나옵니다.

```
def intern(): String
def charAt(x$0: Int): Char
def concat(x$0: String): String
much more output here ...
```

이것이 유용한 이유는 메서드 이름뿐만 아니라 **입력 매개변수와 반환 타입**까지 함께 보여 주기 때문입니다.

### Discussion

Ammonite의 기능 목록은 매우 방대합니다. 또 하나의 유용한 기능은 Unix의 *.bashrc*나 *.bash_profile* 같은 **시작 설정 파일**을 사용할 수 있다는 것입니다. *~/.ammonite/predef.sc* 파일에 다음과 같이 표현식을 넣어 두시면 됩니다.

```scala
import sys.process.*

repl.prompt() = "yo: "
def clear = "clear".!
def cmd(cmd: String) = cmd.!!
def ls(dir: String) = println(cmd(s"ls -al $dir"))
def reset = repl.sess.load()  // similar to the scala repl ':reset' command
```

이후 Ammonite REPL을 시작하시면 프롬프트가 `yo:` 로 변경되며, 이 메서드들 또한 사용 가능해집니다.

또 하나의 훌륭한 기능은 **REPL 세션을 저장**하여 그 시점까지 작업한 모든 내용을 저장할 수 있다는 것입니다. 이를 테스트하시려면 REPL에서 변수를 생성하고 세션을 저장해 보시면 됩니다.

```scala
val remember = 42
repl.sess.save()
```

그다음 또 다른 변수를 생성합니다.

```scala
val forget = 0
```

이제 세션을 다시 로드해 보시면, `remember` 변수는 그대로 유지되지만 `forget` 변수는 "잊혀진" 것을 확인하실 수 있습니다.

```
@ repl.sess.load()
res3: SessionChanged = SessionChanged(removedImports = Set('forget),
addedImports = Set(), removedJars = Set(), addedJars = Set())

@ remember
res4: Int = 42

@ forget
    |val res5 = forget
    |          ^^
    |          Not found: forget
```

또한 이름을 다르게 붙여서 여러 세션을 저장하고 로드하는 것도 가능합니다.

```scala
// do some work
val x = 1
repl.sess.save("step 1")

// do some more work
val y = 2
repl.sess.save("step 2")

// reload the first session
repl.sess.load("step 1")

x   // this will be found
y   // this will not be found
```

더 많은 기능에 대해서는 Ammonite 공식 문서를 참고하시길 바랍니다.

---

## 1.4 Compiling with scalac and Running with scala

### Problem

Scala 애플리케이션을 빌드할 때 일반적으로 sbt 혹은 Mill 같은 빌드 도구를 사용하지만, 작은 테스트 프로그램을 컴파일하고 실행할 때에는 Java에서 `javac`와 `java`를 사용하는 것처럼 좀 더 기본적인 도구를 사용하고 싶을 수 있습니다.

### Solution

작은 프로그램은 `scalac`으로 컴파일하고 `scala`로 실행하시면 됩니다. 예를 들어 *Hello.scala*라는 Scala 소스 파일에 다음과 같이 작성했다고 가정해 봅시다.

```scala
@main def hello = println("Hello, world")
```

명령줄에서 `scalac`으로 컴파일합니다.

```
$ scalac Hello.scala
```

그런 다음 `scala`로 실행하는데, 이때 `scala` 명령에 생성한 `@main` 메서드의 이름을 전달하시면 됩니다.

```
$ scala hello
Hello, world
```

`@main` 메서드를 선언하여 명령줄로부터 원하는 매개변수를 받을 수도 있습니다. 예를 들어 다음처럼 `String`과 `Int`를 받는 예시가 가능합니다.

```scala
@main def hello(name: String, age: Int): Unit =
    println(s"Hello, $name, I think you are $age years old.")
```

이 코드를 `scalac`으로 컴파일한 뒤 다음과 같이 실행하실 수 있습니다.

```
$ scala hello "Lori" 44
Hello, Lori, I think you are 44 years old.
```

두 번째 접근법은 `object` 내부에 `main` 메서드를 선언하는 것으로, Java에서 main 메서드를 선언하는 것과 같습니다. Scala `main` 메서드의 시그니처는 다음과 같은 형태여야 합니다.

```scala
object YourObjectName:
    // the method must take `Array[String]` and return `Unit`
    def main(args: Array[String]): Unit =
        // your code here
```

Java에 익숙하시다면 위 Scala 코드는 다음 Java 코드와 유사합니다.

```java
public class YourObjectName {
    public static void main(String[] args) {
        // your code here
    }
}
```

### Discussion

클래스를 컴파일하고 실행하는 과정은 **classpath**를 포함해 Java와 동일한 개념을 따릅니다. 예를 들어 *Pizza.scala*라는 파일에 `Pizza`라는 클래스가 있고, 이 클래스가 `Topping` 타입에 의존한다고 가정해 봅시다.

```scala
class Pizza(val toppings: Topping*):
    override def toString = toppings.toString
```

그리고 `Topping`은 다음과 같이 정의되어 있다고 가정합니다.

```scala
enum Topping:
    case Cheese, Mushrooms
```

이 `Topping`이 *Topping.scala* 파일에 들어 있고, *classes*라는 하위 디렉터리의 *Topping.class*로 컴파일되어 있다면, *Pizza.scala*는 다음과 같이 컴파일합니다.

```
$ scalac -classpath classes Pizza.scala
```

참고로 `scalac` 명령에는 많은 추가 옵션들이 있습니다. 예를 들어 이전 명령에 `-verbose` 옵션을 추가하시면, `scalac`이 어떻게 동작하는지 보여 주는 추가 출력이 수백 줄씩 표시됩니다. 이러한 옵션들은 시간이 지나면서 변경될 수 있으므로, 추가 정보는 `-help` 옵션으로 확인해 보시길 바랍니다.

```
$ scalac -help
Usage: scalac <options> <source files>
where possible standard options include:
-P                Pass an option to a plugin, e.g. -P:<plugin>:<opt>
-X                Print a synopsis of advanced options.
-Y                Print a synopsis of private options.
-bootclasspath    Override location of bootstrap class files.
-classpath        Specify where to find user class files.
much more output here ...
```

#### Main methods

`main` 메서드를 컴파일하는 이야기를 하는 김에, Scala 3에서는 `main` 메서드를 **두 가지 방법**으로 선언할 수 있다는 점을 알아 두시면 좋습니다.

- 메서드에 `@main` annotation을 사용하는 방법
- `object` 내부에 올바른 시그니처의 `main` 메서드를 선언하는 방법

Solution에서 보이듯이, 매개변수를 받지 않는 간단한 `@main` 메서드는 다음처럼 선언할 수 있습니다.

```scala
@main def hello = println("Hello, world")
```

또한 명령줄에서 원하는 어떤 매개변수든 받도록 `@main` 메서드를 선언하실 수 있습니다. 예를 들어 다음은 `String`과 `Int`를 받는 예시입니다.

```scala
@main def hello(name: String, age: Int): Unit =
    println(s"Hello, $name, I think you are $age years old.")
```

`scalac`으로 컴파일한 뒤 다음과 같이 실행합니다.

```
$ scala hello "Lori" 44
Hello, Lori, I think you are 44 years old.
```

두 번째 접근법인 `object` 내부의 `main` 메서드는 Java의 main 메서드와 동일하며, 시그니처는 반드시 다음 형태여야 합니다.

```scala
object YourObjectName:
    // the method must take `Array[String]` and return `Unit`
    def main(args: Array[String]): Unit =
        // your code here
```

Java 코드로는 다음과 같습니다.

```java
public class YourObjectName {
    public static void main(String[] args) {
        // your code here
    }
}
```

---

## 1.5 Disassembling and Decompiling Scala Code

### Problem

Scala 코드가 어떻게 class 파일로 컴파일되는지 학습하거나, 특정 문제를 이해하기 위해 Scala 컴파일러가 소스 코드로부터 생성하는 bytecode를 살펴보고자 합니다.

### Solution

Scala 코드를 디스어셈블하는 주요 방법은 `javap` 명령을 사용하는 것입니다. decompiler를 사용해서 class 파일을 Java 소스 코드로 되돌리는 방법도 있으며, 이 옵션은 Discussion에서 다룹니다.

#### Using javap

Scala 소스 코드 파일은 일반적인 JVM class 파일로 컴파일되기 때문에, `javap` 명령을 사용해 디스어셈블할 수 있습니다. 예를 들어 *Person.scala*라는 파일에 다음과 같은 소스 코드가 있다고 가정해 봅시다.

```scala
class Person(var name: String, var age: Int)
```

그다음, `scalac`으로 해당 파일을 컴파일합니다.

```
$ scalac Person.scala
```

이제 결과로 생성된 *Person.class* 파일을 `javap`을 사용해 그 시그니처를 디스어셈블할 수 있습니다.

```
$ javap -public Person
Compiled from "Person.scala"
public class Person {
  public Person(java.lang.String, int);
  public java.lang.String name();
  public void name_$eq(java.lang.String);
  public int age();
  public void age_$eq(int);
}
```

이 결과는 `Person` 클래스의 public 시그니처, 즉 public API 혹은 인터페이스를 보여 줍니다. 이처럼 간단한 예제에서도 Scala 컴파일러가 어떤 작업을 수행하는지 보실 수 있습니다. `name()`, `name_$eq`, `age()`, `age_$eq` 같은 메서드들을 생성해 주는 것이죠. Discussion에서는 더 자세한 예시를 보여 드립니다.

추가 정보가 필요하시다면, `javap -private` 옵션을 사용해 보실 수 있습니다.

```
$ javap -private Person
Compiled from "Person.scala"
public class Person {
  private java.lang.String name;    // new
  private int age;                  // new
  public Person(java.lang.String, int);
  public java.lang.String name();
  public void name_$eq(java.lang.String);
  public int age();
  public void age_$eq(int);
}
```

`javap`에는 이 외에도 유용한 옵션들이 많습니다. 실제 Java 바이트코드를 구성하는 명령어를 보려면 `-c` 옵션을, 훨씬 더 자세한 내용을 보려면 `-verbose` 옵션을 추가하시면 됩니다. 모든 옵션에 대한 자세한 내용은 `javap -help`로 확인하실 수 있습니다.

### Discussion

`javap`으로 class 파일을 디스어셈블하는 것은 Scala가 어떻게 동작하는지를 이해하는 데 유용한 방법입니다. `Person` 클래스의 첫 번째 예시에서 보셨듯이, 생성자 매개변수 `name`과 `age`를 `var` 필드로 정의하면 여러 개의 메서드가 자동으로 생성됩니다.

두 번째 예시로, 두 필드에서 `var` 속성을 제거하여 다음과 같은 클래스 정의를 만들어 봅시다.

```scala
class Person(name: String, age: Int)
```

이 클래스를 `scalac`으로 컴파일한 뒤, 결과로 생성된 class 파일에 `javap`을 실행해 보시면 훨씬 짧아진 클래스 시그니처를 확인하실 수 있습니다.

```
$ javap -public Person
Compiled from "Person.scala"
public class Person {
  public Person(java.lang.String, int);
}
```

반대로, 두 필드에 `var`를 그대로 두고 해당 클래스를 `case class`로 바꾸면 Scala가 여러분을 대신해 생성해 주는 코드의 양이 크게 늘어납니다. 이를 확인하시려면 *Person.scala* 코드를 다음과 같이 case class로 바꾸십시오.

```scala
case class Person(var name: String, var age: Int)
```

이 코드를 컴파일하면 두 개의 출력 파일이 생성됩니다. *Person.class*와 *Person$.class* 입니다. 이 두 파일을 `javap`으로 디스어셈블해 봅시다.

```
$ javap -public Person
Compiled from "Person.scala"
public class Person implements scala.Product,java.io.Serializable {
  public static Person apply(java.lang.String, int);
  public static Person fromProduct(scala.Product);
  public static Person unapply(Person);
  public Person(java.lang.String, int);
  public scala.collection.Iterator productIterator();
  public scala.collection.Iterator productElementNames();
  public int hashCode();
  public boolean equals(java.lang.Object);
  public java.lang.String toString();
  public boolean canEqual(java.lang.Object);
  public int productArity();
  public java.lang.String productPrefix();
  public java.lang.Object productElement(int);
  public java.lang.String productElementName(int);
  public java.lang.String name();
  public void name_$eq(java.lang.String);
  public int age();
  public void age_$eq(int);
  public Person copy(java.lang.String, int);
  public java.lang.String copy$default$1();
  public int copy$default$2();
  public java.lang.String _1();
  public int _2();
}

$ javap -public Person$
Compiled from "Person.scala"
public final class Person$ implements scala.deriving.Mirror$Product,
java.io.Serializable {
  public static final Person$ MODULE$;
  public static {};
  public Person apply(java.lang.String, int);
  public Person unapply(Person);
  public java.lang.String toString();
  public Person fromProduct(scala.Product);
  public java.lang.Object fromProduct(scala.Product);
}
```

보시다시피, 클래스를 case class로 정의하면 Scala가 많은 양의 코드를 자동으로 생성합니다. 그리고 위 출력 결과는 해당 코드의 public 시그니처를 보여 줍니다. 이 코드에 대한 자세한 논의는 Recipe 5.14, "Generating Boilerplate Code with Case Classes"를 참고하시길 바랍니다.

#### About Those .tasty Files

눈치채셨겠지만, Scala 3는 컴파일 과정에서 *.class* 파일 외에도 *.tasty* 파일을 생성합니다. 이 파일들은 **TASTy format**으로 되어 있는데, TASTy는 **typed abstract syntax trees**에서 따온 약자입니다.

이 파일이 어떤 것인지에 대해, TASTy Inspection 문서에서는 다음과 같이 설명합니다. "TASTy 파일에는 소스 위치와 문서화 정보를 포함하여, **fully typed tree** 가 들어 있습니다. 코드로부터 semantic information를 분석하거나 추출하는 도구들에게 이상적입니다."

이 파일의 용도 중 하나는 **Scala 3와 Scala 2.13+ 간의 통합**입니다. Scala forward compatibility 페이지에 따르면 "Scala 2.13은 이 (TASTy) 파일들을 읽어서, 예를 들어 특정 의존성에서 어떤 term, type, implicit이 정의되어 있는지, 그리고 이들을 올바르게 사용하기 위해 어떤 코드를 생성해야 하는지 학습할 수 있습니다. 이 작업을 관리하는 컴파일러의 한 부분은 Tasty Reader라고 합니다."

---

## 1.6 Running JAR Files with Scala and Java

### Problem 

Scala 애플리케이션으로부터 JAR 파일을 만들었으며, 이를 `scala` 혹은 `java` 명령으로 실행하고자 합니다.

### Solution 

먼저 Recipe 17.1, "Creating a Project Directory Structure for sbt"에서 보이는 것처럼 기본 sbt 프로젝트를 생성합니다. 그런 다음 *project/plugins.sbt* 파일에 다음 줄을 추가하여 **sbt-assembly**를 프로젝트 구성에 포함시킵니다.

```scala
// note: this version number changes several times a year
addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.15.0")
```

그 프로젝트의 루트 디렉터리에 아래와 같은 *Hello.scala* 소스 코드 파일을 둡니다.

```scala
@main def hello = println("Hello, world")
```

그리고 sbt 셸에서 `assembly` 또는 `show assembly` 명령으로 JAR 파일을 생성합니다.

```
// option 1
sbt:RunJarFile> assembly

// option 2: shows the output file location
sbt:RunJarFile> show assembly
[info] target/scala-3.0.0/RunJarFile-assembly-0.1.0.jar
```

보시는 것처럼 `show assembly` 명령은 출력 JAR 파일이 작성된 위치를 출력합니다. 이 파일 이름의 맨 앞부분인 *RunJarFile*은 *build.sbt* 파일의 `name` 필드 값에서 유래한 것입니다. 마찬가지로, 파일 이름의 *0.1.0* 부분은 동일 파일의 `version` 필드에서 유래합니다.

```scala
lazy val root = project
    .in(file("."))
    .settings(
        name := "RunJarFile",
        version := "0.1.0",
        scalaVersion := "3.0.0"
    )
```

그다음, *Example*이라는 하위 디렉터리를 만들고, 그곳으로 이동한 뒤 JAR 파일을 그 디렉터리로 복사합니다.

```
$ mkdir Example
$ cd Example
$ cp ../target/scala-3.0.0/RunJarFile-assembly-0.1.0.jar .
```

sbt-assembly 플러그인이 필요한 모든 것을 JAR 파일에 패키징해 주기 때문에, 다음 `scala` 명령으로 `hello` main 메서드를 실행하실 수 있습니다.

```
$ scala -cp "RunJarFile-assembly-0.1.0.jar" hello
Hello, world
```

참고로 JAR 파일에 패키지로 된 `@main` 메서드가 여러 개 포함되어 있다면, 다음과 같이 메서드의 전체 경로를 명령 끝에 지정하여 실행할 수 있습니다.

```
scala -cp "RunJarFile-assembly-0.1.0.jar" com.alvinalexander.foo.mainMethod1
scala -cp "RunJarFile-assembly-0.1.0.jar" com.alvinalexander.bar.mainMethod2
```

### Discussion

만약 (a) JAR 파일을 `java` 명령으로 실행하려 하시거나, (b) `sbt assembly` 대신 `sbt package`로 JAR 파일을 생성하셨다면, **JAR 파일의 의존성들을 클래스패스에 수동으로 추가**해 주셔야 합니다. 예를 들어 `java` 명령으로 JAR 파일을 실행할 때에는 다음과 같은 명령을 사용해야 합니다.

```
$ java -cp "~/bin/scala3/lib/scala-library.jar:my-packaged-jar-file.jar" ↵
    foo.bar.Hello
Hello, world
```

참고로 라인 끝의 `foo.bar.Hello`를 포함하여 `java` 명령 전체가 **한 줄에** 있어야 합니다.

이 방식을 사용하시려면 *scala-library.jar* 파일을 찾아야 합니다. 제 경우에는 Scala 3 배포판을 수동으로 관리하기 때문에 위에 보여 드린 디렉터리에서 찾았습니다. Coursier 같은 도구로 Scala 설치를 관리하신다면, 다운로드된 파일은 다음 디렉터리 아래에서 찾으실 수 있습니다.

- macOS: `~/Library/Caches/Coursier/v1`
- Linux: `~/.cache/coursier/v1`
- Windows: `%LOCALAPPDATA%\Coursier\Cache\v1` (예: 사용자 이름이 Alvin인 경우 일반적으로 `C:\Users\Alvin\AppData\Local\Coursier\Cache\v1` 에 해당)

이 디렉터리 위치에 관한 최신 정보는 Coursier Cache 페이지를 참고하시길 바랍니다.

#### Why use sbt-assembly?

참고로, 여러분의 애플리케이션이 managed 혹은 unmanaged 의존성을 사용하는데 `sbt assembly` 대신 `sbt package`를 사용하신다면, **해당 의존성과 그 transitive 의존성을 모두 파악**하고, 이 JAR 파일들을 찾아서 클래스패스 설정에 포함시켜야 합니다. 이 때문에 `sbt assembly` 나 유사한 도구의 사용이 강력히 권장됩니다.
