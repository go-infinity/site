---
title: "Golang Methods"
date: 2020-07-19T14:44:26+05:30
draft: false
toc: true
categories: [Golang]

---

## Whats are Methods
Methods provide behaviour to our types. It provides Object Oriented Programming Model in Golang

## Golang Specification
```ebnf
MethodDecl = "func" Receiver MethodName Signature [ FunctionBody ] .
Receiver   = Parameters .
```
## Method Reciever Types
- Value Receiver - Pass by Value
```go
package book

//Book is an Entity/Business Object in Library Domain
type Book struct {
	BookID int
	BookTitle string
	BookAuthor string
	BookSubject string
}

func(b Book) Title() string {
	return b.BookTitle
}
```
- Pointer Reciever - Pass By reference
```go
func(b *Book) UpdateTitle(newTitle string) bool {
	b.BookTitle = newTitle
	fmt.Println(b.BookTitle)
	return true
}
```
## Value to Pointer Receiver Conversion

## Value Reciever vs Pointer Receiver