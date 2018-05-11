
Scala Class #5

Collection Library #1

# Seq

---

## 복습: Maybe, LinkedList

```scala
sealed trait Maybe[+A]
case object Empty extends Maybe[Nothing]
case class Just[A](a: A) extends Maybe[A]
```

```scala
sealed trait LinkedList[+A]
case object End extends LinkedList[Nothing]
final case class Pair[A](head: A, tail: LinkedList[A]) extends LinkedList[A]
object LinkedList {
  def apply[A](xs: A*): LinkedList[A] =
    if (xs.isEmpty) End else Pair(xs.head, apply(xs.tail: _*))
}
```

--

### Get

```scala
def get(n: Int): Maybe[A] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.get(0) == Just(1))
```

--

### Length

```scala
def length: Int = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.length == 5)
```

--

### Contains

```scala
def contains[AA >: A](elem: AA): Boolean = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.contains(1) == true)
assert(l.contains(0) == false)
```

--

### Find

```scala
def find(p: A => Boolean): Maybe[A] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.find(_ % 2 == 0) == Just(2))
assert(l.find(_ % 6 == 0) == Emppty)
```

--

### Filter

```scala
def filter(p: A => Boolean): LinkedList[A] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.find(_ % 2 == 0) == LinkedList(2, 4))
```

--

### Map

```scala
def map[B](f: A => B): LinkedList[B] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.map(_ + 1) == LinkedList(2, 3, 4, 5, 6))
```

--

### FlatMap

```scala
def flatMap[B](f: A => LinkedList[B]): LinkedList[B] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.flatMap(n => LinkedList(n, n + 1)) == LinkedList(1, 2, 2, 3, 3, 4, 4, 5, 5, 6))
```

--

### FoldLeft

```scala
def foldLeft[B](z: B)(op: (B, A) => B): B = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.foldLeft("")(_ + _ + 1) == "1121314151")
```

--

### FoldRight(1/2)

```scala
def foldRight[B](z: B)(op: (A, B) => B): B = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.foldRight("")(1 + _ + _) == "23456")
```

--

### Reverse

```scala
def reverse: LinkedList[A] = ???
```

```scala
val l = LinkedList(1, 2, 3, 4, 5)
assert(l.reverse == LinkedList(5, 4, 3, 2, 1))
```

> `hint`: foldLeft 이용!

--

### FoldRight(2/2)

`foldLeft`는 쉽게 tail recursion을 적용해 구현할 수 있는 반면, `foldRight`를 그냥 구현하면 tail recursion이 적용되지 않는 형태가 되기 쉽습니다.

`foldRight`을 `foldLeft`를 써서 구현해보세요.

---

## 실습

