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

# Functions

** Note: Return is required (because Go sucks)

```go
func <name>(<params>) <returntype> {
    <body>
    return <return value>
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