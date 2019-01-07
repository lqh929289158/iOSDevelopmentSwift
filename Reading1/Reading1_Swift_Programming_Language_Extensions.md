# Extensions

Motivation: Add new functionality to an existing class, structure, enumertation or protocpol!

> NOTE: _Retroactive modeling_ you are able to extend types for which you do not have access to the original source code.

Extensions in Swift can:

- Add computed instance properties and computed type properties.
- Define instance methods and type methods.
- Provide new initializers.
- Define subscripts.
- Define and use new nested types.
- Make an exisiting type conform to a protocol.

> WARNING: Extensions can add new functionalitiy to a type. But they can not override existing functionality.

## Extension Syntax

```swift
extension SomeType {
  // new functionality to add to SomeType goes here
}
```

```swift
extension SomeType: SomeProtocol, AnotherProtocol {
  // implementation of protocol requirements goes here
}
```

> The new functionality of an existing type will be available on all existing instances, even if they were created before the extension was defined.

## Computed Properties

```swift
extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
let oneInch = 25.4.mm
print("One inch is \(oneInch) meters")
// Prints "One inch is 0.0254 meters"
let threeFeet = 3.ft
print("Three feet is \(threeFeet) meters")
// Prints "Three feet is 0.914399970739201 meters"
```

> WARNING: Stored properties or property observers **ARE NOT** permited.

## Initializers

Motivation: Add new initializers to existing types so that they can accept your own custom types as initializer parameters, or provide additional initialization options that were not included in original implementation.

> WARNING: Extensions can _only_ add new **convenience** initializers, _but_ new designated initializers or deinitializers.

> NOTE: If you use an extension to add an initializer to a value type that provides default values for all of its stored properties and does not define any custom initializers, you can call the default initializer and memberwise initializer for that value type from within your extension’s initializer. This wouldn’t be the case if you had written the initializer as part of the value type’s original implementation,

> NOTE: If you use an extension to add an initializer to a structure that was declared in another module, the new initializer can’t access `self` until it calls an initializer from the defining module.

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
}

extension Rect {
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

## Methods

```swift
extension Int {
    func repetitions(task: () -> Void) {
        for _ in 0..<self {
            task()
        }
    }
}

3.repetitions {
    print("Hello!")
}
// Hello!
// Hello!
// Hello!
```

### Mutating Instance Methods

Mark the extented method as `mutating` if it changes the instance `self`.

```swift
extension Int {
  mutating func square() {
      self = self * self
  }
}

var someInt = 3
someInt.square()
//someInt is now 9
```

## Subscripts

```swift
extension Int {
    subscript(digitIndex: Int) -> Int {
        var decimalBase = 1
        for _ in 0..<digitIndex {
            decimalBase *= 10
        }
        return (self / decimalBase) % 10
    }
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```

## Nested Types

```swift
extension Int {
    enum Kind {
        case negative, zero, positive
    }
    var kind: Kind {
        switch self {
        case 0:
            return .zero
        case let x where x > 0:
            return .positive
        default:
            return .negative
        }
    }
}

func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
        }
    }
    print("")
}
printIntegerKinds([3, 19, -27, 0, -6, 0, 7])
// Prints "+ + - 0 - 0 + "
```

> NOTE: `number.kind` is inferred as type `Int.Kind`. So all of the `Int.Kind` case values can be written in shorthand form inside the `switch` statement. Such as `.negative` instead of `Int.Kind.negative`.


