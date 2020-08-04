---
title: "Interface"
date: 2020-07-19T14:44:26+05:30
draft: false
toc: true
categories: [Golang]

---

## what are Interface
An interface is a collection of method definitions (without implementations) and constant values.

## Interface in Golang
The interface is a custom type that is used to specify a set of one or more method signatures and the interface is abstract, so you are not allowed to create an instance of the interface.
```go
type interface_name interface{
// Method signatures
func Method1() return type
func Method2() return type
}
```

## Interface implementation
- It is mandatory to implement all the methods of a interface for implementing an interface.
- The Pre-define go language interfaces are implemented implicitly.

## Example
```go
package main
import (
	"fmt"
	"math"
)
//interface declaration
type shape interface {
    //Methods
	area() float32
	perimeter() float32
}
// struct type for square
type square struct {
	length float32
	width  float32
}
//Implementation of Method area with reference to square struct
func (sq square) area() float32 {
	var areaOfSquare float32
	if sq.length > 0 && sq.width > 0 {
		areaOfSquare = sq.length * sq.width
	}
	return areaOfSquare
}
//Implementation of Method perimeter with reference to square struct
func (sq square) perimeter() float32 {
	var perimeterOfSquare float32
	if sq.length > 0 && sq.width > 0 {
		perimeterOfSquare = 2 * (sq.length + sq.width)
	}
	return perimeterOfSquare
}
type circle struct {
	radius float32
}
//Implementation of Method area with reference to circle struct
func (cir circle) area() float32 {
	var areaOfCircle float32
	if cir.radius > 0 {
		areaOfCircle = math.Pi * cir.radius * cir.radius
	}
	return areaOfCircle
}
//Implementation of Method perimeter with reference to circle struct
func (cir circle) perimeter() float32 {
	var perimeterOfCircle float32
	if cir.radius > 0 {
		perimeterOfCircle = 2 * math.Pi * cir.radius
	}
	return perimeterOfCircle
}
func main() {
    // Instantiating interface variable
    var sh shape
    //
	sh = circle{5.2}
	fmt.Println("Area of circle is  ", sh.area())
	fmt.Println("Perimeter of circle is ", sh.perimeter())
	sh = square{10.2, 5.4}
	fmt.Println("Area of square is ", sh.area())
	fmt.Println("Perimeter of square is ", sh.perimeter())
}
```

## Interface with Error Handling
```go
package main

import (
	"fmt"
	"math"
)

type shape interface {
	area() (float32, error)
	perimeter() (float32, error)
}

type square struct {
	length float32
	width  float32
}

func (sq square) area() (float32, error) {
	var areaOfSquare float32
	if sq.length > 0 && sq.width > 0 {
		areaOfSquare = sq.length * sq.width
	} else {
		return areaOfSquare, fmt.Errorf("Side can't be negative")
	}
	return areaOfSquare, nil
}

func (sq square) perimeter() (float32, error) {
	var perimeterOfSquare float32
	if sq.length > 0 && sq.width > 0 {
		perimeterOfSquare = 2 * (sq.length + sq.width)
	} else {
		return perimeterOfSquare, fmt.Errorf("Side can't be negative")
	}
	return perimeterOfSquare, nil
}

type circle struct {
	radius float32
}

func (cir circle) area() (float32, error) {
	var areaOfCircle float32
	if cir.radius > 0 {
		areaOfCircle = math.Pi * cir.radius * cir.radius
	} else {
		return areaOfCircle, fmt.Errorf("Side can't be negative")
	}
	return areaOfCircle, nil
}

func (cir circle) perimeter() (float32, error) {
	var perimeterOfCircle float32
	if cir.radius > 0 {
		perimeterOfCircle = 2 * math.Pi * cir.radius
	} else {
		return perimeterOfCircle, fmt.Errorf("Side can't be negative")
	}
	return perimeterOfCircle, nil
}

func main() {

	var sh shape
	sh = circle{-5.2}
	areaOfCircle, err := sh.area()
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("Area of circle is  ", areaOfCircle)
	}
	perimeterOfCircle, err := sh.perimeter()
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("Perimeter of circle is ", perimeterOfCircle)
	}

	sh = square{10.2, 5.4}
	areaOfSquare, err := sh.area()
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("Area of square is ", areaOfSquare)
	}
	perimeterOfSquare, err := sh.perimeter()
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("Perimeter of square is ", perimeterOfSquare)
	}
}
```