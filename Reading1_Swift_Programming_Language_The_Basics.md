# Reading homework 1

Modern programming patterns:

- Variables are always initialized before use.
- Array indices are checked for out-of-bounds errros. (How?)
- Integers are checked for overflow. (what if overflow?)
- Optionals ensure that _nil_ values are handled explicitly.
- Memory is managed automatically.(Implicit Garbage collection?)
- Error handling allows controlled recovery from unexpected failures.

## Beauty

> Swift combines powerful type inference and pattern matching with a modern, lightweight syntax, allowing complex ideas to be expressed in a clear and concise manner.

> As a result, code is not just easier to write, but easier to read and maintain as well.

## The Basics

- Fundamental type from C and Objective-C: `Double`,`Float`,`Int`,`Bool`,`String`...

- Powerful versions of the three primary collection types: `Array`,`Set`,`Dictionary`.

- Variable: name identifying

- Constant: safer and clearer

- New types: `tuples`,`Optional`...

- Heart features: **Optional type**...

- Type-safe

### Constants and Variables

#### Declaring Constants and Variables

```swift
let maximumNumberOfLoginAttempts = 10
var currentLoginAttempt = 0
```

Multiple assignments:
```swift
var x = 0.0, y = 0.0, z = 0.0
```

> Note:
> Use variables ONLY for storing values that need to be able to change. 
> And declare it as a constant with `let` if the stored value won\`t change.

#### Type Annotations

Explicit declaration of your variable type.

```swift
var welcomeMessage: String //Means: Declare a variable called welcomeMessage that is of type String
```

Swift will infer the type even if you do not declare. Because of **Satety and Type Inference**.

```swift
welcomeMessage = "Hello"
```

You can also declare multiple variables.

```swift
var red, green, blue: Double
```

#### Naming Constants and Variables

Constants and variables names can contain almost any character even **Unicode** and **emoji**!
```swift
let π = 3.14159
let 你好 = "你好世界"
let 🐶🐮 = "dogcow"
```

But

- No **whitespace characters**.
- No **mathematical symbols**.
- No **arrows**. (arrows? this? ->)
- No **private-use Unicode scalar values**.(Which are private-use? What is scalar?)
- No **-**
- No box-drawing charactes. (What is box-drawing? this? - | )
- No number at beginning.

**You can change the value of a variable name, but NOT the type!**

#### Printing Constants and Variables

```swift
print(friendlyWelcome)
//Prints "Bonjour"
```

- The function terminates the line it prints by adding a line break.
- No line break
```swift
print(someValue, terminator: "")
```
- **String interpolation** 
```swift
print("The value of friendlyWelcome is =\(friendlyWelcome)")
//print "The value of friendlyWecome is Bonjour"
```

### Comments

The same as C. // and /\*\*/

### Semicolons

**No need of semicolons if you enter a new line.**

**Need of semicolons if you write mulitple statements on a single line.**

```swift
let cat = "CAT"; print(cat)
// Print "CAT"
```

### Integers

**Int8  UInt8**
**Int16 UInt16**
**Int32 UInt32**
**Int64 UInt64**

### Double

`Double` 64-bit floating point number. At least 15 decimal digits.
`Float` 32-bit floating point number. 6 decimal digits.

### Type Safety and Type Inference

### Numeric Literals

Decimal: No prefix
Binary: `0b` prefix
Octal: `0o` prefix
Hexadecimal: `0x` prefix

```swift
let decimalInteger = 17
let binaryInteger = 0b10001 // 17 in decimal
let octalInteger = 0o21     // 17 in decimal
let hexadecimalInteger = 0x11 // 17 in decimal
```

Decimal floating-point: both decimal before and after decimal point.
Hexadecimal floating-point: both hexadecimal before and after decimal point.
Decimal optional _exponent_. 10 exp.  `1.25e-2`(1.25 x 10^-2)
Hexadecimal optional _exponent_. **2 exp**. `0xFp-2`(15 x 2^-2)

```swift
let decimalDouble = 12.1875
let exponentDouble = 1.21875e1
let hexadecimalDouble = 0xC.3p0
```

You can **pad zero** or insert **underscores** to improve readability.
```swift 
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1
```

### Numeric Type Conversion

#### Integer Conversion

```swift
let twoThousand: UInt16 = 2000
let one: UInt8 = 1
let twoThousandAndOne = twoThousand + UInt16(one)
```

Just use SomeType(ofInitialValue) as convertor.

#### Integer and Floating-Point Conversion

**Conversions between Integer and floating-point numeric types must be made explicit.**

```swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
let piInteger = Int(pi)
// pi is inferred to be of type double.
```

**Floating-point values are always truncted when used to initialize a new integer value.**

e.g. 4.75 -> 4, -3.9 -> -3.

> IMPORTANT! The rules for combining numeric constants and variables are different from the rules for numeric literals.
> For example, you can not combine two different types. But you can combine two number with possibly different types.

```swift
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = three + pointOneFourOneFiveNine //Error!
let pi = 3 + 0.14159 //Right because 3 is inferred as double
```

### Type Aliaes

_Type Aliaes_ define alternative name for an existing type.

```swift
typealias AudioSample = UInt16

