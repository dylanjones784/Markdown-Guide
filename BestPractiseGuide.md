# API Design Theories and Background Information

The aim of this guide is to bring together all the various design and implementation strategies used across the industry into one, singular reference. It brings common issues and solutions, practices and standards employed by the likes of Google, Microsoft etc and provides a one-stop-shop for potential REST API Developers. 

REST will be briefly discussed and how we can ensure our APIs adhere to those principles set by Roy Fielding, as well as the how we will utilise the HTTP Specification.


## What’s REST?
Representational State Transfer is a network architectural approach to the communication of hypermedia through computer systems across the web. It is the most common type of API used on the internet to date, and it is independent of any underlying protocols and not restricted to usage just through HTTP calls. 

We define resources as the nouns, and we can expose them to be manipulated with a small number of methods that are referred to as the verbs of REST. 

- These resources are manipulated by having their representations sent to the client through well-defined operations. For HTTP, this would be through the standard methods of GET, PUT, POST, PATCH & DELETE.

- The server will communicate with clients through various content types that are agreed upon by the client and the server. For example, if the above example was used in a GET request, a JSON response body may look like below:

A RESTful API contains resources, which can be representations of data, objects or a service that is accessible by the user.

- Every resource has a unique identifier. This is called the Uniform Resource Identifier.


```text
https://bpg-library.com/books/4
```

Which would return a suitable content type back to the user representing the data. In this instance, its JSON.
```json
{
    "bookID": 3,
    "title": "Almighty Prison",
    "author": "Rath Scollington",
    "pubDate": "1993-04-16",
    "availability": "Unavailable",
    ...
}
```

- RESTful API Client-Server communications are classed as *stateless*. Each request is treated as independent to any other, and it must contain the entirety of the information required for the request to be processed. There are no client sessions, and the server will never rely on previous requests from clients. Having each request being atomic in nature allows for any server to handle any request as there are no session-related dependencies, as well as making the API less complex.

- Responses should be labeled for caching or not appropriately. If the response is cacheable, the client should then be able to reuse that response data later for a specific length of time.

- RESTful APIs should have each component organised into layers to encourage modularity and a separation of concerns between each aspect of the API.


