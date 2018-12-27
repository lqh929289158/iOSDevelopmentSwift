# Enumerations

> Note:Alternatively, enumeration cases can specify associated values of any type to be stored along with each different case value.

## Enumeration Syntax

```swift
enum CompassPoint {
    case north
    case south
    case east
    case west
}

//OR

enum Planet {
    case mercury, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

> NOTE: Each enumeration is a new type. Their names should start with a **capital** letter.

We use dot to specify enumeration value.

```swift
var directionToHead = CompassPoint.west

directionToHead = .east // Type inferrence
```

## Matching Enumeration Values with a Switch Statement

```swift
directionToHead = .south
switch directionToHead {
case .north:
    print("Lots of planets have a north")
case .south:
    print("Watch out for penguins")
case .east:
    print("Where the sun rises")
case .west:
    print("Where the skies are blue")
}
// Prints "Watch out for penguins"
```

## Iterating over Enumeration Cases

By implementing `CaseIterable` protocol. Then you can use `.count` to count cases and `.allCases` to get a `Sequence` for all cases.

```swift
enum Beverage: CaseIterable {
    case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// Prints "3 beverages available"

for beverage in Beverage.allCases {
    print(beverage)
}
// coffee
// tea
// juice
```
## Associated Values

```swift
//definition
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}

//initialization
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

//switch matching
switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
    print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."

//For brevity
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
    print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."
```

## Raw Values

All associated values are all of the same type. And all case values are set when enumeration created.

```swift
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

### Implicitly Assigned Raw Values

```swift
//Integer 1,2,3,4,5,...
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}

//String just the name of case value. "north", "south", "east", "west"
enum CompassPoint: String {
    case north, south, east, west
}

//Access raw value of a case by rawValue
let earthsOrder = Planet.earth.rawValue
// earthsOrder is 3

let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection is "west"
```

### Initializing from a Raw Value

Use raw value to assign a case value.

```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet is of type Planet? and equals Planet.uranus
```

> WARNING: the result of raw-value-initialization is of type enumeration-optional.

## Recursive Enumerations

> A _recursive_ enumeration is an enumeration that has another instance of the enumeration as the associated value for one or more of the enumeration cases. You indicate that an enumeration case is recursive by writing `indirect` before it, which tells the compiler to insert the necessary layer of indirection.

```swift
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}

//make all cases indirect
indirect enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
}

//usage of recursive enumeration
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))

func evaluate(_ expression: ArithmeticExpression) -> Int {
    switch expression {
    case let .number(value):
        return value
    case let .addition(left, right):
        return evaluate(left) + evaluate(right)
    case let .multiplication(left, right):
        return evaluate(left) * evaluate(right)
    }
}

print(evaluate(product))
// Prints "18"
```





