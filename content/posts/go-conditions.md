---
title: "Golang Coditions"
date: 2020-07-19T14:44:26+05:30
tags: [golang, conditions]
draft: true
toc: true
categories: [Golang]

---

## Conditional Statements 
Conditional statements are features of a programming language, which help in performing different computations or actions depending on whether a programmer-specified boolean condition evaluates to true or false.

## Conditional Statements in Go
Go supports multiple branches in the conditional. These statements take the form if...else if...else.

- If Condition
{{< codeWide language="go" >}}
num := 100
if num > 10{
    fmt.Println("Number is greater then 10")
}
{{< /codeWide >}}

- If..else Condition
{{< codeWide language="go" >}}
num := 9
if num > 10{
    fmt.Println("Number is greater then 10")
}else{
    fmt.Println("Number is less then 10")
}
{{< /codeWide >}}

- if..else if...else Nested Condition
{{< codeWide language="go" >}}
if marks == 100 {
    fmt.Println("Perfect Score!")
}else if marks >= 60 {
    fmt.Println("You Pass.")
}else {
    fmt.Println("You fail!")
}
{{< /codeWide >}}





