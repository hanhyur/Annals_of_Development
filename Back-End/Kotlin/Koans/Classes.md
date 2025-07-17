# Classes

## Data classes
```kotlin
class Person

fun getPeople(): List<Person> {
    return listOf(Person("Alice", 29), Person("Bob", 31))
}

fun comparePeople(): Boolean {
    val p1 = Person("Alice", 29)
    val p2 = Person("Alice", 29)
    return p1 == p2  // should be true
}
```

### Data classes
Learn about [classes](https://kotlinlang.org/docs/classes.html), [properties](https://kotlinlang.org/docs/properties.html) and [data classes](https://kotlinlang.org/docs/data-classes.html) and then rewrite the following Java code to Kotlin:

```java
public class Person {
    private final String name;
    private final int age;
​
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
​
    public String getName() {
        return name;
    }
​
    public int getAge() {
        return age;
    }
}
```

Afterward, add the `data` modifier to the resulting class. The compiler will generate a few useful methods for this class: `equals/hashCode`, `toString`, and some others.

**Answer**
```kotlin
data class Person(val name: String, val age: Int)
```

## Smart casts
```kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> TODO()
            is Sum -> TODO()
            else -> throw IllegalArgumentException("Unknown expression")
        }

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```

### Smart casts
Rewrite the following Java code using [smart casts](https://kotlinlang.org/docs/typecasts.html#smart-casts) and the [when](https://kotlinlang.org/docs/control-flow.html#when-expression) expression:

```java
public int eval(Expr expr) {
    if (expr instanceof Num) {
        return ((Num) expr).getValue();
    }
    if (expr instanceof Sum) {
        Sum sum = (Sum) expr;
        return eval(sum.getLeft()) + eval(sum.getRight());
    }
    throw new IllegalArgumentException("Unknown expression");
}
```

**Answer**
```kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> expr.value
            is Sum -> eval(expr.left) + eval(expr.right)
            else -> throw IllegalArgumentException("Unknown expression")
        }
```

## Sealed classes
```kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> TODO()
            is Sum -> TODO()
        }

interface Expr
class Num(val value: Int) : TODO()
class Sum(val left: Expr, val right: Expr) : TODO()
```

### Sealed classes
Reuse your solution from the previous task, but replace the interface with the [`sealed` interface](https://kotlinlang.org/docs/sealed-classes.html). Then you no longer need the `else` branch in `when`.

**Answer**
```kotlin
fun eval(expr: Expr): Int =
        when (expr) {
            is Num -> expr.value
            is Sum -> eval(expr.left) + eval(expr.right)
        }

sealed interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr
```

## Rename on import
```kotlin
// import kotlin.random.Random
// import java.util.Random

fun useDifferentRandomClasses(): String {
    return "Kotlin random: " +
           // KRandom.nextInt(2) +
           " Java random:" +
           // JRandom.nextInt(2) +
           "."
}
```

### Rename on import
When you [import](https://kotlinlang.org/docs/packages.html#visibility-of-top-level-declarations) a class or a function, you can specify a different name for it by adding `as NewName` after the import directive. It can be useful if you want to use two classes or functions with similar names from different libraries.

Uncomment the code and make it compile. Rename `Random` from the `kotlin` package to `KRandom`, and `Random` from the `java` package to `JRandom`.

**Answer**
```kotlin
import kotlin.random.Random as KRandom
import java.util.Random as JRandom

fun useDifferentRandomClasses(): String {
    return "Kotlin random: " +
            KRandom.nextInt(2) +
           " Java random:" +
            JRandom().nextInt(2) +
           "."
}
```

## Extension functions
```kotlin
fun Int.r(): RationalNumber = TODO()

fun Pair<Int, Int>.r(): RationalNumber = TODO()

data class RationalNumber(val numerator: Int, val denominator: Int)
```

### Extension functions
Learn about [extension functions](https://kotlinlang.org/docs/extensions.html#extension-functions). Then implement the extension functions `Int.r()` and `Pair.r()` and make them convert `Int` and `Pair` to a `RationalNumber`.

`Pair` is a class defined in the standard library:

```kotlin
data class Pair<out A, out B>(
    val first: A,
    val second: B
)
```

In the case of `Int`, the denominator is 1.

**Answer**
```kotlin
fun Int.r(): RationalNumber = RationalNumber(this, 1)

fun Pair<Int, Int>.r(): RationalNumber = RationalNumber(first, second)

data class RationalNumber(val numerator: Int, val denominator: Int)
```