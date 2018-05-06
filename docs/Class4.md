
Scala Class #4

# Generic

---

## Box

```scala
final case class Box[A](value: A)
```

```scala
scala> Box(0)
res0: Box[Int] = Box(0)

scala> Box("foo")
res1: Box[String] = Box(foo)

scala>
```

--

### Generic Sum Type

```scala
sealed trait Result[A]
final case class Success[A](result: A) extends Result[A]
final case class Failure[A](reason: String) extends Result[A]
```

--

### Generic Method

```scala
def generic[A](in: A): A = in
```

```scala
scala> generic[String]("foo")
res2: String = foo

scala> generic("foo")
res3: String = foo

scala> generic(1)
res4: Int = 1

scala>
```

--

### Type Parameter Syntax

Class

> (case) class Name[A, B, ...] (...) { ... }

Trait

> trait Name[A, B, ...] { ... }

Method

> def name[A, B, ...] (..) { ... }

---

### Exercise: Generic List

다음 `IntList`를 Generic을 써서 일반적인 타입의 데이터를 담을 수 있도록 수정한 `LinkedList` 타입을 작성해 보세요.

```scala
sealed trait IntList
final case class End() extends IntList
final case class Pair(head: Int, tail: IntList) extends IntList
```

---

## Functions

### Function Type

> (A, B, ...) => C

* A, B, ... : Input Parameter 타입
* C : 결과 타입

(`참고`: 만약 파라미터가 한 개면 괄호를 생략할 수 있습니다.)

> A => B

--

### Function Literal

> (paramter: type, ... ) => expression

--

```scala
scala> val sayHi = () => "Hi!"
sayHi: () => String = $$Lambda$1293/614090237@268f0ff3

scala> sayHi()
res5: String = Hi!
```
```scala
scala> val add1 = (x: Int) => x + 1
add1: Int => Int = $$Lambda$1301/1649942262@1e36baca

scala> add1(10)
res6: Int = 11
```
```scala
scala> val sum = (x: Int, y:Int) => x + y
sum: (Int, Int) => Int = $$Lambda$1302/1423824868@37f7ce20

scala> sum(1, 2)
res7: Int = 3
```

--

```scala
def oneAndTwo[A](f: (Int, Int) => A): A = f(1, 2)
```

```scala
scala> oneAndTwo((x, y) => x + y)
res8: Int = 3

scala> oneAndTwo((x, y) => s"($x, $y)")
res9: String = (1, 2)

scala>
```

---

### Placeholder syntax

```scala
scala> (_: Int) + 1 // (x: Int) => x + 1
res10: Int => Int = $$Lambda$1303/77077379@4446a191

scala> res10(1)
res11: Int = 2

scala>
```

--

만약 파라미터 타입을 추론할 수 있는 경우엔 타입을 생략할 수 있습니다.

```scala
_ + _     // (a, b) => a + b
foo(_)    // (a) => foo(a)
foo(_, b) // (a) => foo(a, b)
_(foo)    // (a) => a(foo)
```

--

```scala
case class Pair[A, B](a: A, b: B) {
  def calc[C](f: (A, B) => C): C = f(a, b)
}
```

```scala
scala> Pair(1, 2).calc(_ + _)
res12: Int = 3

scala> Pair("foo", "bar").calc(_ + _)
res13: String = foobar

scala>
```

---

### Methods to Functions

```scala
object Adder {
  def add1(n: Int): Int = n + 1
}
```

```scala
scala> Adder.add1
<console>:13: error: missing argument list for method add1 in object Adder
Unapplied methods are only converted to functions when a function type is expected.
You can make this conversion explicit by writing `add1 _` or `add1(_)` instead of `add1`.
       Adder.add1
             ^

scala> Adder.add1 _
res14: Int => Int = $$Lambda$1447/179809345@5ad67d82

scala> Adder.add1(_)
res15: Int => Int = $$Lambda$1453/525192807@1f02ce3

scala>
```

--

```scala
case class Box[A](a: A) {
  def modify[B](f: A => B): B = f(a)
}
```

```scala
scala> Box(42).modify(Adder.add1)
res16: Int = 43

scala> Box(42) modify Adder.add1
res17: Int = 43

scala> Box("foo") modify Adder.add1
<console>:15: error: type mismatch;
 found   : Int => Int
 required: String => ?
       Box("foo") modify Adder.add1
```

---

### Curring

```scala
def curring(x: Int)(y: Int): Int = x + y
```

```scala
scala> curring(1)(2)
res18: Int = 3

scala>
```

