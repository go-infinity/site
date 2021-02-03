---
title: "Concurrency Patterns - Locks and Waitgroups"
date: 2021-02-01T00:00:00
draft: false
hideLastModified: true
summaryImage: "concurrency-lock.jpg" 
keepImageRatio: true
tags: ["go", "concurrency"]
summary: "Various Go Concurrency Patterns"
---
> This series will walk through the benefits of writing concurrent programs and the issues along the way. This series assumes basic understanding of Golang concurrency primitives like `mutexes`,`goroutines`,`channels` etc.

This is 1st post in the Go Concurrency series. We will focus on locking and syncronization in Golang. We will use a practical problem to demonstrate various concurrency patterns.

1. Concurrency Patterns - Locks and Waitgroups
2. [Concurrency Patterns - Channels](/posts/go-concurrency/channels/)
3. [Concurrency Patterns - WorkerQueue](/posts/go-concurrency/workerqueue/)

We all know Concurrency in `Go` is easy to start with, but hard to master. We have to be very careful while implementaing it and make sure that there are no race conditions, deadloacks, syncronization between goroutines etc.

Let's first define a base problem which can be used as reference for different patterns. 

{{< exercise >}}
**Problem Statement**

Build a health check system. You are given N URLs as commandline arguments. Do a HTTP GET request for each and save the return HTTP status code in a slice. Print the return codes as stored in the slice. 

You may assume the that the URL will be in proper URI scheme.

**Example:**<br>
**Input:** https://www.google.com https://github.com<br>
**Output:** [200, 200]<br>
{{< /exercise >}}

We will code this problem first naively and then will try to improve on it iteratively.

## Synchronous Execution
{{< codeWide language="go" >}}package main
import (
	"fmt"
	"net/http"
	"os"
)
var resp []int
func main() {
	for _, url := range os.Args[1:] {
		status, err := ScrapeURL(url)
		if err != nil {
			continue
		}
		resp = append(resp, status)
	}
	fmt.Printf("Responses :%v\n", resp)
}
// ScrapeURL takes a URL and do a HTTP Get request
func ScrapeURL(url string) (int, error) {
	res, err := http.Get(url)
	if err != nil {
		return 0, err
	}
	defer resp.Body.Close()
	return res.StatusCode, nil
}
{{< goplay url=abcde >}}
{{< /codeWide >}}
{{< goplay url=abcde >}}
Let's run the above code with some arguments and also use linux `time` command to benchmark it in the naive way. We found that the total execution time is around 4-5 seconds. Please note that we are directly using run command and not building the executable first.

{{< codeWide language="shell" >}}
time go run concurrency.go https://www.google.com https://www.github.com https://www.gitlab.com https://www.linkedin.com
Responses :[200 200 200 200]

real    0m4.519s
user    0m0.907s
sys     0m0.205s
{{< /codeWide >}}

## Concurrent Programs
Without concurrency primitives, The code executes in serial fashion. It means system executes one instruction at a time and then move to next instruction. Sequential Execution does not use the full processing power of multicore machines which provides support to execute instructions parallely. We all have heard the differences between oncurrency and parallelism. Parallelism is the ability of modern systems to execute multiple instruction at the same time and concurrency is the way we enable our programs to support parallelism and provide correct results.

My machine is multicore and I know I can definitely do better than this by splitting the work across multiple goroutines. Let try to improve the above implementation by using the `Go` concurrency. Before starting with the actual code, Lets decide how the goroutines are going to write to the common response slice. This is where our journey on goroutines synchronization will start. Remember the famous `Go` proverb.
> Don't communicate by sharing memory; share memory by communicating

We will first start with what's not advised and will see if there are any drawbacks. 

## Concurrent Implementation Attempt #1 - Waitgroups
Let's try to make the above code concurrent with the help of goroutines. To learn about goroutines refer this [post](/posts/goroutines)

