# Conventions

## Comparison
```kotlin
data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int) : Comparable<MyDate> {
    /* TODO */
}

fun test(date1: MyDate, date2: MyDate) {
    // this code should compile:
    println(date1 < date2)
}
```

### Comparison
Learn about [operator overloading](https://kotlinlang.org/docs/operator-overloading.html) and how the different conventions for operations like `==`, `<`, `+` work in Kotlin. Add the function `compareTo` to the class `MyDate` to make it comparable. After this, the code below `date1 < date2` should start to compile.

Note that when you override a member in Kotlin, the `override` modifier is mandatory.

**Answer**
```kotlin
data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int) : Comparable<MyDate> {
    override fun compareTo(other: MyDate) = when {
        year != other.year -> year - other.year
        month != other.month -> month - other.month
        else -> dayOfMonth - other.dayOfMonth
    }
}
```

## Ranges
```kotlin
fun checkInRange(date: MyDate, first: MyDate, last: MyDate): Boolean {
    return TODO()
}
```

### Ranges
Using [ranges](https://kotlinlang.org/docs/ranges.html) implement a function that checks whether the date is in the range between the first date and last date (inclusive).

You can build a range of any comparable elements. In Kotlin [`in` checks](https://kotlinlang.org/docs/operator-overloading.html#in-operator) are translated to the corresponding `contains` calls and `..` to `rangeTo` calls:

```kotlin
val list = listOf("a", "b")
"a" in list     // list.contains("a")
"a" !in list    // !list.contains("a")

date1..date2    // date1.rangeTo(date2)
```

**Answer**
```kotlin
fun checkInRange(date: MyDate, first: MyDate, last: MyDate): Boolean {
    return date in first..last
}
```

## For loop
```kotlin
class DateRange(val start: MyDate, val end: MyDate)

fun iterateOverDateRange(firstDate: MyDate, secondDate: MyDate, handler: (MyDate) -> Unit) {
    for (date in firstDate..secondDate) {
        handler(date)
    }
}
```

### For loop
A Kotlin [for loop](https://kotlinlang.org/docs/control-flow.html#for-loops) can iterate through any object if the corresponding `iterator` member of extension function is available.

Make the class `DateRange` implement `Iterable<MyDate>`, so that it can be iterated over. Use the function `MyDate.followingDate()` defined in `DateUtil.kt`; you don't have to implement the logic for finding the following date on your own.

Use an [object expression](https://kotlinlang.org/docs/object-declarations.html#create-anonymous-objects-from-scratch) which plays the same role in Kotlin as an anonymous class in Java.

**Answer**
```kotlin
class DateRange(val start: MyDate, val end: MyDate) : Iterable<MyDate> {
    override fun iterator(): Iterator<MyDate> {
        return object : Iterator<MyDate> {
            var current: MyDate = start

            override fun next(): MyDate {
                if (!hasNext()) throw NoSuchElementException()
                val result = current
                current = current.followingDate()
                return result
            }

            override fun hasNext(): Boolean = current <= end
        }
    }
}

fun iterateOverDateRange(firstDate: MyDate, secondDate: MyDate, handler: (MyDate) -> Unit) {
    for (date in firstDate..secondDate) {
        handler(date)
    }
}
```

## Operators overloading
```kotlin
import TimeInterval.*

data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int)

enum class TimeInterval { DAY, WEEK, YEAR }

operator fun MyDate.plus(timeInterval: TimeInterval): MyDate = TODO()

fun task1(today: MyDate): MyDate {
    return today + YEAR + WEEK
}

fun task2(today: MyDate): MyDate {
    TODO("Uncomment") // return today + YEAR * 2 + WEEK * 3 + DAY * 5
}
```

### Operators overloading
Implement date arithmetic and support adding years, weeks, and days to a date. You could write the code like this: `date + YEAR * 2 + WEEK * 3 + DAY * 15`.

First, add the extension function `plus()` to `MyDate`, taking the `TimeInterval` as an argument. Use the utility function `MyDate.addTimeIntervals()` declared in `DateUtil.kt`

Then, try to support adding several time intervals to a date. You may need an extra class.

**Answer**
```kotlin
import TimeInterval.*

data class MyDate(val year: Int, val month: Int, val dayOfMonth: Int)

enum class TimeInterval { DAY, WEEK, YEAR }

operator fun MyDate.plus(timeInterval: TimeInterval): MyDate =
        addTimeIntervals(timeInterval, 1)

class RepeatedTimeInterval(val timeInterval: TimeInterval, val number: Int)

operator fun TimeInterval.times(number: Int) =
        RepeatedTimeInterval(this, number)

operator fun MyDate.plus(timeIntervals: RepeatedTimeInterval) = 
        addTimeIntervals(timeIntervals.timeInterval, timeIntervals.number)

fun task1(today: MyDate): MyDate {
    return today + YEAR + WEEK
}

fun task2(today: MyDate): MyDate {
    return today + YEAR * 2 + WEEK * 3 + DAY * 5
}
```

## Invoke
```kotlin
class Invokable {
    var numberOfInvocations: Int = 0
        private set

    operator fun invoke(): Invokable {
        TODO()
    }
}

fun invokeTwice(invokable: Invokable) = invokavle()()
```

### Invoke
Objects with the `invoke()` method can be invoked as a function.

You can add an `invoke` extension for any class, but it's better not to overuse it:

```kotlin
operator fun Int.invoke() { println(this) }

1() // huh?..
```

Implement the function `Invokable.invoke()` to count the number of times it is invoked.

**Answer**
```kotlin
class Invokable {
    var numberOfInvocations: Int = 0
        private set

    operator fun invoke(): Invokable {
        numberOfInvocations++
            return this
    }
}

fun invokeTwice(invokable: Invokable) = invokable()()
```