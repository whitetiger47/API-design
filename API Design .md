# API Design

# What is REST?

Representational State Transfer (REST) is a software architecture that imposes conditions on how an API should work. REST was initially created as a guideline to manage communication on a complex network like the internet. You can use REST-based architecture to support high-performing and reliable communication at scale. You can easily implement and modify it, bringing visibility and cross-platform portability to any API system.

# Components of REST APIs:

### Unique resource identifier

The server identifies each resource with unique resource
 identifiers. For REST services, the server typically performs resource 
identification by using a Uniform Resource Locator (URL). The URL 
specifies the path to the resource. A URL is similar to the website 
address that you enter into your browser to visit any webpage. The URL 
is also called the request endpoint and clearly specifies to the server 
what the client requires.

### Method

Developers often implement RESTful APIs by using the 
Hypertext Transfer Protocol (HTTP). An HTTP method tells the server what
 it needs to do to the resource. The following are four common HTTP 
methods:

***GET***

Clients use GET to access resources that are located at 
the specified URL on the server. They can cache GET requests and send 
parameters in the RESTful API request to instruct the server to filter 
data before sending.

***POST***

Clients use POST to send data to the server. They 
include the data representation with the request. Sending the same POST 
request multiple times has the side effect of creating the same resource
 multiple times.

***PUT***

Clients use PUT to update existing resources on the 
server. Unlike POST, sending the same PUT request multiple times in a 
RESTful web service gives the same result.

***DELETE***

Clients use the DELETE request to remove the resource. A
 DELETE request can change the server state. However, if the user does 
not have appropriate authentication, the request fails.

### HTTP headers

Request headers are the metadata exchanged between the 
client and server. For instance, the request header indicates the format
 of the request and response, provides information about request status,
 and so on.

***Data***

REST API requests might include data for the POST, PUT, and other HTTP methods to work successfully.

***Parameters***

RESTful API requests can include parameters that give 
the server more details about what needs to be done. The following are 
some different types of parameters:

- Path parameters that specify URL details.
- Query parameters that request more information about the resource.
- Cookie parameters that authenticate clients quickly.

## Difference between POST and PUT methods

The key difference between PUT and POST methods is that a PUT is 
restricted to create or update operations, while a POST operation may 
perform any type of processing. Unlike a POST, PUT operations may only 
operate on the resource identified by the URL provided. HTTP POST 
processing is allowed on any server-side resource, regardless of the 
URL.

POST operations should give preference to resources subordinate to 
the resource identified by the URL. RESTful APIs and operations should 
be predictable in how they manipulate the resources and generate 
results.

## HTTP PUT method example

Imagine a flight tracking system with which users look up the status of American Airlines flight 123 at the following URL:

```
www.example.com/AA123
```

To update flight AA123’s status, a web service performs a PUT operation to that URL.

The PUT operation includes a JSON or XML payload that fully describes the new status:

```
PUT URL: www.example.com/flights/AA123PAYLOAD: {"status":"ontime", "gate":"b12"}

```

### HTTP PUT create example

Now, imagine we need to create a *new* entry for Air Canada flight 789.

A URL that describes this flight does not exist yet, but we know that after creation the URL will be:

```
www.example.com/AC789
```

Since we know the desired URL of the resource before requesting its creation, we must use a PUT operation.

```
PUT URL: www.example.com/flights/AC789
PAYLOAD: {"status":"late", "gate":"c17"}
```

## HTTP POST method example

When a client requests creation of a resource, it doesn’t always know what URL the server will assign to the resource.

When one uses the POST operation is used to create a resource on the 
server, the resource is created as a subordinate resource to the URL 
that the POST operation provides. This places HTTP PUT vs. POST methods 
in stark contrast, as the PUT operation requires the exact URL of the 
resource to be created or updated.

Not knowing the exact URL of a resource prior to its creation is a 
common scenario in database driven applications where a newly created 
resource is identified by a value that’s generated *after* the resource’s data is written to a table.

Primary key-based identifiers fall into this category.

If you need to create a resource that is *subordinate* to the URL provided, you should use a POST method, not an HTTP PUT method.

