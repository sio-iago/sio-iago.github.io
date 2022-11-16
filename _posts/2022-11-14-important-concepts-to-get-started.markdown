---
layout: post
title: "[Kotlin] Important Concepts to Get Started"
date: 2022-11-14 18:20:00 -0300
categories: kotlin
---

In my opinion Kotlin is very similar to Java and almost feels like Java, but in a easier
and more fun way. The main Java frameworks (e.g.: `Spring`, `Quarkus`) have out-of-the-box
Kotlin support, enabling a very flat learning curve, since you already know the framework
and is just getting used to the new language syntax.

When I started learning Kotlin I was no beginner to Functional Programming. I already knew
functional constructs (`map`, `filter`, `reduce`) but coming from a Java background, the
first thing that came to mind is _"let's convert it to a stream like we do in Java"_. But
that's far for the optimal Kotlin way of doing it, since it has native support to a lot
of functional constructs, including the basics we already have on Java Streams.

However, Kotlin is fundamentally different from Java in three subtle aspects:

1. Functions are first class citizens;
2. Immutability: Unless marked as `open` or `mutable`, everything is immutable; and
3. True optionals.

Let's dig a little deeper on how these differences affect our experience with Kotlin.
{: .mb-2}

## Functions are first class citizens

Let's start with an example. Say you have some user input you need to perform validations.
If we're in Javaland, we would need to create a validator class:

```java
// UserValidator.java
public class UserValidator {

    private static int MAX_LENGTH = 20;

    /**
     * Validates a username.
     * Shouldn't be more than 20 characters and no whitespaces.
     */
    public static boolean isValidUsername(String name) {
        return (name.length() < MAX_LENGTH && !name.contains(" "));
    }
}
```

With Kotlin we just need to have a functions file, not even a class, and that
is enough to have one validation function that can be used across all the
application:

```kotlin
// UserValidatorFunctions.kt

const val USERNAME_MAX_LENGTH = 20;

fun isValidUsername(name: String): Boolean =
    name.length < USERNAME_MAX_LENGTH && !name.contains(" ")
```

Having the function definition in Kotlin and using it is as simple as importing
a function. There's no need to call a static method inside a class which the sole
purpose is to have helper functions, nor there's a need to instantiate or inject a
validator class. We can and SHOULD have helper function as Kotlin files, to be
easily imported anywhere we need.

Moreover, testing becomes a lot simpler, since we don't have classes and states,
only a function:

```kotlin
class UserValidatorFunctionsTest {

    @Test
    fun shouldReturnTrueIfUsernameIsValid() {
        val username = "some-valid-user"
        assertTrue(isValidUsername(username))
    }

    @Test
    fun shouldReturnFalseIfUsernameHasWhitespaces() {
        val username = "some invalid-user"
        assertFalse(isValidUsername(username))
    }

    @Test
    fun shouldReturnFalseIfUsernameIsTooBig() {
        // Validator has a limit of 20 characters
        var username = ""
        for (i in 0..(USERNAME_MAX_LENGTH + 1)) {
            username += "a"
        }

        assertFalse(isValidUsername(username))
    }
}
```

There's even a more interesting use case of Kotlin functions. Remember when
you need to perform operations on Collections in Java? We usually have to
either create a `Stream` or convert to stream.

```java
// Calculating the sum of positive numbers smaller than 10
Stream.of(0, -9, -3, null, 2)
    .filter(n -> n != null && n > 0 && n < 10 )
    .reduce(Integer::sum)

// Or, if you have a list before
List.of(0, -9, -3, null, 2)
    .stream()
    .filter(n -> n != null && n > 0 && n < 10 )
    .reduce(Integer::sum)
```

Without getting into details around the internals of Java and Java Streams,
the overhead of using streams here (intermediary classes, etc) is quite heavy.

On the other hand we can perform those operations natively in Kotlin:

```kotlin

val numbers = listOf(0, -9 , -3, 1, null, 2, 3, 5, -2, 8, null, 11, 9, 20)

// Calculating the sum of positive numbers smaller than 10
print(
    numbers
        .filterNotNull()
        .filter { it in 1..9 }
        .reduce { total, next -> total + next }
)
```

**NOTE:** The `it` in Kotlin refers to the argument of a single argument function.
It is the same as writing `filter { n -> n in 1..9 }`.

There are lots to cover about functions in Kotlin, but that's a topic for
a dedicated post.
{: .mb-2}

## Immutability

Kotlin supports multiple flavors of immutability. We will go over a few of the
most commom cases, with an explanation on how they work and what are the defaults.

#### Variables and Constants

The most commom being the use of
`val` to assign immutable values.

```kotlin
val name = "Iago Lorencini Oliveira"
val favoriteNumber = 9

name = "Can't change it" // This is a compilation error
var favoriteNumber = 3 // This is also a compilation error
```

