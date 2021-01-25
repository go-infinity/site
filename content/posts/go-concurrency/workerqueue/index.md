---
title: "Concurrency Patterns - WorkerQueue"
date: 2021-01-23T21:51:13+01:00
draft: false
hideLastModified: true
summaryImage: "workerqueue.jpg" 
keepImageRatio: true
tags: ["go", "concurrency"]
summary: "Various Go Concurrency Patterns"
---
This is 3rd post in the Go Concurrency series.
1. [Concurrency Patterns - Locks and Waitgroups](/posts/go-concurrency/locks/)
2. [Concurrency Patterns - Channels](/posts/go-concurrency/channels/)
3. Concurrency Patterns - WorkerQueue

Next in our series on Golang Concurrency patterns is {{< codeInline >}} WorkerQueue {{< /codeInline>}}  or {{< codeInline>}} JobQueue {{< /codeInline >}}. In this pattern we want to restrict number of parallel threads/goroutines that will work at a time. In other words we can also say that we want to keep a pool of goroutines. This pattern is particularly useful when we dont knew the number of jobs/requests that we will recieve and we want some upper threashold on the number of goroutines so that our infra/server dont get overloaded. Hence we need some mechanism to keep all the jobs and assign this job to a free goroutine/worker.
We will continue with the same example we started with and we will restrict the number of goutines to 2.

{{< codeWide language="go" >}}
package main

import (
	"fmt"
	"log"
	"net/http"
	"os"
)

func main() {
	chStatus := make(chan int)
	// No of goroutines to spawn
	numWorker := 2

	jobQueue := make(chan string, 10)
	// resp is to hold all response status code
	resp := make([]int, 0)

	// Iterate over all the command line arguments to scrape URL
	for _, url := range os.Args[1:] {
		jobQueue <- url
	}
	close(jobQueue)
	for i := 0; i < numWorker; i++ {
		go ScrapeURL(jobQueue, chStatus)
	}
	// Iterate again to read from chStatus and update resp
	for range os.Args[1:] {
		status := <-chStatus
		resp = append(resp, status)
	}
	fmt.Printf("Responses :%v\n", resp)
}

// ScrapeURL takes a URL and do a HTTP Get request
func ScrapeURL(jobs chan string, ch chan int) {
	for url := range jobs {
		res, err := http.Get(url)
		if err != nil {
			log.Fatal(err)
		}
		ch <- res.StatusCode
	}
}
{{< /codeWide >}}