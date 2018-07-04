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
```

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
