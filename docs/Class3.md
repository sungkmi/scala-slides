
Scala Class #3

# Trait

---

## Trait

```scala
import java.time.{Duration, LocalDateTime}
trait Visitor {
  def id: String
  def createdAt: LocalDateTime
  def age = Duration.between(createdAt, LocalDateTime.now())
}
case class Anonymous(
  id: String,
  createdAt: LocalDateTime = LocalDateTime.now()
) extends Visitor
case class User(
  id: String,
  email: String,
  createdAt: LocalDateTime = LocalDateTime.now()
) extends Visitor
```

---

## Sealed Trait

```scala
import java.time.{Duration, LocalDateTime}
sealed trait Visitor {
  def id: String
  def createdAt: LocalDateTime
  def age = Duration.between(createdAt, LocalDateTime.now())
}
case class Anonymous(
  id: String,
  createdAt: LocalDateTime = LocalDateTime.now()
) extends Visitor
case class User(
  id: String,
  email: String,
  createdAt: LocalDateTime = LocalDateTime.now()
) extends Visitor
```

--

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

def missingCase(v: Visitor) = v match {
  case User(_, _, _) => "Got a user"
}

// Exiting paste mode, now interpreting.

<pastie>:18: warning: match may not be exhaustive.
It would fail on the following input: Anonymous(_, _)
def missingCase(v: Visitor) = v match {
                              ^
missingCase: (v: Visitor)String

scala>
```

---

## Final

```scala
sealed trait Visitor { /* ... */ }
final case class User(/* ... */) extends Visitor
final case class Anonymous(/* ... */) extends Visitor
```

---

## Algebraic Data Types

### Product Type Pattern

Foo가 a(type A), b(type B), c(type C)를 가졌으면:

```scala
case class Foo(a: A, b: B, c: C)
```

--

### Sum Type Pattern

Foo가 A, B 또는 C이면:

```scala
sealed trait Foo
final case class A() extends Foo
final case class B() extends Foo
final case class C() extends Foo
```

---

## Missing Patterns

|       | And          | Or       |
| ----- | ------------ | -------- |
| Is-a  | ???          | Sum Type |
| Has-a | Product Type | ???      |

---

### Foo has A, B or C

```scala
trait Foo {
  def bar: Bar
}
sealed trait Bar
final case class A() extends Bar
final case class B() extends Bar
final case class C() extends Bar
```

--

```scala
sealed trait Foo
final case class Bar(a: A) extends Foo
final case class Baz(b: B) extends Foo
final case class Qux(c: C) extends Foo
```

---

### Foo is A, B and C

```scala
trait A
trait B
trait C
trait Foo extends A with B with C
```

---

## Diamond Inheritance Problems

```scala
trait Foo {
  def sound: String
}
trait Bar extends Foo {
  override def sound = "Baaaaaaar!"
}
trait Baz extends Foo {
  override def sound = "Baaaaaaaaz!"
}
case object Qux extends Bar with Baz
```

--

```scala
scala> Qux.sound
res0: String = Baaaaaaaaz!

scala>
```

---

## Polymorphism vs Pattern Matching

### Polymorphism Example

```scala
trait Animal {
  def sound: String
}
class Cat extends Animal {
  override def sound: String = "mew"
}
class Dog extends Animal {
  override def sound: String = "bowwow"
}
```

--

### Pattern Matching Example

```scala
sealed trait Integer {
  def abs: Int = this match {
    case NaturalNumber(n) => n
    case Zero => 0
    case NegativeInteger(n) => -n
  }
}
final case class NaturalNumber(n: Int) extends Integer
final case class NegativeInteger(n: Int) extends Integer
final case object Zero extends Integer
```

---

## 연습: Shape

`Shape` trait을 구현하세요.

다음 abstract method를 갖고 있도록 하세요.

>* sides는 면의 숫자를 구합니다.
>*  perimeter는 둘레를 구합니다.
>* area는 면적을 구합니다.

--

Shape를 확장한 다음 세 클래스를 구현해보세요:

> Circle, Rectangle, Square

(참고: $\pi$ 의 값은  `math.Pi`로 구할 수 있습니다.)

---

## 연습: 신호등

`Red`, `Green`, `Yellow`로 구성된

 `TrafficLight` 데이터타입을 구현해보세요

--

`Red` $\rightarrow$ `Green` $\rightarrow$ `Yellow` $\rightarrow$ `Red` 로 순환되는 method `next`를 구현해보세요.

* 메소드를 클래스 외부에 구현해보세요.
* 메소드를 클래스 내부에 polymorphism으로 구현해보세요.
* 메소드를 클래스 내부에 패턴 매칭으로 구현해보세요.
* 어떤 방법이 가장 나은가요? 그 이유는?

---

## Recursive Data

```scala
sealed trait PeanoNumber {
  @annotation.tailrec
  def toInt: Int = this match {
    case Zero => 0
    case Succ(prev) => 1 + prev.toInt
  }
}
final case object Zero extends PeanoNumber
final case class Succ(prev: PeanoNumber) extends PeanoNumber
```

```scala
scala> Zero.toInt
res1: Int = 0

scala> Succ(Succ(Succ(Zero))).toInt
res2: Int = 3

scala>
```

--

```scala
sealed trait Tree
final case object Leaf extends Tree
final case class Node(left: Tree, right: Tree) extends Tree
```

```
def height(tree: Tree): Int = tree match {
  case Leaf => 0
  case Node(left, right) => 1 + (height(left) max height(right))
}
```

```scala
scala> height(Node(Node(Node(Leaf, Leaf), Leaf), Leaf))
res3: Int = 3

scala>
```

---

### Tail Recurson

```scala
sealed trait PeanoNumber {
  @annotation.tailrec
  def toInt(acc: Int = 0): Int = this match {
    case Zero => acc
    case Succ(prev) => prev.toInt(acc + 1)
  }
}
final case object Zero extends PeanoNumber
final case class Succ(prev: PeanoNumber) extends PeanoNumber
```

```scala
scala> Zero.toInt()
res3: Int = 0

scala> Succ(Succ(Succ(Zero))).toInt()
res4: Int = 3

scala>
```
---

## 연습: IntList

다음 `IntList`정의를 활용해서 `length` 메소드를 구현해보세요.

```scala
sealed trait IntList
final case object End extends IntList
final case class Pair(head: Int, tail: IntList) extends IntList
```

```scala
val example = Pair(1, Pair(2, Pair(3, End)))

assert(example.length == 3)
assert(example.tail.length == 2)
assert(End.length == 0)
```

---

## 연습: Calculator

다음을 구현해보세요

> * `Expression`은 `Addition`, `Subtraction`, 혹은 `Number`입니다.
> * `Addition`은 left와 right `Expression`을 가지고 있습니다.
> * `Subtraction`은 left와 right `Expression`을 가지고 있습니다.
> * `Number`는 `Double`타입의 value 하나를 갖고 있습니다,

--

`Expression`을 실제로 계산해서 `Double`로 바꿔주는

 `eval` 메소드를 구현해보세요

--

실패할 수 있는 계산도 있습니다.

Double에는 NaN이라는 값이 이를 나타내긴 합니다만, 계산 실패와 그 이유를 명시적으로 나타내면 더 좋을 겁니다. 

적절한 데이터 타입 Success, Failure을 구현하시고 eval을 그에 맞추어 수정해보세요.

--

`Division`과 `SquareRoot` 연산을 추가해보세요. 다음을 통과할 수 있어야 합니다.

```scala
assert(Addition(SquareRoot(Number(-1.0)), Number(2.0)).eval ==
       Failure("Square root of negative number"))
assert(Addition(SquareRoot(Number(4.0)), Number(2.0)).eval ==
       Success(4.0))
assert(Division(Number(4), Number(0)).eval ==
       Failure("Division by zero"))
```

