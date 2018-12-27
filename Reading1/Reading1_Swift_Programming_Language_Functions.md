# Functions

`func funcName(parameterName: parameterType) -> returnType`

> NOTE: You can ignore the return value of a function by

```swift
let _ = function(parameter: argument)
```

> NOTE: Use tuples to return multiple values.

## Function Argument Labels and Parameter Names

### Specifying Argument Labels

**Inside** function, use `parameterName`.
**Calling** function, use `argumentLabel`.
```swift
func someFunction(argumentLabel parameterName: Int) {
    // In the function body, parameterName refers to the argument value
    // for that parameter.
}
```
```swift
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino."
```

### Omitting Arugment Labels

```swift
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)
```

> WARNING: If a parameter has an argument label, the argument must be labeled when you call the function.

### Default Parameter Values

```swift
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12
```

> NOTE: Write Non-default parameters first to make it easier to recognize.

### Variadic Parameters

```swift
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
// returns 3.0, which is the arithmetic mean of these five numbers
arithmeticMean(3, 8.25, 18.75)
// returns 10.0, which is the arithmetic mean of these three numbers
```

### In-Out Parameters

To modify the variables **OUTSIDE** the function. Like reference passing.

> WARNING: You CAN NOT pass a constant or a literal value as the argument.

> WARNING: You have to place an ampersand(&) directly before the arguments when calling the function.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}

var someInt = 3
var anotherInt = 107
swapTwoInts(&someInt, &anotherInt)
print("someInt is now \(someInt), and anotherInt is now \(anotherInt)")
// Prints "someInt is now 107, and anotherInt is now 3"
```

## Function Type

Function has type of parameter tuples to returnType. e.g. `(Int, Int) -> Double`, `() -> Int`, `()-> Void`...

### Using Function Types

Copy a function to another variable with the same function type.

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}

var mathFunction: (Int, Int) -> Int = addTwoInts
mathFunction = multiplyTwoInts

let anotherMathFunction = addTwoInts
```

### Function Types as Parameter Types

Pass a function as an argument.

```swift
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"
```

### Function Types as Return Types

## Nested Functions

Functions that are hidden from the outside.

> NOTE: The enclosing function can return the nested functions so that it can be called outside.

```swift
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
```




