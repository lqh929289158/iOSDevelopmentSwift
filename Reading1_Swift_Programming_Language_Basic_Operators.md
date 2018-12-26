# Basic Operators

> NOTE:
> The assignment operator (=) doesn't return a value, to prevent it from being mistakenly used when the equal to operator (==) is intended.

> NOTE:
> Arithmetic operators (+, -, \*, /, %...) detect and disallow value overflow.

## Terminology

- Unary: single target. e.g. `-`
  - prefix : `!b`
  - postfix : `b!`
- Binary: two targets. e.g. `+`
- Ternary: three targets. e.g. `a ? b : c`

## Assignment Operator

```swift
if x = y {
  // NOT VALID!
}
```

## Arithmetic Operators

`+` operator support `String` concatenation.

```swift
"hello" + "world"
```

### Remainder Operator (%)

### Unary Plus Operator (+)

### Unary Minus Operator (-)

## Compound Assignment Operators (e.g. +=)

> WARNING: compound assigment operators DO NOT return a value!

## Comparison Operators

Tuple comparsion: 

- Make sure the two tuples have the same type.
- Make sure the types in the tuples is comparable.
- Compare elements from first to last until they are different.

In another word, the elements at beginning have higher priority!

```swift
(1, "z") < (2, "a") // true
(3, "za") < (3, "az") // false
(4, "dog") == (4, "dog") // true
("blue", false) < ("purple", true) //Error! Boolean is not comparable
```

## Ternary Conditional Operator

`question ? answer1 : answer2`

## Nil-Coalescing Operator

`a ?? b` = `a != nil ? a! : b`

> NOTE: If `a` is non-nil, the value of `b` is not evaluated, which is known as _short-circuit evaluation_.

## Range Operators

### Closed Range Operator

`a...b` range including `a` and `b`, and `a <= b`

```swift
for index in 1...5{
  //do with index
}
```

### Half-Open Range Operator

`a..<b` range including `a` but `b`, and `a <= b`.

> NOTE: IF `a == b`, the range will be empty

```swift
for i in 0..<count {
  //do with i
}
```

### One-Sided Ranges

```swift
for name in names[2...]{
  // do with name
}

for name in names[...2]{
  // do with name
}

for name in names[..<2]{
  // do with name
}

let range = ...5
//..., -100, -99, ... , -1, 0, 1, ... 5

range.contains(4) // true
```
## Logical Operators ( ! , &&, || )

_Short-circuit evaluation_ in `&& ||`

_Left-associative_ `&& ||`






























