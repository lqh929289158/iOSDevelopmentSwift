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