In other words, setting a `val` signals to the compiler (and also who's reading)
that the variable is immutable. Same is applied to class attributes:

```kotlin
data class User(
    val username: String,
    var age: Int // Notice the "var" here
)

val aUser = User("iago", 50)
aUser.age = 20 // No compilation errors, age is "var"
aUser.username = "change-it" // Compilation error, username is "val"
```

Using `val` and `var` effectively will hugely improve readability and maintainability
of the code, since it will be a lot more clear and less prone to bugs (such as
accidental state change).

#### Collections

The second use case of immutability is a bit tricky at first. I've spent countless
hours trying to figure out why I couldn't add items to a list on Kotlin, just to feel
stupid when finding out that collections can be mutable or immutable.

Let's break it down using an example:

```kotlin
// The default collection constructs will always return immutable collections
val listOne = listOf(1, 2, 3)
listOne.add(4) // Compilation error, method doesn't exist

// But if we need a mutable list, for example
val listTwo = mutableListOf(1, 2, 3)
listTwo.add(4) // This is fine now, because our list is mutable
```

The same principles apply to sequences and maps. They default to immutable unless
the mutable construct is called.

It may be complicated at first, but having immutability here is extremely good
because it prevents unintended changes on our collection state. And we can always
explicitly declare mutable collections whenever needed.

```kotlin
data class User(
    val username: String,
    val roles: MutableList<String> = mutableListOf("User"),
) {
    override fun toString(): String = "$username - $roles"
}

val user = User("Iago")
user.roles.add("Staff") // This works since it's a mutable list
print(user) // Prints "Iago - [User, Staff]"
```

#### Classes, Methods and Functions

The third case of immutability is around classes and methods. All
classes, functions and methods in Kotlin are defined as `final`
by default. In order to change that behavior and enable extensibility
we need to declare it as `open`. Let's take a look at the following
example:

```kotlin
open class Car constructor(val make: String, val model: String) {
    open fun run(): String = "The $make $model is running"
}

class BrokenCar: Car {
    constructor(): super("Broken", "Broken")

    override fun run(): String = "This car is broken!!!"
}

val car = Car("Ford", "Focus")
println(car.run()) // The Ford Focus is running

val brokenCar = BrokenCar()
print(brokenCar.run()) // This car is broken!!!
```
{: .mb-2}

## True Optionals

This is one of the most amazing features of Kotlin. If you have experience
with **Swift** it will feel more or less like it. But Optionals in Kotlin
are built-in, meaning we don't need to worry about `NullPointerException`
unless we explicitly allow it. The compiler will scream at you if you use
optionals in an unsafe way and you won't be able to compile until you fix
your code (or throw the unchecked operator `!!`, but PLEASE DON'T).

Let's start with a small example:

```kotlin
fun sum(x: Int? = null, y: Int? = null): Int {
    return if (x == null || y == null) {
        0
    } else {
      x + y
    }
}

println(sum()) // 0
println(sum(1)) // 0
println(sum(2, 3)) // 5
println(sum(null, null)) // 0
```

Let's break down this function.

First, we have two arguments `x` and `y`. They are both optional integers (`?`)
and they have a default value `null`. In other words, we can call this function
passing zero, one or two arguments due to the arguments having defaults.

Also, we have a simple null check on the `if` condition, so on the else we don't
need to unbox the optional because the compiler knows it's already been checked.

if we were to replace the function on the example with the one bellow the code would
throw a `NullPointerException`:

```kotlin
fun sum(x: Int? = null, y: Int? = null): Int = x!! + y!!

println(sum()) // NullPointerException
```

That's because the operator `!!` unsafely unboxes the optional.

Now let's explore a more interesting example:

```kotlin
interface UserService { 
	fun findUser(userId: String): UserDto?

    fun fetchUserRoles(userId: String): Collection<UserRoleDto>?
}

@Path("/users")
class AdminUserResource @Inject constructor(val userService: UserService) {
    
    @GET
    @Path("/{userId}")
    fun getUser(@PathParam("userId") userId: String): FullUserDto? {
        val userDto: UserDto = userService.findUser(userId) ?: throw NotFoundException("user.not.found")
        val userRoleNamess: List<String> = userService.fetchUserRoles(userId)
            .map { it.name } ?: listOf()
        
        return FullUserDto(userDto, userRoleNames)
    }
}
```

In this case we want to throw a `NotFoundException` in case we can't fetch the user,
but we only want to set the role names as an empty list if we can't get info about
the roles.

There are even other usages, such as building an object that depends on many optional
variables. When using the operator `?:` we will get the default value declared after
the operator if any of the properties in the chain is null. For instance:

```kotlin
data class A(val name: String = "A")
data class B(val name: String = "B", val a: A? = null)
data class C(val name: String = "C", val b: B? = null)

fun main() {
    // Classes with references
    val a = A()
    val b = B(a = a)
    val c = C(b = b)

    // An orphan C without references to b or a
    val c2 = C()
    
    val x1 : String? = c.b?.a?.name
    val x2 : String? = c2.b?.a?.name
    val x3 : String = c2.b?.a?.name ?: "Don't know"
    
    println(x1) // A
    println(x2) // null
    println(x3) // Don't know
}
```

I can tell from experience that it is very rare to find an NPE in production on
a service developed with Kotlin. Even when I find one, it's usually inside a Java
class in a dependency somewhere.
{: .mb-3}


### Wrapping Up

Java and Kotlin have lots of similarities, but some differences that may be hard
to get used to when learning one or the other. Hopefully this post will help you
on discovering Kotlin or learning it.

Kotlin is a powerful, modern language and mature language and, the most important,
Kotlin is lots of `fun`!

/iago
