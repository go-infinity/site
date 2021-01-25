---
title: "Golang Maps"
date: 2020-07-19T14:44:26+05:30
draft: true
toc: true
categories: [Golang]

---
## What are Maps/Dictionary
Go Maps enables us to store key values pair where key can be anything that can be checked for equality. That means apart from maps, slices, funcs

## Why use maps instead of array/slices
{{< codeWide language="go" >}}
mapCapitals := map[string]string{"India":"Delhi","Pakistan":"Islamabad"}
mapCapitals["India"]
```
{{< codeWide language="go" >}}
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
{{< /codeWide >}}
 Timecomplexity of seach operation on map is constant time O(1).

## Map Operations
- Initialize
{{< codeWide language="go" >}}
1) mapVariable := map[KeyDataType]ValueDataType{EntryKeyPair} 
ex. mapCapitals := map[string]string{"India":"Delhi","Pakistan":"Islamabad"}
2) mapVariable = make(map[KeyDataType]ValueDataType)
```
- Set
{{< codeWide language="go" >}}
mapVariable[Key] = value
ex. mapCapitals["France"] = "Paris"
```
- Get
{{< codeWide language="go" >}}
value = mapVariable[key]
ex. capitalIndia := mapCapital["India"]
```
- Delete
{{< codeWide language="go" >}}
delete(mapVariable,key)
ex. delete(mapCapitals,"France")
```
- Iterate
{{< codeWide language="go" >}}
for k,v := range mapCapitals{
    fmt.Printf("%s has capital %s",k,v)
}
{{< /codeWide >}}
- Length
{{< codeWide language="go" >}}
len(mapCapitals)
```