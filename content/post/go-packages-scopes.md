---
title: "Golang Packages and Scopes"
date: 2020-07-25T14:44:26+05:30
authors: [admin]
draft: false
featured: true
categories: [Golang]
toc: true

---


## How to define Packages
First Line of every go source file should have package name as below. Package name and file names can be different
```go
package product
```

## Multiple Go files can share the same package names
```go
-- file -- productcategory.go
package product
type ProductCategory struct {
	ParentCategoryName string
	SubCategory        string
}

-- file -- product.go
package product
type Product struct {
	ProductName     string
	ProductCategory ProductCategory
	ProductPrice    float32
	ProductStock    int
}
```
## Same Package files should be in one Directory
Go will throw error on below code as it does not allow different packages under one directory
```go
-- file -- product_category/productcategory.go
package product
type ProductCategory struct {
	ParentCategoryName string
	SubCategory        string
}

-- file -- product/product.go
package product
type Product struct {
	ProductName     string
	ProductCategory ProductCategory
	ProductPrice    float32
	ProductStock    int
}
```
## Package Import
A package can be imported in another packages and should be after Package declaration statement. Below is the format to import a package
```go
import (
    "fmt"
    "log"
)
```
## Package name alaises
Package aliases can be used in case of 
- longer packages names 
- if there are import statement for same package name from 2 different libraries
```go
package product
import (
	"fmt"
	f "fmt"
)
```
## Scopes
If a type variable is exportable i.e. starts with Uppercase letter it can be used in other packages otherwise the scope of variable is limited to its package only
```go
// file -- user/address.go
package address
type Address struct {
	City    string
	State   string
	Country string
}

// file -- user/user.go
package user
import (
    "user"
)
type User struct {
	FirstName       string
	LastName        string
	Role            string
	HomeAddress     address.Address
}
```