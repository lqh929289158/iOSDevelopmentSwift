# Initialization

Preparation for instance of class, structure or enumeration.

> NOTE: Swift _initializers_  **DO NOT** return a value.

## Setting Initial Values for Stored Properties

**Classes and structures _must_ set _all_ of their stored properties to an initial value**.

**Stored properties _CAN NOT_ be left in an indeterminate state**.

### Initializers

```swift
init() {
  //initialization
}
```

Initialization by initializer.

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
var f = Fahrenheit()
print("The default temperature is \(f.temperature)° Fahrenheit")
// Prints "The default temperature is 32.0° Fahrenheit"
```

### Default Property Values

```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

## Customizing Initialization

### Initialization Parameters

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0
let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0
```

### Parameter Names and Argument Labels

- Parameter names: used in methods.
- Argument labels: used when calling methods.

> NOTE:Default argument labels in initializers are parameter names themselves.

```swift
struct Color {
    let red, green, blue: Double
    init(red: Double, green: Double, blue: Double) {
        self.red   = red
        self.green = green
        self.blue  = blue
    }
    init(white: Double) {
        red   = white
        green = white
        blue  = white
    }
}

let magenta = Color(red: 1.0, green: 0.0, blue: 1.0)
let halfGray = Color(white: 0.5)

let veryGreen = Color(0.0, 1.0, 0.0)
// this reports a compile-time error - argument labels are required
```

> WARNING: _Argument labels_ are **NECESSARY** when calling initializers.

### Initializer Parameters Without Argument Labels

Use `_` instead of an explicit argument label to override the default behavior.

```swift
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

### Optional Property Types

No worry, optional property will be initialized as `nil` automatically.

### Assigning Constant Properties During Initialization

> You can assign a value to a constant property at any point during initialization, as long as it is set to a definite value by the time initialization finishes. Once a constant property is assigned a value, it can’t be further modified.

Summary: No modification to constant property after initialization.

> WARNING: Subclass **CAN NOT** modify constant properties inherited from superclass.

## Default Initializers

If you don't write your own initializers, all properties will be set to default value.

### Memberwise Initializers for Structure Types

- Used when no custom initializers.
- **Stored properties could have no default values.**
- Initializer parameter name = property name

