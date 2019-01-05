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

