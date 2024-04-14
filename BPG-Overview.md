# Overview Summary

The following sections cover the key takeaways from each of the sections from the main document of this repository, the [Best Practice Guide](BestPractiseGuide.md). 


This is to be a brief overlook of each section. For more details and information please visit the main guide.


## Defining Principles 
 
The following sections will cover REST and HTTP and how best to conform to the standards set by them.


### Representational State Transfer (REST) 
Representational State Transfer, or REST, is the architectural style that encourages growth, scalability, security, cacheability and self-descriptive services.

A RESTful API is one that has its services defined by "resources", which are representations of data or a service that can be accessed by a client. All interactions between the client and the API are to be *stateless*, which is that all requests must contain all the information necessary to process that request. There are a number of constraints one must adhere to when creating REST APIs:
1. Resources representations are sent to clients through HTTP defined methods, such as GET, PUT, POST, PATCH & DELETE.
2. Server - Client communications can be done through various content types that can be agreed upon between both parties.
3. Each request must be stateless, requiring no previous information and containing all information required to process it.
4. Caching should be supported, and responses should indicate if they are or not.
5. API Components should be split up into layers. This supports modularitty and separating the concerns of the system into individual components.
6. Clients should be able to naturally discover the functionality of resources dynamically.


We establish paths for clients to access resources or perform actions on them, with each resource having a Uniform Resource Identifier (URI). For instance:

```text
GET https://bpg-library.com/books/24 
```

Indicates a GET request (return a representation of the resource) to "bpg-library" domain to access the Book with the ID of 24. There are some common principles for URIs that should be followed:

- A collection resource is a list of resources that are stored, i.e., “.../books” and should be named after the plural version of the noun.
- A simple resource would be a singular resource, i.e., book.
- An HTTP GET request, for example, would be the verb.
- That they follow clear paths, i.e there is no confusion between resources.


API Discoverability is an important aspect of any RESTful API service. With the use of Hypermedia links, clients can be made aware of similar functionalities and resources between a given resource. This is referred to as the *Hypermedia as the Engine of Application State(HATEOAS).*.

For example, a call to the URI below could return a JSON body that describes the base functionality for each exposed endpoint.


```text
GET https://bpg-library.com/books/
```

The returned data contains the collection of books *plus* hypermedia links that shows related functionality alongside the relevant URI to access it.

```json
{
    ...
  "_links": {
    "self": { "href": "bpg-library.com/" },
    "books": { 
        "href": "bpg-library.com/books",
        "action": "GET",
        "rel": "books",
        "types":["text/xml","application/json"]
    },
    "authors": { 
        "href": "bpg-library.com/members",
        "action": "GET",
        "rel": "members",
        "types":["text/xml","application/json"]
    },
    "search": { 
        "href": "bpg-library.com/books",
        "action": "POST",
        "title": "Add a book",
       
    }
    ....
  }
}
```

