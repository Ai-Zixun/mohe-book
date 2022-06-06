# API Design: GraphQL vs. gRPC vs. REST

* They are different tools for different jobs

Design Considerations: there is always a best API style for the problem

## API Styles

* Query APIs
  * Flexibility
  * For client to retrieve data from the server
* Flat File APIs
  * SFTP
* Streaming APIs
* RPC APIs
  * gRPC, thrift
  * A component calling another components and hide the in-between networking
* Web APIs

## RPC - Remote Procedure Call (gRPC, Apache Thrift, Coral)

* Model the function of the server
* Data over the wire (HTTP/2 + protobuf)

Advantages:
* Simple and Easy to Understand
* Lightweight payloads
* High performance

Disadvantages:
* Tight coupling
    * the client and server need to understand each others
* No discoverability
    * no way to understand the api without taking a look at the documentation
* Function explosion
    * An api/function for an specific job; ends up with a lot of api/functions


Good For:
* Micro-services

## REST - Representational State Transfer (json, ION)

* Model the resource of the server
* States machines over the wire

* For API longevity, not for short-term efficiency
* Reduce Server-Client coupling

Entry point
* client sending a request to the entry point
* server sending back the metadata of the api

e.g.
```
GET http://example.com

{
    "conversations": {...},
    "title": {...},
}

GET http://example.com/conversions

{
    "count": 3,
    "value": [...],
}

GET http://example.com/conversions/2/title

{
    "title": "hello world",
}
```

Without any documentation, the user can understand how to use the api by taking a look at the metadata returned from the server.

Describe the operation on the resource

Advantages:
* Decoupled Client and Server
* API can evolve overtime
* Reuses HTTP
    * HTTP verbs (GET POST UPDATE ...)

Disadvantages:
* No single spec
    * Different people are having different understanding of REST
* Big payloads and Chattiness
    * Returning a lot of reach metadata
    * Chattiness: the client need to make a lot of API calls to accomplish an job

## GraphQL - Graph Query Language

Model the query
* Requesting what exactly what the clients want

Schema definition
* Defined the types and query that the client can be made

e.g.
```
{
    listCoversitions {
        title,
        message {
            text
        }
    }
}
```

Advantages
* Low network overhead
* Typed schema
* Fits graph-like data very well

Disadvantages
* Complexity - harder for the backend than REST & RPC
* Caching - always HTTP POST (i.e. no http caching)
* Versioning
* Still early

|                       | Coupling | Chattiness | Client Complexity | Cognitive Complexity | Caching | Discoverability |
| --------------------- | -------- | ---------- | ----------------- | -------------------- | ------- | --------------- |
| **RPC - Functions**   | High     | Medium     | Low               | Low                  | Custom  | Bad             |
| **REST - Resources**  | Low      | High       | Low               | Low                  | HTTP    | GOOD            |
| **GraphQL - Queries** | Medium   | Low        | High              | High                 | Custom  | Good            |


## Use Case: Management API

Solution: REST
Consideration:
* Focus on objects or resources
* Many varied client
* Discoverability and documentation

## Use Case: Command API

Solution: RPC
Consideration:
* Action-oriented
* Simple interaction

## Use Case: Internal Microservice

Solution: RPC
Consideration:
* Tightly coupled services
* High performance

## Use Case: Data or Mobile API

Solution: GraphQL
Consideration:
* Data is Graph-like
* Optimize for high latency

## Use Case: Composite API - Backend for Frontend

Solution: GraphQL
Considerations:
* Connect a lot of different frontends
* Connect a lot of different backends
* The middle service can use GraphQL to combine responses from different backends


## Contract First Design Approach

Contract
* GraphQL => the Schema
* gRPC => protobuf
* REST =>  RAML, Swagger (OpenAPI)

APP
* mock against the contract

API
* mock against the contract

Product
* Merge the APP and API