- Clients should be able to navigate throughout the API without prior knowledge of the applications endpoints, and that with a single URI the application should dynamically attach relevant resources via hyperlinks. This is called *Hypermedia as the Engine of Application State(HATEOAS).*


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
  }
}
```
Each link contains self-descriptive messages on what can be done, how, and where the client needs to go for it.

We can measure an API's adherence to the REST Principles by using the [Richardson Maturity Model for Web APIs](https://martinfowler.com/articles/richardsonMaturityModel.html). Created in 2008 and designed to break down the main elements of the REST approach into three simple steps.

- Level 0 – Single URI that handles all operations of the API.
- Level 1 – Resources have been identified and are accessible through URIs.
- Level 2 – HTTP methods and verbs are used.
- Level 3 – Discoverability of functionality within the API through hypermedia controls.

Level 3 would correlate to having a truly RESTful API that encompasses all of Fielding’s original definition, however typical Web APIs fall into the Level 2 category. To find out further on Level 3 and the usage of Hypermedia, please visit [this section](#resource-discovery).



# HTTP Specifciation
HTTP is an established set of standards that facilitates communication between computer systems. It is one of the core building blocks of the Internet. The official stanadrds for HTTP is the [RFC7231](https://datatracker.ietf.org/doc/html/rfc7231).

REST is not reliant upon any protocol, but we make use of the HTTP application protocols as it is the most popular platform for REST API implementation. 

HTTP enables client applications to interact with your API seamlessly, and provide the protocols and standards for the: 
- URIs (how the clients interact with resources).
- Requests.
- Responses.

These shall be discussed further throughout the document.

## HTTP Status Codes
HTTP Status Codes are indicators on the state of a HTTP request that has been sent to the API. They tell the client whether or not their request has been accepted, failed, not authenticated and more. It is not required to understand and know all the accepted Status Codes, however they can be split up into 5 distinct classes:

| Value | Indicator           | Meaning                                                           |
|-------|---------------------|-------------------------------------------------------------------|
| 1xxx  | Informational       | Request was received and is being processed.                      |
| 2xxx  | Request Success     | Request was understood and executed successfully.                 |
| 3xxx  | Request Redirection | Original method has been replaced or moved, further action needed.|
| 4xxx  | Client Error        | The request was badly formatted or cannot be executed.            |
| 5xxx  | Server Error        | Server failed to fulfill the valid request.                       |


It is recommended for developers to have a dedicated service or class in the server layer that is dedicated to returning generic return messages alongside the correct Status Code. 

Correct HTTP Status Codes must be used for all responses by the API to indicate the outcome of a given request. Below is a table of what developers should return for each of the HTTP operations:



| Method | Description                                | Response Status Code                    |
|--------|--------------------------------------------|-----------------------------------------|
| PATCH  | Create/Modify the resource with JSON       | 200-OK, 201-Created                |
| PUT    | Create/Replace the whole resource          | 200-OK, 201-Created                     |
| POST   | Create new resource                           | 201-Created with URL of created resource |
| POST   | Action                                     | 200-OK                                   |
| GET    | Read resource or collection                | 200-OK                                   |
| DELETE | Delete resource                       | 204-No Content; avoid 404-Not Found     |


If a user is forbidden from access, i.e not authorised, a 403-Forbidden should be returend.


For requested resources who have had their URL changed, the Status Code 301-Moved Permanently / 302- Found (Moved Temporarily) should be returned.

To find more on Status Codes, visit [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-6).



## Content Types
Content Types are the agreed upon format between a client and server on how data should be returned. For example, a GET request may return data in a JSON or XML format. The request will contain a header that specifies the formats that are acceptable, this is referred to as “media types”.

RESTful APIs should include a list of accepted media types, that can be requested by the client for them to understand what media types are supported by the resources.

Requests that are received with a content type that is not supported by the API, or is invalid, should be rejected from the system with 406 (“Not Acceptable”) or 415 (“Refusal to accept request”).

Some of the more popular formats of content types that should be supported are “text/plain“, “application/xml“, “text/html“, “application/json“, “image/gif“, and “image/jpeg“. All of these are indicators of data that can be communicated with.


For more information on the full current best standard for Media Type Specifications, visit [RFC 6838](https://www.rfc-editor.org/rfc/rfc6838).


# Resource-Oriented Design
Resource Oriented APIs are modelled as a “resource hierarchy” where each endpoint is a simple resource, or a collection of resources. Endpoints, what the client interacts with, mirrors distinct resource(s) and forms a cohesive hierarchy of resources. 

For example, the URI:

```text
GET https://bpg-library.com/books
```

Will return the the book collection, and:

```text
GET https://bpg-library.com/books/24
```

Will return the book with an ID of 24. This helps create a hierarchy of resources that can have its routing (the URI) paramterized.

Principles to focus on when designing a resource-oriented API are:
1. The expected resources the API can provide.
2. The relationships between resources. 
3. The schema of each resource (i.e, the fields each resource represents).
4. The HTTP operations that can be executed on each resource.  

Developers should initialise their design process by identifying the business entities and endpoints that will be exposed to the client prior to development. Having your design dependent on the resources you expect to expose allows for a simplistic interface that can adapt rapidly to evolving functionality.

For example, in a bookkeeping system, the main entities that may be exposed could be Authors, Books and Orders. 

To create a new Book, a HTTP POST request that contains the book information would be sent to our API. The following HTTP response would then indicate if the book was created, or if the process had failed.

| Example                                                   | Description |
| --------------------------------------------------------- | ----------- |
| [https://bpg-library.com/books](https://bpg-library.com/books)         | Good        |
| [https://bpg-library.com/create-book](https://bpg-library.com/create-book) | Avoid    |

It is important to note that a resource doesn’t have to be a single piece of data. For example, the book resource might be comprised of multiple tables from a database, but the client would only see the returned object as a single entity. 

Developers should not, when designing their API, mimic their database relationships for it is the purpose of REST to model the entities and operations that the application can perform on those entities. The inner workings of the system should not be exposed to clients.


## Resource Hierarchy
Resource-oriented APIs are modelled as a resource hierarchy, where each node (resource endpoint) is either a simple resource or a collection resource. 

Developers should adopt a consistent naming convention for URIs that follows the noun / verb pattern.

- A collection resource is a list of resources that are stored, i.e., “books” and should be named after the plural version of the noun.
- A simple resource would be a singular resource, i.e., “member”.
- An HTTP GET request would be the verb.
- That they follow clear paths, i.e there is no confusion between resources.


HTTP GET requests to this collection URI would retrieve a list of book objects that are available in the database. Each book in that collection would have its own unique URI, that the client can use HTTP GET request on to return the details of that book.  

```text
GET https://bpg-library.com/books 
```

The returning body would like the example below.

```json
[
  {
    "bookID": 24,
    "title": "Big Book of Knowledge",
    "author": "Peter Jackson",
    "pubDate": "2012-04-16",
    "availability": "Available",
    "links": {
      "self": "bpg-library.com/books/24"
    }
  },
  {
    "bookID": 57,
    "title": "The Art of Programming",
    "author": "Donald Knuth",
    "pubDate": "1997-07-01",
    "availability": "Available",
    "links": {
      "self": "bpg-library.com/books/57"
    }
  },
  {
    "bookID": 102,
    "title": "To Kill a Mockingbird",
    "author": "Harper Lee",
    "pubDate": "1960-07-11",
    "availability": "Unavailable",
    "links": {
      "self": "bpg-library.com/books/102"
    }
  }
  ...
]

```

The main API service is “bpg-library.com” and features two collection URIs, books and members. /books is the path to the book collection, and /books/10 would retrieve the book with an ID of 10. A RESTful API should also support parametrized URI paths, such as the books//{id} path and should behave similar to a network file path. Having the resources set in a hierarchical nature leads to a logical and easily understandable naming convention. 



A pitfall the example above can face is the over-expression of relationships between the different resources that are exposed. For example, each book has an author, and a client could navigate to a specific author via the path /books/{id}/author/, but it could also go in the other direction and the association between the author and each of their books could be retrieved via the path /authors/{name}. 


Building your API around these associations will quickly lead to confusion and become far larger than the scope originally required, to which the REST solution would be to use HATEOAS to enhance the discoverability of the resources in the API and how they can be manipulated. It is better to keep it simple across the system rather than unnecessary complexity.


## Resource Discovery 
Resource discoverability is at the heart of our design principles for creating REST APIs. This is primarily done with HATEOAS, or Hypermedia as the Engine of Application State. It is an architectural constraint of REST and is unique from other network architectures. We can use HATEOAS to enable resource and service discoverability for our clients without them requiring prior knowledge on the API.

The term “hypermedia” refers to content that contains any link to any other forms of media, mainly text, images, or movies. 

Including Hypermedia links in the response shows clients related resources dynamically. Each HTTP GET request should return the information to find the resources directly related to the requested object. We do this through using hyperlinks in the response, and they provide enough information for the client to describe what operations can be done on each of the related resources.


For example, if a client makes a request to retrieve the books with the ID of 24, the response will contain extra links that contains the information necessary to discover directly related resources and services.


```json
	200 OK
	Content-Type: application/json; charset…
	Content-Length:…
{
    "bookID": 24,
    "title": "Big Book of Knowledge",
    "author": "Peter Jackson",
    "pubDate": "2012-04-16",
    "availability": "Available",
    "links": [
        {
            "rel": "self",
            "href": "https://bpg-library.com/books/24",
            "action": "GET",
            "types": ["text/xml", "application/json"]
        },
        {
            "rel": "self",
            "href": "https://bpg-library.com/books/24",
            "action": "PUT",
            "types": ["application/x-www-form-urlencoded"]
        },
        {
            "rel": "self",
            "href": "https://bpg-library.com/books/24",
            "action": "DELETE",
            "types": []
        },
        ...
    ]
}