--

```scala
def generic[A, B](a: A, f: A => B): B = f(a)
generic(1, _ + 1) //compile error
```

```scala
def genericCurring[A, B](a: A)(f: A => B): B = f(a)
```

```scala
scala> genericCurring(1)(_ + 1)
res19: Int = 2
```

---

## Exercise: Pairs

`Pair` 를 구현해보세요. 다음이 성립하도록 해야 합니다.

```scala
val pair1 = Pair("foo", 1)
val pair2 = Pair(2, "bar")
assert(pair1.one == "foo")
assert(pair1.two == 1)
assert(pair2.one == 2)
assert(pair2.two == "bar")
```

---

## Tuple

* Pair를 더 많은 원소를 가질 수 있도록 일반화한 Scala의 built-in 타입
* Tuple1[A], Tuple2[A, B], ..., Tuple22[A, B, C, ...]
* (A, B, C, ...)의 형태로도 사용가능 (syntactic sugar)

--

### 선언

```scala
scala> Tuple2("foo", 1)
res20: (String, Int) = (foo,1)

scala> ("foo", 1)
res21: (String, Int) = (foo,1)

scala> ("foo", 1, true)
res22: (String, Int, Boolean) = (foo,1,true)

scala>
```

--

### 패턴매칭

```scala
scala> ("foo", 1) match { case (a, b) => a + b }
res23: String = foo1

scala>
```

--

### 원소 접근

```scala
scala> val x = (1, "b", true)
x: (Int, String, Boolean) = (1,b,true)

scala> x._1
res29: Int = 1

scala> x._3
res30: Boolean = true

scala>
```

---

## Exercise: Generic Sum

두 개의 서브타입 `First`와 `Second`를 가지는 `Sum`타입을 구현해보세요. 다음 테스트가 통과되도록 하셔야 합니다.

```scala
assert(First[Int, String](1).value == 1)
assert(Second[Int, String]("foo").value == "foo")
val sum: Sum[Int, String] = Second("foo")
val matched: String = sum match {
  case First(x) => x.toString
  case Second(x) => x
}
asert(matched == "foo")
```

---

## Exercise: Maybe

두 개의 서브타입 `Just`와 `Empty`를 가지는 `Maybe`타입을 구현해보세요. 다음 테스트가 통과되도록 하셔야 합니다.

```scala
def divide(a: Int, b: Int): Maybe[Int] = b match {
  case 0 => Empty
  case _ => Just(a/b)
}
assert(divide(10, 2) == Just(5))
assert(divide(10, 0) == Empty)
```

---

## Exercise: map

앞서 작성했던 `LinkedList`에 `map`메소드를 구현해보세요.

타입 시그니쳐는 다음과 같습니다.

```scala
def map[B](f: A => B): LinkedList[B] = ???
```

다음 테스트를 통과해야 합니다.

```scala
val l = Pair(1, Pair(2, Pair(3, End)))
assert(l.map(_ + 1) == Pair(2, Pair(3, Pair(4, End))))
```

---

## Exercise: fold

`LinkedList`에 `fold`메소드를 구현해보세요.

타입 시그니쳐는 다음과 같습니다.

```scala
def fold[B](init: B)(f: (A, B) => B): B = ???
```

다음 테스트를 통과해야 합니다.

```scala
val list0 = Pair(1, Pair(2, Pair(3, Pair(4, End))))
assert(list0.fold(0)(_ + _) == 10)
val list1 = Pair(1, Pair(2, Pair(3, Pair(4, End))))
assert(list1.fold(1)(_ * _) == 24)
```

---

## Exercise: flatMap

`LinkedList`에 `flatMap`메소드를 구현해보세요.

타입 시그니쳐는 다음과 같습니다.

```scala
def flatMap[B](f: A => LinkedList[B]): LinkedList[B] = ???
```

다음 테스트를 통과해야 합니다.

```scala
val list = Pair(1, Pair(2, Pair(3, Pair(4, End))))
val filtered = list.flatMap{
  case n if x % 2 == 0 => End
  case n => Pair(n, End)
}
assert(filtered == Pair(1, Pair(3, End)))
```
---

## Variance (주의: 어려움)

```scala
sealed trait Maybe[A]
final case class Just[A](value: A) extends Maybe[A]
final case class Empty[A]() extends Maybe[A]
```

--

```scala
sealed trait Maybe[A]
final case class Just[A](value: A) extends Maybe[A]
final case object Empty extends Maybe[???]
```

--

