---
title: "Golang Functions"
date: 2020-07-19T14:44:26+05:30
tags: [golang, functions]
draft: true
toc: true
categories: [Golang]

---
## What is Function
A function is a group of statements that together perform a task. We can divide up your code into separate functions. How we divide up our code among different functions is up to us, but logically the division is such that each function performs a specific task.

## Why Functions
- Reusability :
Functions provide us the ability to reuse the same code which ultimately saves the excessive use of memory, acts as a time saver 
- Organization :
Functions helps in organizing our code which provides better readability of the code

## Function Signature
FunctionDecl = "func" FunctionName Parameters [ Result ] . [ FunctionBody ] .
Parameters     = "(" [ ParameterList [ "," ] ] ")" .
Result         = Parameters | Type .

## Function Example
Named Return
```go
func ToUSD(amountINR float64) (amountUSD float64) {
    amountUSD = amountINR/70 
    return amountUSD
}
```

Return without name
```go
func ToUSD(amountINR float64) float64 {
    amountUSD := amountINR/70 
    return amountUSD
}
```

Multiple return values of same type
```go
func ToUSDAndPound(amountINR float64) (float64,float64) {
    return amountINR / 70, amountINR / 90
}
```
Multiple return values of different type
```go
func ToUSD(amountINR float64) (amountUSD float64,symbol string) {
    return amountINR / 70, "$"
}
```
Variadic Functions : 
- The function that called with the varying number of arguments is known as variadic function. We can pass any number of same type arguments to the function.
- Variadic function is used when we don’t know the quantity of parameters.
```go
func SumInt(ints ...int) int{
    sum : = 0
    for i,val := range ints{
        sum = sum + val
    }
    return sum
}
```
Init Functions :
- An init() function doesn't take or return any arugument. 
- This function is called when the package is initialized. 
- We are allowed to create multiple init() function in the same program which will be exected in the Order of there creation. 
- We can't refer init() function from anywhere. 
- init() functions are execute before main() function
```go
var CompanyName string
var ExchangeRate int

func init() {
	CompanyName = "Go-Infinity"
	ExchangeRate = 70
}
```
Anonymous Functions
Function Type
- An anonymous function is a function which doesn’t contain any name.
- Anonymous function are used when we want to create inline function.
- These are also know as function literal
- We can assign an anonymous function to a variable. When you assign a function to a variable, then the type of the variable is of function type and you can call that variable like a function call as shown in the below example.
```go
var HelloFunc func(string) string
HelloFunc = func(audience string) string {
    return "Hello " + audience
}
fmt.Printf("%s\n\n", HelloFunc("Go-Infinity"))
```
First Class Functions
- First class functions allows functions to be assigned to variables, passed as arguments to other functions and returned from other functions.
```
package main

import "fmt"

func main(){
    value := func(){
        fmt.Println("Welcome to Go-Infinity")
    }
    value()
}


```
Stateful Functions

