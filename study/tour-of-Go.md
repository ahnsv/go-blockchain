# A Tour of Go

## Functions
### Short variable declarations
`:=` short assignment statement can be used in place of a `var` delcation with implicit type

Outside of a function, every statement begins with a keyword, so the `:=` construct is not available

### Basic types
```go
var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	fmt.Printf("Type: %T Value: %v\n", ToBe, ToBe) // Type: bool Value: false
	fmt.Printf("Type: %T Value: %v\n", MaxInt, MaxInt) // Type: uint64 Value: 18446744073709551615 
	fmt.Printf("Type: %T Value: %v\n", z, z) // Type: complex128 Value: (2+3i)
}

```

### Type conversions
The expression `T(v)` converts the value `v` to the type `T`.

```go
func main() {
	var x, y int = 3, 4
	var f float64 = math.Sqrt(float64(x*x + y*y))
	var z uint = uint(f)
	fmt.Println(x, y, z) // 3 4 5
}

### Type inference
When declaring a variable without specifying an explicit type (either by using the `:=` syntax or `var =` expression syntax), the variable's type is inferred from the value on the right hand side.

```go
// Too big for an int
const big = 10000000000

// Still ok: "untyped = untyped * untyped"
const bigger = big * 100

// No problem: Result fits in an int
var i int = big / 100

// Compile time error: "constant 10000000000 overflows int"
var j int = big
```

An untyped constant has no limits. When it’s used in a context that requires a type, a type will be inferred and a limit applied.

### For loop
- you can use `for` as `while`
- And make an infinite loop by omitting everything
- Like for, the if statement can start with a short statement to execute before the condition.
```go
func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}
```

### Pointers
No pointer arithmetic
#### Pointers to structs
```go
type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

To access the field X of a struct when we have the struct pointer p we could write (*p).X. However, that notation is cumbersome, so the language permits us instead to write just p.X, without the explicit dereference.

### Arrays
Fixed size, but have ways around
```go
func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1]) // Hello World
	fmt.Println(a) // [Hello World]

	primes := [6]int{2, 3, 5, 7, 11, 13}
	fmt.Println(primes) // [2 3 5 7 11 13]
}
```

#### Slice
a dynamically-sized, flexible view into the elements of an array. Much more common than arrays

A slice is formed by specifying two indices, a low and high bound, separated by a colon:

```go
a[low : high]
```

A slice does not store any data, it just describes a section of an underlying array.

Changing the elements of a slice modifies the corresponding elements of its underlying array.

Other slices that share the same underlying array will see those changes.

```go
func main() {
	names := [4]string{
		"John",
		"Paul",
		"George",
		"Ringo",
	}
	fmt.Println(names) // [John Paul George Ringo]

	a := names[0:2]
	b := names[1:3]
	fmt.Println(a, b) // [John Paul] [Paul George]

	b[0] = "XXX"
	fmt.Println(a, b) // [John XXX] [XXX George]
	fmt.Println(names) // [John XXX George Ringo]
}
```

#### Creating a slice with make
Slices can be created with the built-in make function; this is how you create dynamically-sized arrays.

The make function allocates a zeroed array and returns a slice that refers to that array:

```go
    a := make([]int, 5)
	printSlice("a", a) // a len=5 cap=5 [0 0 0 0 0]

	b := make([]int, 0, 5)
	printSlice("b", b) // b len=0 cap=5 []

	c := b[:2]
	printSlice("c", c) // c len=2 cap=5 [0 0]

	d := c[2:5]
	printSlice("d", d) // d len=3 cap=3 [0 0 0]
```

#### Appending to a slice
To append new elements to a slice, use `apend` function

`func append(s []T, vs ...T) []T`

If the backing array of s is too small to fit all the given values a bigger array will be allocated. The returned slice will point to the newly allocated array.

### Range

The range form of the for loop iterates over a slice or map.

When ranging over a slice, two values are returned for each iteration. The first is the index, and the second is a copy of the element at that index.

```go
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}

func main() {
	for i, v := range pow {
		fmt.Printf("2**%d = %d\n", i, v) // index, value
	}
}
```
you can skip the index or value by assigning to `_`

### Maps

A map maps keys to values.

The zero value of a map is nil. A nil map has no keys, nor can keys be added.

```go
type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

#### Map literals
Map literals are like struct literals, but the keys are required.

```go
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}
```

### Function

Functions are values too. They can be passed around just like other values.

Function values may be used as function arguments and return values.


#### Function closures
Go functions may be closures. A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.

For example, the adder function returns a closure. Each closure is bound to its own sum variable.

```go
func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}
```

### Methods

Go does not have classes, but you can define **methods** on types

A method is a function with a special *receiver* argument.

```go
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

// same as 
func Abs(v Vertex) float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

In this example, the Abs method has a receiver of type Vertex named v.

You can only declare a method with a receiver whose type is defined in the **same package as the method**. You cannot declare a method with a receiver whose type is defined in another package (which includes the built-in types such as int).

#### Pointer receivers
```go
func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(10)
	fmt.Println(v.Abs()) // 50
}

// when Scale has a value receiver
func (v Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

// prints out 5 cuz it would operate on a copy of the original vertex value, like any other function arguments
```

#### Methods and pointer indirection

Whilst functions with a pointer arg must take a pointer, methods with pointer receiver take either a value or a pointer as the receiver

Go interprets the statement `v.Scale(5)` as `(&v)`.

#### Choosing a value or pointer receiver

Why use a pointer receiver?

