---
title: "Concurrency Patterns - Channels"
date: 2021-01-31T21:51:13+01:00
draft: false
hideLastModified: true
summaryImage: "concurrency-channel.jpg" 
keepImageRatio: true
tags: ["go", "concurrency"]
summary: "Various Go Concurrency Patterns"
---
> This series will walk through the benefits of writing concurrent programs and the issues along the way. This series assumes basic understanding of Golang concurrency primitives like `mutexes`,`goroutines`,`channels` etc.

This is 2nd post in the Go Concurrency series.
1. [Concurrency Patterns - Locks and Waitgroups](/posts/go-concurrency/locks/)
2. Concurrency Patterns - Channels
3. [Concurrency Patterns - WorkerQueue](/posts/go-concurrency/workerqueue/)

In the previous [post](/posts/go_concurrency/locks/), we learned about 2 basic synchronization constructs in golang.
- `sync.waitgroups` to wait for all the goroutines to finish before the main goroutine exits.
- `sync.mutex` to apply mutually exclusive locks to synchronize access to shared data.

In this post we will work on the same problem statment and will try to work out using another synchronization pattern with `Channels` that is more popular and is `Go` way of implementing Communicating Sequential Processes(CSP), a paper by Tony Hoare published in 1978. The Goal of this post is not to summarize CSP but to demonstrate the practical usage of channels.

## Definition
A Channel is way for 2 or more goroutines to pass data to each other. It can be thought of as a Queue(Buffered/UnBuffered) on which multiple goroutines can either pass data or read data. Multiple different Queue Patterns such as Fan-In, Fan-Out are possible using `Go` Channels.

Go provides typesafe channels which can be created using `chan` keyword. Go Provides both buffered and unbuffered channels. Also its possible to specify direction of channels means whether we can only send or recieve or do both. Channel will be covered in detail in another post. We will start with basic implementation and then will try to improve on the same.

{{< codeWide language="go" >}}
package main

import (
	"fmt"
	"log"
	"net/http"
	"os"
)

func main() {
	// resp is to hold all response status code
	resp := make([]int, 0)
	// chStatus to hold respone from a single API call
	chStatus := make(chan int)
	// Iterate over all the command line arguments to scrape URL
	for _, url := range os.Args[1:] {
		go ScrapeURL(url, chStatus)
	}
	// Iterate again to read from chStatus and update resp
	for range os.Args[1:] {
		status := <-chStatus
		resp = append(resp, status)
	}
	fmt.Printf("Responses :%v\n", resp)
}

// ScrapeURL takes a URL and do a HTTP Get request
func ScrapeURL(url string, done chan int) {
	res, err := http.Get(url)
	if err != nil {
		log.Fatal()
	}
	defer resp.Body.Close()
	done <- res.StatusCode
}
{{< /codeWide >}}

Performance of the above code is similar to our solution using `sync.Mutex`. So we have not improved much on performance but there are other advantages to this pattern highlighted in the next section.
{{< codeWide language="shell" >}}
time go run concurrency.go https://www.google.com https://www.github.com https://www.gitlab.com https://www.linkedin.com
Responses :[200 200 200 200]

real    0m2.000s
user    0m0.733s
sys     0m0.238s
{{< /codeWide >}}

But you might have already observed that we get rid of `waitgroups` which already is big improvement and make our code simpler to understand. Some of you who have come from other programming languages backgorund migh have hard time to wrap your head around this concept as it was the case with me. You might have already start wondering how come we don't have race conditions in this solutions and how different goroutines are coordinating. Its this property of `go` channels which makes them special as highlighted below.

> Channel Operations are blocking in nature. Sending to a channel with no available buffer space blocks the sender until the send can complete; receiving from a channel with no available messages blocks the receiver until the receive can complete.

So if we read the code again, we come to understand that as we are using unbuffered channel {{< codeInline >}} chStatus := make(chan int) {{< /codeInline >}} only one item can be pushed. Trying to push more items in the channel will be a blocking operation until we read something out of the channel. Because in our case we know the number of goroutines that we have spawned, We do that in the second iteration. The other go routines will be able to complete and push their output to the channel.
