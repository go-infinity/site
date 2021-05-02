---
title: "REST APIs"
date: 2020-08-22T14:44:26+05:30
tags: [api, rest]
draft: true
summaryImage: "api-rest.png" 
keepImageRatio: true
toc: true
categories: [api,http]
summary: "Server REST APIs"
---
## HTTP services
HTTP is the defacto standard to access application/services these days, Hence all software is written to be accessed over HTTP protocol. HTTP has 3 different versions -
- HTTP/1.1
- HTTP/2.0
- HTTP/3.0

### Client Server Model
Now the http services can be consumed by http clients over APIs

{{< mermaid align="center" theme="neutral" >}}
graph LR;
   client-->Network;
   Network-->Server;
{{</mermaid>}}

Now there are different standards of API
- REST
- OpenAPI
- RPC
- GraphQL

## Definition
REST is Representational State Transfer which basically means exposing functionality based on entity/resource of the application.
- Its core principle is to define named resources that can be manipulated using a small number of methods. The *resources* and *methods* are known as nouns and verbs of APIs.
- With the HTTP protocol, the resource names naturally map to URLs, and methods naturally map to HTTP methods POST, GET, PUT, PATCH, and DELETE.

REST APIs are defined using HTTP standard.

We will take an example of Employee Management System where employee is one of the resource/entity in our application.

## REST and HTTP

### __Resources__

Resource[https://cloud.google.com/apis/design/resources#resources] is like an entity on which we perform operations.It depends on how you are modelling your applications.
REST standard allow you to work on single resource at a time.

### __Resource Representation__
Resources can be represented in multiple ways like XML format, Json format etc

```xml
<!--> XML Representation<!-->
<employee>
   <id>1</id>
   <employee_name>Tiger Nixon</employee_name>
   <employee_salary></employee_salary>
</employee>   
```
OR 
```json
# JSON Representation
{
   "id": 1,
   "employee_name": "Tiger Nixon",
   "employee_salary": 320800,
   "employee_age": 61,
   "profile_image": ""
}
```


### __Endpoint__ 
Based on employee managemnet application, we can define one endpoint for e.g. `/api/employees` 

### __Operation Type__ 
HTTP Verbs[https://tools.ietf.org/html/rfc2616#section-5.1.1] are used for different operation types as listed below

|Operation|HTTP verb|
|---------|---------|
|Read     | HTTP GET Verb|
|Create   | HTTP POST verb|
|Delete   | HTTP DELETE verb|
|Update   | HTTP PUT/PATCH verb|

### __Return Status__ 
To provide whether the API call was successful or not we rely on _HTTP status_ codes

|Http Status Code Range|Meaning|
|---------|---------|
|100- 199| Informational|  
|200-299   | Success|
|300-399  | Redirection|
|400-499  | Client Error|
|500-  | Server Error|

### __Content Negotiation__ 
To provide information of the sent/expected data format of the payload we use below *_HTTP headers_* 

|Http Header|Meaning|Examples|
|---------|---------|---------|
|Content-Type   | Request format sent to Server|text/plain, application/xml, text/html, application/json|
|Accept-Type  | Response format expected from Server|text/plain, application/xml, text/html, application/json|

### __Response Compression__ 
To provide information for respeonce compression REST use below _HTTP headers_

|Http Header|Meaning|Examples|
|---------|---------|---------|
|Content-Encoding   | Compression Method used byserver Server|gzip|
|Accept-Encoding  | Compresseion Methods Client understands|compress, gzip, br|


