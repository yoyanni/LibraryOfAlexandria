# What is REST?

REST (REpresentational State Transfer) is an architectural style that describes best practises to expose web services over HTTP.

# Why REST?

Applications can leverage the benefits of HTTP:
- Simplicity
- Ubiquity
- Interoperability
- etc..

The motivation behind using REST is that it works well with:
- Mobile applications
- Microservices
- Browsers
	- Single Page Applications (SPA)
	- Asynchronous Javascript and XML (AJAX)

[[0 - RESTful application with Spring Boot#Why REST?|More details..]]
# What are some of REST Principles?

- Expose resources through URIs (Uniform Resource Identifier)
	- Models must be nouns, not verbs (eg. accounts, orders, items)
- Resources support limited set of operations which have well-defined semantics
	- GET
	- PUT
	- POST
	- DELETE

[[0 - RESTful application with Spring Boot#REST Principles - 1|Examples and Details]]

- Clients can request particular representations of the resources, for example:
	- JSON
	- XML
	- more..

- Stateless architecture
	- No HttpSession usage
	- GETs can be cached on URL
	- Clients keep track of state
	- Scalable
	- Looser coupling between client and server

[[0 - RESTful application with Spring Boot#REST Principles - 2|More Details]]

- HTTP headers and status codes communicate results to clients

# REST and Java

## Jakarta RESTful Web Services

Jakarta EE is a set of specifications for building Cloud Native Java Applications which supersedes Java EE.

Jakarta RESTful Web Services (JAX-RS) is one of the various Jakarta EE specifications.

Various implementations that also provide Spring support include:
- Jersey
- RESTEasy
- Apache CXF

## Spring-MVC

Spring-MVC also provides REST support by using familiar and consistent programming models.

**NOTE:** Spring-MVC does not implement JAX-RS.

The `RestTemplate` for building REST clients in Java.
# How does Spring MVC support REST?

- Map requests based on HTTP methods
- Define response status (404, 204, 201, etc..)
- Message Converters
- Access request and response body data
- Build valid Location URIs (HTTP POST handling)

# How to build a REST Client Application?

By using `RestTemplate`, you can build a REST Client app with the help of [[3 - Utilise RestTemplate to invoke RESTful services#`RestTemplate` for building REST Client application|these]] http methods.
## How can you create a `RestTemplate`?

You can use the `new` operator as shown [[3 - Utilise RestTemplate to invoke RESTful services#Creating `RestTemplate`|here]]. It steps up the same default `HttpMessageConverters` as the server.

You can also use the auto-configured `RestTemplateBuilder` for Spring Applications. You can inject the class into any other class to have access to it but it also takes into account properties in the `application.properties` file.

[[3 - Utilise RestTemplate to invoke RESTful services#Usage Examples|Examples of RestTemplate]]

## Using the `ResponseEntity` with the `RestTemplate`

You can access the response headers and body using the `ResponseEntity` generic class.

[[3 - Utilise RestTemplate to invoke RESTful services#Using `ResponseEntity`|Example]]

## Using the `RequestEntity` and the `ResponseEntity`

You can set up your own request using a combination of the `RequestEntity` and the `ResponseEntity`.

[[3 - Utilise RestTemplate to invoke RESTful services#`RequestEntity` and `ResponseEntity`|Example here]]


# Is there an alternative for `RestTemplate`?

Yes, you can also use `WebClient` which has mainly taken over since `RestTemplate` is not being worked on anymore.

`WebClient` is used for more modern use cases.
#
#
#