```scala
sealed trait Maybe[A]
final case class Just[A](value: A) extends Maybe[A]
final case object Empty extends Maybe[Nothing]
```

--

```scala
scala> val perhaps: Maybe[Int] = Empty
<console>:13: error: type mismatch;
 found   : Empty.type
 required: Maybe[Int]
Note: Nothing <: Int (and Empty.type <: Maybe[Nothing]), but trait Maybe is invariant in type A.
You may wish to define A as +A instead. (SLS 4.5)
       val perhaps: Maybe[Int] = Empty
                           ^

scala>
```

--

```scala
sealed trait Maybe[+A]
final case class Just[A](value: A) extends Maybe[A]
final case object Empty extends Maybe[Nothing]
```

---

| 이름          | Type    | 의미                                                       |
| ------------- | ------- | ---------------------------------------------------------- |
| invariant     | Foo[T]  | -                                                          |
| covariant     | Foo[+T] | 만약 A가 B의 서브타입이면, Foo[A]는 Foo[B]의 서브타입이다. |
| contravariant | Foo[-T] | 만약 A가 B의 수퍼타입이면, Foo[A]는 Foo[B]의 서브타입이다. |

*contravariant가 주로 쓰이는 곳은 함수의 인자 자리입니다.*

---

### Function Type

```scala
trait Function0[+R] {
  def apply: R
}

trait Function1[-A, +B] {
  def apply(a: A): B
}

trait Function2[-A, -B, +C] {
  def apply(a: A, b: B): C
}

// ...
```

--

```scala
case class Box[A](a: A) {
  def map[B](f: Function1[A, B]): Box[B] = Box(f(a))
}
```

---

### Contravariant Position

```scala
case class Box[+A](a: A) {
  def set(a: A): Box[A] = Box(a) // 컴파일 안됨!
}
```

--

```scala
case class Box[+A](a: A) {
  def set[AA >: A](a: AA): Box[AA] = Box(a)
}
```

---

### Type Bound

| Type Bound | Meaning               |
| ---------- | --------------------- |
| A <: B     | A is a supertype of B |
| A >: B     | A is a supertype of B |

---

### Contravariant Position Case 2

```scala
sealed trait Sum[+A, +B] {
  def flatMap[C](f: B => Sum[A, C]): Sum[A, C] = this match {
    case Failure(v) => Failure(v)
    case Success(v) => f(v)
  }
}
final case class Failure[A](value: A) extends Sum[A, Nothing]
final case class Success[B](value: B) extends Sum[Nothing, B]
```

--

```scala
scala> :paste
// Entering paste mode (ctrl-D to finish)

sealed trait Sum[+A, +B] {
  def flatMap[C](f: B => Sum[A, C]): Sum[A, C] = this match {
    case Failure(v) => Failure(v)
    case Success(v) => f(v)
  }
}
final case class Failure[A](value: A) extends Sum[A, Nothing]
final case class Success[B](value: B) extends Sum[Nothing, B]
```
```scala
// Exiting paste mode, now interpreting.

<pastie>:12: error: covariant type A occurs in contravariant position in type B => Sum[A,C] of value f
  def flatMap[C](f: B => Sum[A, C]): Sum[A, C] = this match {
                 ^

scala>
```

--

```scala
sealed trait Sum[+A, +B] {
  def flatMap[AA >: A, C](f: B => Sum[AA, C]): Sum[AA, C] = this match {
    case Failure(v) => Failure(v)
    case Success(v) => f(v)
  }
}
final case class Failure[A](value: A) extends Sum[A, Nothing]
final case class Success[B](value: B) extends Sum[Nothing, B]
```

---

## Exercise: Calculator

다음에서 `eval` 메소드를 구현해보세요

```scala
sealed trait Expression {
  def eval: Sum[String, Double] = ???
}
final case class Addition(left: Expression, right: Expression) extends Expression
final case class Subtraction(left: Expression, right: Expression) extends Expression
final case class Division(left: Expression, right: Expression) extends Expression
final case class SquareRoot(value: Expression) extends Expression
final case class Number(value: Double) extends Expression
```

다음 테스트를 통과해야합니다.

```scala
assert(Addition(Number(1), Number(2)).eval == Success(3))
assert(SquareRoot(Number(-1)).eval == Failure("Square root of negative number"))
assert(Division(Number(4), Number(0)).eval == Failure("Division by zero"))
assert(Division(Addition(Subtraction(Number(8), Number(6)), Number(2)), Number(2)).eval == Success(2.0))
```

