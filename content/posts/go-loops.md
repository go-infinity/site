---
title: "Golang Loops"
date: 2020-07-28T14:44:26+05:30
tags: [golang, loops]
draft: true
toc: true
categories: [Golang]
---
## Loop Control
When inside a program you need to run some block of code repeatedly either on a number of times or infinitely

## Loop construct
Golang only provides `For` statement for iteration

## Loop Syntax
```ebnf
ForStmt = "for" [ Condition | ForClause | RangeClause ] Block .
Condition = Expression .
```
## Loop using condition
```go
package main

import "fmt"

func main() {
	a := 10
	b := 20

	for a < b {
		fmt.Println("A is still less than b")
		a = a + 1
	}
}
```

## Loop using Index
```go
package main

import "fmt"

func main() {
	for i := 1; i <= 10; i++ {
		fmt.Println("Run Number", i)
	}
}
```
## Loop using range
```go
package main

import "fmt"

func main() {
	//Slice Example
	s := []int{1, 2, 3, 4, 5, 6, 7, 8, 9}
	for i, val := range s {
		fmt.Printf("The value at %d index is %d\n", i, val)
	}
	//Map Example
	m := map[string]string{"A": "Apple", "B": "Ball", "C": "Cat"}
	for key, val := range m {
		fmt.Printf("The value of Key %s is %s\n", key, val)
	}
}
```