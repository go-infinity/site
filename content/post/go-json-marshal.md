---
title: "Go JSON Marshal"
date: 2020-08-21T14:44:26+05:30
tags: [go, json]
draft: false
toc: true
categories: [go]
---
## Simple Marshal
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id   int
		Name string
	}

	e := Employee{Id: 1, Name: "ABC"}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}
```

## Marshal Slice of Struct
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id   int
		Name string
	}

	e := []Employee{
		Employee{Id: 1, Name: "ABC"},
		Employee{Id: 2, Name: "DEF"},
		Employee{Id: 3, Name: "GHI"},
	}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}

```

## Marshal to Different JSON key names
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id   int    `json:"employee_id"`
		Name string `json:"employee_name"`
	}

	e := []Employee{
		Employee{Id: 1, Name: "ABC"},
		Employee{Id: 2, Name: "DEF"},
		Employee{Id: 3, Name: "GHI"},
	}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}

```

## Marshal JSON except some sensitive fields
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id     int    `json:"employee_id"`
		Name   string `json:"employee_name"`
		Salary int    `json:"-"`
	}

	e := []Employee{
		Employee{Id: 1, Name: "ABC", Salary: 100000},
		Employee{Id: 2, Name: "DEF", Salary: 200000},
	}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}

## Marshal JSON except zero value intiialized fields
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id   int    `json:"employee_id"`
		Name string `json:"employee_name"`
		Age  int    `json:"employee_age,omitempty"`
	}

	e := []Employee{
		Employee{Id: 1, Name: "ABC"},
		Employee{Id: 2, Name: "DEF", Age: 40},
	}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}

```

## Marshal JSON with default value for zero value intiialized fields

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	type Employee struct {
		Id   int    `json:"employee_id"`
		Name string `json:"employee_name"`
		City string `json:"employee_city"`
	}

	e := []Employee{
		Employee{Id: 1, Name: "ABC", City: "Gurgaon"},
		Employee{Id: 2, Name: "DEF"},
	}
	for i, employee := range e {
		if employee.City == "" {
			e[i].City = "Delhi"
		}
	}
	jsonData, err := json.Marshal(e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Println(string(jsonData))
}

```

## CustomJSON Marshaller
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

type Employee struct {
	Id   int    `json:"employee_id"`
	Name string `json:"employee_name"`
}

func (e *Employee) MarshalJSON() ([]byte, error) {
	type AliasEmployee Employee
	e.Name = "Mr. " + e.Name
	return json.Marshal((*AliasEmployee)(e))
}

func main() {

	e := []Employee{
		Employee{Id: 1, Name: "ABC"},
		Employee{Id: 2, Name: "DEF"},
	}

	jsonData, err := json.Marshal(&e)
	if err != nil {
		fmt.Println(err)
		os.Exit(2)
	}
	fmt.Println(string(jsonData))
}

```