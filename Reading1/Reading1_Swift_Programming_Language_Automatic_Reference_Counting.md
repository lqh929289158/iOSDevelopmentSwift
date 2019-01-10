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





