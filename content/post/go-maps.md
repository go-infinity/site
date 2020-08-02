---
title: "Golang Maps"
date: 2020-07-19T14:44:26+05:30
draft: false
toc: true
categories: [Golang]

---
## What are Maps/Dictionary
Go Maps enables us to store key values pair where key can be anything that can be checked for equality. That means apart from maps, slices, funcs

## Why use maps instead of array/slices
```go
mapCapitals := map[string]string{"India":"Delhi","Pakistan":"Islamabad"}
mapCapitals["India"]
```
```go
type struct Capitals {
    County string
    Capital string
}
arrayCapitals := [2]Capitals{{Country:"India","Capital":"Delhi"},   - slice
                              {Country:"Pakistan","Capital":"Islamabad"}}
for i, v := range arrayCapitals {
    if v.Country == "India" {
        v.Capital
    }
}
```
 Timecomplexity of seach operation on map is constant time O(1).

## Map Operations
- Initialize
```go
1) mapVariable := map[KeyDataType]ValueDataType{EntryKeyPair} 
ex. mapCapitals := map[string]string{"India":"Delhi","Pakistan":"Islamabad"}
2) mapVariable = make(map[KeyDataType]ValueDataType)
```
- Set
```go
mapVariable[Key] = value
ex. mapCapitals["France"] = "Paris"
```
- Get
```go
value = mapVariable[key]
ex. capitalIndia := mapCapital["India"]
```
- Delete
```go
delete(mapVariable,key)
ex. delete(mapCapitals,"France")
```
- Iterate
```go
for k,v := range mapCapitals{
    fmt.Printf("%s has capital %s",k,v)
}
```
- Length
```go
len(mapCapitals)
```