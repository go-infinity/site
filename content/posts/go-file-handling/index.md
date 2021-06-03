---
title: "Go File Handling"
date: 2020-08-17T14:44:26+05:30
tags: [go, file handling]
draft: true
summaryImage: "files.jpg" 
keepImageRatio: true
toc: true
categories: [go]
summary: "File operation in golang"
---
## Type of Files
Files on a basic level are of two types
- Binary e.g. xlsx, pdf, jpg
- Non Binary e.g. txt, csv, json

> Golang provides full support for non binary files manipulation and have limited support for Binary files.

## File Permissions
POSIX standard has 2 set of information related to file permissions
- file permissions
- file owner/group

### File Permission
- contains 10 bits to represent file/directory and permissions(read,write,execute) for owner,group and others in the below format
- file/dir-permission for owner-permission for group-permission for others

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
```

## Read File in Chunks
```go
package main

import (
	"fmt"
	"io"
	"os"
)
func main() {
	f, err := os.Open("Test.txt")
	defer f.Close()
	if err != nil {
		fmt.Printf(err.Error())
		os.Exit(1)
	}
	b := make([]byte, 5)
	for {
		n, err := f.Read(b)
		if err != nil {
			if err != io.EOF {
				fmt.Println(err)
			}
			break
		}
		fmt.Print(string(b[:n]))
	}
}
```

## Read File using FileMode
## Read File using Stat
## Read File using ioutil
```go
package main

import (
	"fmt"
	"io/ioutil"
)

func main() {
	data, err := ioutil.ReadFile("Test.txt")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(string(data))

}
```
## Rename File
## Remove File 