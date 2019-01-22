# Automatic Reference Counting

_ARC(Automatic Reference Counting)_ to track and manage memory usage.

_ARC_ will do the memory management for you automatically in almost all cases.

But in some cases, you have to provide more information for ARC.

> NOTE: ARC only applies to **instances of classes**. Structures and enumerations are not reference varaible.

## How ARC Works

To make sure that instance don't disappear while they are still needed, ARC tracks **how many** properties, constants, and variables are currently referring to each class instance. It will not deallocate an instance as long as **at least one active** reference to that instance still exists.

Whenever you assign a class instance to a property/constant/variable, that property/constant/variable makes a _strong reference_ to the instance. (Strong means it does not allow the instance to be deallocated as long as the strong reference remains)

## ARC in Action

```swift
class Person {
    let name: String
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    deinit {
        print("\(name) is being deinitialized")
    }
}
```

```swift
reference1 = Person(name: "John Appleseed")
// Prints "John Appleseed is being initialized"

reference2 = reference1
reference3 = reference1

reference2 = reference1
reference3 = reference1

reference3 = nil
// Prints "John Appleseed is being deinitialized"
```

## Strong Reference Cycles Between Class Instances

Deadlock???

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

```swift
var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```
![Reference Illustration](https://docs.swift.org/swift-book/_images/referenceCycle02_2x.png)
```swift
john = nil
unit4A = nil
```
![Strong Reference Cycles](https://docs.swift.org/swift-book/_images/referenceCycle03_2x.png)

## Resolving Strong Reference Cycles Between Class Instances

- Weak references (the other instance has a shorter lifetime, which means can be deallocated first)
- Unowned references (the other instance has the same or longer lifetime)

Avoid strong hold. The instances can refer to each other without strong reference cycle.

### Weak References

Possible for the instance to be deallocated while weak reference is still referring to it. And the reference will be set to `nil` at runtime. So the weak reference must be declared as **variable of optional** type, rather than constants.

> NOTE: Property observers aren't called when ARC sets a weak reference to `nil`.

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```

![Weak Reference Illustration](https://docs.swift.org/swift-book/_images/weakReference01_2x.png)

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
```

![Weak Reference No Deadlock](https://docs.swift.org/swift-book/_images/weakReference02_2x.png)

```swift
unit4A = nil
// Prints "Apartment 4A is being deinitialized"
```

![Weak Reference No Deadlock](https://docs.swift.org/swift-book/_images/weakReference03_2x.png)

### Unowned References

Used when the other instance has the **same** or **longer** lifetime. Keyword: `unowned`.

An unowned reference is expected to always have a value. So ARC never sets an unowned reference to `nil`, which means that unowned references are defined using **nonoptional** types.

> NOTE: If you try to access the value of an unowned reference that has been deallocated, you will get a **runtime error**.

New example `Customer` and `CreditCard`. Differnce: `CreditCard` must has one owner.

```swift
class Customer {
    let name: String
    var card: CreditCard?
    init(name: String) {
        self.name = name
    }
    deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
    let number: UInt64
    unowned let customer: Customer
    init(number: UInt64, customer: Customer) {
        self.number = number
        self.customer = customer
    }
    deinit { print("Card #\(number) is being deinitialized") }
}
```
```swift
var john: Customer?
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

![Unowned reference illustration](https://docs.swift.org/swift-book/_images/unownedReference01_2x.png)

```swift
john = nil
// Prints "John Appleseed is being deinitialized"
// Prints "Card #1234567890123456 is being deinitialized"
```

Because there is no strong references to the `Customer` instance, it is deallocated. And, the `CreditCard` is also deallocated.

> NOTE: There is a type called `unowned(unsafe)`. Different from `unowned`, if you try to access it after it is deallocated, you will get the value in the same location in memory, which is an unsafe operation.

### Unowned References and Implicitly Unwrapped Optional Properties

- First scenario: `Person` and `Apartment`, both allowed to be `nil`.
- Second scenario: `Customer` and `CreditCard`, one of the property is not allowed to be `nil`.
- **Third scenario**: `Country` and `City`, both properties should always have a value.

```swift
class Country {
    let name: String
    var capitalCity: City!
    init(name: String, capitalName: String) {
        self.name = name
        self.capitalCity = City(name: capitalName, country: self)
    }
}

class City {
    let name: String
    unowned let country: Country
    init(name: String, country: Country) {
        self.name = name
        self.country = country
    }
}
```

The initializer for `Country` **CAN NOT** pass `self` to `City` until a new `Country` instance is fully initialized.

To deal with it, the annotation `City!` is used. That means that the `capitalCity` property has a default value of `nil`, but can be accessed without unwrapping. Since, property `name` and `captialCity` both have initial values, we can pass `self` to `City` initializer.

```swift
var country = Country(name: "Canada", capitalName: "Ottawa")
print("\(country.name)'s capital city is called \(country.capitalCity.name)")
// Prints "Canada's capital city is called Ottawa"
```

## Strong Reference Cycles for Closures

A strong reference cycle may occur if you assign a **closure** to a **property or method** of a class instance.

Why? Because, **Closure are REFERENCED TYPES**.

Swift provides an elegant solution called _closure capture list_.

```swift
class HTMLElement {

    let name: String
    let text: String?

    lazy var asHTML: () -> String = {
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }

    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }

    deinit {
        print("\(name) is being deinitialized")
    }

}
```

The `asHTML` property is named and used like an instance method. BUT, it is a closure of referenced type. You can replace the default value of the `asHTML` with a custom closure.

```swift
let heading = HTMLElement(name: "h1")
let defaultText = "some default text"
heading.asHTML = {
    return "<\(heading.name)>\(heading.text ?? defaultText)</\(heading.name)>"
}
print(heading.asHTML())
// Prints "<h1>some default text</h1>"
```

> NOTE: The property `asHTML` is labeled as `lazy`, so you can access `self`, because the `asHTML` will only evaluated when ued.

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"
```

Unfortunately, the `HTMLElement` class creates a strong reference cycle between the class and the closure.

![Closure strong referecne cycle](https://docs.swift.org/swift-book/_images/closureReferenceCycle01_2x.png)

> NOTE: Even though the closure refers to `self` multiple times, it only captures one strong reference to the `HTMLElement` instance.

## Resolving Strong Reference Cycles for Closures

Defining a _capture list_.

A capture list defines the rules to use when capturing one or more reference types within the closure's body.

> WARNING: Swift requires you to write `self.property` or `self.method` to remind you.

### Defining a Capture List

```swift
lazy var someClosure: (Int, String) -> String = {
    [unowned self, weak delegate = self.delegate!] (index: Int, stringToProcess: String) -> String in
    // closure body goes here
}
```

If a closure does not specify a parameter list or return type because they can be inferred from context, place the capture list at the very start of the closure, followed by the in keyword:

```swift
lazy var someClosure: () -> String = {
    [unowned self, weak delegate = self.delegate!] in
    // closure body goes here
}
```

### Weak and Unowned References

Define a capture in a closure as an unowned reference when the closure and the instance it captures will always refer to each other, and will always be **deallocated at the same time**.

Conversely, define a capture as a weak reference when the captured reference may become `nil` at some point in the future. Weak references are always of an optional type, and automatically become `nil` when the instance they reference is deallocated. This enables you to check for their existence within the closure’s body.

```swift
class HTMLElement {

    let name: String
    let text: String?

    lazy var asHTML: () -> String = {
        [unowned self] in
        if let text = self.text {
            return "<\(self.name)>\(text)</\(self.name)>"
        } else {
            return "<\(self.name) />"
        }
    }

    init(name: String, text: String? = nil) {
        self.name = name
        self.text = text
    }

    deinit {
        print("\(name) is being deinitialized")
    }

}
```

```swift
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "hello, world")
print(paragraph!.asHTML())
// Prints "<p>hello, world</p>"

paragraph = nil
// Prints "p is being deinitialized"
```

![Capture list illustration](https://docs.swift.org/swift-book/_images/closureReferenceCycle02_2x.png)





