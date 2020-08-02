---
title: "Golang Slices"
date: 2020-07-19T14:44:26+05:30
draft: false
toc: true
categories: [Golang]

---

## What are slices
Slices are golang provided data structure to create dynamic sized collection of same type elements
Slices are mutable.
## Slice Initialization
```go
Shorthand initialization
s := []string{"TV","VCR","DVD"}

Intialization with zero value of the type
t := make([]string,10)
```

## Slice versus Array
Sometimes our program requires index based collection to grow dynamically in such cases it is better to use slices.

## Slice Internals
Slices is a pointer to and it points to an backing array.

## Inbuilt functions to support slice
- append: to add data to an existing slice
```go
myslice := []string{"a","b","c","d","e"}
myslice = append(myslice, "f","g")
fmt.Println(len(myslice))
```
- len: Provide length(number of elements it contain) of a slice 
```go
myslice := []string{"a","b","c","d","e"}
fmt.Println(len(myslice))
```
- cap: Provide capacity(number of elements in the underlying array, counting from the first element in the slice) of a slice
```go
myslice := []string{"a","b","c","d","e"}
fmt.Println(cap(myslice))
```
- make: Used to create slice
```go
myslice = make([]string,10) // Here 10 is the length
myslice1 = make([]string,5,10)// Here 5 is length and 10 is capacity

```

## Slices Operations
- Append/Push
```go
team := []string{"D", "S", "A", "V", "M"}
team = append(team, "A")
```
- Get
```go
team := []string{"D", "S", "A", "V", "M"}
fmt.Printf("First member of the team is %s\n", team[0])
```
- Set
```go
team := []string{"D", "S", "A", "V", "M"}
team[2] = "Azazul"
fmt.Printf("Third member of the team is %s\n", team[2])
```
- Iterate
```go
team2 := []string{"X", "Y", "Z"}
for i, val := range team2 {
    fmt.Printf("%d index has value:%s\n", i, val)
}
```
- Delete
```go
team1 := []string{"D", "S", "A", "V", "M"}
team2 := append(team1[:2], team1[3:]...)
```
- Insert
- Pop
```go
team1 := []string{"D", "S", "A", "V", "M"}
x, team := team1[len(team1)-1], team1[:len(team1)-1]
```

- Slicing
```go
team1 := []string{"D", "S", "A", "V", "M"}
// Slice[start:end)
// To include all elements
team2 := team1[:]
// To include all element till nth index
team2 := team[:n]
// To include all elements till end starting from nth index
team2 := team[n:]
```
- Extend
```go
team1 := []string{"D", "S", "A", "V", "M"}
team2 := []string{"X", "Y", "Z"}
team := append(team1, team2...)
```