var maxAmplitudeFound = AudioSample.min
```

### Booleans

### Tuples

Define a variable as tuple.

```swift
let http404Error = (404, "Not Found")
```

Extract values from a tuple to variables.

```swift
let (statusCode, statusMessage) = http404Error
```

Extract part of values from a tuple to variables.

```swift
let (justTheStatusCode,_) = http404Error
```

Access inividual element values in a tuple.

```swift
print(http404Error.0)
print(http404Error.1)
```

Name the elements in a tuple when it is defined.

```swift
let http200Status = (statusCode: 200, description: "OK")

print(http200Status.statusCode)
print(http200Status.description)
```

**Conclusion: Useful as return value in function**

### _Optionals_

Cope with absent value.

```swift
let possibleNumber = "123"
let convertedNumber = Int(possibleNumber)
// convertedNumber has a type of Int? because the conversion may fail when there is no number characters in the string.
```

#### nil

You can set an optional variable with nil.
```swift
var serverResponseCode: Int? = 404
serverResponseCode = nil
```

Default value for optional variable is nil.

```swift
var surveyAnswer: String?
// surveyAnswer is automatically set to nil
```

> Note:

> `nil` in Objective-C is a pointer to a nonexistent object.

> While `nil` in Swift is the absence of a value of a certain type. Optionals of any type can be set to `nil`, not just object types.

#### If Statements and Forced Unwrapping

Certainly, you can compare an optinal variable with `nil`.

```swift
if convertedNumber != nil {
  print("convertedNumber contains some value")
}
```

Also, if you are sure that there is some value in an optional variable, you can **upwrap** it.

```swift
print("convertedNumber has an integer \(convertedNumber!).")
```

If you unwrap an optional variable with no value, the program will crash with a runtime error.

#### Optional Binding

Make sure that there is some value in it and unwrap it.

```swift
if let actualNumber = Int(possibleNumber) {
  print("The string \"\(possibleNumber)\" has an integer value of \(actualNumber)")
} else {
  print("The string \"\(possibleNumber)\" could not be converted to an integer")
}
```

**Warning: The optional binding variables are only available in the `if` block.**

**The constants and variables created with a `guard` statement are available in the lines of code that follow the `guard` statement.

#### Implicitly Unwrapped Optionals

To use a normal optional variable as an unwrapped variable.

```swift
let possibleString: String? = "An optional string."
let forcedString: String = possibleString! // requires an exclamation mark

let assumedString: String! = "An implicitly unwrapped optional string."
let implicitString: String = assumedString // no need for an exclamation mark
```

**Primary use: class initialization!**

### Error Handling

```swift
func makeASandwich() throws {
    // ...
}

do {
    try makeASandwich()
    eatASandwich()
} catch SandwichError.outOfCleanDishes {
    washDishes()
} catch SandwichError.missingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```

### Assertions and Preconditions

**Key point: assertions and preconditions will terminate your program if the conditions are not satisfied.**

Preconditions: Checked in **production** and **debug** builds.

Assertion: Only checked in **debug** builds. So you can use as many assertions as you want during development process, **without impacting performance in production**.

#### Debugging with Assertions

```swift
let age = -3
assert(age >= 0, "A person`s age can't be less than zero.")
```

```swift
assertionFailure("Forced termination")
```












