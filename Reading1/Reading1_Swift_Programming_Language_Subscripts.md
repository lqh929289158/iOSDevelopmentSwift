# Subscripts

Classes, structures, enumerations can define _subscripts_.

> Like: opertor `[]` overloding.

## Subscript Syntax

More similar to **computed properties**.

```swift
subscript(index: Int) -> Int {
    get {
        // return an appropriate subscript value here
    }
    set(newValue) {
        // perform a suitable setting action here
    }
}
```

Or you can just use get, when you will ignore keyword `get`.

```swift
subscript(index: Int) -> Int {
    // return an appropriate subscript value here
}
```

## Subsript Usage.

Usually used to access elements in a _sequence_.

> NOTE: The return value of accessing dictionary is of type _optional_.

## Subscript Options

- Parameter number : **NO** limitation.
- Parameter type : **NO** limitation.
- Return value type : **NO** limitation.
- Variadic paramter : **OK**.
- **NO** In-Out parameters.
- **NO** Default parameter values.
- _Overloading_ : **OK**. With different paramter type.