```

Hypermedia links are required to be in an array of “links” in the response body. This can be indicated by "links" or "_links".

Devlopers should use the [RFC8288](https://www.rfc-editor.org/rfc/rfc8288) standard as a framework for building links that define the relationships of a given resource. It states that hypermedia links must contain the following properties:

1. Target URI – Represented by the href attribute.
2. Link Relation – The relation between the source and the target resource.
3. Attribute Type – HTTP method.

Access Control with HATEOAS should be considered and should be the main factor for developers to consider when de-coupling the admin and client server interactions from another. It may not be relevant to provide HATEOAS references for every request, whether that be over-engineering for the problem or due to security concerns. 

The hypermedia links may change as the resource state is altered, i.e there is data deleted or updated,  and this is what it means by being the "engine of application state".

As we provide the hypermedia for related content in a request, it is recommended that we do a list of things to secure and limit access to higher level data access. HATEOAS should not be used for Admin services, nor should hypermedia links expose more of the system than necessary.

It should be noted that many scenarios may not allow for this level of discoverability, perhaps due to company policy or data security. If that is the case, HATEOAS is not necessarily needed and a RESTful API that reaches Level 2 in the Richardson Maturity Model is still a solid service.



# Implementing the API


This section of the guide focuses on the collected best practices and patterns for implementing a RESTful Web API. 



## Access Control and Security

An integral aspect of RESTful APIs is the concept of a layered system architecture, where each component of the API cannot see beyond its own layer. Each layer may have its own purpose but will all work together to fulfil the client’s request.  Developers should split their API into four distinct layers, examples of which can be found below.
-	Presentation 
-	Logic 
-	Data 

### Why have three layers?


Beyond keeping in-line with REST principles of not allowing clients to see further than the component they are interacting with, adopting a three-tier approach ensures that the API is cleanly organised with each functionality identified by the component it resides in, as well as greatly supporting the scalability of the API if further functionality is to be added. 


There are some drawbacks to this, mainly being the increased application complexity due to having multiple layers of a system rather than a monolithic design & difficult to manage on a large scale, however the positives of having a system that is modular and secure outweigh the negatives.


It is important to note that some layers may perform multi-uses, such as one layer handling both the business logic and data access of the API, or that requests are validated within the presentation layer prior to be sent to the server. Developers should keep these layers as simplistic as possible, allowing the API to remain simplistic in design and easily adaptable to future functionality being added.


The following sections will cover the best practices design patterns one should follow when creating, configuring and validating the access control for an API. It is a collation of security practices from Microsoft, Google  and The Open Worldwide Application Security Project Top 10 Web Application Security Risks & the API Security Top 10. To delve deeper into the OWASP Top 10, which features an extensive list of potential security risks and ways to mitigate the effects, visit the [OWASP API Security Project](https://owasp.org/www-project-top-ten/) website.


## Securing and Verifying Communication Channels
Communication channels are how the API receives and sends data to the client and with HTTPS, data in transit is secure with the Transport Layer Security (TLS) protocol.

The TLS protocol was created to facilitate privacy and data security for communications between systems on the internet. It is advised that developers do not use anything other than HTTPS if creating a HTTP RESTful API, and to adopt further methods of securing client data.


TLS will encrypt the data, authenticate the request to ensure that the client is legitimate, and that the data has not been tempered with in transit.

## Sanitising and Validating Inputs

On the Presentation Layers that allow for users to enter their own data for submission to the API, for example a sign-up form or to change an accounts detail, the front-end must be sanitised and validated so that no Injection styled attack can be used. Sanitisation is the practice of taking a users input and validating it so that any unsafe characters, i.e. escape characters in SQL, are removed before being transmitted.

 ```text
