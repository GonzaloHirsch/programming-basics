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

# Complex Types

## Pointers

Go has pointers.

It's zero value is `nil`

There is **no** pointer arithmetic 

How to declare a pointer:
```
var p *int
```
To generate a pointer to a variable you use the `&` operator:
```
i := 43
p = &i
```
To dereference the value of the pointer you use the `*` operator:
```
fmt.Println(*p)
*p = 21
```

## Structs

Collection of fields
```
type Point struct {
    x int
    y int
}
```

### Initializing
There are multiple ways to create an instance of a struct

Using the `Name:` syntax (order of fields is irrelevant)
```
v = Vertex{x: 1} // y:0 is implicit
```
Using all implicit values
```
v = Vertex{} // y:0 and x:0 is implicit
```
Using all explicit values
```
v = Vertex{1, 2}
```

### Fields

Fields are accessed via the dot operator (`.`)
```
v = Point{1, 2}
v.x = 4
```

Fields can also be accessed via pointers to structs, there is no need to dereference the pointer
```
v := Point{1, 2}
p := &v
p.x = 1e9
```

## Arrays
Arrays cannot be resized and use zero-based notation

They are declared like this
```
var a [10]int
```
Or like this in an explicit declaration of elements
```
a := [6]int{1,2,3,4,5,6}
```

## Slices
These are dynamically-sized flexible views into arrays

It's zero value is `nil`

**They are like references to an array, any modification to the slice will impact the array and other slices referencing that array part**

They have properties:
 - **length** -> number of items in slice, accessed via `len(slice)`
 - **capacity** -> number of elements in underlying array, accessed via `cap(slice)`

### Creation

It needs an upper and lower bound to be created (lower bound included and upper bound excluded)
```
a[low : high]
```
These can be ommited in order to use default values, the default for low is 0 and the default for high is the array length

They are declared like this
```
a := [6]int{1,2,3,4,5,6}
var s []int = a[1:4]
s = a[:4]
s = a[3:]
s = a[:]
```

Slices can also be declared in a literal way(note the lack of length between the [])
```
b := []bool{false, true, false}
```
This creates the array then it creates the slice from it

Slices can also be created using `make`, it allocates a zeroed array and returns a reference to that array using `make(array, length, capacity)` or `make(array, length)`
```
a := make([]int, 5)
b := make([]int, 0, 5)
```

### Appending

There is a built in funcion to append to a slice
```
func append(s []T, vs ...T) []T
```

The result is the give slice with the extra elements
```
var s []int
s = append(s, 4)
s = append(s, 3)
```

### Range

There is a `range` form of the `for` loop

Two values are returned in each iteration, the index and a copy of the element
```
for i, v := range elems {
    // do smth
}
```

The index or the value can be skipped.
```
// Skipping the index
for _, value := range elems{
    // do smth
}
// Skipping the value
for i, _ := range elems{
    // do smth
}
for i := range elems{
    // do smth
}
```

## Map

It maps keys to values

It's zero value si `nil`, it has no keys and keys cannot be added

### Creation

It can be created using make
```
type Point struct {
    x, y float64
}

var m map[string]Point

func main() {
    m = make(map[string]Point)
    m["Origin"] = Point{0.0, 0.0}
}
```

It can be created in a literal way
```
var m map[string]Point{
    "Origin": Point{
        0.0, 0.0
    },
    "One-One": Point{
        1.0, 1.0
    }
}
```

If the top-level type is a type name, it can be omited
```
var m map[string]Point{
    "Origin": {
        0.0, 0.0
    },
    "One-One": {
        1.0, 1.0
    }
}
```

### Insertion, Update and Deletion

To insert/update an element
```
m[key] = elem
```

To retrieve an element
```
elem = m[key]
```

To delete an elem
```
delete(m, key)
```

To query if a jey is present
```
elem, ok = m[key]
// If key is in m, ok is true. It is false otherwise
```

## Functions

Functions can be passed as values and returned as return values

The type for a function is `func(params) returnValue`
```
func compute(fn func(float64, float64) float64){
    return fn(3, 4)
}

func main() {
    hypot := func(x, y float64) float64 {
        return math.Sqrt(x*x + y*y)
    }
    fmt.Println(compute(hypot))
}
```

Functions can be closures, it means it can reference variables outside its body and be bound by that value

# Methods

Go does not have classes

Methods can be defined, and have a `receiver` which is the object that receives the call to the methods

