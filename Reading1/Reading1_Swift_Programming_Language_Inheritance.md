# Inheriance

_Subclass_ and _superclass_.

What a subclass can do?

- Call all inherited methods or subscripts.
- Access all inherited properties.
- _Override_ methods, properties, and subscipts.
- Add property _observers_ to inherited properties to be notified when the value of the property changes. No matter stored or computed property.

## Defining a Base Class

> Any class that does not inherit from another class is known as a _base class_.

```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
```

## Subclassing

Syntax:

```swift
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
```

Example:

```swift
class Bicycle: Vehicle {
    var hasBasket = false
}

class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
```

## Overriding

Modifying or redefining what inherited from superclass

- instance method
- type method(static)
- instance property
- type property(static)
- subscript

> NOTE: The compiler will make sure that what you are overriding is DO a thing inherited from superclass, including type and name.

### Accessing Superclass Methods, Properties, and Subscripts

Motivation: Use what is inherited as part of your override.

- Method : Overridden method `someMethod()` call superclass-version `super.someMethod()`
- Property : Overridden property `someProperty` call superclass-version `super.someProperty`
- Subscript : Overridden subscript `someIndex` call superclass-version `super[someIndex]`

### Overriding Methods

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
```

### Overriding Properties

#### Overriding Property Getters and Setters

> WARNING: If you override setter, you must also override getter!

```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
```

#### Overriding Property Observers

> WARNING: **NO** observers to **inherited constant stored properties** or **inherited read-only computed properties**.

> WARNING: **NOT** both observer and setter. If you use observer, no need to override setter!

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 10.0) + 1
        }
    }
}
```

## Preventing Overrides

Use keyword `final` before keyword `var`,`func`, `class func`, `subscript`.

Then the inherited can not be overridden !

Or you can use keyword `final class` when class definition. 

Then all inherited of superclass can not be overridden!


