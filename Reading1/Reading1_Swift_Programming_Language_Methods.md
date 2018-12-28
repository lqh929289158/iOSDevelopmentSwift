# Methods

Functions associated with a particular type, including class, structure and enumeration.

## Instance Methods

### The _self_ Property

Usage: when a parameter name is the same as a property name in the instance.

### Modifying Value Types from Within Instance Methods

> WARNING: By default, the properties of a _value_ type **CAN NOT** be modified from **within** its instance methods.

If you want to do so, use keyword `mutating`.

> WARNING: But, a _value-type_ constant **CAN NOT** call a `mutating` function!

### Assigning to self Within a Mutating Method

## Type Methods

Use keyword `static`. 

Use keyword `class` before keyword `func` to allow subclasses to **override** the superclass's implementation.

