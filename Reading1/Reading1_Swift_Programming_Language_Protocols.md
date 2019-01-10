# Protocols(Important!)

Protocol: A _blueprint_ of methods, properties and other requirements that suit a particular task or piece of functionality.

Conform: Any type that satisfies the requirement of a protocol is said to _conform_ to that protocol.

## Protocol Syntax

```swift
protocol SomeProtocol {
  // protocol definition goes here
}


struct SomeStructure: FirstProtocol, AnotherProtocol {
  // structure defnition goes here
}

class SomeClass: SomeSuperClass, FirstProtocol, AnotherProtocol {
  // class definition goes here
}

```

## Property Requirements

- Instance property
- Type property(Static)

**DO NOT** specify whether it should be stored or computed. _Only_ specify **name** and **type**.

Can also specify the property must be **gettable** or **gettable and settable**.

- gettable: Can be satisfied by any kind of property.
- gettable and settable: **CAN NOT** be fulfilled by a **constant stored** property or a **read-only computed** property.

Property requirements are **always** declared as **variable** properties with `var`.

```swift
protocol SomeProtocol {
  var mustBeSettable: Int {get set}
  var doesNotNeedToBeSettable: Int {get}
}

protocol AnotherProtocol {
  static var someTypeProperty: Int {get set}
}
```

## Method Requirements

The same way as for normal instance and type methods, **without** curly braces or method body.

> WARNING: Default values **CAN NOT** be specified for method parameters within protocol's definition.

```swift
protocol SomeProtocol {
  static func someTypeMethod()
  func someInstanceMethod() -> ReturnType
}
```

## Mutating Method Requirements

> NOTE: If you have alreay mark a protocol instance method requirement as `mutating`, you **DO NOT** need to write the `mutating` keyword when writing its implementation.

> NOTE: `Mutating` keyword is only used by **structures** and **enumerations**.

```swift
protocol Togglable {
    mutating func toggle()
}

enum OnOffSwitch: Togglable {
    case off, on
    mutating func toggle() {
        switch self {
        case .off:
            self = .on
        case .on:
            self = .off
        }
    }
}
var lightSwitch = OnOffSwitch.off
lightSwitch.toggle()
// lightSwitch is now equal to .on
```

## Initializer Requirements

### Class Implementations of Protocol Initializer Requirements

You can implement a protocol initializer as either a designated initializer or a convenience initializer. But in both cases, you must mark the initializer implementation with the `required` modifier.

```swift
class SomeClass: SomeProtocol {
    required init(someParameter: Int) {
        // initializer implementation goes here
    }
}

// Make sure that all subclasses also conforms to the protocol.
```

> NOTE: **NO NEED** to mark `required` for `final` classes because they can't be subclassed.

If a subclass overrides a designated initializer from a superclass, and also implements a matching initializer requirement from a protocol, mark the initializer implementation with both the `required` and `override`.

```swift
protocol SomeProtocol {
    init()
}

class SomeSuperClass {
    init() {
        // initializer implementation goes here
    }
}

class SomeSubClass: SomeSuperClass, SomeProtocol {
    // "required" from SomeProtocol conformance; "override" from SomeSuperClass
    required override init() {
        // initializer implementation goes here
    }
}
```

### Failable initializer Requirements

For a nonfailable initializer requirement, you can

- use a nonfailable initializer
- use an implicityly unwrapped failable initializer.

## Protocol as Types

Although protocol doesn't implement any functionality, they become a fully-fledged type for use in code.

So you can use protocol as:

- A parameter type or return type in function, method, or initializer.
- The type of a constant, variable, or property.
- The type of items in an array, dictionary, or other container.

> NOTE: Protocol are used as type, so begin their name with a **Capital** letter.

```swift
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}

var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 {
    print("Random dice roll is \(d6.roll())")
}
// Random dice roll is 3
// Random dice roll is 5
// Random dice roll is 4
// Random dice roll is 5
// Random dice roll is 4
```

## Delegation

```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate: AnyObject {
    func gameDidStart(_ game: DiceGame)
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(_ game: DiceGame)
}
```

Mark `AnyObject` for class-only protocol.

```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        board = Array(repeating: 0, count: finalSquare + 1)
        board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
        board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    }
    weak var delegate: DiceGameDelegate?
    func play() {
        square = 0
        delegate?.gameDidStart(self) // optional chaining
        gameLoop: while square != finalSquare {
            let diceRoll = dice.roll()
            delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll) // optional chaining
            switch square + diceRoll {
            case finalSquare:
                break gameLoop
            case let newSquare where newSquare > finalSquare:
                continue gameLoop
            default:
                square += diceRoll
                square += board[square]
            }
        }
        delegate?.gameDidEnd(self) // optional chaining
    }
}
```

```swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(_ game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            print("Started a new game of Snakes and Ladders")
        }
        print("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(_ game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        numberOfTurns += 1
        print("Rolled a \(diceRoll)")
    }
    func gameDidEnd(_ game: DiceGame) {
        print("The game lasted for \(numberOfTurns) turns")
    }
}
```

```swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```

## Adding Protocol Conformance with an Extension

Use extensions to extend an existing type to conform to a new protocol.

```swift
protocol TextRepresentable {
    var textualDescription: String { get }
}

extension Dice: TextRepresentable {
    var textualDescription: String {
        return "A \(sides)-sided dice"
    }
}

let d12 = Dice(sides: 12, generator: LinearCongruentialGenerator())
print(d12.textualDescription)
// Prints "A 12-sided dice"

extension SnakesAndLadders: TextRepresentable {
    var textualDescription: String {
        return "A game of Snakes and Ladders with \(finalSquare) squares"
    }
}
print(game.textualDescription)
// Prints "A game of Snakes and Ladders with 25 squares"
```

### Conditionally Conforming to a Protocol

By writing constraints after the name of the protocol with keyword `where`.

```swift
extension Array: TextRepresentable where Element: TextRepresentable {
    var textualDescription: String {
        let itemsAsText = self.map { $0.textualDescription }
        return "[" + itemsAsText.joined(separator: ", ") + "]"
    }
}
let myDice = [d6, d12]
print(myDice.textualDescription)
// Prints "[A 6-sided dice, A 12-sided dice]"
```

### Declaring Protocol Adoption with an Extension

- All requirements satisfied.
- And declaring protocol adoption explicitly.

```swift
struct Hamster {
    var name: String
    var textualDescription: String {
        return "A hamster named \(name)"
    }
}
extension Hamster: TextRepresentable {}

let simonTheHamster = Hamster(name: "Simon")
let somethingTextRepresentable: TextRepresentable = simonTheHamster
print(somethingTextRepresentable.textualDescription)
// Prints "A hamster named Simon"
```

> WARNING: Protocol conformance **must** be declared explicitly.

## Collection of Protocol Types

```swift
let things: [TextRepresentable] = [game, d12, simonTheHamster]

for thing in things {
    print(thing.textualDescription)
}
// A game of Snakes and Ladders with 25 squares
// A 12-sided dice
// A hamster named Simon
```

In a word, put the variables or constants that conform a certain protocol to a collection. And you can iterate them following the functionality of the protocol.

## Protocol Inheritance

Protocol can _inherit_ one or more other protocols and add further requirements.

```swift
protocol InheritingProtocol: SomeProtocol, AnotherProtocol {
    // protocol definition goes here
}

protocol PrettyTextRepresentable: TextRepresentable {
    var prettyTextualDescription: String { get }
}
```

```swift
extension SnakesAndLadders: PrettyTextRepresentable {
    var prettyTextualDescription: String {
        var output = textualDescription + ":\n"
        for index in 1...finalSquare {
            switch board[index] {
            case let ladder where ladder > 0:
                output += "▲ "
            case let snake where snake < 0:
                output += "▼ "
            default:
                output += "○ "
            }
        }
        return output
    }
}

print(game.prettyTextualDescription)
// A game of Snakes and Ladders with 25 squares:
// ○ ○ ▲ ○ ○ ▲ ○ ○ ▲ ▲ ○ ○ ○ ▼ ○ ○ ○ ○ ▼ ○ ○ ▼ ○ ▼ ○
```

## Class-Only Protocols

```swift
protocol SomeClassOnlyProtocol: AnyObject, SomeInheritedProtocol {
    // class-only protocol definition goes here
}
```

> NOTE: Use a class-only protocol when the behavior defined by that protocol’s requirements assumes or requires that a conforming type has reference semantics rather than value semantics.

## Protocol Composition

- `,` when you declare a type to conform multiple protocol.
- `&` when you combine protocols

```swift
protocol Named {
    var name: String { get }
}
protocol Aged {
    var age: Int { get }
}
struct Person: Named, Aged {
    var name: String
    var age: Int
}
func wishHappyBirthday(to celebrator: Named & Aged) {
    print("Happy birthday, \(celebrator.name), you're \(celebrator.age)!")
}
let birthdayPerson = Person(name: "Malcolm", age: 21)
wishHappyBirthday(to: birthdayPerson)
// Prints "Happy birthday, Malcolm, you're 21!"
```

You can also combine **Protocol with Class**.