You need an instance of the receiver to be able to call the method


Methods are functions with receiver arguments

## Receivers

Many objects can be receivers:
 - Structs
```
type Point struct {
    x, y float64
}

// Here Abs() is a method that has a Point as a receiver
func (p Point) Abs() float64 {
    return math.Sqrt(p.x*p.x + p.y*p.y)
}

func main(){
    myPoint := Point{1,2}
    fmt.Println(myPoint.Abs())
}
```
 - Non-struct types
```
type MyFloat float64

func (mf MyFloat) Abs() float64 {
    if mf < 0 {
        return float64(-mf)
    }
    return float64(mf)
}

func main(){
    mf := MyFloat{1.432}
    fmt.Println(mf.Abs())
}
```
 - Pointers
```
type Point struct {
    x, y float64
}

// Here Abs() is a method that has a Point as a receiver
func (p *Point) Abs() float64 {
    return math.Sqrt(p.x*p.x + p.y*p.y)
}

func main(){
    myPoint := Point{1,2}
    fmt.Println(myPoint.Abs())
}
```

If the receiver is not a pointer, values held by the receiver cannot be modified because the program is working with a copy. Pointers need to be used if the values contained in the receiver want to be changed

## Interfaces

Interfaces are defined as a set of method signatures
```
type Abser interface {
    Abs() float64
}
```

A value of the interface type can hold any object that implements the interface

They are **implicitly** implemented, there is no way to declare that a receiver implements an interface

The receiver can be `nil` when calling the function interface, but this does not generate a null pointer exception
```
func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}
```

### Empty Interfaces

Empty interfaces can hold values of any type
```
func main(){
    var i interface{}
    i = 42
    i = "hello"
}
```
Useful when the type is not known

## Type Assertions

The type of a variable can be asserted in this way (`T` is the type being asserted)
```
t := i.(T)
```

If the variable `i` is not of type `T`, it will panic

There is an alternate way, in order to assert this
```
t, ok := i.(T)
```
Where `ok` will be `true` if it is of type `T` or `false` otherwise, and `t` will have the value of `i` or the zero value otherwise

### Type Switches

The value of a type assertion can be used in switches in this way
```
func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Printf("Twice %v is %v\n", v, v*2)
	case string:
		fmt.Printf("%q is %v bytes long\n", v, len(v))
	default:
		fmt.Printf("I don't know about type %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
}
```
Note the `v := i.(type)`

## Errors

Functions return error values along with the returned values, if there is an error, it will return something different than `nil` in the error variable
```
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```

## Readers

The IO package contains `readers`, which are the read end of streams of data

All readers contain a method called `Read`
```
func (T) Read(b []byte) (n int, err error)
```
Where `n` is the amount of bytes read and `err` is the return value of the operation

If there is no more data to read, it will return `io.EOF` in `err`

# Go Tour Exercise Solutions

