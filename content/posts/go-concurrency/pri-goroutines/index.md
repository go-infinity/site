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

Many people like `Go` because of its simple and efficient concurrency model apart from other benefits language has to offer. It is very easy to spawn concurrent units of executions along with the main thread/goroutine. It feels like a breath of fresh air when you come from other Programming Languages background. In this post, we will look at what Goroutines are, various ways to spawn them. This post assumes that you are new to Go Programming, and has basic understanding of `Go`. If you already have some experience with Go Concurrency, you can skip this post and look at my other posts on Go Concurrency.

### Lets Dive In...
Okay, we have heard enough of Goroutines in almost all the Go posts/books, but what the heck it is? Why should I care about it? Isn't my program performant without Goroutines? Doesn't this make code less readable? Arent't they hard to debug. How its better than concurrency models of other languages? All these questions are quite common to everyone new in Go.

Lets just go through these questions one by one.

***Why Concurrency:***
It's now a no-brainer that most of the hardware we use our programs to run on, are equipped with multicores/multithreading and provides support to run instructions parallelly. We will write concurrent programs and will leave it on hardware to run it parallel if it is supported. Implementing Concurrency correctly can enhance the throughput and performance of our programs drastically with reduced waiting and response time by utilizing the existing resources more efficiently. Most of the modern programming languages provide support to write concurrent Programs

***Various Concurrency Model***
Different Programming languages has different concurreny models.

_Multi Processes_ - Managed by OS. High Overhead. Python has multiprocessing module which use `fork` to create sperated processes to povide paralellism.

_Kernal Level Threads_ - OS Manages the threads. Light Weight than processes. Java Threads uses Kernal Level Threads. There is another project called `Loom` which will bring support for User Space threads in Java.

_Single Threaded_ - Some Programming Languages has adopted Single Threaded non-blocking asynchornous concurrent Model(Event Loop or AsyncIO).

_Green Threads_ - OS does not create or manage them. OS does not schedule and preempt them. Completely managed in Userspace and Language runtime. Ln `Go` gorotuines falls into this category

***What's Goroutines:***
Goroutines are the `Go` way of providing concurrency. Goroutines are functions or methods which execute independenly and concurrently with the other goroutines of your program. Goroutines can be think of like Java Threads with the difference being that these are userspace threads and managed by `Go` runtime/scheduler.

***Why Goroutines:***
Being in userspace and managed by `Go` runtime/scheduler, Goroutines require less memory, has faster startup/destroy time, can be multiplexed on the kernel threads. There is no callback hell. You can literally spawn thousands of goroutines and will not see much overhead in terms of Memory/CPU.

>Go Concurrency Model (goroutines) to concurrency is what Docker is to virtualization


### Show me the code
Let's keep the tradition going and start with a **helloworld** example of go concurrency below. You will see there is a single keyword called `go` to create concurrent threads of execution. 

{{< codeWide language="go" >}}package main
import (
	"fmt"
	"time"
)
func hello() {
	fmt.Println("Hello World")
}
func main() {
	go hello() //create a goroutine
	time.Sleep(time.Second * 1) //stupid man synchronization
}
{{< /codeWide >}}

Lets first try to understand the above code. At line 6, we have defined a function named `hello` which prints "Hello World" on the STDOUT. In line 11, we have called this function with `go` keyword which will start this as goroutine.
Copy the above code and try to run it in your editor with/without `time.Sleep`. You will observe that without `time.sleep` main gorotuines exits without waiting for hello goroutine to finish. This is the way how it's implemented in `Go` where the main goroutine doesn't wait for other goroutines to finish. This is called as Fork Join Model and requires some kind of Join condition in the end to wait for all goroutines to finish. This requires some synchornization among goroutine through channels,waitgroups etc. `time.Sleep` is just a naive way to increase the probability that hello goroutine will finish its execution during that time.

Okay, we have got our hands dirty in the concurrency mud, just kidding:smile:. We have just put our foot on the concurrent world. Even when it's easy to launch goroutines and scheduling is handled by `Go` runtime, It's hard to write correct concurrent programs. We need to handle synchronization/coordination between goroutines, cancelling goroutines, collecting errors, etc.

In the above code, We have a seperate `hello` function which we are executing concurrently using the go keyword. Its not manadatory to create a seperate function and we can use anonymous function to achieve the same result as below.

{{< codeWide language="go" >}}
package main
import (
	"fmt"
	"time"
)
func main() {
	func() {
		fmt.Println("Hello World")
	}()
	time.Sleep(time.Second * 1) //stupid man synchronization
}
{{< /codeWide >}}

### Concurrent Merge Sort
Okay, Now we know the basics of writing concurrent program in `Go` let's try to solve an actual problem. We all have used/learnt Merge Sort during our work/college. Below is the non-concurrent version.

{{< codeWide language="go" >}}
// MergeSort sorts the slice s using Merge Sort Algorithm
func MergeSort(s []int) []int {
	if len(s) <= 1 {
		return s
	}
	n := len(s) / 2
	var l []int
	var r []int
	l = MergeSort(s[:n])
	r = MergeSort(s[n:])
	return merge(l, r)
}

func merge(l, r []int) []int {
	ret := make([]int, 0, len(l)+len(r))
	for len(l) > 0 || len(r) > 0 {
		if len(l) == 0 {
			return append(ret, r...)
		}
		if len(r) == 0 {
			return append(ret, l...)
		}
		if l[0] <= r[0] {
			ret = append(ret, l[0])
			l = l[1:]
		} else {
			ret = append(ret, r[0])
			r = r[1:]
		}
	}
	return ret
}
{{< /codeWide >}}

And below we see the that we only have main goroutine running on one of the processor
{{< figure src="maingoroutine.png" height="400" >}}

The same code using concurrency with two goroutines.
{{< codeWide language="go" >}}
// MergeSortMulti sorts the slice s using Merge Sort Algorithm
func MergeSortMulti(s []int) []int {
	fmt.Println(runtime.NumCPU())
	if len(s) <= 1 {
		return s
	}
	n := len(s) / 2
	wg := sync.WaitGroup{}
	wg.Add(2)
	var l []int
	var r []int
	go func() {
		l = MergeSortMulti(s[:n])
		wg.Done()
	}()
	go func() {
		r = MergeSortMulti(s[n:])
		wg.Done()
	}()
	wg.Wait()
	return merge(l, r)
}

func merge(l, r []int) []int {
	ret := make([]int, 0, len(l)+len(r))
	for len(l) > 0 || len(r) > 0 {
		if len(l) == 0 {
			return append(ret, r...)
		}
		if len(r) == 0 {
			return append(ret, l...)
		}
		if l[0] <= r[0] {
			ret = append(ret, l[0])
			l = l[1:]
		} else {
			ret = append(ret, r[0])
			r = r[1:]
		}
	}
	return ret
}
{{< /codeWide >}}

We see that now the we have goroutines running concurrently on both the processor.
{{< figure src="multigoroutine.png" height="400" >}}

We will not compare the performance as we dont have big data set to play with and corectly benchmark the results. But the idea here is show how utilizing `Go` concurrency model utilize all CPU Processors optimally and efficiently.

Nothing comes for free and goroutines has its own pitfalls too. I will cover these in another post.
Also to learn more about concurrency and `Go` scheduler, I encourage you to watch below 2 videos to learn more about `Go` concurrency model.
1) Famous talk from Rob Pike on Parallelism vs Concurrency.
2) Talk on `Go` Scheuler internals from Kavya Joshi.

{{% twoVideos  id1="oV9rvDllKEg" id2="YHRO5WQGh0k" ratio1="4:3" ratio2="4:3"%}}