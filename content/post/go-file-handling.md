---
title: "Go File Handling"
date: 2020-08-17T14:44:26+05:30
tags: [go, file handling]
draft: false
toc: true
categories: [go]
---
## Type of Files
Files on a basic level are of two types
- Binary e.g. xlsx, pdf, jpg
- Non Binary e.g. txt, csv, json

> Golang provides full support for non binary files manipulation and have limited support for Binary files.
## File Stats
```go
package main

import (
	"fmt"
	"os"
)

func main() {
	f, err := os.Stat("sample.txt")
	if err != nil {
		fmt.Printf(err.Error())
		os.Exit(1)
	}
	fmt.Println(f.Size()) // to get the file size in bytes
	fmt.Println(f.Mode()) // to get the file permission mode
}
```
## Create File
```go
package main

import (
	"fmt"
	"os"
)

func main() {
	f, err := os.Create("Test.txt")
	defer f.Close()
	if err != nil {
		fmt.Printf(err.Error())
		os.Exit(1)
	}
	fmt.Printf(f.Name())
}
```
## Write File
```go
package main

import (
	"fmt"
	"os"
)

func main() {
	f, err := os.Create("Test.txt")
	defer f.Close()
	if err != nil {
		fmt.Printf(err.Error())
		os.Exit(1)
	}

	n, err := f.WriteString("Hi this is a test file")
	if err != nil {
		fmt.Printf(err.Error())
		os.Exit(1)
	}
	fmt.Printf("N:%d", n)
}
{{< goplay url=cb6B6zX7iL0 >}}
```

## Read File
## Rename File
## Remove File 