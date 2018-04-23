Scala Class #1

# Object

---

## Scala 설치
1. [Java 8 JDK 설치](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
2. [Scala 설치](http://www.scala-lang.org/download/)
3. (optional) Spark / Zeppelin 설치

---

## Web playgrounds
* [scalafiddle.io](https://scalafiddle.io/)
* [scastie](https://scastie.scala-lang.org/)

---

## 실습
[Exercise 1](https://github.com/sungkmi/scala-slides/blob/master/exercise1.md)

---

Scala의 특징(1/2)
## 일반적 장점
1. 간결한 표현 <!-- .element: class="fragment" data-fragment-index="1" -->
2. 강력한 타입체크 <!-- .element: class="fragment" data-fragment-index="2" -->
3. 준수한 성능 <!-- .element: class="fragment" data-fragment-index="3" -->
4. 방대한 라이브러리 <!-- .element: class="fragment" data-fragment-index="4" -->

--

Scala의 특징(2/2)
## Spark에서
1. Spark native <!-- .element: class="fragment" data-fragment-index="1" -->
2. Compile-time type checked<!-- .element: class="fragment" data-fragment-index="2" -->

---

## Expressions, Types, and Values

1. expression <!-- .element: class="fragment" data-fragment-index="1" -->
2. (compile time) type <!-- .element: class="fragment" data-fragment-index="2" -->
3. (evaluate/run time) value <!-- .element: class="fragment" data-fragment-index="3" -->

--

```scala
scala> 1 + 1
res0: Int = 2
```

---

Object와 상호작용하기(1/3)

### Method without argument

```scala
"hello".toUpperCase
// res: String = HELLO
```

--

Object와 상호작용하기(2/3)

### Method with single argument

```scala
"hello".take(3)
// res: String = hel
"hello".take(2)
// res: String = he
```

--

Object와 상호작용하기(3/3)

### Infix Operator

```scala
1 + 1
// res: Int = 2
1.+(1)
// res: Int = 2
"hello" take 2
// res: String = he
```

---

Literal Objects (1/6)

### Numbers

```scala
1
// res: Int = 1
1.0
// res: Double = 1.0
1.0f
// res: Float = 1.0
1L
// res: Long = 1
```

--

Literal Objects (2/6)

### Booleans

```scala
true
// res: Boolean = true
false
// res: Boolean = false
```

--

Literal Objects (3/6)

### Char

```scala
'a'
// res: Char = a
```

--

Literal Objects (4/6)

### String

```scala
"Hello world"
// res: String = Hello world
```

--

Literal Objects (5/6)

### Null

``` scala
null
// res: Null = null
```

--

Literal Objects (6/6)

### Unit

```scala
()

:type ()
// Unit

println("Hello world")
// Hello world

:type println("Hello world")
// Unit
```

--

Q. 다음 Scala literals들의 타입은?

```scala
42

true

123L

42.0
```



---

## Object 정의하기

### 빈 Object

```scala
object Test
```

```scala
Test
// res: Test.type = Test$@7957aa57
```

--

### 이런 식도 가능함

```scala
object Test {}
```

```scala
Test
// res: Test.type = Test$@5b476930
```

---

### Method 있는 Object

```scala
object Test2 {
  def name: String = "홍길동"
}
```

```scala
Test2.name
// res: String = 홍길동
```

> repl 상에서 한 번에 여러 줄을 입력할 때는
>
> `:paste` 명령 사용

--

### Parameter 받는 Method

```scala
object Test3 {
  def hello(to: String): String = "Hello " + to
}
```

```scala
Test3.hello("Alice")
// res: String = Hello Alice
Test3 hello "Alice"
// res: String = Hello Alice
```

--

### Default Parameter Values

```scala
object Test4 {
  def hello(firstName: String, lastName: String = "!"): String =
    "Hello " + firstName + " " + lastName
}
```
```scala
Test4.hello("Tony", "Stark")
// res: String = Hello Tony Stark
Test4 hello "Tony"
// res: String = Hello Tony !
```
--

### Keyword Parameters

``` scala
Test4.hello(lastName = "Tony", firstName = "Stark")
// res: String = Hello Tony Stark
```

--

### Apply Method

```scala
object inc {
  def apply(n: Int): Int = n + 1
}
```

```scala
inc.apply(1)
// res: Int = 2
inc(1)
// res: Int = 2
```

---

### Fields

```scala
object Test5 {
  val name: String = "Alice"
  def hello(other: String): String = name + " says hi to " + other
}
```

```scala
Test5.name
// res: String = Alice
Test5.hello("Bob")
// res: String = Alice says io to Bob
```

---

### Methods vs Fields

``` scala
object Test6 {
  val simpleField = {
    println("Evaluating simpleField")
    42
  }
  lazy val lazyField = {
    println("Evaluating lazyField")
    42
  }
  def noParameterMethod = {
     println("Evaluating noParameterMethod")
     42
  }
}
```

```scala
Test6
// Evaluating simpleField
// res: Test6.type = Test5$@498102a4
```

--

```scala
Test6.simpleField
// res: Int = 42
Test6.simpleField
// res: Int = 42
```

```scala
Test6.lazyField
// Evaluating lazyField
// res: Int = 42
Test6.lazyField
// res: Int = 42
```

```scala
Test6.noParameterMethod
// Evaluating noParameterMethod
// res: Int = 42
Test6.noParameterMethod
// Evaluating noParameterMethod
// res: Int = 42
```

--

Q. 실행결과는?

```scala
object foo {
  def a = {
    println("a")
    1
  }
  val b = {
    println("b")
    a + 2
  }
  def c = {
    println("c")
    a
    b + "c"
  }
}
```

```scala
foo.c + foo.b + foo.a
```



---

## Hello world

HelloWorld.scala

```scala
object HelloWorld {
  def main(args: Array[String]): Unit = {
    println("Hello, world!")
  }
}
```

```bash
> scalac HelloWorld.scala
```

```bash
> scala HelloWorld
```

--

## 더 간단하게: extends App

HelloWorld.scala

```scala
object HelloWorld extends App {
  println("Hello, world!")
}
```

---

## Blocks
### Semicolon-separated
```scala
scala> {1; 2; 3}
<console>:12: warning: a pure expression does nothing in statement position; multiline expressions might require enclosing parentheses
       {1; 2; 3}
        ^
<console>:12: warning: a pure expression does nothing in statement position; multiline expressions might require enclosing parentheses
       {1; 2; 3}
           ^
res0: Int = 3
```

--

### newline-separated
```scala
{
  println("This is a side-effect")
  println("This is a side-effect as well")
  3
}
// This is a side-effect
// This is a side-effect as well
// res: Int = 3
```

--

### 계산중간값에 이름붙이기
```scala
def name: String = {
  val title = "Doctor"
  val name = "Strange"
  title + " " + name
}

name
// res: String = Doctor Strange
```

---

## Conditionals

```scala
if (1 < 2) "Yes" else "No"
// res: String = Yes

:type if (1 < 2) "Yes" else "No"
// String

if(1 < 2) println("Yes") else println("No")
// Yes

:type if(1 < 2) println("Yes") else println("No")
// Unit
```



---

## Writing Methods

1. 메소드 입출력 타입 파악 및 메소드 타입 선언부 작성 <!-- .element: class="fragment" data-fragment-index="1" -->
2. 테스트케이스 준비 <!-- .element: class="fragment" data-fragment-index="2" -->
3. 코드 실행 <!-- .element: class="fragment" data-fragment-index="3" -->
4. 메소드 구현 <!-- .element: class="fragment" data-fragment-index="4" -->
5. 코드 재실행 <!-- .element: class="fragment" data-fragment-index="5" -->


---

Homework(1/2)
### Method 작성 연습

1. `Double` 하나를 입력받아 그 제곱을 출력하는 `square` method를 가진 `calc` object를 작성해보세요.

2. `Double` 하나를 입력받아 그 세제곱을 출력하는 `cubic` method를 `calc` object 안에 작성해보세요.
   구현할 때 `square`를 이용해보세요.

3. 위의 두 method를 `Double` 뿐만 아니라 `Int` 값도 입력받을 수 있도록 해 보세요.


--

Homework(2/2)
### Object 작성 연습

다음 object들을 작성해보세요.

| object         | realName        | position      |
| -------------- | --------------- | ------------- |
| CaptainAmerica | "Steve Rogers"  | "Leader"      |
| IronMan        | " Tony Stark"   | "Range Deler" |
| Hulk           | "Bruce Banner"  | "Tanker"      |
| Thor           | " Thor Odinson" | "God"         |

