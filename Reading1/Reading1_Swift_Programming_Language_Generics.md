# Generics (Template?)

Motivation: Flexiable, reusable functions and types under requirements. Clear, abstracted manner.

## The problem That Generics Solve

A function that swap two object.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
func swapTwoStrings(_ a: inout String, _ b: inout String) {
    let temporaryA = a
    a = b
    b = temporaryA
}
func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

> NOTE: Key point here is the requirement that the two parameters must be of the same type.(Because you can not assign two objects with different types to each other.)

## Generic Functions (Isn't it Template?????)

_Generic functions_ can work with **any** type.

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```
```swift
var someInt = 3
var anotherInt = 107
swapTwoValues(&someInt, &anotherInt)
// someInt is now 107, and anotherInt is now 3

var someString = "hello"
var anotherString = "world"
swapTwoValues(&someString, &anotherString)
// someString is now "world", and anotherString is now "hello"
```

> NOTE: Use `swap(_: _:)` in Swift standard library.

## Type Parameters

_Type parameter_: e.g. `<T>`

## Naming Type Parameters

Meaningful name: `Dictionary<Key, Value>`

Meaningless name: `swapTwoValues<T>`

> NOTE: Beginning upper camel case, like `T`,`MyTypeParameter`.

## Generic Types

Example: `Stack`. Like `Array` and `Dictionary`.

One-End operation FIFO, pushing \& poping.

```swift
struct Stack<Element>{
  var item = [Element]()
  mutating func push(_ item: Element){
    items.append(item)
  }
  mutating func pop() -> Element{
    return items.removeLast()
  }
}
```

## Extending a Generic Type

When extending, **NO** type parameter list because you can use the _original_ parameter list in definition.

```swift
extension Stack {
  var topItem: Element? {
    return items.isEmpty ? nil : items[items.count - 1]
  }
}
```

## Type Constaints

E.g. the `Key` of `Dictionary` must be `Hashable`.

### Type Constaint Syntax

Placing **single class** or **protocol** constaint after a type parameter's name.

```swift
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U){
  //function
}
```

- `T` must be a subclass of `SomeClass`.
- `U` must conform to `SomeProtocol`.

### Type Constraints in Action

```swift
func findIndex<T>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

**THE CODE ABOVE ERROR!!!!**

```swift
func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

The type `T` must conform to protocol `Equatable` if you use the operator `=`.

## Associated Types (Abstract Protocol?)

When defining a protocol, it’s sometimes useful to declare one or more associated types as part of the protocol’s definition. An _associated type_ gives a placeholder name to a type that is used as part of the protocol. The actual type to use for that associated type isn't specified until the protocol is adopted. Keyword `associatedtype`.

### Assciated Type in Action

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

When you implement the protocol, make sure fill the associatedtype correctly.

```swift
struct IntStack: Container {
    // original IntStack implementation
    var items = [Int]()
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

Swift can infer the accociated type by `append(_: Int)`. So, it will still work even if you delete
```swift
typealias Item = Int
```

You can also make the generic `Stack` conforming to the `Container`.

```swift
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items = [Element]()
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```

### Extending an Existing Type to Specify an Associate Type

Since `Array` already provides `append(_:)` `count` and `subscript()`, you can simply make it conform to `Container` by
```swift
extension Array: Container {}
```

### Adding Constraints to an Associated Type

```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

### Using a Protocol in Its Associated Type's Constraints

```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}
```

```swift
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack.
}
```

```swift
extension IntStack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack<Int> {
        var result = Stack<Int>()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack<Int>.
}
```

## Generic Where Clauses

Define requirements for associated types by _generic where clause_.

```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    where C1.Item == C2.Item, C1.Item: Equatable {

        // Check that both containers contain the same number of items.
        if someContainer.count != anotherContainer.count {
            return false
        }

        // Check each pair of items to see if they're equivalent.
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }

        // All items match, so return true.
        return true
}
```

- `C1` must conform to `Container` protocol (`C1: Container`)
- `C2` same as above.
- `Item` in `C1` and `C2` must be the same type (`C1.Item == C2.Item`)
- `Item` in `C1` must conform to `Equatable` protocol (`C1.Item: Equatable`)

## Extensions with a Generic Where Clause

```swift
extension Stack where Element: Equatable {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}
```

```swift
extension Container where Item: Equatable {
    func startsWith(_ item: Item) -> Bool {
        return count >= 1 && self[0] == item
    }
}
```

```swift
extension Container where Item == Double {
    func average() -> Double {
        var sum = 0.0
        for index in 0..<count {
            sum += self[index]
        }
        return sum / Double(count)
    }
}
print([1260.0, 1200.0, 98.6, 37.0].average())
// Prints "648.9"
```

## Associated Types with a Generic Where Clause

```swift
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }

    associatedtype Iterator: IteratorProtocol where Iterator.Element == Item
    func makeIterator() -> Iterator
}
```

```swift
protocol ComparableContainer: Container where Item: Comparable { }
```

## Generic Subscripts

```swift
extension Container {
    subscript<Indices: Sequence>(indices: Indices) -> [Item]
        where Indices.Iterator.Element == Int {
            var result = [Item]()
            for index in indices {
                result.append(self[index])
            }
            return result
    }
}
```

- `Indices` conforms to `Sequence`.
- Subscript takes only a single parameter `indices` of type `Indices`.
- Sequence must traverse over elements of type `Int`.(Ensure the indices of the same type)











