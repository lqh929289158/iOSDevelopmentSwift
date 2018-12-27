# Structures and Classes

> NOTE: **NO** need to create seperate interface and implementation files for custome structures and classes.

## Comparing Structures and Classes

Similarities:

- Define **properties** to store values
- Define **methods** to provide functionality
- Define **subscripts** to provide access to their values using subscript syntax
- Define **initializers** to set up their initial state
- Be **extended** to expand their functionality beyond a default implementation
- Conform to **protocols** to provide standard functionality of a certain kind

Classes' additional capabilities that structures don't have:

- **Inheritance** enables one class to inherit the characteristics of another.
- **Type casting** enables you to check and interpret the type of a class instance at runtime.(???)
- **Deinitializers** enable an instance of a class to free up any resources it has assigned.
- **Reference counting** allows more than one reference to a class instance.

### Definition Syntax

Both structure and class are a new Swift type, so name them with a **capital** beginning letter.

They are almost the same and both need to initialize all properties in definition.

### Structure and Class Instances

Use simplest initializer(with no parameters) and all properties will be set to default value.

### Accessing Properties

Using dot `.` to access properties.

### Memberwise Initializers for Structure Types

`StructureName(property1: literal1, property2: literal2)`

## Structures and Enumerations Are Value Types

A _value type_ is a type whose value is copied when it’s assigned to a variable or constant, or when it’s passed to a function.

> NOTE: Collections defined by the standard library like **arrays, dictionaries, and strings** use an optimization to reduce the performance cost of copying. Instead of making a copy immediately, these collections share the memory where the elements are stored between the original instance and any copies. If one of the copies of the collection is modified, the elements are copied just before the modification. The behavior you see in your code is always as if a copy took place immediately.

## Class Are Reference Types

Unlike value types, _reference_ types are not copied when they are assigned to a variable or constant, or when they are passed to a function. Rather than a copy, a reference to the same existing instance is used.

### Identity Operators

Check two constants or variables refer to the same single instance.

- Identical to (===)   (DO NOT mistake it with _equal to_ ==)
- Not identical to (!==)

### Pointers

More refer to [Manual Memory Management](https://developer.apple.com/documentation/swift/swift_standard_library/manual_memory_management)



