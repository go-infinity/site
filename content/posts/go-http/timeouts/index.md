---
title: "Resiliency Patterns - HTTP Timeouts"
date: 2021-05-14T00:00:00
draft: false
hideLastModified: true
summaryImage: "timeout.png" 
keepImageRatio: true
tags: ["go", "resiliency","timeouts"]
summary: "Various Resiliency Patterns"
---

We, API developers always try to build robust `HTTP` servers and clients. But there is a network involved in between, and we cannot be very sure of the network reliability. We might not face these issues for a very long time until one day. Recently I faced such an issue that inspired me to write this post. The idea here is that there can be too many factors, abstractions, and integrations on which our `HTTP` Backends are based on and we should be designing our software to tolerate all such failures. You might have guessed it already where I am going with this. Yes we are going to look at various resiliency patterns. 

> Resiliency patterns helps to prevent cascading failures and to preserve functionality in the event of service failure.

The first pattern we are going to speak of is Timeouts. We do not speak much of this when designing our ditributed application but it really is a critical bit of your solution architecture.

{{% notificationBlue %}}
Timeout is the time limit allowed to expect a result otherwise an error is thrown and the action or event is canceled. Calculating the right timeouts needs more work along the lines of SLAs and Latencies Benchmarking.
{{% /notificationBlue %}}

## HTTP Protocol
> There are many Application Layer Protocols(HTTP, SMTP, FTP, etc.) and there are different ways to configure timeouts for different protocols. In this post, we will target `HTTP` protocol specifically.

HTTP is the application layer protocol as defined in OSI/TCP-IP model and the protocol we most commonly rely upon to expose and consume our web applications and web services over private/public networks. All the modern-day backends are based on HTTP protocols or some variant of HTTP such as GRPC, WebSockets, etc.

___HTTP versions___

Different HTTP protocol versions are being used these days as illustrated in the image below. We will remain focused on how to create resilient `HTTP` servers/clients and will not go into differences of different HTTP implementations.
{{< figure src="HTTP-v1-v2-v3-stacks.png" height="400" caption="https://www.nginx.com/blog/introducing-technology-preview-nginx-support-for-quic-http-3/">}}

___HTTP Request Lifecycle___

Now from the diagram above, We know that HTTP(S) relies on TCP connection and then TLS if we are using HTTPS which is mostly the case. But the complete HTTP request lifecycle includes some other pieces as well such as DNS resolution, sending request, receiving response, etc. Below you see some of the `HTTP` timeouts available in `Go`.
{{< figure src="HTTPLifecycle.png" height="400">}}
## HTTP Server and Timeouts
___Default HTTP Server___

`Go` provides first-class support for writing HTTP servers and clients. Its easier to start with default servers and clients provided in the `net/http` package and as described here and in the next section. The default server as in the below code snippet does not have any timeouts defined, which means in case of slow/stuck clients, the server will wait infinitely and might leak file descriptors. 

{{< codeWide language="go" >}}server := &http.Server{Addr: ":8080", Handler: nil}
server.ListenAndServe()
{{< /codeWide>}}

___Resilient HTTP Server___

Lets now see how we can create a resilient server with the timeouts explicitly defined.
{{< codeWide language="go" >}}resilientServer := &http.Server{
    Addr:         ":8080",
    ReadTimeout:  1 * time.Second,
    WriteTimeout: 1 * time.Second,
    IdleTimeout:  30 * time.Second,
    Handler:      nil}
server.ListenAndServe()
{{< /codeWide>}}

Various `HTTP` Server timeouts we have in `Go`
- _http.Server.ReadTimeout_: ReadTimeout is the maximum duration for reading the entire request, including the body.

- _http.Server.WriteTimeout_: WriteTimeout is the maximum duration before timing out writes of the response.

- _http.Server.IdleTimeout_: IdleTimeout is the maximum amount of time to wait for the next request when keep-alives are enabled

Even when we have defined `http.Server.WriteTimeout` as 1 second and the http client will get empty response, the handler executing the request will not stop and keep on executing till it completes. What is needed is to end the handler execution as well after 1 second.

