---
title: "Concurrency Primitives - Goroutines"
date: 2021-01-22T21:51:13+01:00
draft: false
hideLastModified: true
summaryImage: "goroutine.png" 
keepImageRatio: true
tags: ["go", "concurrency"]
summary: "Go Concurrency Primivite - Goroutines"
---

Below is the **helloworld example** of go concurrency. We see with simple go keyword we can run hello function concurrently alongwith the main goroutine. Ignore `time.sleep` for now if it's not clear, it's only to make sure funtion hello finishes before main exits. We will discuss this in subsequent sections. 

{{< codeWide language="go" >}}package main
import (
	"fmt"
	"time"
)
func main() {
	go hello()
	time.Sleep(time.Second * 1)
}
func hello() {
	fmt.Println("Hello World")
}
{{< /codeWide >}}

Okay we have got our hands dirty in the concurrency mud, just kidding:smile:. We have put our foot on the concurrent world.