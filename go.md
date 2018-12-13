# Program structure

* 4 declaration types: `func`, `type`, `const` and `var`
* `var name type = expression`. You can omit `type` or `= expression` but not both
* If you don't specify a value, it defaults to the default value of that type. Everything is initialized
* `:=` is a _declaration_, whereas `=` is an _assignment_

## Pointers
* a _pointer_ value is the address of a variable. It is the location at which the value is stored.
* `&x` yields a pointer to an integer (if `var x int`). That is, a value of type *int: "pointer to int"

```
x := 1
p := &x         // p, of type *int, points to x
fmt.Println(*p) // "1"
*p = 2          // equivalent to x = 2
fmt.Println(x)  // 2
```

* the 0 value of a pointer is `nil`
* -> the test `p != nil` is true if `p` points to a variable
* pointers are equal if they point to the same variable or if they're both nil


## Validation assignments

```
v, ok = m[key]
v, ok = x.(T)
v, ok = <- ch
```

## Type declarations

You can define simple type declarations

```
type Celsius float
type Farenheit float

var i = 1
c = Celsius(i)
f = Farenheit(i)
```

## Packages

Each package is defined by :

* a unique string called it's *import path*
* a *package name*
* you can fetch all the deps for a project and add them to your local machine with `go get -d ./...`

By convention, the end of the import path matches the package name.

Any file may contain any number of functions whose declaration is just `func init(){ ...}`. They are automatically run when the program starts (they are typically used to initialize complicated variables, like tables of data)

## Data types

There are basically 4 data types:
 * *basic types*: numbers, strings and bools
 * *aggregate types*: arrays and structs
 * *reference types*: pointers, maps, slices, functions and channels
 * *interfaces*

### integers
They can be signed or unsigned, and they can be of four different sizes: 8, 16, 32 and 64. Using `int` will let the compiler pick the best one for the platform

`rune` is a a synonym of `int32`, which can represent a unicode codepoint
`byte` is a synonym of `uint8`
`int` has a range of -128 to 127, and `uint` has a range of 0 to 255

Typically, use `int` all the time, even if the number will only be positive. `uints` are used when bit operations are important (like crypto, or hyper optimized transport)

* You can use octal numbers by prefixing 0, like `0666` (used only for POSIX permissions, really...)
* You can use 0x for hexadecimal notation (caps don't matter)

### floats
They can be of size 32 of 64

### Complex numbers
They can be of size `complex64` or `complex128`, who's components are of `floaty32` and `float64`, respectively.
```
var x complex128 = complex(1, 4) // 1 + 4i
fmt.Println(real(x)) // 1
fmt.Println(imag(x)) // 4
x := 1 + 4i // also works for assignment
```

### Strings
They are simply a sequence of bytes (`uint8`). They are conventionally interpreted as UTF8 encoded sequences of Unicode code points (`rune`s, or `int32`)
* `len` returns the number of bytes, not runes
* `s[i]` gets the `i-th` byte, not rune
* strings are immutable, so you can't assign `s[i] := 'L' // compile error`
* a *string literal* is a sequence of bytes enclosed in double quotes `"Hello, world"`
* ALl strings are encoded with UTF8 (encoding unicode spec). It has nice properties, like substring, suffix or prefix matching can work directly on the encoded value
* `unicode`, `strings`, `bytes` and `strconmv` are useful packages for handy functions
* Notably, the `bytes` package provides the `Buffer` type for efficient manipulation of byte slices (byte slices are mutable, strings are not)
* A `Buffer` starts empty but grows as you add `string`, `byte` or `[]byte` things to it
* converting an integer to a string interprets it as a rune value: `string(65) // 'A'`. If invalid, it prints the unknown `?` unicode symbol

## Composite types

structs, arrays, slices, and maps
A slice has 3 components: 
* a `length`: number of elts in the slice
* a `pointer`: points to the first element of the slice, which isn't necessarily the first element of the underlying array
* a `capacity`: number of available elements between the first elt of the slice and the size of the underlying array

Unlike arrays, slices are not comparable, and you cannot use `==` to test equality.
The zero value of a slice type is `nil`.

Using `append` to extend slices is the right way to do it, since it's smart about resizing the cap of the underlying array

## Errors

> Example

```
type ObsoleteVersionError struct {
	taskID         int
	requestVersion int
	dbVersion      int
}

func (o ObsoleteVersionError) Error() string {
	return fmt.Sprintf("task %d save obsolete version error: request version %d is older than db version %d", o.taskID, o.requestVersion, o.dbVersion)
}
```
