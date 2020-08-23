---
title: "Go JSON Unmarshal"
date: 2020-08-20T14:44:26+05:30
tags: [go, json]
draft: false
toc: true
categories: [go]
---

## Unmarsalling structured json data

```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	employeeJsonData := `{"id":"1","name":"Tiger Nixon","salary":"320800","age":"61","image":""}`

	type Employee struct {
		Id     string
		Name   string 
		Salary string
		Age    string
		Image  string
	}

	var e Employee
	err := json.Unmarshal([]byte(employeeJsonData), &e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Printf("%+v", e)
}
```

## Unmarshal structured JSON data with struct tags
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	employeeJsonData := `{"id":"1","employee_name":"Tiger Nixon","employee_salary":"320800","employee_age":"61","employee_image":""}`

	type Employee struct {
		Id     string
		Name   string `json:"employee_name"`
		Salary string `json:"employee_salary"`
		Age    string `json:"employee_age"`
		Image  string `json:"employee_image"`
	}

	var e Employee
	err := json.Unmarshal([]byte(employeeJsonData), &e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Printf("%+v", e)
}

```

## Unmarshal structured nested JSON
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	employeeJsonData := `{
		"id": "1",
		"employee_name": "Tiger Nixon",
		"employee_details": {
			"employee_salary": "320800",
			"employee_age": "61",
			"employee_image": ""
		}
	}`

	type EmployeeDetails struct {
		Salary string `json:"employee_salary"`
		Age    string `json:"employee_age"`
		Image  string `json:"employee_image"`
	}
	type Employee struct {
		Id              string
		Name            string          `json:"employee_name"`
		EmployeeDetails EmployeeDetails `json:"employee_details"`
	}

	var e Employee
	err := json.Unmarshal([]byte(employeeJsonData), &e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Printf("%+v", e)
}

```

## Unmarshal structured List based JSON
```go
package main

import (
	"encoding/json"
	"fmt"
	"os"
)

func main() {

	employeeJsonData := `	[{
		"id": "1",
		"name": "Tiger Nixon1",
		"phone":[1,2]
	},
	{
		"id": "2",
		"name": "Tiger Nixon1",
		"phone": [3,4]
	}
]`

	type Employee struct {
		Id    string
		Name  string
		Phone []int
	}

	var e []Employee
	err := json.Unmarshal([]byte(employeeJsonData), &e)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
	fmt.Printf("%+v", e[0]) // Print 1st object
}

```