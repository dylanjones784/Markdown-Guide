# Introduction
This is a best practice guide that is foundationally rooted in addressing the most common security concerns identified with Web APIs by OWASP, and layered with other standards that are from the likes of Microsoft, Google, IBM and more.

I believe that a collaborative, community-driven document is needed by the industry so that we are the driving force behind the common practices and standards. 

Following the following sections can, and will, help ensure that client experience is solid, by being:

- Seamless and Simple
- Efficient
- Consistent patterns across the API; Error Messages, Status 
is as seamless and simple as possible:




Simple, Consistent, Easy To Use, Adaptable , Efficient, Effective, Self-explanatory. This isn’t a do or don’t, this is amalgamation of standards that as a developer, you should follow, to promote those keywords.


We will be using keywords that should be interpreted as described in BCP 14, RFC2119, RFC8174. These are "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL”.


## Overview Summary

The following sections cover the key takeaways from each of the identified themes of best practices and standards for REST APIs. This list follows;



- Background information on REST and HTTP, how it should be used.


- Resource and Design patterns and standards



## Defining Principles: REST and HTTP
REST and HTTP are the defining factors of our API; we utilise REST

## HTTP

https://datatracker.ietf.org/doc/html/rfc7231
Verbs and Nouns
Idempotency
HTTP Status codes
Requests and Responses

Query paramters and headers
Content types,
Data types

## REST

--JSON 
--URI Patterns, values
--Hypermedia 
--Custom Methods
Stateless requests.

- URI patterns.
- HTTP, request and responses
- Query Paramters (Sorting and Limiting Fields) and Headers




## API Access Control and Security
Broken access control being a leading factor in OWASP top 10.
--Validation, Securing and validating

ALl the broken access control. Security & Authentication
Authenticating locally, each endpoint. Validating request data types. Rejecting anything that doesnt match.

Supporting API keys / JWTs

Validation & Sanitisation

Validating against Cross site request forgery


Rate Limiting

## Common API Implementation Practices
--LROs
Supporting Partial responses
Data Retention



## Exception handling and Error Logging
Handling errors gracefully. Returning messages
Returning correct status codes
Keeping a log of failed sign ins etc (audit trails)


## Filtering Responses


## Caching


## Versioning