## Exercise: Slices
[Problem URL](https://tour.golang.org/moretypes/18)

Problem:

Implement Pic. It should return a slice of length dy, each element of which is a slice of dx 8-bit unsigned integers. When you run the program, it will display your picture, interpreting the integers as grayscale (well, bluescale) values.

The choice of image is up to you. Interesting functions include (x+y)/2, x*y, and x^y.

(You need to use a loop to allocate each []uint8 inside the [][]uint8.)

(Use uint8(intValue) to convert between types.)

Solution:
```
package main

import "golang.org/x/tour/pic"

func Pic(dx, dy int) [][]uint8 {
	s := make([][]uint8, dy)
	for i := 0; i < dy; i++ {
		s[i] = make([]uint8, dx)
		for j := 0; j < dx; j++ {
			s[i][j] = transform(i, j)
		}
	}
	return s
}

func transform(x, y int) uint8 {
	var val uint8 = uint8(x^y)
	return val
}

func main() {
	pic.Show(Pic)
}
```

## Exercise: Maps
[Problem URL](https://tour.golang.org/moretypes/23)

Problem:

Implement WordCount. It should return a map of the counts of each “word” in the string s. The wc.Test function runs a test suite against the provided function and prints success or failure.

You might find strings.Fields helpful.

Solution:
```
package main

import (
	"golang.org/x/tour/wc"
	"strings"
)

func WordCount(s string) map[string]int {
	m := make(map[string]int)
	words := strings.Fields(s)
	for i := 0; i < len(words); i++ {
		elem := m[words[i]]
		m[words[i]] = elem + 1
	}
	return m
}

func main() {
	wc.Test(WordCount)
}
```

## Exercise: Fibonacci closure
[Problem URL](https://tour.golang.org/moretypes/26)

Problem:

Let's have some fun with functions.

Implement a fibonacci function that returns a function (a closure) that returns successive fibonacci numbers (0, 1, 1, 2, 3, 5, ...).

Solution:
```
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	prevToPrev, prev := 0, 1
	return func() int {
		toReturn := prevToPrev
		prevToPrev, prev = prev, prev + prevToPrev
		return toReturn
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```

## Exercise: Stringers
[Problem URL](https://tour.golang.org/methods/18)

Problem:

Make the IPAddr type implement fmt.Stringer to print the address as a dotted quad.

For instance, IPAddr{1, 2, 3, 4} should print as "1.2.3.4".

Solution:
```
package main

import "fmt"

type IPAddr [4]byte

// TODO: Add a "String() string" method to IPAddr.

func (ip IPAddr) String() string {
	return fmt.Sprintf("%v.%v.%v.%v", ip[0], ip[1], ip[2], ip[3])
}

func main() {
	hosts := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for name, ip := range hosts {
		fmt.Printf("%v: %v\n", name, ip)
	}
}
```

## Exercise: Readers
[Problem URL](https://tour.golang.org/methods/22)

Problem:

Implement a Reader type that emits an infinite stream of the ASCII character 'A'.

Solution:
```
package main

import "golang.org/x/tour/reader"

type MyReader struct{}

// TODO: Add a Read([]byte) (int, error) method to MyReader.

func (reader MyReader) Read(s []byte) (int, error){
	s = s[:cap(s)]
	for i := range s {
		s[i] = 'A'
	}
	return cap(s), nil
}

func main() {
	reader.Validate(MyReader{})
}
```

## Exercise: rot13Reader
[Problem URL](https://tour.golang.org/methods/23)

Problem:

A common pattern is an io.Reader that wraps another io.Reader, modifying the stream in some way.

For example, the gzip.NewReader function takes an io.Reader (a stream of compressed data) and returns a *gzip.Reader that also implements io.Reader (a stream of the decompressed data).

Implement a rot13Reader that implements io.Reader and reads from an io.Reader, modifying the stream by applying the rot13 substitution cipher to all alphabetical characters.

The rot13Reader type is provided for you. Make it an io.Reader by implementing its Read method.

Solution:
```
package main

import (
    "io"
    "os"
    "strings"
)

type rot13Reader struct {
    r io.Reader
}

func rot13byte(sb byte) byte {
    s := rune(sb)
    if s >= 'a' && s <= 'm' || s >= 'A' && s <= 'M' {
        sb += 13
    }
    if s >= 'n' && s <= 'z' || s >= 'N' && s <= 'Z' {
        sb -= 13
    }

    return sb
}
func (rot13 rot13Reader) Read(data []byte) (readed int, err error) {
    readed, err = rot13.r.Read(data)
    for i := 0; i < readed; i++ {
        data[i] = rot13byte(data[i])
    }

    return
}

func main() {
    s := strings.NewReader("Lbh penpxrq gur pbqr!")
    r := rot13Reader{s}
    io.Copy(os.Stdout, &r)
}
```

## Exercise: Images
[Problem URL](https://tour.golang.org/methods/25)

Problem:

Remember the picture generator you wrote earlier? Let's write another one, but this time it will return an implementation of image.Image instead of a slice of data.

Define your own Image type, implement the necessary methods, and call pic.ShowImage.

Bounds should return a image.Rectangle, like image.Rect(0, 0, w, h).

ColorModel should return color.RGBAModel.

At should return a color; the value v in the last picture generator corresponds to color.RGBA{v, v, 255, 255} in this one.

Solution:
```
package main

import (
	"golang.org/x/tour/pic"
	"image"
	"image/color"
)

type Image struct {
	rows, cols int
}

func (m *Image) Bounds() image.Rectangle {
	return image.Rect(0, 0, m.rows, m.cols)
}

func (m *Image) ColorModel() color.Model {
	return color.RGBAModel
}

func (m *Image) At(x, y int) color.Color {
	v := uint8(x ^ y)
	return color.RGBA{v, v, 255, 255}
}

func main() {
	m := Image{64, 64}
	pic.ShowImage(&m)
}
```