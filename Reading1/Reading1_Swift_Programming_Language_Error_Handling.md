# Error Handling

## Representing and Throwing Errors

Conforms to the `Error` protocol. This _empty_ protocol indicates that a type can be used for error handling.

```swift
enum VendingMachineError: Error {
    case invalidSelection
    case insufficientFunds(coinsNeeded: Int)
    case outOfStock
}
```

```swift
throw VendingMachineError.insufficientFunds(coinsNeeded: 5)
```

## Handling Errors

Four ways to handle errors in Swift:

- Propagate the error from a function to the code that calls that function.
- Handle the error using a `do-catch` statement
- Handle the error as an optional value.
- Assert that the error will not occur.

> NOTE: Error handling in Swift **DOSE NOT** involve unwinding the call stack. As such, the performance characteristics of a `throw` statement are comparable to those of a `return` statement.

### Propagating Errors Using Throwing Functions

A function marked with `throws` is called a _throwing function_. If there is return type, write `throws` before `->`.

```swift
func canThrowErrors() throws -> String

func cannotThrowErrors() -> String
```

> WARNING: _ONLY_ throwing functions can propagate errors. Any errors thrown **inside** a _nonthrowing_ function must be handled inside the function.

```swift
struct Item {
    var price: Int
    var count: Int
}

class VendingMachine {
    var inventory = [
        "Candy Bar": Item(price: 12, count: 7),
        "Chips": Item(price: 10, count: 4),
        "Pretzels": Item(price: 7, count: 11)
    ]
    var coinsDeposited = 0

    func vend(itemNamed name: String) throws {
        guard let item = inventory[name] else {
            throw VendingMachineError.invalidSelection
        }

        guard item.count > 0 else {
            throw VendingMachineError.outOfStock
        }

        guard item.price <= coinsDeposited else {
            throw VendingMachineError.insufficientFunds(coinsNeeded: item.price - coinsDeposited)
        }

        coinsDeposited -= item.price

        var newItem = item
        newItem.count -= 1
        inventory[name] = newItem

        print("Dispensing \(name)")
    }
}
```

Call _throwing function_ with `try`.

```swift
let favoriteSnacks = [
    "Alice": "Chips",
    "Bob": "Licorice",
    "Eve": "Pretzels",
]
func buyFavoriteSnack(person: String, vendingMachine: VendingMachine) throws {
    let snackName = favoriteSnacks[person] ?? "Candy Bar"
    try vendingMachine.vend(itemNamed: snackName)
}
```

Propagte errors to upper function.

```swift
struct PurchasedSnack {
    let name: String
    init(name: String, vendingMachine: VendingMachine) throws {
        try vendingMachine.vend(itemNamed: name)
        self.name = name
    }
}
```

### Handling Errors Using Do-Catch

```swift
do {
    try expression
    statements
} catch pattern 1 {
    statements
} catch pattern 2 where condition {
    statements
} catch {
    statements
}
```

> NOTE: If a `catch` clause doesn't have a pattern, the clause matches any error and binds the error to a local constant named `error`.

```swift
var vendingMachine = VendingMachine()
vendingMachine.coinsDeposited = 8
do {
    try buyFavoriteSnack(person: "Alice", vendingMachine: vendingMachine)
    print("Success! Yum.")
} catch VendingMachineError.invalidSelection {
    print("Invalid Selection.")
} catch VendingMachineError.outOfStock {
    print("Out of Stock.")
} catch VendingMachineError.insufficientFunds(let coinsNeeded) {
    print("Insufficient funds. Please insert an additional \(coinsNeeded) coins.")
} catch {
    print("Unexpected error: \(error).")
}
// Prints "Insufficient funds. Please insert an additional 2 coins."
```

> WARNING:In a _nonthrowing_ function, an enclosing do-catch clause must handle the error. In a _throwing_ function, either an enclosing do-catch clause or the caller must handle the error. If the error propagates to the top-level scope without being handled, you’ll get a runtime error.

```swift
func nourish(with item: String) throws {
    do {
        try vendingMachine.vend(itemNamed: item)
    } catch is VendingMachineError {
        print("Invalid selection, out of stock, or not enough money.")
    }
}

do {
    try nourish(with: "Beet-Flavored Chips")
} catch {
    print("Unexpected non-vending-machine-related error: \(error)")
}
// Prints "Invalid selection, out of stock, or not enough money."
```

### Converting Errors to Optional Values

Using `try?` to convert an error to an optional value. If error is thrown, the value is `nil`.

```swift
func someThrowingFunction() throws -> Int {
    // ...
}

let x = try? someThrowingFunction()

let y: Int?
do {
    y = try someThrowingFunction()
} catch {
    y = nil
}
```

Use optional to get return value from a _throwing_ function.

```swift
func fetchData() -> Data? {
    if let data = try? fetchDataFromDisk() { return data }
    if let data = try? fetchDataFromServer() { return data }
    return nil
}
```

### Disabling Error Propagation

Use `try!` to disable error propagation, and wrap the call in a runtime assertion that no error will be thrown.

```swift
let photo = try! loadImage(atPath: "./Resources/John Appleseed.jpg")
```

## Specifying Cleanup Actions

Use `defer` to clean up  before exiting the current block of code, no matter _how_ you exit it(error or `return` or `break`)

> WARNING: Deferred actions are executed in the reverse of the order written in src code. That means, the first `defer` will be exectued last when exit. The last `defer` statement in src code will be executed first.

```swift
func processFile(filename: String) throws {
    if exists(filename) {
        let file = open(filename)
        defer {
            close(file)
        }
        while let line = try file.readline() {
            // Work with the file.
        }
        // close(file) is called here, at the end of the scope.
    }
}
```

> NOTE: You can use `defer` without error handling involved.




