# Strings and Characters

## String Literals

### Multiline String Literals

```swift
let quotation = """
The w
assd

awea"we
gfge"
"""
```

![Whtiespace rules](https://docs.swift.org/swift-book/_images/multilineStringWhitespace_2x.png)

### Special Characters in String Literals

- `\0` : null character
- `\\` : backslash
- `\t` : horizontal tab
- `\n` : line feed
- `\r` : carriage return 
- `\"` : double quotation mark
- `\'` : single quotation mark
- `\u{n}` : Unicode, n is a 1-8 digit **hexadecimal**.

## Initializing an Empty String

```swift
var emptyString = ""
var anotherEmptyString = String()

if emptyString.isEmpty {
  print("Nothing") 
}
//print "Nothing"
```

## String Mutability

Variables `var` can be mutated, while constants `let` CAN NOT.

## String Are Value Types

In Swift, `String` type is a **value type**.

## Working with Characters

```swift
for character in "Dog!" {
  print(character)
}
// D
// o
// g
// !
```

You can also create a stand-alone `Character` constant or variable.
```swift
let c: Character = "!"
var ch: Character = "w"
```

Convert `[Character]` to `String`

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// Prints "Cat!🐱"
```

## Concatenating Strings and Characters

Concatenating `String`

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome now equals "hello there"

var instruction = "look over"
instruction += string2
// instruction now equals "look over there"
```

Concatenating `Character` to `String`

```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome now equals "hello there!"
```

> NOTE: You CAN NOT concatenating `String` or `Character` to `Character`

Concatenating lines of `String`

```swift
let badStart = """
one
two
"""
let end = """
three
"""
print(badStart + end)
// Prints two lines:
// one
// twothree

let goodStart = """
one
two

"""
print(goodStart + end)
// Prints three lines:
// one
// two
// three
```

## String Interpolation

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message is "3 times 2.5 is 7.5"
```

> NOTE: NO unescaped backslash, a carriage return, or a line feed in interpolation part.

## Unicode

## Counting Characters

```swift
var word = "cafe"
print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in cafe is 4"

word += "\u{301}"    // COMBINING ACUTE ACCENT, U+0301

print("the number of characters in \(word) is \(word.count)")
// Prints "the number of characters in café is 4"
```

> NOTE: `count` and `length` may be different concept when considering unicode.

## Accessing and Modifying a String

### Accessing by index

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

`String` DO NOT include `endIndex`

```swift
greeting[greeting.endIndex] // Error
greeting.index(after: greeting.endIndex) // Error
```

> NOTE: The syntax above about `index` **conforms** to  `Collection` protocol.

### **IMPORTANT: indices**

```swift
for index in greeting.indices {
    print("\(greeting[index]) ", terminator: "")
}
// Prints "G u t e n   T a g ! "
```

### Insert and Removing

- Insert: Use `insert(contentsOf:at:)`

```swift
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome now equals "hello!"

welcome.insert(contentsOf: " there", at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there!"
```

- Remove: Use `remove(at:)`

```swift
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

> NOTE: The syntax above about inseration and removal **conforms** to `RangeReplacableCollection` protocol.

## Substrings

`Substring` is a new type. When you extract a slice from a `String`, you will get an instance of `Substring`.

Anyway, you do not have to pay any attention to the memory management.

```swift
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"

// Convert the result to a String for long-term storage.
let newString = String(beginning)
```

![String and Substring](https://docs.swift.org/swift-book/_images/stringSubstring_2x.png)

> NOTE: Both `String` and `Substring` **conform** to `StringProtocol` protocol, so that the methods and functions are shared.

##  Comparing Strings

- `==` : compare characters one by one.
- `hasPrefix(_:)` : Match prefix
- `hasSuffix(_:)` : Match suffix







