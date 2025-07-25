# Introduction

## Hello, world!
```kotlin
fun start(): String = TODO()
```

### Simple Functions
Check out the [function syntax](https://kotlinlang.org/docs/basic-syntax.html) and change the code to make the function `start` return the string `"OK"`.

In the Kotlin Koans tasks, the function `TODO()` will throw an exception. To complete Kotlin Koans, you need to replace this function invocation with meaningful code according to the problem.

**Answer**
```kotlin
fun start(): String = "OK"
```

## Named arguments
```kotlin
fun joinOptions(options: Collection<String>) = 
        options.joinToString(TODO())
```

### Named arguments
Make the function `joinOptions()` return the list in a JSON format (for example, `[a, b, c]`) by specifying only two arguments.

[Default and named](https://kotlinlang.org/docs/functions.html) arguments help to minimize the number of overloads and improve the readability of the function invocation. The library function `joinToString` is declared with default values for parameters:

```kotlin
fun joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    /* ... */
): String
```

It can be called on a collection of Strings.

**Answer**
```kotlin
fun joinOptions(options: Collection<String>) = 
        options.joinToString(prefix = "[", postfix = "]")
```

## Default arguments
```kotlin
fun foo(name: String, number: Int, toUpperCase: Boolean) =
        (if (toUpperCase) name.uppercase() else name) + number

fun useFoo() = listOf(
        foo("a"),
        foo("b", number = 1),
        foo("c", toUpperCase = true),
        foo(name = "d", number = 2, toUpperCase = true)
)
```

### Default arguments
Imagine you have several overloads of 'foo()' in Java:

```java
public String foo(String name, int number, boolean toUpperCase) {
    return (toUpperCase ? name.toUpperCase() : name) + number;
}
public String foo(String name, int number) {
    return foo(name, number, false);
}
public String foo(String name, boolean toUpperCase) {
    return foo(name, 42, toUpperCase);
}
public String foo(String name) {
    return foo(name, 42);
}
```

You can replace all these Java overloads with one function in Kotlin. Change the declaration of the `foo` function in a way that makes the code using `foo` compile. Use [default and named](https://kotlinlang.org/docs/functions.html) arguments.

**Answer**
```kotlin
fun foo(name: String, number: Int = 42, toUpperCase: Boolean = false) =
        (if (toUpperCase) name.uppercase() else name) + number
```

## Triple-quoted strings
```kotlin
const val question = "life, the universe, and everything"
const val answer = 42

val tripleQuotedString = """
    #question = "$question"
    #answer = $answer""".trimIndent()

fun main() {
    println(tripleQuotedString)
}
```

### Triple-quoted strings
Learn about the [different string literals and string templates](https://kotlinlang.org/docs/strings.html) in Kotlin.

You can use the handy library functions `trimIndent` and `trimMargin` to format multiline triple-quoted strings in accordance with the surrounding code.

Replace the `trimIndent` call with the `trimMargin` call taking `#` as the prefix value so that the resulting string doesn't contain the prefix character.

**Answer**
```kotlin
val tripleQuotedString = """
    #question = "$question"
    #answer = $answer""".trimMargin("#")
```

## String templates
```kotlin
val month = "(JAN|FEB|MAR|APR|MAY|JUN|JUL|AUG|SEP|OCT|NOV|DEC)"

fun getPattern(): String = TODO()
```

### String templates
Triple-quoted strings are not only useful for multiline strings but also for creating regex patterns as you don't need to escape a backslash with a backslash.

The following pattern matches a date in the format `13.06.1992` (two digits, a dot, two digits, a dot, four digits):

```kotlin
fun getPattern() = """\d{2}\.\d{2}\.\d{4}"""
```

Using the `month` variable, rewrite this pattern in such a way that it matches the date in the format `13 JUN 1992` (two digits, one whitespace, a month abbreviation, one whitespace, four digits).

**Answer**
```kotlin
fun getPattern(): String = """\d{2} $month \d{4}"""
```

## Nullable types
```kotlin
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
) {
    TODO()
}

class Client(val personalInfo: PersonalInfo?)
class PersonalInfo(val email: String?)
interface Mailer {
    fun sendMessage(email: String, message: String)
}
```

### Nullable types
Learn about [null safety and safe calls](https://kotlinlang.org/docs/null-safety.html) in Kotlin and rewrite the following Java code so that it only has one `if` expression:

```java
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if (client == null || message == null) return;
​
    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;
​
    String email = personalInfo.getEmail();
    if (email == null) return;
​
    mailer.sendMessage(email, message);
}
```

**Answer**
```kotlin
fun sendMessageToClient(
        client: Client?, message: String?, mailer: Mailer
) {
    val email = client?.personalInfo?.email
    	if (email != null && message != null) {
            mailer.sendMessage(email, message)
        }
}
```

## Nothing type
```kotlin
import java.lang.IllegalArgumentException

fun failWithWrongAge(age: Int?): TODO() {
    throw IllegalArgumentException("Wrong age: $age")
}

fun checkAge(age: Int?) {
    if (age == null || age !in 0..150) failWithWrongAge(age)
    println("Congrats! Next year you'll be ${age + 1}.")
}

fun main() {
    checkAge(10)
}
```

### Nothing type
[Nothing type](https://kotlinlang.org/docs/exceptions.html) can be used as a return type for a function that always throws an exception. When you call such a function, the compiler uses the information that the execution doesn't continue beyond the function.

Specify `Nothing` return type for the `failWithWrongAge` function. Note that without the `Nothing` type, the `checkAge` function doesn't compile because the compiler assumes the `age` can be `null`.

**Answer**
```kotlin
import java.lang.IllegalArgumentException

fun failWithWrongAge(age: Int?): Nothing {
    throw IllegalArgumentException("Wrong age: $age")
}

fun checkAge(age: Int?) {
    if (age == null || age !in 0..150) failWithWrongAge(age)
    println("Congrats! Next year you'll be ${age + 1}.")
}

fun main() {
    checkAge(10)
}
```

## Lambdas
```kotlin
fun containsEven(collection: Collection<Int>): Boolean =
        collection.any { TODO() }
```

### Lambdas
Kotlin supports functional programming. Learn about [lambdas](https://kotlinlang.org/docs/lambdas.html) in Kotlin.

Pass a lambda to the `any` function to check if the collection contains an even number. The `any` function gets a predicate as an argument and returns true if at least one element satisfies the predicate.

**Answer**
```kotlin
fun containsEven(collection: Collection<Int>): Boolean =
        collection.any { it % 2 == 0 }
```