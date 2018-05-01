
Scala Class #2

# Class

---

## Ammonite REPL

http://ammonite.io

맥에서는 Homebrew로 쉽게 설치할 수 있습니다.

```bash
$ brew install ammonite-repl
```



---

## Class 정의하기

```scala
class Person {
  val firstName = "Martin"
  val lastName = "Odersky"
  def name = firstName + " " + lastName
}
```

--

Object와는 다르게 값을 바로 만들어주지는 않습니다.

```scala
scala> Person
<console>:12: error: not found: value Person
       Person
       ^

scala>
```

---

### Class를 이용해 Object 만들기

```scala
scala> val martin = new Person
martin: Person = Person@7be2f29a

scala> martin.firstName
res1: String = Martin

scala>
```

--

```scala
scala> martin
res2: Person = Person@7be2f29a

scala> val martin2 = new Person
martin2: Person = Person@4b03cbad

scala> val martin3 = new Person
martin3: Person = Person@25c1f5ee

scala>
```

---


```scala
object greeter {
  def greet(p: Persion) =
    "Greeting, " + p.firstName + " " + p.lastName
}

greeter.greet(martin)
// res: String = Greeting, Martin Odersky

greeter greet martin2
// res: String = Greeting, Martin Odersky
```



---

## 기본 생성자 ( Constructor )

```scala
class Person(first: String, last: String) {
  val firstName = first
  val lastName = last
  def name = firstName + " " + lastName
}
```

--

```scala
scala> val heungjin = new Person("Heungjin", "Kim")
niklaus: Person = Person@1c411474

scala> heungjin.name
res3: String = Heungjin Kim

scala>
```

--

```scala
class Person(val firstName: String, val lastName: String) {
  def name = firstName + " " + lastName
}

new Person("Heungjin", "Kim").name
// res: String = Heungjin Kim
```

---

## Keyword Parameters & Default Parameter Values

```scala
class Person(val firstName: String, val lastName: String = "!") {
  def name = firstName + " " + lastName
}

new Person(lastName = "Last", firstName = "First")
// res: Person = Person(First,Last)

new Person("Heungjin")
// res: Person = Person(Heungjin,!)
```

---

## Apply

```scala
class Adder(amount: Int) {
  def apply(in: Int): Int = in + amount
}
```

```scala
scala> val add3 = new Adder(3)
add3: Adder = Adder@7552f05b

scala> add3(2)
res4: Int = 5

scala>
```

---

## 연습: Avengers Class

Avengers 클래스를 작성하고 이를 이용해 다음 object들을 생성해보세요.

| object name    | realName        | position      |
| -------------- | --------------- | ------------- |
| CaptainAmerica | "Steve Rogers"  | "Leader"      |
| IronMan        | " Tony Stark"   | "Range Deler" |
| Hulk           | "Bruce Banner"  | "Tanker"      |
| Thor           | " Thor Odinson" | "God"         |

---

## Scala Type Hierarchy

![Scala Type Hierarchy](https://docs.scala-lang.org/resources/images/tour/unified-types-diagram.svg)

--

### Nothing, Null

```scala
def badness = throw new Exception("Error")
// badness: Nothing
null
// res: Null = null
val bar = if(true) 123 else badness
// bar: Int = 123
val baz = if(false) "it worked" else null
// baz: String = null
```

---

## Companion Objects

```scala
class Timestamp(val seconds: Long)
object Timestamp {
  def apply(hours: Int, minutes: Int, seconds: Int): Timestamp =
    new Timestamp(hours*60*60 + minutes*60 + seconds)
}
Timestamp(1, 1, 1).seconds
// res: Long = 3661
```

--

```scala
scala> Timestamp
res1: Timestamp.type = Timestamp$@21263314

scala>
```

---

## Case Classes

```scala
case class Person(firstName: String, lastName: String) {
  def name = firstName + " " + lastName
}
```

```scala
val martin = new Person("Martin", "Odersky") // we have a class
// martin: Person = Person(Martin,Odersky)
Person // and a companion object too
// res: Person.type = Person
```

---

Features of a case class (1/4)

### Field for each constructor argument

```scala
martin.name
// res: String = Martin
```

--

Features of a case class (2/4)

### Default toString method

```scala
martin
// res: Person = Person(Martin,Odersky)
```

--

Features of a case class (3/4)

### Default equals, hashCode method

```scala
scala> val martin2 = new Person("Martin", "Odersky")
martin2: Person = Person(Martin,Odersky)

scala> martin equals martin2
res1: Boolean = true

scala> martin == martin2
res2: Boolean = true

scala>
```

--

Features of a case class (4/4)

### A copy method

```scala
scala> martin.copy(firstName = "Martin2")
res3: Person = Person(Martin2,Odersky)

scala> martin.copy(lastName = "Odersky2")
res4: Person = Person(Martin,Odersky2)

scala>
```

---

### 참고: Case Objects

```scala
case object Citizen {
  def firstName = "Martin"
  def lastName  = "Odersky"
  def name = firstName + " " + lastName
}
```

---

## Pattern Matching

```scala
case class Person(firstName: String, lastName: String)
```

```scala
object Stormtrooper {
  def inspect(person: Person): String = person match {
    case Person("Luke", "Skywalker") => "Stop, rebel scum!"
    case Person("Han", "Solo") => "Stop, rebel scum!"
    case Person(first, last) => s"Move along, $first"
  }
}
```

```scala
Stormtrooper.inspect(Person("Martin", "Odersky"))
// res: String = Move along, Martin
Stormtrooper.inspect(Person("Han", "Solo"))
// res: String = Stop, rebel scum!
```

---

Pattern Syntax(1/4)

## Name

```scala
Person(first, last)
```

--

Pattern Syntax(2/4)

## Underscore

```scala
Person(first, _)
```

--

Pattern Syntax(3/4)

## Literal

```scala
Person("Luke", "Skywalker")
```

--

Pattern Syntax(4/4)

## Another case class

--

그 외에도 많습니다.