{{< codeWide language="go" >}}package main
import (
	"fmt"
	"log"
	"net/http"
	"os"
	"sync"
)
var resp []int
func main() {
	var wg sync.WaitGroup
	for _, url := range os.Args[1:] {
		wg.Add(1) //Increment the waitgroup counter
		go ScrapeURL(url, &wg)
	}
	wg.Wait() //Blocking - wait for all goroutines to finish finish
	fmt.Printf("Responses :%v\n", resp)
}
// ScrapeURL takes a URL and do a HTTP Get request
func ScrapeURL(url string, wg *sync.WaitGroup) {
	res, err := http.Get(url)
	if err != nil {
		log.Fatal()
	}
	defer resp.Body.Close()
    resp = append(resp, res.StatusCode)
	wg.Done() //decrease the waitfroup counter
}
{{< /codeWide >}}

You might have also noticed some other code using `waitgroup` type provided by `sync` package. This is for synchronization between multiple goroutines. It mainly provides the capability to wait for a gorup of goroutines to wait before the main goroutine exits. To learn more about `waitgroups` refer this [post](/posts/waitgroups)

We use {{< codeInline>}} wg.Add(1) {{< /codeInline >}} to increment the group's counter telling the system there are X calls we need to wait to finish. {{< codeInline>}} wg.Done()  {{< /codeInline >}} to decrements the group's counter by 1. {{< codeInline>}} wg.Wait()  {{< /codeInline >}} will block the program from running the fmt.Printf() until the group's counter hits 0

> Note that a WaitGroup must be passed to functions by pointer. Reference for [waitgroups](https://golang.org/pkg/sync/#WaitGroup)

Lets run above code and see how it performs.

{{< codeWide language="shell" >}}
time go run concurrency.go https://www.google.com https://www.github.com https://www.gitlab.com https://www.linkedin.com
Responses :[200 200 200 200]

real    0m2.387s
user    0m1.060s
sys     0m0.332s
{{< /codeWide >}}

Above is the time taken for our first attempt to write concurrent program. Yayy, We have improved by about 50% without much hassle. But, the above code has a severe issue even if its performing well. And we know correctness of a program is above all other mesaures. Take a pause and try to understand the above code for any issues.


I hope most of you have already found the bug in the above code. Yes its unprotected access to our result slice as slices in golang are not concurrency safe data structure. This introduces race condition which means that multiple goroutines could access the shared data and they try to change it at the same time. We might be able to see different results in different executions of the same program. I was not able to reproduce this may be due to less number of goroutine, But I can easily detect the race conditions using `-race` flag to the `go run` or `go test` command. It confirms there is a race condition, now lets improve on this.

## Concurrent Implementation Attempt #2 - Locks
One way to overcome the race condition errors is only allowing one thread/goroutine to access the shared state at one point of time. Locking is the common technique used across multiple programming languages to synchronize shared access to a piece of data. In `Go` we can use `sync.mutex`. Below is the improved version with locking in place.

{{< codeWide language="go" >}}package main
import (
	"fmt"
	"log"
	"net/http"
	"os"
	"sync"
)
var (
	resp []int
	lock sync.Mutex
)
func main() {
	var wg sync.WaitGroup
	for _, url := range os.Args[1:] {
		wg.Add(1)
		go ScrapeURL(url, &wg)
	}
	wg.Wait()
	fmt.Printf("Responses :%v\n", resp)
}
// ScrapeURL takes a URL and do a HTTP Get request
func ScrapeURL(url string, wg *sync.WaitGroup) {
	res, err := http.Get(url)
	if err != nil {
		log.Fatal()
	}
	defer resp.Body.Close()
	UpdateResp(res.StatusCode)
	wg.Done()
}
// UpdateResp appends the return HTTP status to the result slice using mutex
func UpdateResp(res int) {
	lock.Lock()  // single goroutine acquires lock at a time
	resp = append(resp, res)
	lock.Unlock() // free locks for the other goroutine
}
{{< /codeWide >}}

Below we see that with locking applied we almost see the same performance as above code but without any race conditions. In more complex use cases, performance might degrade because of lock contention.
{{< codeWide language="shell" >}}
time go run concurrency.go https://www.google.com https://www.github.com https://www.gitlab.com https://www.linkedin.com
Responses :[200 200 200 200]

real    0m2.519s
user    0m2.307s
sys     0m0.205s
{{< /codeWide >}}

Just to summarize, In this post we have started with syncronous implementaion and then moved to asynchronous implementaiton without any correct gurantees and in the final version we have also used locking techniques to make sure we have implemented concurrent safe program.