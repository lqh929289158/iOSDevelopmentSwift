# Properties

- Stored property (class, structure)
- Computed property (class, structure, emumeration)

You can also set **property observers** that monitor changes in a property's value.

## Stored Properties

### Stored Properties of Constant Structure Instances

> WARNING: You CAN NOT modify the stored properties of a constant. (Even if the property is a variable)

> NOTE: This is NOT true for classes! Because structure is _value_ type while class is _reference_ type.

> Why? Because when an instance of a value type is marked as a constant, so are all of its properties.

### Lazy Stored Properties

A _lazy stored property_ is a property whose initial value is not calculated **until the first time it is used**.

> WARNING: Lazy property must be **variable**, because its initial value might not be retrieved until after instance initialization completes. 

> NOTE: Lazy properties are useful when the initial value for a property is dependent on outside factors whose values are not known until after an instance's initialization is complete.

> NOTE: Lazy properties are also useful when the initial value for a property requires complex or computationally expensive setup that should not be performed unless or until it is needed.

```swift
class DataImporter {
    /*
    DataImporter is a class to import data from an external file.
    The class is assumed to take a nontrivial amount of time to initialize.
    */
    var filename = "data.txt"
    // the DataImporter class would provide data importing functionality here
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
    // the DataManager class would provide data management functionality here
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// the DataImporter instance for the importer property has not yet been created
```

### Stored Properties and Instance Variables

Unified in Swift! NO worry !

## Computed Properties

> NOTE: All computed properties must be **variable**!

```swift
struct Point {
    var x = 0.0, y = 0.0
}
struct Size {
    var width = 0.0, height = 0.0
}
struct Rect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0),
                  size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// Prints "square.origin is now at (10.0, 10.0)"
```

### Shorthand Setter Declaration

The default parameter name of `set` is `newValue`.

```swift
struct AlternativeRect {
    var origin = Point()
    var size = Size()
    var center: Point {
        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            return Point(x: centerX, y: centerY)
        }
        set {
            origin.x = newValue.x - (size.width / 2)
            origin.y = newValue.y - (size.height / 2)
        }
    }
}
```

### Read-Only Computed Properties

NO setter. No need to write `set` keyword.

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// Prints "the volume of fourByFiveByTwo is 40.0"
```

## Property Observers

Called every time a property's value is set, even if the new value is the same as the current.

> You can add property observers to any **stored properties** you define, **except for lazy stored properties**. You can also add property observers to any **inherited property** (whether stored or computed) by **overriding** the property within a subclass. You don’t need to define property observers for nonoverridden computed properties, because you can observe and respond to changes to their value in the computed property’s setter. Property overriding is described in [Overriding](https://docs.swift.org/swift-book/LanguageGuide/Inheritance.html#ID196).

- `willSet`: called just before the value is stored. Get parameter with default name `newValue` to be stored.
- `didSet`: called immediately after the new value is stored. Get parameter with default name `oldValue` to be flushed.

```swift
class StepCounter {
    var totalSteps: Int = 0 {
        willSet(newTotalSteps) {
            print("About to set totalSteps to \(newTotalSteps)")
        }
        didSet {
            if totalSteps > oldValue  {
                print("Added \(totalSteps - oldValue) steps")
            }
        }
    }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

> NOTE: If you pass a property that has observers to a function as an in-out parameter, the willSet and didSet observers are **always** called. This is because of the copy-in copy-out memory model for in-out parameters: The value is always written back to the property at the end of the function.

## Global and Local Variables

- Global: defined outside of any **function**, **method**, **closure**, or **type context**.
- Local: defined within any **function**, **method**, or **closure context**.

Both global and local variables can be defined as _computed_ and with _observers_.

> NOTE: Global constants and variables are **always** computed **lazily** and no need to be marked with `lazy`.

> NOTE: Local constants and variables are **never** computed lazily.

## Type Properties

The properties belongs to **the type**(structure, class or enumeration) but **instance**.

In another word, the type properties are universal to **all instances** of a particular type.

Stored type properties can be constants or variables. While, computed type properties are always declared as variable.

> NOTE: Unlike stored instance properties, you **MUST** always give stored type properties a default value!

> Why? This is because the type itself does not have an initializer that can assign a value to a stored type property at initialization time.

> NOTE:Stored type properties are **lazily** initialized on their first access. They are guaranteed to be initialized only once, even when accessed by multiple threads simultaneously, and they DO NOT need to be marked with the lazy modifier.

### Type Property Syntax

`static` in type's definition!

```swift
struct SomeStructure {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 1
    }
}
enum SomeEnumeration {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 6
    }
}
class SomeClass {
    static var storedTypeProperty = "Some value."
    static var computedTypeProperty: Int {
        return 27
    }
    class var overrideableComputedTypeProperty: Int {
        return 107
    }
}
```

### Querying and Setting Type Properties

Queried by `TypeName.TypePropertyName`

```swift
struct AudioChannel {
    static let thresholdLevel = 10
    static var maxInputLevelForAllChannels = 0
    var currentLevel: Int = 0 {
        didSet {
            if currentLevel > AudioChannel.thresholdLevel {
                // cap the new audio level to the threshold level
                currentLevel = AudioChannel.thresholdLevel
            }
            if currentLevel > AudioChannel.maxInputLevelForAllChannels {
                // store this as the new overall maximum input level
                AudioChannel.maxInputLevelForAllChannels = currentLevel
            }
        }
    }
}
```

> NOTE: In the first of these two checks, the didSet observer sets currentLevel to a different value. This **DOES NOT**, however, cause the observer to be called again, which means there is no **recursive** `didSet`.