```swift
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

## Initializer Delegation for Value Types

_Initializer delegation_ : Call another initializer in a initializer.

Motivation : Avoid duplicating code.

> NOTE: Initializer delegation for value types (structures and enumerations) is simpler. Because initializer can only delegate to another initializer provided by the value type. **NO inheritance**.

Use `self.init` to refer to other initializers.

> WARNING: If you have defined a custom initializer, the default initializer will be **invalid**.(Including memberwise initializer for structures)

> NOTE: If you want to keep the default initializer, write custom initializer in an extension.

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
    init() {}
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}

let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)

let originRect = Rect(origin: Point(x: 2.0, y: 2.0), size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)

let centerRect = Rect(center: Point(x: 4.0, y: 4.0), size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

## Class Inheritance and Initialization

> WARNING: **ALL** stored properties of class, _must_ be assigned an initial value during initialization.

### Designated Initializers and Convenience Initializers

- _Designated Initializers_
  - Primary
  - Few, usually have only one
  - At least one( maybe inheriting one or more designated initializers from a superclass [Automatic Initializer Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html#ID222)
- _Convenience Initializers_
  - Secondary
  - To call a designated initializer with some of the designated initializer's parameters set to default values.
  - To create an instance of that class for a specific use case or input value type.
 
 
 ### Syntax for Designated and Convenience Initializers
 
 - Designated:
 ```swift
 init(parameters) {
    statements
}
```

- Convenience:
```swift
convenience init(parameters) {
    statements
}
```

### Initializer Delegation for Class Types

**Three rules:**

- A designated initializer must call a designated initializer from its immediate superclass.
- A convenience initializer must call another initializer from the same class.
- A convenience initializer must ultimately call a designated initializer.

Simpler way to say:

- **Designated _Up_**
- **Convenience _Across_**

![Class initializer delegation](https://docs.swift.org/swift-book/_images/initializerDelegation01_2x.png)

More complex example:
![Class initializer delegation](https://docs.swift.org/swift-book/_images/initializerDelegation02_2x.png)

### Two-Phase Initialization

- First phase: Each stored property is assigned an initial value.
- Second phase: Each class is given the chance to customize its stored properties further before the instance is ready.

Why two phases?

- Prevent property value from being accessed before initialization
- Prevent property value from being set to a different value by another initializer unexpectedly.

Four **safety-check**:

- Designated initializer _must_ initialize all properties introduced by **its** class **before delegation**.
- Designated initializer _must_ delegate up **before** assigning value to **inherited property**.
- Convenience initializer _must_ delegate **before** assigning value to **any** property.
- Initializer _CAN NOT_ :
  - Call _any_ instance methods.
  - Read values of _any instance properties.
  - Refer to `self` as a value unless **first phase complete**(All properties set).

**Phase 1**
- Designated/Convenience initializer is called.
- Memory of the instance is allocated.
- Designated confirm all stored properties introduced by **its** class have value. Memory initialized.
- Designated delegate up. Make sure inherited properties initialized.
- Continue until the class inheritence chain complete!

**Phase 2**
- From top to down. Each designated can customize the instance.
  - Access `self`
  - Modify properties
  - Call instance methods.

Phase 1 illustration:
![Phase1](https://docs.swift.org/swift-book/_images/twoPhaseInitialization01_2x.png)
Phase 2 illustration:
![Phase2](https://docs.swift.org/swift-book/_images/twoPhaseInitialization02_2x.png)

### Initializer Inheritance and Overriding

> WARNING: In Swift, subclass **DO NOT** inherit initializers from its superclass _by default_.

> NOTE: But in certain safe circumstances, superclass initializers is inherited. See [Automatic Initializer Inheritance](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html#ID222)

If you are writing a initializer that _matches_ a superclass's **designated** initializer, you have to add `override` before initializer. This is true even if you are overriding an automatically provided _default initializer_ or you are writing a convenience initializer.

If you are writing a initializer that _matches_ a superclass's **convenience** initializer, you **DO NOT** add `override`. Because superclass convenience initializer will never be called in subclass. See [Initializer Delegation for Class Types](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html#ID219)

```swift
class Vehicle {
    var numberOfWheels = 0
    var description: String {
        return "\(numberOfWheels) wheel(s)"
    }
}

//Must delegate up, then customize!
class Bicycle: Vehicle {
    override init() {
        super.init()
        numberOfWheels = 2
    }
}

class Hoverboard: Vehicle {
    var color: String
    init(color: String) {
        self.color = color
        // super.init() implicitly called here
    }
    override var description: String {
        return "\(super.description) in a beautiful \(color)"
    }
}

```

> NOTE: Subclasses can modify inherited variable properties during initialization, but **CAN NOT** modify inherited constant properties.

### Automatic Initializer Inheritance

**Assumption**: All new properties introduced by subclass have default values.

- **Rule 1**: If subclass has **NO designated**, it inherits **all** superclass designated automatically.
- **Rule 2**: If subclass implements **ALL** superclass designated, it inherits **ALL** superclass convenience automatically.
  - Inherits superclass designated by **Rule 1**.
  - Implements superclass designated by customizing definition.

> If **Rule 2** satisfied, subclass can implement superclass designated as subclass convenience.

### Designated and Conveience Initializers in Action

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}

class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    //Since superclass designated has been implemented below,
    //all superclass convenience are inherited ( init() )
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}

class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
//No custom initializer, so, all designated and convenience inherited.
```

![Initializer chain](https://docs.swift.org/swift-book/_images/initializersExample03_2x.png)

```swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

## Failable Initializers

`init?`

Motivation: Cope with invalid parameter, absence of required external resource...

> WARNING: Failable and nonfailable initializers **CAN NOT** match (The same parameter types and names).

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}

let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal

if anonymousCreature == nil {
    print("The anonymous creature could not be initialized")
}
// Prints "The anonymous creature could not be initialized"
```

> WARNING: `""` empty string is a valid, nonoptional `String`. It is not `nil`.

### Failable Initializers for Enumerations

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}
```

### Failable Initializers for Enumerations with Raw Values

Default failable initializer. If no matching, return `nil`.

```swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
}
```

### Propagation of Initialization Failure

Delegate across in the same class, or delegate up to superclass.

> NOTE: A failable initializer can also delegate to a nonfailable initializer.

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { return nil }
        self.quantity = quantity
        super.init(name: name)
    }
}
```

### Overriding a Failable Initializer
