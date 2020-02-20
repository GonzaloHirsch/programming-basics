# Go Guide
[Resource](https://tour.golang.org/welcome/1)

# Basics

## Basic Types
These are the basic types in Go
 - bool
 - string
 - int int8 int16 int32 int64
 - uint uint8 uint16 uint32 uint64 uintptr
 - byte (alias uint8)
 - rune (alias int32, represents Unicode code point)
 - float32 float64
 - complex64 complex128

The `int`, `uint` and `uintptr` are 32 bits in 32 bits machines and 64 in 64 bits machines

If an integer is needed, `int` should be used unless there is a special need

### Type Conversions

To convert from one type to another, the statement is `T(v)` where T is the type and v the value
```
var i int = 42
var f float64 = float64(i)
i := 42
f := float64(i)
```


## Variables

The `var` statement declares a list of variables
```
var var1, var2, var3 type
var c, python, java bool
var i int
```
If a variable is declared without an explicit initial value, it is given it's *zero value*
 - `0` for numeric types 
 - `false` for boolean type
 - `""` (empty string) for string type

### Initializers

Declarations can include initializers, **one per variable**
```
var i, j int = 1, 2
```
If there is an initializer present, the type can be ommited and the variable takes the type of the initializer
```
var c, python, java = true, false, "no!"
```

### Short Declaration

**Only inside functions** the `:=` operator can be used instead of a `var` statement, the type is implicit

### Constants

They are declared like variables but with the `const` keyword and cannot be declared with the `:=` operator
```
const Pi = 3.14
```
Untyped constants take the type needed by the context

## Functions

### Parameteres
Can take 0 or more parameters

Type comes **after** variable name and return value comes **after** function parameters
```
func add(x int, y int) int {
    return x + y
}
```
If many parameters share the same type, the type can be ommited except for the last one
```
func add(x, y int) int {
    return x + y
}
```

### Return Values
Can return 0 or more results, sepparated by a comma (",")
```
func swap(x, y string) (string, string) {
    return y, x
}
```
Can use **named** return values, used as variables declared at the top of the function. Those values are returned with a "naked" return (this should be used only in short functions because it harms readability)
```
func split(sum int) (x, y int){
    x = sum * 4 / 9
    y = sum - x
    return
}
```

## How to Run

Everything inside the `main` function is run
```
func main(){
    ...
}
```

## Imports

There are 2 ways of importing stuff

Multiple statements:
```
import "fmt"
import "math"
```
Factored statement:
```
import (
    "fmt"
    "math"
)
```

### Exported Names

Any function beginning with capital letter is exported, and accessable with the dot (".") operator

## Packages

Programs start running in package `main`
```
package main
```
Package name is the same as the last element of the import path

# Flor Control
The only looping statement is the `for`

## For
The for has the **init statement**(optional), **condition statement** and the **post statement**(optional), all separated by ;
```
sum := 0
for i := 0; i < 10; i++ {
    sum += 1
}
```

## While
The "while" is a `for` without the init and post statements
```
sum := 1
for sum < 10 {
    sum += sum
}
```

## Forever
Infinite loop compressed
```
for {
    ...
}
```

## If
The expression between the "if" and the braces ("{}") is evaluated
```
func do(x int) {
    if x < 5 {
        return 1
    }
    return 0
}
```
Variables can be declared inside the scope of the if statement and will last until the end of the if.
```
func do(x int) {
    if y := x + 4; y > 10 {
        return y
    }
    return 0
}
```
Variables declared inside the scope of an `if` can be used in an `else`
```
func do(x int) {
    if y := x + 4; y > 10 {
        return y
    } else {
        return y - 10
    }
}
```

## Switch
Switches only run the selected case, and can use scope declarations like an `if`
```
func exaple(x string) {
    switch v := x + "_"; v {
        case "hi_":
            ...
        case "bye_":
            ...
        default:
            ...
    }
}
```
`break` statements are not needed, they are provided automatically by Go

Cases are evaluated from top to bottom, and in a **lazy** way, it stops when it finds a match

If the switch has no condition, it is the same as `switch true`
```
func whatTime(t int) string {
    switch {
        case t < 12:
            return "am"
        case t < 24:
            return "pm"
        default:
            return "wtf"
    }
}
```

## Defer

The `defer` statement postpones the execution of a function until the enclosing function returns
```
import "fmt"

func main() {
    defer fmt.Println("World")
    fmt.Println("Hello")
}
```
The function above prints "Hello World"

Multiple defered function calls are pushed onto a stack, and when the enclosing funcion ends, it calls them in a LIFO order.
```
import "fmt"

func main() {
    fmt.Println("start")

    for i := 0; i < 10; i++ {
        defer fmt.Println(i)
    }

    fmt.Println("end")
}
```
The function above prints "start end 9 8 7 6 5 4 3 2 1 0"