# Closures

> Closures are self-contained blocks of functionality that can be passed around and used in your code. Closures in Swift are similar to blocks in C and Objective-C and to lambdas in other programming languages.

> Closures can capture and store references to any constants and variables from the context in which they are defined. This is known as _closing over_ those constants and variables. Swift handles all of the memory management of capturing for you.

Three forms of closure:

- Global functions are closures that have a **name** and **NO capturing value**.
- Nested functions are closures that have a **name** and capturing values from their **enclosing function**.
- Closure expressions are **unnamed closures** written in a lightweight syntax that can capture values from their **surrounding context**.

Four optimizations:

- Inferring parameter and return value types from context
- Implicit returns from single-expression closures {-\$0}
- Shorthand argument names (\$0,\$1...)
- Trailing closure syntax

## Closure Expressions

Motivation: Shorter versions of function-like constructs without a full declaration and name.

Closure expressions essence: optimizated syntax!

### Closure Expression Syntax

```swift
{ (parameters) -> returnType in 
  statements
}
```

Example (before closure)

```swift
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
```

Example (after closure)

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

### Inferring Type From Context

Refine the code more!

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

### Implicit Returns from Single-Expression Closures

Refine the code further!

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

> WARNING: Only _Single-Expression_ closure can use implicit returns.

### Shorthand Argument Names

Refine the code more and more!

```swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

### Operator Methods

```swift
reversedNames = names.sorted(by: >)
```

## Trailing Closures

Motivation: Pass a closure expression to a function as the final argument.

Method: Write the closure when you call the function!

```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // function body goes here
}

// Here's how you call this function without using a trailing closure:

someFunctionThatTakesAClosure(closure: {
    // closure's body goes here
})

// Here's how you call this function with a trailing closure instead:

someFunctionThatTakesAClosure() {
    // trailing closure's body goes here
}
```

So the example can be 

```swift
reversedNames = names.sorted() { $0 > $1 }
```

If the closure is the only argument, you can even ignore the parentheses.

```swift
reversedNames = names.sorted { $0 > $1 }
```

Another elegant example using trailing closure. (If your closure is long)

The method `map(_: )` for `Array` is to map each element to another thing and form a new array.

```swift
let digitNames = [
    0: "Zero", 1: "One", 2: "Two",   3: "Three", 4: "Four",
    5: "Five", 6: "Six", 7: "Seven", 8: "Eight", 9: "Nine"
]
let numbers = [16, 58, 510]

let strings = numbers.map { (number) -> String in // type of 'number' is inferred
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// strings is inferred to be of type [String]
// its value is ["OneSix", "FiveEight", "FiveOneZero"]
```

## Capturing Values

Simplest case: Nested function which can access and modify variables outside itself and inside the enclosing function.

## Closures Are Reference Types

In a word, many pointers pointing to the same closure will, for example, modify the same capturing variable.

## Escaping Closures

A closure is said to _escape_ a function when the closure is passed as an **argument** to the function, but is called **after the function returns**. When you declare a function that takes a closure as one of its parameters, you can write `@escaping` before the parameter’s type to indicate that the closure is allowed to escape.

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}

func someFunctionWithNonescapingClosure(closure: () -> Void) {
    closure()
}

class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Prints "200"

completionHandlers.first?()
print(instance.x)
// Prints "100"
```

## Autoclosures

**NO** argument. Return the value of evaluated expression.

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"

print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"

// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Prints "Now serving Alex!"


// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```

