---
title: "Golang Functions"
date: 2020-07-19T14:44:26+05:30
tags: [golang, functions]
draft: false
toc: true
categories: [Golang]

---

## Why Functions
- Reusability
- Organization

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
Variadic Functions
```go
func SumInt(ints ...int) int{
    sum : = 0
    for i,val := range ints{
        sum = sum + val
    }
    return sum
}
```
Init Functions
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
```go
var HelloFunc func(string) string
HelloFunc = func(audience string) string {
    return "Hello " + audience
}
fmt.Printf("%s\n\n", HelloFunc("Go-Infinity"))
```
First Class Functions
```

```
Stateful Functions