POST https://bpg-library.com/books/4
...
{
    "title": "The Catcher in the Rye",
    "author": "J.D. Salinger",
    "pubDate": "1951-07-16",
    "availability": "Available'; DROP TABLE Books; --"
}
```

The "availability" field has, initially, a valid value, however with the use of the escape character " ' ", it can be manipulated to run malicious SQL code. In this example, it would delete the Books table. Sanitisation of the data would remove these unwanted characters before the request reaches the server.

Data that is contained in the clients request should be mapped to an object that exists within the backend of the API. This allows for a safe and secure conversion of data for the server to deal with, in addition to sending back a bad request Status Code if a field does not match.

Avoid using hardcoded request.getParameters to place data into queries for insertion into a database. One way of doing this is to map data to an identical model of the object so that another layer of validation is ensured. 

For example, the Book class is set out as such in this examples C#.
```code
public class Book
{
    public int BookID { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public DateTime PubDate { get; set; }
    public string Availability { get; set; }
}
```

Everytime a Book object is received in a request, the request body parameters that contains the JSON should each have individual value assigned to a new instance of the Book class. This allows for a clear and concise way of handling incoming data, as well as being heavily reusable.

# Configuring Access Control
Broken or misconfigured access control on APIs were in the top 6 of both the OWASP Web API & the API Security Risks in 2021 and 2023 respectively. The root causes of these issues stemmed from:

- Broken access control.

- Insecure design practices.

- Lack of identification and authentication on API endpoints.

- Request bodies being modified prior to reaching the API with incorrect or malicious data.

- Failure to correctly log and monitor events, such as errors and authentication failures that have occurred.

- Outdated components still being active.

- No sanitation on data or procedures in place to mitigate data integrity failing.

These are issues that, if properly addressed at the start, are easily identifiable and fixable by employing the correct design patterns and methodologies that are stated below.

## Broken Access Control
Broken Access Control had the [most occurrences](https://owasp.org/Top10/) in the dataset collected by OWASP with over 318 thousand counts. 

Broken access control could allow unathenticated users access to:
- Restricted resources and/or information.
- The ability to modify or destruct data.
- To peform business functions that are outside of a user’s privilege. 


Access Control checks can be broken and bypassed by modifying the URL, internal application state or by using an attack tool that can modify a requests body. This can then allow bad actors to gain access to parts of the system that is typically unreachable by the user, such as editing or viewing someone else’s account, by elevating their own privilege to that as an admin when logged in as a user, or acting as a user whilst not being logged in. 

An example from the [OWASP Top 10 Security Risks for APIs](https://owasp.org/Top10/A01_2021-Broken_Access_Control/#example-attack-scenarios) shows an attacker that can simply modify the browsers paramters in the query to send whatever account value they want.

```text
 https://example.com/app/accountInfo?acct=notmyacct
```

If this is not properly validated, the attacker could gain access to any users account.

To put it simply, if a client can simple force itself through into the system, or an unauthenticated user can access restricted resolurces, this is a serious flaw.

-----
To prevent such attacks from occurring, developers should adopt the [Least Privilege](https://www.cloudflare.com/en-gb/learning/access-management/principle-of-least-privilege/)
pattern, where a user’s default access level to any resource is set as “denied” until given permission explicit and that except for public resources, denying requests should be default. Privilege should not be granted based on purely a single condition, i.e. a Boolean flag attached to the request, and should instead be set by a combination of conditions based on resource type.



Insecure Design is the fourth most identified security concern in the [OWASP Top 10 Security Risks for APIs](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/). 

An [example](https://owasp.org/Top10/A04_2021-Insecure_Design/#example-attack-scenarios) from the OWASP guide shows how Insecure Design could be abused by clients: 

```text
Scenario #2: A cinema chain allows group booking discounts and has a maximum of fifteen attendees before requiring a deposit. Attackers could threat model this flow and test if they could book six hundred seats and all cinemas at once in a few requests, causing a massive loss of income.
```

The main methods that help prevent an abuse of insecure design practice are:

- Clients should be locally authenticated on each request to lower latency and reduce the coupling between services. 

- Each Rest CALL is stateless and should check whether the caller is authorised to perform said actiod on that resource.

- Enforce [Rate Limiting](#limiting-the-rate-of-requesting-rate-limiting) on all incoming requests.


---

Endpoints that are exposed to handle object identifiers, such as any endpoint that receives the ID of an object and performs any actions on said object, should implement access and object-level authorisation checks. These should validate whether the user who is logged in has permissions to perform the action on the object. When this isn’t done, it typically leads to unauthorised information discloser and the potential modification and destruction of all data.



It is recommended that, rather than rely on an ID of an object, to use a [GUID](https://guid.one/guid), or the Globally Unique Identifier. This is a 128-bit string that represents an extra ID for an object and can be used to identify information or objects.


Access Control can be furthered by having an allowlist of permitted HTTP methods, especially for user groups and levels of authentication. Requests that don’t match the allowlist should be responded with a HTTP response code 405-Method Not Allowed.


## Tokens and API Keys


Public REST APIs that have no access control run the major risk of having its resources manipulated and farmed, leading to excessive bills for bandwidth and computing cycles. To avoid attacks such as Denial of Service, a lead cause of such effects, access tokens such as JSON Web Token (JWT), or API Keys, can be attached to a request so that the REST service can authenticate the API against either an identify provider or server storage.

### Whats a JWT?

A JSON Web Token is a data structure that contains a set of claims that are used for access control decisions. Cryptographic should be attached to the token so that its integrity is protected. The official standard for a JWT is the [RFC7519](https://www.rfc-editor.org/rfc/rfc7519) 

Developers could use Message Authentication Code to protect the integrity of the token, however if it is used then it should be kept in mind that any service that validate the JWTs can also be used to create new ones with the same key, meaning that all services that use the same key must trust each other mutually. Consequently, if one service is comprised, the rest are.

JSON Web Tokens carry information to identify the client and are encoded to ensure it's safety.

An example of a JWT from the [JWT.IO](https://jwt.io/#debugger) shows that the structure is as followed:

```text
[Base64(HEADER)].[Base64(PAYLOAD)].[Base64(SIGNATURE)]


eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.
TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```


There are a *lot* of caveats to JWTs, and for a more in-depth analysis on their security issues, visit the [OWASP JWT Cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html).

### Whats an API Key?
Primarily used as an authentication to access an APIs service, API keys are used to ensure that the user making the request is verified within the system and that the API key is valid and verified within the system. 

-	API keys must be used in every request sent by the user.
-	They must not be publicly accessible.
-	Violation of the user agreement or through unauthorised access of services would result in the API key to be revoked.

API keys should only be used within private environments and should not be used or trusted with third-party clients to access your API unless strictly necessary. 

**Remember, anyone with an API key that is not updated or changed regularly can have access to the system.**

Timestamps could be added to the header of the request so that the server can compare the current timestamp to the request and can only accept requests if it is within a set timeframe, i.e. 30 seconds. 


## Data Retention
Data retention is a critical aspect of any services reliability, and not having safety measures in place that ensure clients cannot mistakenly or incorrectly delete data should be avoided at all cost. Without having data retention in the REST API, it increases the chances that clients who make mistakes cannot go back on them.

APIs should adopt a data retention policy, and a suitable "undelete" function which can revert a previous delete request. There are some guidelines that a developer should follow for creating these policies: 

- Personal data should be stored in-line with country-specific laws. In the UK, GDPR requires personal data to be deleted as soon as it is no longer needed. 
- For metadata or important information on users, a policy of 30-days minimum for data retention.
- For whole database tables, large resources or BLOBS, developers should adopt a 7-day minimum retention period.

During the data retention window, users must be able to undelete their data without any data loss. 


# Cross-Site Request Forgery

Requests can be forged by malicious actors who have gained access to an authenticated user’s web browser. This type of access could be gained by tricking the user through a malicious website, email, or program so that unwanted actions can be performed on their system. 

The higher level of privilege the victim has, the more damage that can be done and the system will be unable to distinguish between legitimate, authorised requests and forged ones. Cross-Site Scripting (XSS) can defeat all mitigation techniques for CSRF as it will execute within the same origin as the site being exploited. 


Luckily, CSRF attacks can only work when proper authentication is not used and can only exploit functionality exposed by the API and the users privilege. 

To defend from CSRF attacks, most tech frameworks will have built-in CSRF protection [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#use-built-in-or-existing-csrf-implementations-for-csrf-protection), but if not, CSRF Tokens can be used to ensure that all state-changing requests need to be validated on the backend [OWASP XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html).

As we are focused on HTTP RESTful APIs, we want to ensure that all communications are stateless between the client and the user. The Double-Submit Cookie Pattern [OWASP CSRF Prevention Cheat Sheet - Double Submit Cookie Pattern](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#alternative-using-a-double-submit-cookie-pattern) uses a secret key that is known only to the server, ensuring that the attacker cannot create and inject their own (similar to how JWTs can be forged).



# Handling Requests Properly
The below sections cover certain practices and standards that have been ascertained through my research. The main discussion points are: 

- Adhering to REST and HTTP Principles.
- Handling different types of requests.
- Solutions or patterns to solve common problems faced by RESTful APIs.

## Idempotent Requests
There are certain HTTP methods that, if performed numerous times on the same resource, should result in that resource being in the same state it was prior. HTTP Methods like GET, PUT, DELETE and HEAD are all methods that are idempotent.

For example, sending multiple GET requests to the same URI should result in the same object being retrieved, or a DELETE request to return either 204 for the first request, and 404 for subsequent requests.


```text
GET https://bpglibrary-official.com/books


DELETE https://bpglibrary-official.com/books/4
```


These two examples are requests that are *idempotent*. Using these requests consecutively would not have any side-effects on the system, as DELETE can only be executed once before a 204-No Content Status Code is returned.



POST would be considered not idempotent, for it could cause partial side effects within the system beyond creating a resource. For example, an Order being created could cause some functionality to fire off, and that changes a value elsewhere.

PATCH requests are idempotent, but do require some thought behind the design of such a request to ensure that there are no side-effects within the system.

Take a PATCH request for example. The client wants to update the 4th Books Author field, and would use a request formatted similar to the one below:

```text
PATCH https://bpglibrary-official.com/books/4
Content-Type: application/json...
{
  "author": "Dylan Jones"
}
```
This would result in the 4th Book having its Author value changed to "Dylan Jones". If this same request repeatedly with the same body, the Author field would remain unchanged.

RESTful APIs that utilise HTTP should ensure that idempotent methods cause no side effects within the system, otherwise it is not REST-adherant.


## Long Running Operations

Sometimes a user’s request can take longer than expected to handle and process correctly, to which the API may return a response to the client that states that the operation is in process. The API should return a 202 Accepted Status Code to indicate the request has been received and initiate a separate operation to perform the work needed. 

The response must also provide a method of monitoring the progress of the request, this would be in the form of another endpoint or the resource itself.

This decouples the long-running process from the initial request and allows clients to retrieve the results when wanted after execution.


## Ensuring Stateless Requests
Stateless Requests are when each request must contain all the information required to complete that request, each request is isolated from another and the server will not retain any context between consecutive requests. 

This means that each request will contain all the information necessary, and that the system shouldn’t have to search further than the requests body and header tags to authorise the action the client wants to perform.

A request which does not contain this information should be responded with a 401-Unauthorised, including a message on what the user requires to be authenticated to access the resource.

If the client is attempting to access a restricted resource or operation, the request must contain the required information to authenticate their access level. This could be done with a [JWT or an API key](#tokens-and-api-keys).

To demonstrate a "Stateless" request, take the below example:


```text
POST https://bpglibrary-official.com/v1/books
Authorization: Bearer...
Content-Type: application/json
{
  "title": "Dylans New Book"...
}
```

The request contains a valid URI & Content Type, an accepted HTTP method, relevant Authorisation, the correct version and the body of data that is to be sent to the API.

This is what is meant by **stateless**, that each request is able to be processed without reliance on any prior.


## Combatting Excessive Resource Consumption and Chatty APIs

An API is more than just the resources it exposes and the operations it can execute on them. Every API request requires network bandwidth to be sent and responded to; CPU, memory and storage are required to actually do the processes and store all the data. Some APIs may even make use of external, third-party APIs that handle specific functionality, such as sending emails, texts or retrieving a set of data that only exists outside the main API. 

These are all the things that make up the API itself, and it is crucial for us, as developers, to address the potential issues that can come with excessive resource consumption and the over-exposure of endpoints. We want to ensure that our endpoints are meaningful and provide full context, whilst also limiting potential bad clients interactions with the client through safety checks and authentication.

### What are Chatty APIs? 

Chatty APIs are APIs which expose a large number of small resources, requiring the client to make multiple requests to get all the data they need. We want to avoid these situations, ensuring that the system is not slowed down by numerous I/O operations.

We can identify a "chatty" API by the following example:


```text