[Exercise 2](https://github.com/sungkmi/scala-slides/blob/master/exercise2.md)

---

## Seq

```scala
scala> val sequence = Seq(1, 2, 3)
sequence: Seq[Int] = List(1, 2, 3)

scala>
```

--

### Apply

```scala
scala> sequence.apply(0)
res0: Int = 1

scala> sequence(0)
res1: Int = 1
```

```scala
scala> sequence(3)
java.lang.IndexOutOfBoundsException: 3
  at scala.collection.LinearSeqOptimized.apply(LinearSeqOptimized.scala:63)
  at scala.collection.LinearSeqOptimized.apply$(LinearSeqOptimized.scala:61)
  at scala.collection.immutable.List.apply(List.scala:86)
  ... 28 elided

scala>
```

--

### Head / Tail

```scala
scala> sequence.head
res3: Int = 1

scala> sequence.tail
res4: Seq[Int] = List(2, 3)

scala> sequence.headOption
res5: Option[Int] = Some(1)

scala>
```

--

### Length

```scala
scala> sequence.length
res6: Int = 3

scala>
```

--

### Contains / Find / Filter

```scala
scala> sequence.contains(2)
res7: Boolean = true
```

```scala
scala> sequence.find(_ == 3)
res8: Option[Int] = Some(3)

scala> sequence.find(_ > 4)
res9: Option[Int] = None
```

```scala
scala> sequence.filter(_ > 1)
res10: Seq[Int] = List(2, 3)
```

--

### SortWith

```scala
scala> sequence.sortWith(_ >  _)
res11: Seq[Int] = List(3, 2, 1)
```

--

### Append / Prepend / Concat

```scala
scala> sequence.:+(4)
res12: Seq[Int] = List(1, 2, 3, 4)

scala> sequence :+ 4
res13: Seq[Int] = List(1, 2, 3, 4)
```

```scala
scala> sequence.+:(0)
res14: Seq[Int] = List(0, 1, 2, 3)

scala> 0 +: sequence
res15: Seq[Int] = List(0, 1, 2, 3)
```

```scala
scala> sequence ++ Seq(4, 5, 6)
res16: Seq[Int] = List(1, 2, 3, 4, 5, 6)
```

--

### Map

```scala
scala> sequence.map(_.toString)
res17: Seq[String] = List(1, 2, 3)
```

--

### FlatMap

```scala
scala> sequence.flatMap(x => Seq(x, x + 1))
res18: Seq[Int] = List(1, 2, 2, 3, 3, 4)
```

--

### Foreach

```scala
scala> sequence foreach println
1
2
3

scala>
```

--

### FoldLeft

```scala
scala> sequence.foldLeft("")(_ + _ + 1)
res19: String = 112131

scala> ("" /: sequence)(_ + _ + 1)
res20: String = 112131
```

--

### FoldRight

```scala
scala> sequence.foldRight("")(1 + _ + _)
res21: String = 234

scala> (sequence :\ "")(1 + _ + _)
res22: String = 234
```

---

## Scala API Docs

https://www.scala-lang.org/api/current/

---

## List

[List Api Docs](https://www.scala-lang.org/api/current/scala/collection/immutable/List.html)

--

### Nil

```scala
scala> Nil
res23: scala.collection.immutable.Nil.type = List()
```

--

### Cons

```scala
scala> val list = 1 :: 2 :: 3 :: Nil
list: List[Int] = List(1, 2, 3)

scala> 4 :: 5 :: list
res24: List[Int] = List(4, 5, 1, 2, 3)
```

--

### Apply

```scala
scala> List(1, 2, 3)
res25: List[Int] = List(1, 2, 3)
```

--

### Concat

```scala
scala> List(1, 2, 3) ::: List(4, 5, 6)
res26: List[Int] = List(1, 2, 3, 4, 5, 6)
```

--

### Pattern Matching

```scala
def map[A, B](list: List[A])(f: A => B): List[B] = list match {
  case Nil => Nil
  case x :: xs => f(x) :: map(xs)(f)
}
```

---

## IndexedSeq / Vector

```scala
scala> IndexedSeq(1, 2, 3)
res27: IndexedSeq[Int] = Vector(1, 2, 3)
```

---

## Queue

```scala
scala> import collection.immutable.Queue
import collection.immutable.Queue

scala> Queue(1, 2, 3)
res28: scala.collection.immutable.Queue[Int] = Queue(1, 2, 3)
```

---

## Range

```scala
scala> 1 to 10
res29: scala.collection.immutable.Range = Range 1 to 10

scala> (1 to 10).toList
res30: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

scala> (1 to 10 by 2).toList
res31: List[Int] = List(1, 3, 5, 7, 9)

scala> (1 until 10).toList
res32: List[Int] = List(1, 2, 3, 4, 5, 6, 7, 8, 9)
```

---

## Stream

참고: https://docs.scala-lang.org/overviews/collections/concrete-immutable-collection-classes.html#streams

---

## Exercise: RunLength

```scala
def runLength[A](l: List[A]): List[(A, Int)] = ???
```

```scala
assert(runLength("abbbcc".toList) ==
  List(('a', 1), ('b', 3), ('c', 2))
)
```

---

## Exercise: FizzBuzz

```scala
def fizzBuzz(n: Int)(cond: (Int, String)*): Unit = ???
```

```scala
fizzBuzz(20)((3, "Fizz"), (5, "Buzz"))
```