### HTTP Specification
HTTP is an established set of standards that facilitates communication between computer systems. It is one of the core building blocks of the Internet. The official stanadrds for HTTP is the [RFC7231](https://datatracker.ietf.org/doc/html/rfc7231).

The main factors to be discussed in the following sections are: 
- Uniform Resource Locators.
- Requests and Responses.
- HTTP Status Codes and Methods.
- Security.
- Headers.
- Resource Design.

#### Uniform Resource Locators 

Making the endpoints accessible to clients is a key way of facilitating streamlined communication. Following a pattern of the following will keep resource collections and methods easily accessible for clients.

```text
https://<domain-name>/<resource-collection>/<resource-id>:<action>?<input parameters>
```

The HTTP method should dictate what is done on a request. Resource collections should be created by a POST request to ".../noun". For example:

```text
POST https://bpglibrary-official.com/books
Authorization: Bearer...
Content-Type: application/json
{
  "title": "Dylans New Book"...
}
```
The above request will create a new resource under the Books collection.

Conventing to keep in mind when establishing the URLS should be:
- Easily understandable.
- Readable.
- Define the resources as nouns.




#### Requests and Responses

The core of it all is the communication between the client and server. Implementing the correct strategies to handle client requests and responses is key to providing a reliable and usable service. Failing to do this 


HTTP uses Status Codes to indicate the state of a request. It provides further insight on what wrong with the request and how to potentially fix the issue. There are 5 distinct classes of Status Codes:

| Value | Indicator           | Meaning                                                           |
|-------|---------------------|-------------------------------------------------------------------|
| 1xxx  | Informational       | Request was received and is being processed.                      |
| 2xxx  | Request Success     | Request was understood and executed successfully.                 |
| 3xxx  | Request Redirection | Original method has been replaced or moved, further action needed.|
| 4xxx  | Client Error        | The request was badly formatted or cannot be executed.            |
| 5xxx  | Server Error        | Server failed to fulfill the valid request.                       |


HTTP methods supported should return with the correct Status Code, and that they should cause as little side-effects within the system as possible. These types of requests are referred to as Idempotent Request, and allows for requests to be retried without any reactions in the system.

Multiple GET requests to the same URI should result in the same object being retrieved, or a DELETE request to return either 204 for the first request, and 404 for subsequent requests.


| Method | Description                                | Response Status Code                    | Idempotent |
|--------|--------------------------------------------|-----------------------------------------|------------|
| PATCH  | Create/Modify the resource with JSON       | 200-OK, 201-Created                | Yes    |
| PUT    | Create/Replace the whole resource          | 200-OK, 201-Created                     | Yes        |
| POST   | Create new resource                           | 201-Created with URL of created resource | No         |
| POST   | Action                                     | 200-OK                                   | No         |
| GET    | Read resource or collection                | 200-OK                                   | Yes        |
| DELETE | Delete resource                       | 200-OK / 204-No Content; avoid 404-Not Found     | Yes        |


Content Types are formats of communication set between the client and the API. This guide mainly discusses JavaScript Object Notation(JSON), but there are [other formats](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) that can be used. 

Requests that are received with a content type that is not supported by the API, or is invalid, it should be rejected from the system with 406-Not Acceptable or 415-Refusal to Accept Request. 

A request which does not contain this information should be responded with a 401-Unauthorised, including a message on what the user requires to be authenticated to access the resource.

To add an extra layer of security, API Keys or JWTs could be used to verify clients sending requests. Supporting these features can lead to a more secure system, at the sake of having clients remember to include the relevant authorisation in every request.


Data deleted should be retained for an X perod time. Helps improve reliability of service if in the case where someone deletes by mistake / was not authorised.

For requests that may take longer to process, The API should return a 202-Accepted to indicate the request has been received and initiate a separate operation to perform the work needed. A URI should be provided so the client can keep updated.


The API should support paramterised queries, allowing for actions to be performed on resources, or to alter the expected response back. Semantically, it should follow a format as below: 

```text
https://<domain-name>/<resource-collection>/<resource-id>:<action>?<input-parameters>
```

This paramaters must be validated against, otherwise clients could gain unauthorised access to resources and operations. 


APIS must enforce Rate Limiting. It is a simple way of ensuring clients cannot make too many requests at once, or within a certain period on a given resource. Without this, resources can be excessively accessed which will increase latency and the traffic between the API.  For example, the request below states that they wish to retrieve a maximum of 50 books.

```text
GET https://bpg-library.com/v1/books/
...
max_results: 50
```

If this this value is not validated against, the client could alter the request to look like:

```text
GET https://bpg-library.com/v1/orders/
...
max_results: 10000
```
This is just one method of how clients could excessively consume resources, and that developers should create strategies to counter them.

Clients who have exceeded the amount of requests on a given resource should have a 429-Too Many Requests Status Code returned to them.




#### Headers

[HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) enables further information to be added about a given request or response, such as:
 - Client Authorisation.
 - Version Indication.
 - Caching.
 - Payload Information. 

Client-side caching can be done through Cache-Control headers that should be supported. The header provides the necessary information for clients to indicate if a resource is cacheable, how long it can be cached and the state of the cached version. 


The common Cache-Control headers that should be supported  are:

| Header           | Description                                                             |
|------------------|-------------------------------------------------------------------------|
| Max-age          | The maximum time in seconds for how long the cache will exist.          |
| Private          | Indicates that the response is only cacheable on the client’s browser.  |
| Public           | Indicates that the response is publicly cacheable.                      |
| No-Cache         | Indicates that the response can be cached, but it will be revalidated by the API before used. |
| No-Store         | Response cannot be cached by the client.                                |
| Must-Revalidate | Indicates the cached response must be revalidated before being used.   |


Custom headers are defined by "x-", but should be avoided as they are deprecated as of June 2012 and the standards became [RFC6648](https://datatracker.ietf.org/doc/html/rfc6648). Its important that developers support a large range of headers as specified in the HTTP specification, and to let clients know which are supported and not.

---- 
ETags are values that can be attached in the response as a digital fingerprint of the resource, and should be allowed to be stored client-side to be used in further requests. 

Clients can use the If-None-Match or If-Match header to determine if the state matches what is stored within the API. 

If-None-Match would tell the API to attempt to match with the ETag stored on the server. If both values match, a 304 Not Modified Status Code should be returned to indicate to the client that the cached version is not stale and can still be used.

If-Match is used to specify what state of resource to access. This allows for clients to specify the exepcted state of a resource before an operation, and only returns data from GET or HEAD requests if the value sent can be matched. 


### Resource Design
Identifying the resources, endpoints and workflows a client will go through using your API can be key to identifying potential pitfalls, as well as visualising the hierarchy the API will comprise of. A resource hierarchy is formed by the services URLs that allow clients to use HTTP Methods like GET, POST, PUT etc. For example:

For example, the URI:
```text
GET https://bpg-library.com/books
```
Will return the the book collection. 

```text
POST https://bpg-library.com/books/
...
{
    "title": "Three Body Problem",
    "author": "Cixin Liu",
    "pubDate": "2008",
    "availability": "Available",

}
    ...
```
This is an example request a client may send that will create a new Book resource. This helps create a hierarchy of resources that can have its routing (the URI) paramterized.

A really solid example of identifying the workflows and resources before implementation is the [How to GET a Cup of Coffee](https://www.infoq.com/articles/webber-rest-workflow/) by Ian Robinson and Jim Webber. It discusses how a workflow begins by viewing it from a customers viewpoint on what they want to achieve, and build up from there. A workflow is a structured set of work that needs to be done to complete a given task. Think of it as splitting up the steps from A to Z, breaking down each step.

For example, in the booking keeping system used in this guide, we can safely expect that clients will want to:
- Create a new Book record
- View Book records
- Create Orders for Customers
- Delete Old Orders

The list could continue. Identifying these workflows *prior* to implementation streamlines the development process, increases understanding of client requirements and minimizes the risk of missing critical functionality.



## API Access Control and Security
Security is by **far** the most important aspect of creating a REST API. Broken access control and securtiy
Broken access control being a leading factor in OWASP top 10.
--Validation, Securing and validating

Keeping components separate from another is another key principle of REST. The API services can be broken down into three, distinct layers, however they may vary from name / quantity depedent on the system. 

-	Presentation Layer 
-	Logic Layer
-	Data Layer


JWTs and API keys should be used as an extra layer of security, making sure that only authorised clients are able to access the API's services. They are typically generated by the service and given to the client securely.

Audit logs of large transactions and users interactions should be kept for moderation.

Store logs of failed logins, failed requests and large transactions. These two logs can be critical in identifying problematic actors and tracking issues within the system.

Data that is coming in, any of it, should be validated against to ensure that is the right type and the right length. Status code 400-Bad Request should be returned as the server cannot process the request.


Locally authenticate each endpoint. Assume that each request has "Least Privilege" from the get-go, meaning that any user's default access level is set as "denied" until given permission. This could be added to the clients headers.

Client data should be sanitised to protect against SQL Injections. Sanitisation is stripping the inputs of any escape characters to stop any sort of malicious code being ran on the system.

Deleted data should be retained for X amount of days before being erased completely. Clients should have the ability to rollback their changes so that data can be recovered promptly.


**Validating against Cross site request forger Rate Limiting - Headers, Services implemented?**


## Common API Implementation Practices
Requests can sometimes take longer than expected to process, in which case the client should have a 202-Accepted Status code returned to show that the request has been accepted, as well as a URI that can be used to show the progress. This is referred to as an Long Running Operation(LRO)


Do not let your API be chatty. When designing your API, keep in mind the 
workflow? Client journey? REsource Consumption



## Exception handling and Error Logging
Error logs should be kept and maintained regularly. They should be in a consistent format, and it should store any malicious

Returning the correct status code to the client is a main factor of maintaining a services reliability. The most common ones that should be returned per HTTP method can be found below:

| HTTP Method | Successful Response (2xx)       | Client Error Response (4xx)         | 
|-------------|---------------------------------|--------------------------------------|
| GET         | 200 OK                          | 404 Not Found                        |
| POST        | 201 Created                     | 400 Bad Request                      | 
| PUT         | 200 OK (or 201 Created)         | 404 Not Found                        |
| DELETE      | 200 OK (or 204 No Content)      | 404 Not Found                        |
| PATCH       | 200 OK (or 204 No Content)      | 404 Not Found                        |

Error messages attached should be meangingful and relevant to the situation, i.e, if a parameter is missing / incorrect, then the message should indicate such.

## Filtering Responses

Pagination splits large collections into "pages", which are chunks of data that can be specified by the client. This should be implemented if resource collections are bound to get large. 

The URI should follow a similar pattern as below:

```text
GET https://**/*collection*?limit=XX&offset=XX
```

Take the below example:

```text
GET https://bpg-library.com/orders?limit=5&offset=8
```
This is telling the endpoint to return a dataset maximum of 5, using the order specified by the offset value of 8. This will return the first 5 books after the 8th record stored.


URI Query parameters should be accepted by the API. URI Structure for performing actions on resource collections should follow the below format:

```
https://.../*collection*/*id*:*action*?*parameters*
```


## Caching and Optimisation
Caching **must** be available for clients using a REST API. Not only is it a principle of the REST architecture, it cuts latency and network bandwidth usage by the API and the client just by sup
porting it. Caching enables clients to store responses from previous requests to minimise the amount of traffic to the API.

- GET Methods can be cached by default.

- Do not allow sensitive data to be cacheable.

### Cache Control

Clide-Side Caching can be done by clients adding request headers to provide the information on caching the resource. It is called "[Cache-Control](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)".

The API should support the Cache Control headers of:

| Header           | Description                                                             |
|------------------|-------------------------------------------------------------------------|
| Max-age          | The maximum time in seconds for how long the cache will exist.          |
| Private          | Indicates that the response is only cacheable on the client’s browser.  |
| Public           | Indicates that the response is publicly cacheable.                      |
| No-Cache         | Indicates that the response can be cached, but it will be revalidated by the API before used. |
| No-Store         | Response cannot be cached by the client.                                |
| Must-Revalidate | Indicates the cached response must be revalidated before being used.   |



### ETags

An ETag, or Entity Tag, is a digital fingerprint of a resource. Returning an ETag value with a GET or HEAD will allow caching of unchanged resources. 






## Versioning


Versioning comes as apart of the natural lifecycle the API will go through. Changes can be introduced that adds or alters existing functionality, and these changes may affect clients current usage. This is referrred to as a "breaking change". 

Versioning must be done carefully to ensure that a clients workflow is not disrupted. For example, if a resource has been renamed, or a method removed, and the client requires it to complete their workload, that is a "breaking change".

Clients must be able to indicate which version of the API they wish to access, and they should do that through one of these methods:
- Versioning through the **URI**.

```text
 GET   https://bpglibrary-official.com/v1/orders/3
 ...
```

- Versioning through **Custom Header Paramters**.

```text
 GET   https://bpglibrary-official.com/orders/3
 Custom-Header: api-version=2
 ...
```

If the client has not entered an incorrect header or has an incorrect API version, a status code of 400 and an apt message should be returned to the client.

As long as the indication of the version is clear, the format and context can be depedent on the developer.


Developers may also have scenarios where as their RESTful API grows, new functionality can be added without disrupting clients workloads. In these scenarios, it is not requied and it would not be worth versioning the API to avoid potential confusion.