```swift
class Location {
    var latitude: Double
    var longitude: Double
    init(latitude: Double, longitude: Double) {
        self.latitude = latitude
        self.longitude = longitude
    }
}
class City: Location, Named {
    var name: String
    init(name: String, latitude: Double, longitude: Double) {
        self.name = name
        super.init(latitude: latitude, longitude: longitude)
    }
}
func beginConcert(in location: Location & Named) {
    print("Hello, \(location.name)!")
}

let seattle = City(name: "Seattle", latitude: 47.6, longitude: -122.3)
beginConcert(in: seattle)
// Prints "Hello, Seattle!"
```

Meaning: Any type that is a subclass of `Location` and conforms to the `Named` protocol.

## Checking for Protocol Conformance

The same way as checking for type.

- `is` operator returns `true` if an instance conforms to a protocol and return `false` otherwise.
- `as?` returns `nil` optional if instance doesn't conform to a protocol.
- `as!` forces downcast to the protocol type and will trigger runtime error if fails.

```swift
protocol HasArea {
    var area: Double { get }
}

class Circle: HasArea {
    let pi = 3.1415927
    var radius: Double
    var area: Double { return pi * radius * radius }
    init(radius: Double) { self.radius = radius }
}
class Country: HasArea {
    var area: Double
    init(area: Double) { self.area = area }
}

class Animal {
    var legs: Int
    init(legs: Int) { self.legs = legs }
}
```

Although the three classes have no shared base class, they are all `class`. Use `AnyObject`

```swift
let objects: [AnyObject] = [
    Circle(radius: 2.0),
    Country(area: 243_610),
    Animal(legs: 4)
]

for object in objects {
    if let objectWithArea = object as? HasArea {
        print("Area is \(objectWithArea.area)")
    } else {
        print("Something that doesn't have an area")
    }
}
// Area is 12.5663708
// Area is 243610.0
// Something that doesn't have an area
```

## Optional Protocol Requirements

`optional` requirements don't have to be implemented by types that conform to the protocol.

But you have to mark `@objc`.

The property or method in an optional requirement auotomatically becomes an optional. e.g. `(Int) -> String` -> `((Int) -> String)?`

```swift
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}

class Counter {
    var count = 0
    var dataSource: CounterDataSource?
    func increment() {
        if let amount = dataSource?.increment?(forCount: count) {
            count += amount
        } else if let amount = dataSource?.fixedIncrement {
            count += amount
        }
    }
}

class ThreeSource: NSObject, CounterDataSource {
    let fixedIncrement = 3
}
```

```swift
var counter = Counter()
counter.dataSource = ThreeSource()
for _ in 1...4 {
    counter.increment()
    print(counter.count)
}
// 3
// 6
// 9
// 12
```

```swift
class TowardsZeroSource: NSObject, CounterDataSource {
    func increment(forCount count: Int) -> Int {
        if count == 0 {
            return 0
        } else if count < 0 {
            return 1
        } else {
            return -1
        }
    }
}
```

```swift
counter.count = -4
counter.dataSource = TowardsZeroSource()
for _ in 1...5 {
    counter.increment()
    print(counter.count)
}
// -3
// -2
// -1
// 0
// 0
```

## Protocol Extensions (Difficult?)

Protocol can be extended to provide method, initializer, subscript, and computed property implementations to conforming types.

This allows you to define **behavior** on protocol _themselves_, rather than in each type's **inidivdual conformance** or in a **global function**.

```swift
extension RandomNumberGenerator {
    func randomBool() -> Bool {
        return random() > 0.5
    }
}
```
So, all conforming types automatically gain this method `randomBool()`.

```swift
let generator = LinearCongruentialGenerator()
print("Here's a random number: \(generator.random())")
// Prints "Here's a random number: 0.3746499199817101"
print("And here's a random Boolean: \(generator.randomBool())")
// Prints "And here's a random Boolean: true"
```

### Providing Default Implementations

Provide a default implementation to any method or computed property defined in protocol.

The default implementation will be replaced by the one provided by the conforming type.

```swift
extension PrettyTextRepresentable  {
    var prettyTextualDescription: String {
        return textualDescription
    }
}
```

### Adding Constraints to Protocol Extensions

Use `where` clause after the name of protocol to specify constraints that conforming types must satisfy before the methods and properties of the extension are available.

```swift
extension Collection where Element: Equatable {
    func allEqual() -> Bool {
        for element in self {
            if element != self.first {
                return false
            }
        }
        return true
    }
}
```

```swift
print(equalNumbers.allEqual())
// Prints "true"
print(differentNumbers.allEqual())
// Prints "false"
```

> NOTE: If a conforming type satisfies the requirements for multiple constrained extensions that provide implementations for the same method or property, Swift uses the implementation corresponding to the most specialized constraints.

In a word, same type, same method/property -> Use the implementation with most specialized constraints.







