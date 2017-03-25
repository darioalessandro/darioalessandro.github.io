---
layout: post
title:  "6. Go: Cheatsheet"
date:   2017-03-05 10:00:00 -0500
categories: go programming google golang general motors
---

## Declare variables and constants

# Constants

```go
const pi = 3.1416
```

# Variable

```go
var a = 3
```

# Variable2

```go
 a := 3
```

## For

# Classic

```go
for i:= 0; i < 4; i++ {
    fmt.Println(i)
}
```

# For using previously defined variable

```go
i:= 0

for  i < 4 {
    fmt.Println(i)
    i = i + 1 //i++ is supported but I do not really like it.
}
```

## If

```go
isAFanBoy := true

if isAFanBoy  {
    fmt.Println("Fanboy!")
} else {
    fmt.Println("Not a fanboy")
}
```

## Switch

# Classic switch

```go
age := 30

switch age {
    case 14, 15 : fmt.Println("you are 14 or 15")
    case 16 : fmt.Println("go drive")
    default : fmt.Println("go do whatever")
}
```

# Switch without variable

This can be used for pattern matching, I also find it really useful to create clean if statements.

```go
age := 30

switch {
    case age < 15 : fmt.Println("you are less than 15")
    case age == 15 : fmt.Println("you are 15")
    case age == 16 : fmt.Println("go drive")
    case age == 18 : fmt.Println("go vote")
    default : fmt.Println("go do whatever")
}

```

## Arrays

# Create

```go
var arrayOfFloats = [5] float64 //all zeros
```

# Create and initialize

```go
var arrayOfFloats = [5] float64 {0.01,1,2,3.3456,4.0001}
```

# Traverse array

```go
for i:=0;i < len(arrayOfFloats);i++ {
    fmt.Println("arrayOfFloats[",i,"] = ", arrayOfFloats[i])
}
```

# Traverse array with direct access to index and value

```go
for i, value := range arrayOfFloats {
    fmt.Println("arrayOfFloats[",i,"] = ", value)
}
```

# Traverse array with direct access to value ignoring index

```go
for _, value := range arrayOfFloats {
    fmt.Println("arrayOfFloats element = ", value)
}
```

## Slices

# Create

Create a slice of integers.
Contains a length and capacity of 5 elements.
```go
slice := make([]int, 5)
```

Create a slice of integers.
Contains a length of 3 and has a capacity of 5 elements.
```go
slice := make([]int, 3, 5)
```

<blockquote>
“Remember, if you specify a value inside the [ ] operator, you’re creating an array. If you don’t specify a value, you’re creating a slice.”
- William Kennedy. “Go in Action.”
</blockquote>


# Create and Initialize

```go
sliceOfFloats := [] float64 {0,1,2,3,4,5}
```

# Get slice of slice

```go
sliceOfSlice := sliceOfFloats[3:5] // [3 4] Warning: 5 is not included!!
```

```go
sliceOfSlice := sliceOfFloats[:3] // [0 1 2]
```

```go
sliceOfSlice := sliceOfFloats[2:] // [2 3 4 5]
```

# Copy

```go
a := [] float64 {0,1,2,3,4,5}
b := make([]float64,5)
var numberOfCopiedElements = copy(b,a)
fmt.Println(numberOfCopiedElements) //5 because b has capacity of 5
fmt.Println(b) //[0 1 2 3 4]

```

## Maps

# Create

```go
mapOfAges := make(map[string] int)
```

# Set keys and values

```go
mapOfAges["Werfaefgaerg"] = 23
mapOfAges["Bismarerwerer"] = 35
```

# Delete keys

```go
delete(mapOfAges,"Werfaefgaerg")
```

# Count keys

```go
len(map)
```

## Functions

# Classic Functions

** Note: Return is required (because Go sucks)

```go
func <name>(<params>) <returnType> {
    <body>
    return <returnValue>
}
```
Example:

```go
func addValues(numbers [] float64) float64 {
	var res = 0.0; //force type system to use float
	for _,value := range numbers {
		res = res + value
	}
	return res //so damn old style
}
```

# Return 2 values for function

```go
func generateDeterministicRandomPair() (float64,float64) {
	return rand.Float64(), rand.Float64()
}
```

# Variable number of arguments

```go
func addValues2(args...float64) float64 {
	var res = 0.0
	for _,value := range  args {
		res = res + value
	}
	return res;
}
```

## Defer

or as I call it, the equivalent of C's old good "goto" for Gophers:

```go
func main() {
    fmt.Println("Go is")
    defer fmt.Println("Very")
    fmt.Println("Stupid")
}
```

Prints:
```go
Go is
Stupid
Very
```
At this juncture, I understand that the intention of such a lame keyword is to give a chance to the "gopher" to define a
function which will be called before the current function returns, might be used for cleanup, but if you like go you are
already dirty, forever.

## Pass by Value vs Reference

For the longest time, having access to memory has been by far, one of the biggest attack vector to C programs.

In total disregard of this, Go devs bring back the hability to point to garbage and break things around by allowing low
level access to memory (fail).

# Pass by Value

```go
func main() {
    x := 0
    changeVal(x)
    fmt.Println(x) // 0
}

func changeVal(x int) {
    x = 2
}
```

# Pass by Reference

```go
func main() {
    x := 0
    changeVal(&x)
    fmt.Println(x) // 2
}

func changeVal(x *int) {
    *x = 2
}
```

# Note on pointer arithmetic.

Raw access to pointers allows developers to create a total new set of mistakes and open security holes in their web
applications having to do with pointer arithmetic.

Remember Heartbleed? that was the result of an excessive usage of pointer arithmetic, that is because using it once it's
one too many.