## Timeout Handler
`net/http` provides `TimeoutHandler` which will run handlers for only specified time limit and has below signature

{{< codeWide language="go" >}}func TimeoutHandler(h Handler, dt time.Duration, msg string) Handler{{< / codeWide >}}

We can define this as in below example where we have defined 1 second as the timout limit. After 1 second if our mux handlers has not finished yet, Timeout Handler will stop it and return "Timeout!\n" message in the `http.ResponseWriter`

{{< codeWide language="go" >}}muxHTTP := http.NewServeMux()
muxHTTP.Handle("/", ourHandler) //our handler mapped to "/" path
resilientServer := &http.Server{
    Addr:         ":8080",
    ReadTimeout:  1 * time.Second,
    WriteTimeout: 1 * time.Second,
    IdleTimeout:  30 * time.Second,
    Handler:      http.TimeoutHandler(http.HandlerFunc(mux), 1*time.Second, "Timeout!\n"),}
server.ListenAndServe()
{{< /codeWide>}}

## HTTP Client and Timeouts
___Default HTTP Client___

If you are directly using `http.Get(URL)` or `&Client{}` that uses the http.DefaultClient, please remember that there are no default timeouts set on the request. So your request remains stuck in case of some network or server issues.

___Resilient HTTP Client___

Good design is to have some default timeout for your HTTP Client requests as in below code snippet.
{{< codeWide language="go" >}}var resilientClient = &http.Client{
  Timeout: time.Second * 10,
}
response, _ := resilientClient.Get(url)
{{< /codeWide >}}

Here we are setting 10 second as the timeout to wait for `HTTP` server responses.

If we need more finer-grained control, we can even have timeout limits on {{< codeInline >}}TCP connection setup{{< /codeInline >}} and {{< codeInline >}}TLS Handshake{{< /codeInline >}} as in the below code snipped. 
{{< codeWide language="go" >}}var resilientTransport = &http.Transport{
  Dial: (&net.Dialer{
    Timeout: 5 * time.Second,
  }).Dial,
  TLSHandshakeTimeout: 5 * time.Second,
}
var resilientClient = &http.Client{
  Timeout: time.Second * 10,
}
response, _ := resilientClient.Get(url)
{{< /codeWide >}}

- _http.Client.Timeout_: specifies a time limit for requests made by this Client. The timeout includes connection time, any redirects, and reading the response body.

- _net.Dialer.Timeout_: Timeout is the maximum amount of time a dial will wait for a connect to complete.

- _http.Transport.TLSHandshakeTimeout_: TLSHandshakeTimeout specifies the maximum amount of time waiting to wait for a TLS handshake. Zero means no timeout.
 
## Request Cancellation
As with the above `HTTP` client, the requests are going to timeout after 10 seconds but still, the server will continue to process the requests. And this will make our servers continue to consume resources and make them slow as they are doing unnecessary work. `Go` context package introduces in the standard library is specifically to handle such as scenarios. 

_Context_ in a brief is used for cancellation and Deadlines, value propagation within and across process boundaries. `Go` context deserves a separate post as it has a quite an interesting use cases and properties. For our use case purpose, what we want is that we stop server-side processing whenever we cancel that HTTP client request.

The `Request` type availabe in `net/http` package has a `context` attached to it, which we will use for cancellations. For incoming server requests, the server cancels the context when the client’s connection closes, when the request is canceled.

{{< codeWide language="go" >}}// construct the client and request. The HTTP client timeout is independent of the context timeout.
client := http.Client{Timeout: 2 * time.Second}
req, _ := http.NewRequest(http.MethodGet, URL, nil)

// We initialize the context, and specify a context timeout
ctx, cancel := context.WithTimeout(context.Background(), 2 * time.Second)
defer cancel() // to cancel the deadline

// We attach the initialized context to the request, and execute a request with it. 
reqWithDeadline := req.WithContext(ctx)
response, clientErr := client.Do(reqWithDeadline)
{{< /codeWide>}}

## Conclusions

- Always define Timeout explicitly in clients and servers.

- Always cancel the upstream work via context cancellations, deadlines.