For example, to create a new customer, a RESTful microservice uses 
the HTTP protocol’s POST method. All of the information needed to create
 the new customer is provided as JSON or XML in the request’s payload:

```
POST URL: www.example.com/customers
PAYLOAD: {"name":"Joe", "age":"29", "city":"ajax"}
```

### HTTP POST create example

When the server completes this request, it creates a new URL that uniquely identifies the new resource.

The following example shows a possible URL generated by a 
database-driven application for this new customer after a successful 
POST operation:

```
www.example.com/customers/j567
```

When a POST operation creates a new URL, REST demands that the new 
URL is sent back to the calling program as a location header. This 
allows the calling program to use PUT operations for future updates.

This is why the difference between a PUT and POST operation is often phrased as: *To create an object, use a POST. PUT should be used for updates.*
 That’s an oversimplification of the purpose of the HTTP verbs, and by 
no means should you map HTTP methods onto SQL-based CRUD operations. 
Nevertheless, as a high-level simplification, it’s allowable.

## POST and PUT idempotence

One rule the HTTP protocol places on PUT operations is that they must
 be idempotent. Idempotency has never been a requirement of POST 
operations.

To be idempotent, an operation could be invoked once or 100 times and the result on the server would be the same.

### Idempotence example for PUT and POST methods

For example, I perform a PUT operation to set my bank account balance
 to $100. Even if I perform that operation 100 times, the end result is a
 balance of $100.

In contrast, if I perform 100 POST operations that each increases my 
account balance by 10%, the result of each interaction creates a 
different result. That is not an idempotent operation.

A PUT method allows only idempotent operations. POST methods are not subject to idempotency requirements.

![https://itknowledgeexchange.techtarget.com/coffee-talk/files/2023/08/safe-vs-idempotent.png](https://itknowledgeexchange.techtarget.com/coffee-talk/files/2023/08/safe-vs-idempotent.png)

PUT is idempotent. POST is not.

## Should I use PUT or POST?

The HTTP specification [RFC 7231](https://www.rfc-editor.org/rfc/rfc7231#section-4.3.4) is very clear that the PUT method is only used to perform idempotent create or update operations:

> The PUT 
method requests that the state of the target resource be created or 
replaced with the state defined by the representation enclosed in the 
request message payload.
> 

In contrast to the PUT, POST operations are not limited to creates and updates. That HTTP specification [also says](https://www.rfc-editor.org/rfc/rfc7231#section-4.3.3) that upon receiving a POST invocation, the server may choose and implement any logic to support the request:

> The POST method requests that the target resource process the representation enclosed in the request according to the resource's own specific semantics.
> 

This is why many RESTful APIs extensively use the HTTP protocol’s 
POST method. If a function or service doesn’t map neatly onto one of the
 HTTP protocol’s GET, PUT, PATCH or DELETE methods, the POST method gets
 used.

When software architects build and design a RESTful API, it is 
important for them to respect the manner in which to use the various 
methods of the HTTP protocol. That includes knowing the difference 
between when to use a HTTP PUT vs POST operation.

| HTTP POST vs. PUT comparison chart |  |  |
| --- | --- | --- |
|  | **PUT** | **POST** |
| Resource URL required | Yes | No |
| Safe | No | No |
| Idempotent | Yes | No |
| Use cases | Create and updates | Form handling, creates, blog posts, processing |
| Response codes | 200, 201, 204 | All but 206 |

## What does the RESTful API server response contain?

REST principles require the server response to contain the following main components:

### Status line

The status line contains a three-digit status code that 
communicates request success or failure. For instance, 2XX codes 
indicate success, but 4XX and 5XX codes indicate errors. 3XX codes 
indicate URL redirection.

The following are some common status codes:

- 200: Generic success response
- 201: POST method success response
- 400: Incorrect request that the server cannot process
- 404: Resource not found

### Message body

The response body contains the resource representation. 
The server selects an appropriate representation format based on what 
the request headers contain. Clients can request information in XML or 
JSON formats, which define how the data is written in plain text. For 
example, if the client requests the name and age of a person named John,
 the server returns a JSON representation as follows:

'{"name":"John", "age":30}'

### Headers

The response also contains headers or metadata about the
 response. They give more context about the response and include 
information such as the server, encoding, date, and content type.