Not convinced, look at this [Article](https://www.theregister.co.uk/2014/04/09/heartbleed_explained/) by Chris Williams.

By default, go disables pointer arithmetic but anyone can just import "unsafe" and have fun with your code.

## Good old structs

Allows users to define custom data structures.

# Declaration

```go
type Human struct {
    name string
    age int
}
```

# Initialization

```go
func main() {
    h := Human{name : "Roliaserasf", age: 100}
}
```

# Accessing properties
```go
fmt.Human(h.name, "'s age is", h.age) //Roliaserasf 's age is 100
```

# Attaching functions to structs:

```go
func (h * Human) sayHi() {
    fmt.Println(h.name,"says hi!")
}
```

## Interfaces

Consider an interface with the form:
```go
type Mammal interface {
    dailyAverageMaternalMilkConsumption() float64
}
```
Let's define a couple of Mammals and have them implement "dailyAverageMaternalMilkConsumption"

```go
type Human struct {
	name string
	age int
}

type Whale struct {
	name string
	age int
}
```

```go
func (w Whale) dailyAverageMaternalMilkConsumption() float64 {
	switch {
		case w.age <= 2 : return 400000 //Yes, 400 L
		default: return 0
	}
}

func (h Human) dailyAverageMaternalMilkConsumption() float64 {
	switch {
		case h.age <= 2 : return 500
		default: return 0
	}
}
```

From now on, you can use Human and Whale like this:

```go
func getMills(m Mammal) float64 {
	return m.dailyAverageMaternalMilkConsumption()
}

danny := Whale{name:"Danny", age:1}
fmt.Println(getMills(danny)) //400 000 ml

edgar := Human{name:"Edgar", age:1}
fmt.Println(getMills(edgar)) //500 ml

```

## Strings

```go
import ("strings"
        "fmt"
        "sort"
        "strconv"
        )
```


# Creation:
```go
sampString := "Hello World"
```

#Contains

```go
fmt.Println(strings.Contains(sampString,"lo")) // true
```

# Index

```go
fmt.Println(strings.Contains(sampString,"lo")) // 3
```
# Count

```go
fmt.Println(strings.Count(sampString,"l")) // 3
```

# Replace

```go
fmt.Println(strings.Replace(sampString,"l", "x", 2)) // Hexxo World
```

# Split

```go
csvString := "1,2,3,4,5,6"
fmt.Println(strings.Split(csvString,",")) // [1,2,3,4,5,6]
```

# Sort

```go
listOfLetters := []string{"c","a","b"}
sort.Strings(listOfLetters) // [a,b,c]
```
# Join

```go
listOfNums := strings.Join([]string{"3","2","1"},", ") //"3, 2, 1, "
```

# ParseInt and ParseFloat

```go
newInt, errInt := strconv.ParseInt("30",0,64)
newFloat, errInt := strconv.ParseFloat("31",64)
```

## File IO

```go
import ("os"
        "fmt"
        "log"
        "io/ioutil")
```

# Create file

```go
file, err:= os.Create("sample.txt")
if err != nil {
    log.Fatal(err)
}
```

# Write to file

```go
file.WriteString("This is some random text")
```

# Close file
```go
file.Close()
```

# Read file
```go
stream, err := ioutil.ReadFile("samp.txt")
if err != nil {
    log.Fatal(err)
}
readString := string(stream)
```

## Concurrency vs parallelism

![Concurrency vs parallelism]({{ site.url }}/assets/concurrencyVsParallelism.png)
<blockquote>Figure 1: Taken from William Kennedy. “Go in Action.”  </blockquote>

## Goroutines
```go
import ("sync"
        "fmt"
        "runtime"
        "time")
```

```go
var wg sync.WaitGroup

func main() {

	wg.Add(10)
	for i := 0; i <10; i++ {
		go count(i)
	}
	fmt.Println("Waiting To Finish")
	wg.Wait()

}

func count(id int) {
	defer wg.Done()
	for i := 0; i <10; i++ {
		fmt.Println(id,":",i)
	}
}
```

## Channels

```go
import ("fmt"
        "time"
        "strconv")

var pizzaNum = 0
var pizzaName = ""

func makeDough(stringChan chan string) {
    pizzaNum = pizzaNum + 1
    pizzaName = "Pizza #" + strconv.Itoa(pizzaNum)
    fmt.Println("Make Dough and Send for Sauce")
    stringChan <- pizzaName
    time.Sleep(time.Millisecond * 10)
}


func addSauce(stringChan chan string) {
    pizza := <- stringChan
    fmt.Println("Add Sauce and Send", pizza, "for toppings")
    stringChan <- pizzaName
    time.Sleep(time.Millisecond * 10)
}

func addToppings(stringChan chan string) {
    pizza := <- stringChan
    fmt.Println("Add Toppings to", pizza, "and ship")
    time.Sleep(time.Millisecond * 10)
}

func main() {
    stringChan := make(chan string)
   for i := 0; i < 3; i++ {
        go makeDough(stringChan)
        go addSauce(stringChan)
        go addToppings(stringChan)
        time.Sleep(time.Millisecond * 5000)
   }
}

```

## Concurrency and synchronization
(TBD)

## Reading arguments

```go
env := flag.String(<key>, <default value>, <description>)
```
Example:

```go
env := flag.String("env", "prod", "Environment")
	flag.Parse()
	fmt.Println("env:", *env)
```

## Creating a test.

Main command.

```go
go test
```

No need to create an special package, in order to create a test file, just name it:

```go
*_test.go
```

Example: main_test.go

```go
package main

import "testing"

func TestTruth(t * testing.T) {
	if true != true {
		t.Error("everything I know is wrong.")
	}
}

```

