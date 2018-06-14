# Guard for Kotlin

## What? ##
This generic function adds a mechanism similar to the `guard` statement in Swift to Kotlin. The `guard` mechanism checks a precondition (usually a `null` check), optionally binding variables if the precondition worked out. If a precondition fails, an `else` block is executed, which must end with a statement to leave the current scope, forcing an early return.

## Usage ##
In Swift, this is expressed as: 
```swift
guard let aa = a else { return }
```
However, this implementation follows the usual pattern of Kotlin standard helper function such as `apply` or `let`:
```kotlin
val w = v.guard{
    println("Value was null!")
    return
}
```

## Advanced usage ##
Unlike Swift's `guard`, which can take any number and kind of preconditions, this leans on other standard Kotlin functions for additional conditions to be tested, e.g.:
```kotlin
val w = value
        .takeIf { it < 256 }
        .guard {
            println("value was either null or too big")
            return
        }
```

## Rationale ##
The canonical Kotlin way to check for `null` and otherwise return is either using the "Elvis" operator `?:` or a construct such as `let{}` or `apply{}`. Using an "Elvis return", a function contains expressions such as: 
```kotlin
val unwrapped = maybe ?: return // With optional return value
```
The right-hand side of an Elvis expression can only be a single statement, even though you may want to log the error before the return, or forcing a retry.

The `let` or `takeIf` methods either require the entire logic for the "safe" value to be moved into the accompanying block, building upon the "pyramid" structure of code, or assignment followed by another Elvis for an early return.

Using a `guard`, any error handling takes place in the adjacent block, while the "usual" logic of the function goes on below the guard, at the usual indentation scope and indentation level. The guard also forces you to exit early, facilitating reasoning about the core logic of the function at hand, since each time you see a `guard`, you know that one source of errors has been excluded.

## Example code ##
```kotlin
fun test() {
    var maybe:String? = "something" // maybe is a nullable string
    println(maybe is String) // true

    val unwrapped = maybe.guard{
    	println("This would only run if `maybe` was null")
    	return
    }
    println(unwrapped is String) // true

    maybe = null
    println(maybe is String) // false
    
    val never = maybe.guard{
    	println("Found a null value, exiting function")
    	return
    }
    println("This line is unreachable because `maybe` is set to null")
}

fun main(args: Array<String>) {
	test()
	println("Back in main")
}
```