- the method can modify the value that its receiver points to.
- to avoid copying the value on each method call. This can be more efficient if the receiver is a large struct, for example.
- 

### Interfaces
An interface type is defined as a set of method signatures.

A value of interface type can hold any value that implements those methods.

#### Interfaces are implemented implicitly
A type implements an interface by implementing its methods. There is no explicit declaration of intent, no "implements" keyword.

Implicit interfaces decouple the definition of an interface from its implementation, which could then appear in any package without prearrangement.

#### Interface values
Under the covers, interface values can be thought of as a tuple of a value and a concrete type:

`(value, type)`

An interface value holds a value of a specific underlying concrete type.

Calling a method on an interface value executes the method of the same name on its underlying type.

#### Interface values with nil underlying values
If the concrete value inside the interface itself is nil, the method will be called with a nil receiver.

```go
func (t *T) M() {
	if t == nil {
		fmt.Println("<nil>")
		return
	}
	fmt.Println(t.S)
}
```

#### Nil interface values
A nil interface value holds neither value nor concrete type.

Calling a method on a nil interface is a run-time error because there is no type inside the interface tuple to indicate which concrete method to call.

### Type assertions

provide access to an interface value's underlying concrete value
`t := i.(T)`
This statement assets that the interface value `i` holds the concrete type `T` and assigns the underlying `T` value to the variable `t`.

If it is false, it will trigger a panic

```go
func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)

	f = i.(float64) // panic
	fmt.Println(f)
}
```

### Type switches
A construct that permits several type assertions in series
```go
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
```

### Stringer
```go
type Stringer interface {
    String() string
}
```
A Stringer is a type that can describe itself as a string. The fmt package (and many others) look for this interface to print values.

#### Exercise: Stringer
```go
// TODO: Add a "String() string" method to IPAddr.
func (ipaddr IPAddr) toString() string {
	return fmt.Sprintf("%v.%v.%v.%v", ipaddr[0], ipaddr[1], ipaddr[2], ipaddr[3])
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

### Error

Go programs express error state with error values.

The error type is a built-in interface similar to fmt.Stringer:

```go
type error interface {
    Error() string
}
```

### Readers
The io.Reader interface has a Read method:

```go
func (T) Read(b []byte) (n int, err error)
```

Read populates the given byte slice with data and returns the number of bytes populated and an error value. It returns an `io.EOF` error when the stream ends.

The example code creates a strings.Reader and consumes its output 8 bytes at a time.

### Images

```go
package image

type Image interface {
    ColorModel() color.Model
    Bounds() Rectangle
    At(x, y int) color.Color
}
```

## Concurrency

### Goroutines
A goroutine is a lightweight thread managed by the Go runtime.

```go
go f(x, y, z)
```

**Goroutines are functions or methods that run concurrently with other functions or methods.** Goroutines can be thought of as light weight threads. The cost of creating a Goroutine is tiny when compared to a thread. Hence its common for Go applications to have thousands of Goroutines running concurrently.

- Goroutines are extremely cheap when compared to threads. They are only a few kb in stack size and the stack can grow and shrink according to needs of the application
- The Goroutines are multiplexed to fewer number of OS threads. 
- Goroutines communicate using channels. Channels by design prevent race conditions from happening when accessing shared memory using Goroutines. 

```go
package main

import (  
    "fmt"
    "time"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    time.Sleep(1 * time.Second) // wait for other Goroutines to finish their executions 
    fmt.Println("main function")
}
```
Multiple Goroutines
```go
package main

import (
	"fmt"
	"time"
)

func numbers() {
	for i := 1; i <= 5; i++ {
		time.Sleep(250 * time.Millisecond)
		fmt.Printf("%d ", i)
	}
}
func alphabets() {
	for i := 'a'; i <= 'e'; i++ {
		time.Sleep(400 * time.Millisecond)
		fmt.Printf("%c ", i)
	}
}
func main() {
	go numbers()
	go alphabets()
	time.Sleep(3000 * time.Millisecond)
	fmt.Println("main terminated")
}
```

### Channels
Channels are a typed conduit through which you can send and receive values with the channel operator, <-.

```go
ch <- v    // Send v to channel ch.
v := <-ch  // Receive from ch, and
           // assign value to v.
```
Like maps and slices, channels must be created before use:

```go
ch := make(chan int)
```

By default, sends and receives block until the other side is ready. This allows goroutines to synchronize without explicit locks or condition variables.

```go
func sum(s []int, c chan int) {
	sum := 0
	for _, v := range s {
		sum += v
	}
	c <- sum // send sum to c
}

func main() {
	s := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(s[:len(s)/2], c)
	go sum(s[len(s)/2:], c)
	x, y := <-c, <-c // receive from c

	fmt.Println(x, y, x+y)
}
```

#### Range and close
A sender can close a channel to indicate that no more values will be sent. Receivers can test whether a channel has been closed by assigning a second parameter to the receive expression: after

```go
v, ok := <-ch
```

ok is `false` if there are no more values to receive and the channel is closed.

The loop for `i := range c` receives values from the channel repeatedly until it is closed.

### Select
The select statement lets a goroutine wait on multiple communication operations.

A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.

```go
func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

### Default Selection
```go

func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
}
```
### sync.Mutex
One goroutine can access a variable at a time to avoid conflict, called mutual exclusion, mutex

`Lock` and `Unlock` are provided by `sync.Mutex`

Can also use `defer` to ensure the mutex will be unlocked

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```