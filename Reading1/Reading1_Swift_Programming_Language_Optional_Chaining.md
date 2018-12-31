# Optional Chaining

Cope with `nil` value.

## Optional Chaining as an Alternative to Forced Unwrapping

Optional chaining fails _gracefully_ when the optional is `nil`, while forced unwrapping triggers a _runtime error_.

Method: Use `let` and `?` to access your optional, and write a `if-else` branch.

```swift
class Person {
    var residence: Residence?
}

class Residence {
    var numberOfRooms = 1
}

let john = Person()

//Forced Unwrapping
let roomCount = john.residence!.numberOfRooms
// this triggers a runtime error

//Optional chaining
if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "Unable to retrieve the number of rooms."

john.residence = Residence()

if let roomCount = john.residence?.numberOfRooms {
    print("John's residence has \(roomCount) room(s).")
} else {
    print("Unable to retrieve the number of rooms.")
}
// Prints "John's residence has 1 room(s)."

```

## Defining Model Classes for Optional Chaining