    [HttpGet("{bookId}/title")]
    public IActionResult GetBookTitle(int bookId)
    {
        ...
    }

    [HttpGet("{bookId}/pubdate")]
    public IActionResult GetBookPubDate(int bookId)
    {
        ...
    }

```

The bookkeeping system has an endpoint to retrieve a books Author, Title, ISBN, and Publish Date. The client have to make a request to be accepted like below to return the chosen books data.



This type of issue can be abused, and if there is a lack of strategies to mitigate the effect of this issue being abused, it could critically cost the API in both down time and monetary value.  

It could also cause a denial of service for other clients due to resource starvation.



### Limiting the Rate of Requesting (Rate Limiting)

An API that does not limit client interactions or resource consumption can face bad clients crafting their requests in such a way that can overload the system, impacting the performance and the responsiveness. A specially crafted API request could also expose system functionality, leading to authentication breaking and data leakage.

Rate limiting is a simple way of ensuring clients cannot make too many requests at once, or within a certain period on a given resource / operation. Excessive resource consumption is when there are **no** safeguards in place, and the system allows for an unlimited amount of requests to be sent to a given resource.

Take, for example, the request below:


```text
GET https://bpg-library.com/v1/orders/
...
max_results: 50
```

Here, the client is requesting to retrieve a maximum of 50 Order records. If this value is not validated against, the client could alter the request to look like:

```text
GET https://bpg-library.com/v1/orders/
...
max_results: 1000
```

This request abuses the lack of rate limiting, and allows clients to retrieve excessive numbers of records from the API.


To combat such avenues of exploitation on your services, there are methods and patterns that developers can consider:

-	Locally authenticating each endpoint to ensure that the client can access the resource or operation they have requested. 

-	Request Header contents such as max_return and page_size parameter enforced. Requests MUST have these, and a hard validation on how much can be set for each.

-	Defining a maximum size of data on incoming parameters and payloads. Max length for strings, number of elements in an array / maximum upload file size must be validated.

-	Using HATEOAS to lower the amount of calls a client must make to discover the functionality for a given resource.

-	Having a finite number of times that requests can be sent within a timeframe before a 405-Method Not Allowed Status Code is sent.


## Custom Methods
Resource-oriented design RESTful APIs can express further actions one can do on a resource beyond standard HTTP methods. Custom methods typically align with the intent of the method itself and should only be used for functionality that cannot be easily expressed via standard HTTP methods.

Types of functionalities could consist of performing a task, to undelete a resource, to cancel the last or outstanding operation (i.e., a request that is currently being processed) or to batch get a collection of multiple resources.


The [Google API Design Guide](https://cloud.google.com/apis?hl=en) defines Custom Methods to have the following guidelines to be applied when choosing the HTTP mapping of a Custom Method:

1.	Custom methods should use HTTP post for it has the most flexible semantics, accommodating for a wider range of input data and actions that can be done. 

2.	Custom methods should not use HTTP PATCH. This is to avoid potential confusion surrounding the semantics of the HTTP verb and its usage in the custom method.

3.	Custom methods that use HTTP GET should be idempotent and have no side effects. 

4.	The name of the resource or collection that the custom method is related to should be explicitly map to the URL.

5.	URL path must end in a suffix consisting of a colon followed by the custom method verb.

6.	If the HTTP verb used for the custom method allows for a request body to be attached, the configuration of the custom method must use the “ body: “*” “ clause and all remaining message fields shall map to the HTTP request body.

7.	If the HTTP verb used for the custom method does not accept an HTTP request body, i.e. a GET or DELETE, the HTTP configuration must not use a request body at all, and all other request messages shall map to the URL query parameters.


For example, below is an URI to a custom method that would set the Book with an ID of 3 as "Rented"

```text 
https://bpg-library.com/v1/books/3:rent
```

Or, if we wanted to remove the Order with the ID of 3, we can call the below URI to use the "remove" custom method.

```text 
https://bpg-library.com/v1/orders/3:remove
```

Before using a custom method to achieve functionality, developers should ensure that using a custom method is needed instead of standard methods. For example, if the client wants to query a resource with different parameters, a standard GET would do fine, or to change a resources property a PUT would suffice. Only when it is necessary should a custom method be use.


## Exception Handling and Error Logging
Correctly implementing exception handling and error logging means that detecting, identifying, and responding to breaches becomes far easier. In addition to this, a solid error-catching system will return meaningful messages to clients which have incorrectly formatted their request, including the correct HTTP Status Code and a message that can guide the client to fixing their problem. 

The last thing a developer wants is to find out way after the fact that their service has been breached and have no way of fixing it due to lack of information on the issue.

An API which does not correctly handle exceptions or log their errors may contain these risks: 
-	Failure to audit high-value events, such as logins, failed attempts and large transactions.
-	Warnings and errors generate unclear log messages, increasing the difficulty of identifying the root issue.

- Logs stored locally, following no format.

- Client confusion when a badly formatted request is sent and no meaningful message / Status Code is returned.


Developers should follow these patterns when developing their API's exception and error handling:

- All logins, access control and server-side input validations to be logged with sufficient user context (i.e., an identifiable user detail) so that suspicious or malicious accounts can be identified and analysed forensically.
-	Ensure that all error logs that are generated are in a consistent format.

-	An audit trail of a user’s interactions and commands they have executed within the system should be kept and only allowed access by admin users.

-	In the event of a breach or illicit conduct, sufficient incident response or recover plans should be in place to help mitigate the aftereffects. See the [National Standards and Technology (NIST) 800-61r2](https://csrc.nist.gov/pubs/sp/800/61/r2/final) or later for more.

-	Use a standardised error response format.

-	Usage of [HTTP Status Codes](#http-status-codes).

Developers should not, under any circumstance, alert the user by making the logging and alerting events visible to the user as it will leave leak vulnerable information.



## Filtering and Paginating Data
Resource collection requests can be exhaustive on the API, and if not handled correctly can cause resource starvation for other clients. Imagine having an API that contains a large set of data on books, and every bookstore that uses that API requires that collection to perform their day-to-day business. 

Without some sort of filtering technique in place, the API would require clients to manage their own filter and query system on the returned large collection of data.

We don’t want the server to be negatively affected by network traffic and increased latency. It is in our best interest to enable clients to filter down responses into smaller chunks.


### Splitting Large Collections with Pagination
A solid RESTful API will implement a technique called Pagination. It is a technique used to segment a response into smaller, manageable chunks, or “pages”, and it significantly enhances the performance of the API.

The REST API should support query strings that allows clients to filter and refine requests so that the fetched data can be delivered in a more manageable manner. There are parameters that the user can add to their request that indicates to the API that they want to paginate the results.

Lets take this URI for example.
```text
GET https://bpg-library.com/orders
```

This URI could conceivably return a large amount of data, and we are essentially forcing the client to deal with this and then comb over the data once its returned. Instead, the URI could be altered to such:

```text
GET https://bpg-library.com/orders?limit=10&offset=20
```

This is telling the endpoint to return a dataset maximum of 10, using the order specified by the offset value of 20. This will return the first 10 books after the 20th record stored.


Developers must ensure that the paginated responses contain certain metadata to tell the user what page they are on, how many pages are left and how many records are returned per page. 



| Field             | Type      | Description                                       |
|-------------------|-----------|---------------------------------------------------|
| Page              | Integer   | Page number of results being returned.            |
| Page Size         | Integer   | How many objects are returned per page.           |
| Total Pages       | Integer   | The number of pages that are available to the client. |
| Total Records     | Integer   | How many objects are available in total, i.e., the total resources within that collection. |
| Navigation Links  | Hyperlinks| Links enabling navigation between previous and subsequent pages. |



A paginated response would look like this:

```JSON
{
  "page": 1,
  "page_size": 5,
  "total_pages": 2,
  "total_records":10,
  "orders": [
    {"id": 1, ...},
    {"id": 2, ...},
    {"id": 3, ...},
    {"id": 4, ...},
    {"id": 5, ...},
    "..."
  ],
  "navigation_links": {
    "next": "bpg-library.com/orders?page=2&page_size=5"
  }
}


```

### Sorting and Limiting Fields

Clients may want specific resources that match certain fields, or may wish to return a specific value from a dataset.

The API should support client filtering by taking a relevant field name as a sort parameter.

For example, a client could send a URI like the one below to sort the returned books by their Book ID.

```
GET https://bpg-library.com/books?sort=BookId
```

The URI should follow a format similar to below:

```
https://.../*collection*/*id*:*action*?*parameters*
```

For example:
```
GET https://bpg-library.com/books?sort=BookId
```
Returning only specific values is an extension of how sorting would be implemented, and we can alter it slightly by making it accept a comma-separated list preceded by a command to indicate what values are wanted.

For example, let’s say the bookkeepers want to find all books created by George R.R Martin within our /books collection, but only want the ID and title returned.

```
GET https://bpg-library.com/books?fields=BookID,Title
```


To find more information on this topic, especially the legality side, visit [this link](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/data-protection-principles/a-guide-to-the-data-protection-principles/the-principles/storage-limitation/#:~:text=The%20UK%20GDPR%20does%20not%20dictate%20how%20long%20you%20should,how%20long%20you%20need%20it).



## Caching and Optimisation 

Caching involves storing copies of responses from previous requests, minimising the amount of traffic that flows through the API. Cached data can be stored either locally on the client, or on an intermediary cache server, such as a proxy cache.

By including caching in the API, it optimises the network usage and improves the overall quality of the service for it means there is less time taken for the client to achieve their goal. It reduces bandwidth due not needing to retrieve data from the server, reduces the load on servers and decreases the chance of network failures.

High performant RESTful APIs must contain caching capabilities. Developers should take the following points into account when implementing caching and improving optimisation.

- GETS must be cacheable by default, unless the client specifies otherwise, or a certain condition has been met. 

- Sensitive Data, such as user profile details, passwords, admin details, should **NOT** be cacheable. 


### Supporting Client-Side Caching
Client-side caching is done through the request headers by the client attaching various headers to the request. They are essential to the process, and they provide the necessary information for clients and other components about whether a resource is cacheable. These headers are called Cache-Control.

The common Cache-Control headers that should be supported  are:

| Header           | Description                                                             |
|------------------|-------------------------------------------------------------------------|
| Max-age          | The maximum time in seconds for how long the cache will exist.          |
| Private          | Indicates that the response is only cacheable on the client’s browser.  |
| Public           | Indicates that the response is publicly cacheable.                      |
| No-Cache         | Indicates that the response can be cached, but it will be revalidated by the API before used. |
| No-Store         | Response cannot be cached by the client.                                |
| Must-Revalidate | Indicates the cached response must be revalidated before being used.   |


Cached responses may become “stale” once they can no longer be used as their current state. It requires a request to the server to either retrieve the data again, or to validate the caches contents.

Below is an example of a request, and the response that specifies Control-Control headers.

``` text
GET https://bpglibrary-official.com/books/44
```

``` JSON
	200 OK
    Cache-Control: max-age=1000, private
    Content-Type: text/json; charset=utf-8
    Content-Length: ...
{
    "bookID": 44,
    "title": "Big Book of Knowledge",
    "author": "Peter Jackson",
    "pubDate": "2012-04-16",
    "availability": "Available",
    "links": [
        {
            ...
        }
    ]
}

```

The response indicates that the response can be cached for a maximum of 1000 seconds, and that it is only for the clients use as indicated by the "private" value.

### ETags
In addition to Cache-Control headers, a response may also contain an ETag, or an Entity Tag. Think of it as the digital fingerprint of the resource. 

The ETag header is an indicator for the version of a given resource. Each time a resource is modified or changed, the ETag value must change as well. When a response contains an ETag and is cached by the client, the ETag should also be stored. 


Clients would use the [If-None-Match](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-None-Match) header to include the ETag into a request, to which the server will then attempt to match with the ETag stored on the server. If both values match, a 304-Not Modified Status Code can be returned without a body to indicate to the client that the cached version is not stale and can still be used. Otherwise, the server should respond with the new state of the resource to update the cached version.



ETags can be strongly or weakly validated. Weakly validated ETags are prefixed with a W/, which are easy to generate but are not very useful for comparing resource states. In context, a strong validation means that the two resources with the same ETag are identical, byte-for-byte. Strongly validated ETags allow for caching of partial responses.

Strong validators are ideal for comparing the resource states, but difficult to generate efficiently.  

An example by [Mozilla Devloper](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag) demonstrates how a Strong and Weak Etag is represented

```text
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4" 
ETag: W/"0815"
```


[If-Match](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-Match) is another header to be used with ETags which compares the ETag attached to the request to what is stored with the resource. It allows for clients to specify what state of the resource they wish to access.

If a value does not match what is contained in the If-Match header, a 412-Precondition Failed should be returned.



# Versioning
Versioning comes as the API evolves over time and how it may be required to make changes that could potentially break existing client usage. With versioning, we can introduce these changes whilst maintaining compatibility with the older versions. 

The addition of versioning must keep in mind that client applications may not be aware of the updated functionality and must ensure that client workloads are still available regardless of versioning. This means that clients using the older version must not have existing work break.

A version indicator could be a number, a project name, or anything that can be both meaningful and easy to differentiate between the various versions.

If the client has not entered an incorrect header or has an incorrect API version, a Status Code of 400 and an apt message should be returned to the client.

Although not related to REST, there are multiple approaches developers can follow that help clients navigate versioned APIs. 

## No Versioning 
For some applications, it may not be required to version the API. This could be due to it being an internal API, or because the changes being made will not affect client applications. 

For example, a request to an API with no versioning would like this:


```text
https://bpglibrary-official.com/...
```


Changes would be represented as a new resource or operation that can be executed without reliance on existing functionality. 

Clients **must** be made aware of the new functionality. Otherwise, how else will they know to use it?

No versioning means that client applications will continue to function as intended without alteration of URIs, however new functionality added may not be utilised by the client if they are unaware, as well causing issues with clients if their applications are not fitted to handle new or changed fields.

If more radical changes are made to an API, such as removing fields or renaming them, the relationship between the resources will have changed and can possibly interrupt a client’s usage.

Due to this, this method may not be the best fit. The following approaches may be more suitable in this situation.

## Versioning through the URI 
The URI for each resource exposed will include a version indicator of the API. For example, if we wanted to make a request to the 2.0 version of the book API, a request would look like this:


```text
https://bpglibrary-official.com/v2/books 
```

And the response body returned would contain a collection of Books in JSON matching the API-Version 2 Schema for Books.

```JSON
{
  "books": [
    {
      "id": 1,
      "title": "The Great Gatsby",
      "author": "F. Scott Fitzgerald",
      "pubDate": "1960-07-11",
    },
    {
      "id": 2,
      "title": "To Kill a Mockingbird",
      "author": "Harper Lee",
      "pubDate": "1945-08-11",
    }
  ],
    "links": [
        {
            ...
        }
    ]
}

```

URIs from previous versions must still be usable by the client, which does mean that we must violate the principle of having each URI refer to a unique resource. 


The previous URIs should return the resource that is the same as their original schema. For example, take the below URI that will call the API-Version 1 collection of Books.

```text
https://bpglibrary-official.com/v1/books 
```
```JSON
{
  "books": [
    {
      "id": 1,
      "title": "The Great Gatsby",
      "author": "F. Scott Fitzgerald",
    },
    {
      "id": 2,
      "title": "To Kill a Mockingbird",
      "author": "Harper Lee",
    }
  ]
}

```


As you can probably see, this can get quite cumbersome and complicated as the API grows and evolves. HATEOAS would be complex to implement as each link would also have to include the version number.

## Versioning through a Custom Request Header
An easier, more consistent method of versioning the API is to include the API version indicator in the request headers. This method avoids the complexity of including the version for resources across the versions.

Lets say we have a versioned API and that the client wants to return Order data from the API-Version 1. They would include this header in their request so that the API can verify and differentiate between the version to use.

```text
 GET   https://bpglibrary-official.com/orders/3
 Custom-Header: api-version=1
 ...
```



Now, lets say the client wants to call the API version 2.0, as that contains more fields than the original version. They would add the correct version indicator to the Custom Header.


```text
 GET   https://bpglibrary-official.com/orders/3
 Custom-Header: api-version=2
 ...
```


Again, the object schema must match what is in the version being set **UNLESS** it is a conditional value.

All clients are required to include that custom header with their targeted API version. If it is omitted, or left out, a *Default* Version can be used. This is when the API service will apply a default version for all incoming requests.

If the API version is not included, a Status Code of 400-Bad Request and an relevant message describing the issue must be returned to the client. 

## Deprecating Functionality
It has been stated before that as API functionality evolves and grows, changes may be made to functionality that break existing client applications. Developers should avoid disrupting client interactions, however sometimes this is not possible. 

Old functionality may be deprecated if a major overhaul or change has been made to the inner workings of the system, and we may not want clients to use the old functionality anymore as it is less secure or not as efficient. It is up to the developer to alert the client that the service is going to be deprecated, and to provide a date or indication of when they should expect this functionality to stop working. 


Let us say we deprecate the service which allows clients to rent a book through the book keeping API. The client may be unaware of this, and will send a request to API with the relevant URI:

```text
https://bpglibrary-official.com/v1/books/4/rent
```


If the functionality is still available, but is going to be deprecated in a weeks’ time, the response **should** contain a similar format as below to indicate to the user when this feature will be removed from service.


```text
<description of Service> will retire on <date> (url)
```
The (url) is optional and can be used to show more information on the matter for the client.

If the functionality has fully deprecated and is no longer used by the API, the API must return a response that contains a 410-Gone Status Code.


Additionally, developers should be removing unused dependencies and libraries that are deprecated or no longer supported. Reviews of used libraries and dependencies should be conducted regularly.


# Open API Specification
The Open API Initiative was created by industry experts who recognised the need of standardising how APIs are described, and that having a language-agnostic interface allows for both humans and computers to discover and understand the full capabilities of an API without access to the source code.  

Although some aspects of this guide have taken influence from the Open API initiative, it requires developers to adhere to a stricter framework and set of standards that it must contain for it to be considered Open API adherent.

To find out more on the specification, visit [spec.openapis.org/oas/latest.html](https://spec.openapis.org/oas/latest.html)





