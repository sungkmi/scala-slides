
Scala Class #6

# Collection Library 2

---

## For Comprehensions

--

### Map

```scala
Seq(1, 2, 3).map(_ * 2)
```

```scala
for {
  x <- Seq(1, 2, 3)
} yield x * 2
```

--

### FlatMap

```scala
val data = Seq(Seq(1), Seq(2, 3), Seq(4, 5, 6))
```

```scala
data.flatMap(_.map(_ * 2))
```

```scala
for {
  subseq <- data
  element <- subseq
} yield element * 2
```

--

```scala
for {
  x <- a
  y <- b
  c <- c
} yield e
```

```scala
a.flatMap(x => b.flatMap(y => c.map(z => e)))
```

```scala
for(x <-a ; y <- b; z <- c) yield e
```

--

### Filter / withFilter

```scala
for { x <- Seq(-2, -1, 0, 1, 2) if x > 0 } yield x
// res: Seq[Int] = List(1, 2)
```

--

### Zip

```scala
Seq(1, 2, 3) zip Seq(4, 5, 6)
// res: Seq[(Int, Int)] = List((1, 4), (2, 5), (3, 6))
```

```scala
for{
  (x, y) <- Seq(1, 2, 3) zip Seq(4, 5, 6)
} yield x + y
// res: Seq[Int] = List(5, 7, 9)
```

--

### ZipWithIndex

```scala
for(x <- Seq(1, 2, 3).zipWithIndex) yield x
// res: Seq[(Int, Int)] = List((1,0), (2,1), (3,2))
```

--

### Intermediate Result

```scala
for {
  x <- Seq(1, 2, 3)
  square = x * x
  y <- Seq(4, 5, 6)
} yield square * y
// res: Seq[Int] = List(4, 5, 6, 16, 20, 24, 36, 45, 54)
```

---

## Options

```scala
sealed abstract class Option[+A] extends Product with Serializable
object None extends Option[Nothing] with Product with Serializable
final case class Some[+A](value: A) extends Option[A] with Product with Serializable
```

[Option API Docs](https://www.scala-lang.org/api/current/scala/Option.html)

[참고: Product with Serializable](https://typelevel.org/blog/2018/05/09/product-with-serializable.html)

---

### Example

```scala
def readInt(str: String): Option[Int] = if (str matches "\\d+") Some(str.toInt) else None
```

```scala
scala> readInt("123")
res0: Option[Int] = Some(123)

scala> readInt("abc")
res1: Option[Int] = None
```

---

### getOrElse

```scala
readInt("abc").getOrElse(0)
```

---

### Pattern matching

```scala
readInt("123") match {
  case Some(number) => number + 1
  case None => 0
}
```

---

### Options as Sequences

```scala
sealed abstract class Option[+A] extends Product with Serializable {
  def getOrElse(default: A): A
  def isEmpty: Boolean
  def isDefined: Boolean = !isEmpty
  def filter(func: A => Boolean): Option[A]
  def find(func: A => Boolean): Option[A]
  def map[B](func: A => B): Option[B]
  def flatMap(func: A => Option[B]): Option[B]
  def foreach(func: A => Unit): Unit
  def foldLeft[B](initial: B)(func: (B, A) => B): B
  def foldRight[B](initial: B)(func: (A, B) => B): B
}
```

--

```scala
def sum(optionA: Option[Int], optionB: Option[Int]): Option[Int] =
  optionA.flatMap(a => optionB.map(b=> a + b))

sum(readInt("1"), readInt("2"))
sum(readInt("1"), readInt("b"))
sum(readInt("a"), readInt("2"))
```

--

```scala
def sum(optionA: Option[Int], optionB: Option[Int]): Option[Int] = for {
  a <- optionA
  b <- optionB
} yield a + b
```

---

### Avoiding Null Pointer Exceptions

```scala
//proper way to wrap calls to java apis
val map = new java.util.HashMap[String, String]()
map.put("key1", "value1")
val maybeValue1 = Option(map.get("key1")) //Some("value1")
val maybeValue2 = Option(map.get("key2")) //None
```

---

## Either

```scala
sealed abstract class Either[+A, +B] extends Product with Serializable
final case class Left[+A, +B](value: A) extends Either[A, B] with Product with Serializable
final case class Right[+A, +B](value: B) extends Either[A, B] with Product with Serializable
```

[Either API Docs](https://www.scala-lang.org/api/current/scala/util/Either.html)

---

## Try

```scala
sealed abstract class Try[+T] extends Product with Serializable
final case class Failure[+T](exception: Throwable) extends Try[T] with Product with Serializable
final case class Success[+T](value: T) extends Try[T] with Product with Serializable
```

[Try API Docs](https://www.scala-lang.org/api/current/scala/util/Try.html)

---

## Maps

```scala
scala> val example = Map("a" -> 1, "b" -> 2, "c" -> 3)
example: scala.collection.immutable.Map[String,Int] = Map(a -> 1, b -> 2, c -> 3)
```

```scala
scala> "a" -> 1
res2: (String, Int) = (a,1)
```

--

```scala
example("a") // The same as example.apply("a")
// res: Int = 1
example.get("a")
// res: Option[Int] = Some(1)
```

```scala
example("d")
java.util.NoSuchElementException: key not found: d
example.get("d")
// res: Option[Int] = None
```

--

```scala
example.getOrElse("d", -1)
// res: Int = -1
```

--

```scala
example.contains("a")
// res: Boolean = true
```

--

```scala
example.size
// res: Int = 3
```

--

```scala
example.+("c" -> 10, "d" -> 11, "e" -> 12)
// res: scala.collection.immutable.Map[java.lang.String,Int] =
Map(e -> 12, a -> 1, b -> 2, c -> 10, d -> 11)
```

```scala
example.-("b", "c")
// res: scala.collection.immutable.Map[java.lang.String,Int] =
Map(a -> 1)
```

```scala
example + ("d" -> 4) - "c"
// res: scala.collection.immutable.Map[java.lang.String,Int] =
//        Map(a -> 1, b -> 2, d -> 4)
```

---

### SortedMaps

```scala
scala> Map("a"->1) + ("b"->2) + ("c"->3) + ("d"->4) + ("e"->5)
// res: scala.collection.immutable.Map[String,Int] =
//        Map(e -> 5, a -> 1, b -> 2, c -> 3, d -> 4)
```

```scala
scala> scala.collection.SortedMap("a"->1) + ("b"->2) + ("c"->3) + ("d"->4) + ("e"->5)
// res: scala.collection.SortedMap[String,Int] =
//        Map(a -> 1, b -> 2, c -> 3, d -> 4, e -> 5)
```

---

## Sets

```scala
scala> Set(1, 1, 2, 2, 3, 3)
res5: scala.collection.immutable.Set[Int] = Set(1, 2, 3)
```

---

## RDDs

[Scala Docs](https://spark.apache.org/docs/2.3.0/api/scala/index.html#org.apache.spark.rdd.RDD)

[Programming Guide](https://spark.apache.org/docs/2.3.0/rdd-programming-guide.html)

---

