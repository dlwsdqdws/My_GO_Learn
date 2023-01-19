# My GO Cookbook

- [My GO Cookbook](#my-go-cookbook)
  - [Basic Grammer](#basic-grammer)
    - [Variable](#variable)
    - [Control Structures](#control-structures)
      - [If structure](#if-structure)
      - [Switch Structure](#switch-structure)
      - [For Structure](#for-structure)
    - [Data Structure](#data-structure)
      - [Array](#array)
      - [Slice](#slice)
      - [Map](#map)
    - [Struct](#struct)
    - [Iteration](#iteration)
      - [transverse a slice](#transverse-a-slice)
      - [transverse a map](#transverse-a-map)
      - [transverse a struct](#transverse-a-struct)
    - [Function](#function)
      - [pass by value](#pass-by-value)
      - [pass by reference](#pass-by-reference)
    - [Errors](#errors)
    - [String](#string)
      - [Format](#format)
      - [String Handling](#string-handling)
      - [Strconv](#strconv)
    - [JSON](#json)
    - [Time](#time)
    - [Processes](#processes)
  - [Concurrent and Parallel](#concurrent-and-parallel)
    - [Goroutine](#goroutine)
    - [Channel](#channel)
    - [Synchronization](#synchronization)
      - [Mutex](#mutex)
      - [WaitGroup](#waitgroup)
  - [Dependency Management](#dependency-management)
    - [GO Path](#go-path)
    - [GO Vendor](#go-vendor)
    - [GO Module](#go-module)
  - [Test](#test)
    - [Unit Test](#unit-test)
    - [Mock](#mock)
    - [Benchmark](#benchmark)
  - [Code Optimization](#code-optimization)
    - [Go Style](#go-style)
      - [Standard](#standard)
      - [Format Tools](#format-tools)
      - [Comment](#comment)
      - [Naming Conventions](#naming-conventions)
        - [Variable](#variable-1)
        - [Function](#function-1)
        - [Package](#package)
      - [Code](#code)
        - [Control Flow](#control-flow)
        - [Error Handling](#error-handling)
    - [Performance Optimization](#performance-optimization)
      - [Slice](#slice-1)
      - [Map](#map-1)
      - [String](#string-1)
      - [Struct](#struct-1)
      - [Atomic Package](#atomic-package)
  - [Useful Tools](#useful-tools)
  - [Acknowledgements](#acknowledgements)

**WELLCOME TO EDIT**

## Basic Grammer

### Variable

1. `:=` and `const` can automatically infer the variable type, `var` is typically used for variables whose type is inconsistent with the initialization expression.

```go
num := 1
const pi = 3.14
var a string
```

2. Scientific notation is supported.
   eg, `const i = 3e20 / h`

### Control Structures

#### If structure

1. bool expression does not need `()`, but content needs `{}`
2. Variable can be declared just behind `if`.

```go
if v := math.Pow(x,y); v < limit {
    return v
}
```

#### Switch Structure

1. switch - case is essentially a sequence of if - else statements, that is, case can be used without constants. `break` is not needed.

```go
fmt.Println("When's Saturday?")
today := time.Now().Weekday()
switch time.Saturday {
case today + 0:
    fmt.Println("Today.")
case today + 1:
    fmt.Println("Tomorrow.")
case today + 2:
    fmt.Println("In two days.")
case today + 3:
    fmt.Println("In three days.")
case today + 4:
    fmt.Println("In four days.")
default:
    fmt.Println("Too far away.")
}
```

2. Can be used to beautify an if - else sequence. Variable can be declared just behind switch.

```go
switch t := time.Now() {
case t.Hour() < 12:
	fmt.Println("morning")
case t.Hour() < 18:
	fmt.Println("afternoon")
default:
	fmt.Println("evening")
}
```

#### For Structure

1. Dead loop

```go
for {	}
```

2. Can be used as `while` loop in C/C++

```go
i := 1
for i <= 3 {
	fmt.Println(i)
	i = i + 1
}
```

### Data Structure

#### Array

1. Declaration

```go
// without initialization
var a [5]int

// with initialization
b := [5]int{1, 2, 3, 4, 5}
```

Other operations are very similar with those in C/C++.

#### Slice

1. Slice itself does not store the data. It just like reference of variable-length array in C/C++.Changing a value in a slice will change the data it 'points' the array, so other slices 'point' the array(eg, copy) will also change.
2. Declaration

```go
// part of array
a := [5]int{1, 2, 3, 4, 5}
var b []int = a[2:4]

// use make
s := make([]string, 3)

// [](type) without a number in []
good := []string{"g", "o", "o", "d"}
```

3. append operation needs to be assigned to the original slice.

```
s = append(s, "good")
```

4. Initialization

```go
board := [][]int{
	[]int{1, 0, 1},
	[]int{0, 1, 1},
	[]int{1, 1, 0},
}
```

#### Map

1. Declaration

```go
m := make(map[key]value)
```

2. Initialization

```go
var m = map[string]int{
	"four": 4,
	"five": 5,
}
```

3. CRUD

```go
// create & update
m[key] = value

// read : ok == true if (key, value) in m else false
value, ok := m[key]

// delete
delete(m, key)
```

### Struct

1. Declaration

```go
type user struct {
	name string
	id   int
}
```

2. Initialization

```go
var user1 = user{}   // name : empty, id : 0
var user2 = user{name: "lulei"} // name : "lulei", id : 0
var user3 = user{"lulei", 1}   // name : "lulei", id : 1
```

3. Access Member
   We can access struct's member by using `.`

```go
var user4 = user{}
user4.id = 2     // name : empty, id : 2
```

4. Method

```go
func (u user) checkId(id int) bool {
	return u.id == id
}
```

It can be written as a function.

```go
func checkIdFunc(u *user, id int) bool {
	return u.id == id
}
```

And just like function, method can also pass by reference.

### Iteration

#### transverse a slice

```go
nums := []int{2, 3, 4}
for index, value := range nums {
	fmt.Println(index, value)
}
```

The order of output element traversed is determined by its index.

#### transverse a map

```go
// iterate hole map
for k, v := range m {
	fmt.Println(k, v)
}

// iterate only keys
for k := range m {
	fmt.Println("key", k)
}

// iterate only values
for _, v := range m {
	fmt.Println("value", v)
}
```

The storage location of the data in the map is random, so a map can NOT be expected to return results in some desired order when traversed.

#### transverse a struct

```go
// Firstly, use reflect.ValueOf() to get the reflection instance
value := reflect.ValueOf(user3)

// Secondly, traverse through NumField
for i := 0; i < value.NumField(); i++ {
    fmt.Println(i, value.Field(i))  // Thirdly, obtain the field
}
```

### Function

#### pass by value

Function in GO is pass by value by default

```go
func function_name(variable variable_type) return_type {
    return return_value
}
```

A function can return multiple values.

```go
func exist(m map[string]int, k string) (v int, err bool) {
    v, err = m[k]
    return v, err
}

```

#### pass by reference

<br>Pointers are needed to edit parameters of the function

```go
func increase(a int) {
    a += 1
}

func increase2(a *int) {
    *a += 1
}

num := 1
increase(num)    // pass by value
fmt.Println(num) // 1

increase2(&num)  // pass by reference
fmt.Println(num) // 2

```

### Errors

Usually, we will return a boolean value `error` alone with return values. `nil` for no error. <br>For example, when searching for an element in an array, the return value should contain the found element and `error`. When the element is found, `error` should be `nil` otherwise it should remind the operator of an error.

```go
func search(users []user, name string) (u *user, err error) {
	for _, u := range users {
		if u.name == name {
			return &u, nil
		}
	}
	return nil, errors.New("No such user")
}
```

When calling a function, we should first check whether the returned `error` reports an exception.

```go
if err != nil {
	panic(err)
} else {
	fmt.Println(u.id)
}
```

### String

#### Format

| format |              meaning               |
| :----: | :--------------------------------: |
|  `%v`  |        return native value         |
| `%+v`  |  expand struct's names and values  |
| `%#v`  |       value in syntax format       |
|  `%b`  |            binary value            |
|  `%f`  |            float number            |
| `%.2f` | float number with 2 decimal places |

#### String Handling

|         method          |                                   meaning                                   |
| :---------------------: | :-------------------------------------------------------------------------: |
|         `Join`          |                    Concatenate string arrays (or slices)                    |
|         `Split`         |                Separate a string according to certain rules                 |
|         `Count`         | Counts the number of non-overlapping occurrences of a substring in a string |
|        `Fields`         |            Separate strings with one or more consecutive spaces             |
| `HasPrefix`/`HasSuffix` |              Check if a string has a certain prefix or suffix               |
|       `IndexFunc`       |             Find index of a character or substring in a string              |
|   `ToLower`/`ToUpper`   |                               Case conversion                               |

For more functions like io operations, please refer to https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter02/02.1.html

#### Strconv

Strconv can realize the conversion between string and numeric types

|    method    |                         meaning                          |
| :----------: | :------------------------------------------------------: |
|  `ParseInt`  |  Convert a string to a number, can set base and bitsize  |
| `ParseBool`  |              Convert a string to a boolean               |
| `ParseFloat` |       Cconvert a 'float' string to a float number        |
| `ParseUnit`  | Similar to the ParseInt(), but does not accept +/- signs |
|    `Atoi`    |      Convert an integer of type string to type int       |

### JSON

### Time

Time is a kind of data type.

| method  |               meaning               |
| :-----: | :---------------------------------: |
|  `Now`  |            Current time             |
| `Date`  |            Create a Time            |
|  `Sub`  |           Time difference           |
| `Parse` |    Parse a time string to a Time    |
| `Unix`  |      Convert to unix timestamp      |
| `Round` | Get the Time on the hour/minute/... |

For Timer functions, please refer to https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter04/04.4.html

### Processes

## Concurrent and Parallel

### Goroutine

1. Differences between Goroutine and Threads

- Goroutine : works on user mode, lightweight thread, KB level in stack.
- Thread : works on kernel mode, MB level in stack.

2. Use `go` start a new goroutine.

```go
go func(){}
```

Function can be anonymous. When a goroutine needs to be blocked, the system will move other goroutines waiting to run on this thread to other threads that can run programs, so other goroutines will not be stuck.

### Channel

1. Definition

Go advocates sharing memory through communication instead of communication through sharing memory. Sharing memory through communication needs a **channel**. A channel is a data structure used to transfer data. It can be used between two goroutines to synchronize operation and communication by passing a value of a specified type.

2. Usage

We can create a channel by using

```go
make(chan mem_type ,[buffer_size])
```

- If buffer is NOT used, the channel is a synchronous channel. The sender will block until a receiver has received a value from the channel. The receiver blocks until there is a value to receive.
- If buffer is used, the channel is a producer-consumer model. The sender will block until the sent value is sent into the buffer. If the buffer is full, the sender will block until a receiver receives a value. The receiver blocks until there is a value to receive.

The operator `<-` is used to specify the direction of the channel to achieve sending or receiving. In particular, if no direction is specified, it is a bidirectional channel.

```go
// Send data to channel ch
ch <- data

// Receive data from channel ch and assign it to d
d := <- ch
```

The channel should be closed after being used, otherwise it is easy to cause deadlock.

```go
defer close(chan)
```

### Synchronization

Communication through sharing memory can lead to multiple goroutines accessing the same data at the same time, and thus we need a **Lock**.

#### Mutex

Each thread tries to lock the data before accessing it. The operation(R/W) can only be performed after successful locking, and then unlocked after the operation is completed. In other words, only one goroutine can access the data at a time when a mutex is used.

```go
var lock sync.Mutex // Declaration
lock.Lock() // add lock
//code...
lock.Unlock() // unlock
```

#### WaitGroup

WaitGroup can help set 'sleep_time' accurately. WatiGroup can wait until all goroutines are executed and block the execution of the main thread until all goroutines are executed. WaitGroup has three methods:

| method |                                      meaning                                       |
| :----: | :--------------------------------------------------------------------------------: |
| `Add`  |                Add the number of waiting goroutines to the counter                 |
| `Done` | Decrement the value of the counter, should be executed at the end of the goroutine |
| `Wait` |                     block until all WaitGroup counts become 0                      |

Note that the number set by `Add()` must be consistent with the number of waiting goroutines otherwise a deadlock will happen. For more examples please refer to https://www.cnblogs.com/sunshineliulu/p/14779158.html.

## Dependency Management

### GO Path

- bin : compiled binaries
- pkg : compiled intermediate products to speed up compilation
- src : source code

Disadvantage : Unable to achieve Version Control.

### GO Vendor

- vendor : place a copy of all dependent packages.

Disadvantage : Dependencies conflict.

### GO Module

- go.mod : identify module path and version information (${MAJOR}.{MINOR}.${PATCH}), describe unit dependencies (including labeling indrect and incompatible dependencies). When compiling, go will choose the lowest compatible version.
- Proxy : cache version content to achieve reliable dependency distribution.
- go get/mod : local tools

## Test

### Unit Test

- All test files end with `_test.go`
- Test function `func TestXxxx(t *testing.T)`
- Initialization should be included in `TestMain` function.

```go
func TestMain(m *testing.M) {
   // do init
   code := m.Run() // run test
   // do close and release
   os.Exit(code)
}
```

- `assert` package

```go
import "github.com/stretchr/testify/assert"

func TestFunc(t *testing.T) {
    output := Func()
    expectOutput := ""
    assert.Equal(t, expectOutput, output)
}
```

- **Code coverage** is the standard for judging whether the tests are good or bad. Using `--cover` can get the code coverage. It is good to make it more than 50%, better for more than 80%.

For more examples, please refer to https://juejin.cn/post/6908938380114034701.

### Mock

- Use the reflection mechanism to replace some functions such as R/W to achieve idempotent and make sure that the test is not affected by the environment or other users.
- [monkey package](https://github.com/bouk/monkey) : can mock methods or instances.

```go
// replace a funtion with another
func Patch(target, replacement interface{}) *PatchGuard {
	t := reflect.ValueOf(target)
	r := reflect.ValueOf(replacement)
	patchValue(t, r)
	return &PatchGuard{t, r}
}

// remove all monkey patches on target
// return if the target was patched in the first place
func Unpatch(target interface{}) bool {
	return unpatchValue(reflect.ValueOf(target))
}

func TestProcessFirstLineWithMock(t *testing.T) {
	monkey.Patch(ReadFirstLine, func() string {
		return "line110"
	})
	defer monkey.Unpatch(ReadFirstLine)
	line := ProcessFirstLine()
	assert.Equal(t, "line000", line)
}
```

### Benchmark

- Very similar with unit test. It is to test the performance of the code and the consumption of CPU.
- Start with `Benchmark` and using `gobench xxx_test.go` to run.

```go
// Serial
func BenchmarkRandom(b *testing.B) {
   InitServerIndex()
   b.ResetTimer()
   for i := 0; i < b.N; i++ {
      Random(10)
   }
}

// Parallel
func BenchmarkRandomParallel(b *testing.B) {
   InitServerIndex()
   b.ResetTimer()
   b.RunParallel(func(pb *testing.PB) {
      for pb.Next() {
         Random(10)
      }
   })
}
```

## Code Optimization

### Go Style

#### Standard

- Simplicity : eliminate unnecessary expressions
- Readability : easy to understand
- Productivity : easy to cooperate with teammates

#### Format Tools

Two useful packages.

- [gofmt](https://pkg.go.dev/cmd/gofmt)
- [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports)

#### Comment

- Good code has lots of comments, bad code requires lots of comments.
- Both `/* */` and `//` are supported.
- Should explain what the code does, why it works, why it is needed and what goes wrong.
- Always add comments to the public symbols like `Read`.

```go
/*
  A Builder is used to efficiently build a string using Write methods.
  It minimizes memory copying. The zero value is ready to use.
  Do not copy a non-zero Builder.
*/
type Builder struct {
    addr *Builder // of receiver, to detect copies by value
    buf  []byte
}
```

For more exmaples please refer to https://juejin.cn/post/7189519144897740861.

#### Naming Conventions

##### Variable

- Acronyms should be all uppercase, eg `ServeHTTP`. but when it is at the beginning of the variable and does not need to be exported, could be all lowercase, eg `xmlHTTPRequest`.
- The farther a variable is from where it is used, the more contextual information its name needs to carry, eg `deadline` instead of `t`.

##### Function

- function name should be as short as possible and it does not need to carry the information of the package.
- Type information can be omitted when the return type's name is consistent with package name. eg,

```go
package http

// bad
func ServeHTTP(I net.Listener, handler Handler) error

// good
func Serve(I net.Listener, handler Handler) error
```

- Type information should be added to the function name when the package name is not consistent with the return type's name of the function.

##### Package

- Consists of lowercase letters only.
- Be short and contain some contextual information.
- Do not use the same name as the standard library.
- Better not to use commonly used variable names like `bufio` instead of `buf`.
- Better to use singular instead of plural like `encoding` instead of `encodings`.
- Use abbreviations sparingly.

#### Code

##### Control Flow

- Avoid nesting

```go
// bad
if foo {
    return x
} else {
    return nil
}
​
// good
if foo {
    return x
}
return nil
```

- Keep normal code paths with minimal indentation

```go
// bad
func OneFunc() error {
    err := doSomething()
    if err == nil{
        err := doAnotherThing()
        if err == nil{
            return nil
        }
        return err
    }
    return err
}

// good
func OneFunc() error {
    if err := doSomething(); err != nil{
        return err
    }
    if err := doAnotherThing(); err != nil{
        return err
    }
    return nil
}
```

##### Error Handling

- For simple errors that occur few times, use `errors.New()`.
- To format errors can use `fmt.Errorf`.
- For complicated errors, use Wrap and Unnwrap.

```go
list, _, err := c.GetBytes(cache.Subkey(a.actionID, "srcfiles"))
if err != nil {
    return fmt.Errorf("reading srcfiles list: %w", err)
}
```

- Use `errors.Is` instead of `==` to determine whether an error is a specific error. Use `errors.As` can get the specific kind of error.

```go
data, err = lockedfile.Read(targ)
if errors.Is(err, fs.ErrNotExist) {
    return []byte{}, nil
}else{
    // do something
}

if _, err := os.Open("non-existing"); err != nil {
    var pathError *fs.PathError
    if errors.As(err, &pathError) {
        fmt.Println("Failed at path:", pathError.Path)
    } else {
        // do something
    }
}
```

- `panic/recover` is not recommended unless it is really a huge problem. `recover` should be used in `defer` and takes effect only on the current goroutine.

Note that `defer` is actually a stack.

```go
func main(){
    defer fmt.Printf("1")
    defer fmt.Printf("2")
    defer fmt.Printf("3")
}
// output : 321
```

### Performance Optimization

#### Slice

- Better to provide size when `make` a slice.
- Better to use `copy` to replace re-slice.

#### Map

- Better to provide size when `make` a map.

#### String

- Use `strings.Builder` to replace `+` or `bytes.Buffer`.
- Better to provide size using `strings.Builder.Grow(size)`.

#### Struct

- Use an empty struct as a placeholder.

```go
m ：= make(map[int]struct{})

for i:= 0; i < n; i++{
    m[i] = struct{}{}
}
```

- `map`[key](Empty struct) = `set`

#### Atomic Package

- Atomic package provides atomic operations which can replace lock with a higher efficiency.

```go
var x int32 = 100
func f_add() {
	atomic.AddInt32(&x, 1)
}

func f_sub() {
	atomic.AddInt32(&x, -1)
}

func main() {
	for i := 0; i < 100; i++ {
		f_add()
		f_sub()
	}
	fmt.Printf("x: %v\n", x)   // 100
}
```

- For non-numeric operations, can use `atomic.Value`, which can carry an `interface{}`.

## Useful Tools

1. [A Tour of Go](https://go.dev/tour/welcome)
2. [Effective Go](https://go.dev/doc/effective_go)

## Acknowledgements

Many thanks to Kechun Wang, Zheng Zhao, Lei Zhang from ByteDance for their help.
