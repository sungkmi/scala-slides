
Scala Class #7

# Type Class

---

## Example: Show type class

```scala
trait Show[A] {
  def show(a: A): String
}
object Show {
  val intCanShow: Show[Int] = new Show[Int] {
    def show(int: Int): String = s"int $int"
  }
}
```

```scala
import Show._
println(intCanShow.show(42))
```

--

### Implicit parameters / Implicit values

```scala
object Show {
  def show[A](a: A)(implicit sh: Show[A]): String = sh.show(a)

  implicit val intCanShow: Show[Int] = new Show[Int] {
    def show(int: Int): String = s"int $int"
  }
}
```
```scala
import Show._
println(show(42))
```

--

#### 참고: Implicit values 선언방법

```scala
implicit val example1 = ???
implicit var example2 = ???
implicit def example3 = ???
implicit object example4 {}
```

--

### Implicitly

```scala
def implicitly[T](implicit e: T): T = e
```

```scala
scala> import Show._
import Show._

scala> implicitly[Show[Int]].show(42)
res0: String = int 42
```

--

### Context bound

```scala
def show[A: Show](a: A): String = implicitly[Show[A]].show(a)
```

https://docs.scala-lang.org/tutorials/FAQ/context-bounds.html

--

### Apply

```scala
def apply[A](implicit sh: Show[A]): Show[A] = sh
```

```scala
def show[A: Show](a: A): String = Show.apply[A].show(a)
```

```scala
def show[A: Show](a: A): String = Show[A].show(a)
```

--

> Type Class Interface Pattern

```scala
trait ExampleTypeClass[A] {
  def doSomething(in: A): Foo
}
object ExampleTypeClass {
  def apply[A](implicit instance: ExampleTypeClass[A]): ExampleTypeClass = instance
}
```

--

### 중간요약

```scala
trait Show[A] {
  def show(a: A): String
}
object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh
  def show[A: Show](a: A): String = Show[A].show(a)
  implicit val intCanShow: Show[Int] = new Show[Int] {
    def show(int: Int): String = s"int $int"
  }
}
```

---

## Implicit Classes

```scala
implicit class ShowOps[A: Show](a: A) {
  def show: String = Show[A].show(a)
}
```

```scala
import Show._
println(42.show)
```

--

### Value Classes

```scala
implicit class ShowOps[A](val a: A) extends AnyVal {
  def show(implicit sh: Show[A]): String = sh.show(a)
}
```

https://docs.scala-lang.org/overviews/core/value-classes.html

--

### 중간요약 2

```scala
trait Show[A] {
  def show(a: A): String
}
object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh
  def show[A: Show](a: A): String = Show[A].show(a)
  implicit class ShowOps[A](val a: A) extends AnyVal {
    def show(implicit sh: Show[A]): String = sh.show(a)
  }
  implicit val intCanShow: Show[Int] = new Show[Int] {
    def show(int: Int): String = s"int $int"
  }
}
```

---

## 다른 타입도 지원해보자!

```scala
implicit val stringCanShow: Show[String] = new Show[String] {
  def show(str: String): String = s"string $str"
}
```

--

### 중복이 많으니까 공통부분을 뽑아내자

```scala
def instance[A](f: A => String): Show[A] = new Show[A] {
  def show(a: A): String = f(a)
}

implicit val intCanShow: Show[Int] = instance(int => s"int $int")
implicit val stringCanShow: Show[String] = instance(str => s"string $str")
```

--

### 2.12 only: SAM conversion

```scala
implicit val intCanShow: Show[Int] = int => s"int $int"
implicit val stringCanShow: Show[String] = str => s"string $str"
```

--

### 중간요약 3

```scala
trait Show[A] {
  def show(a: A): String
}
object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh
  def show[A: Show](a: A): String = Show[A].show(a)
  implicit class ShowOps[A](val a: A) extends AnyVal {
    def show(implicit sh: Show[A]): String = sh.show(a)
  }
  implicit val intCanShow: Show[Int] = int => s"int $int"
  implicit val stringCanShow: Show[String] = str => s"string $str"
}
```

---

## Tuple도 지원해봅시다!

```scala
implicit def tupleCanShow[A, B](implicit sa: Show[A], sb: Show[B]): Show[(A, B)] = {
  case (a, b) => s"tuple (${a.show}, ${b.show})"
}
```

```scala
implicit def tupleCanShow[A: Show, B: Show]: Show[(A, B)] = {
  case (a, b) => s"tuple (${a.show}, ${b.show})"
}
```

--

### 최종 결과물

```scala
trait Show[A] {
  def show(a: A): String
}
object Show {
  def apply[A](implicit sh: Show[A]): Show[A] = sh
  def show[A: Show](a: A): String = Show[A].show(a)
  implicit class ShowOps[A](val a: A) extends AnyVal {
    def show(implicit sh: Show[A]): String = sh.show(a)
  }
  implicit val intCanShow: Show[Int] = int => s"int $int"
  implicit val stringCanShow: Show[String] = str => s"string $str"
  implicit def tupleCanShow[A: Show, B: Show]: Show[(A, B)] = {
    case (a, b) => s"tuple (${a.show}, ${b.show})"
  }
}
```

