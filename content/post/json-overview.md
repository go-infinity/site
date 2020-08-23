---
title: "Json Overview"
date: 2020-08-20T14:44:26+05:30
tags: [json]
draft: false
toc: true
categories: [json]
---
## JSON
**Json** (Javascript Object Notation) is a lightweight format to store information in an organized, easy-to-access manner. 

It is maily used to transmit data between server and client or web applications.

- It stores data in key-value pair. 
- json data is enclosed in curly braces {}.
- Key is of type string and must be enclosed in double-quotes.
- Value can be of type :
    - string
    - int
    - boolean
    - list
    - Array of objects

```json
{
    "id": 1,
    "employee_name": "Tiger Nixon",
    "employee_salary": 320800.00,
    "employee_age": "61",
    "is_active": true,
    "interest":["reading","swimming","content-writing"]
}
```

## Type of JSON

- **Structured JSON**: When the json schema is fixed. e.g. hitting an API will always produce reponse of below 
```json
{
    "id": 1,
    "employee_name": "Tiger Nixon",
    "employee_salary": "320800",
    "employee_age": "61"
}
```

- **Unstructured JSON**: when the json schema is not fixed. e.g. API response can vary
```json
[
    {
        "id": "1",
        "employee_name": "Tiger Nixon",
        "employee_salary": "320800",
        "employee_age": "61"
    }
    {
        "id": 2,
        "employee_name": {"first_name":"ABC","last_name":"XYZ"},
        "employee_salary": "320800",
        "employee_age": "61"
    }
]
```
