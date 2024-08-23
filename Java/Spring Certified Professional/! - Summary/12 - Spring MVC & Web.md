# What is Spring MVC?

It is a web framework which is based on:
- Model/View/Controller pattern
- POJO programming
- Testable components
- Spring for configuration

It also supports Server-side rendering and REST.

***The material is focused on REST***

## What types of MVC Applications are there?

- Web Servlets
- Web Reactive

[[0 - Spring Web Introduction#Types of Spring MVC Applications|More Details]]

***The material is focused on the traditional Web Servlets***
## What Servlet containers are there?

- Traditional
	- WAR packaging
- Embedded
	- Packaging an embedded container within a deployment artefact in a "fat" JAR file
	- Run web applications from the command line

***The material is focused on the embedded approach***
# Starter Dependencies

By using `spring-boot-starter-web`, you ensure Spring Web and Spring MVC are on the classpath.

[[0 - Spring Web Introduction#Spring Web "Starter" Dependencies|Details]]

[[3 - Configure for WAR or JAR deployment#Spring Boot for Web Applications|More Details]]
# How does Spring MVC work and what are some of its components?

## The heart of MVC: Dispatcher Servlet

The Dispatcher Servlet:
- Receives all the requests for our application
- It does not know how to do things
- Knows how to delegate to the correct components for the requests to be handled

[[0 - Spring Web Introduction#Spring Boot Creates Spring MVC Components|Diagram flow]]

## Handler Mapping

First thing the Dispatcher Servlet does is delegate to the **Handler Mapping**. It knows which controller is in charge of a particular request

## Handler Adapter

Once we know the controller the Dispatcher Servlet sends the request through to **Handler Adapter** to adapter the request with the controller. 

It can:
- Parse the parameters from the request
- Parse the variables from the URI
- Provide anything from the Servlet API

## Controller

The controller is where all our handler methods go, that is where the logic is actioned. Depending on what was requested, the controller could return a logical view that then needs processing or it could return simple objects. 

## Message Converters

When the controller returns simple objects, the Dispatcher Servlet delegates to the **Message Converters**.  

The Message Converters converts the objects which are typically converted to JSON, XML or another format and then get sent back to the user.

## Other Components

### View Resolver

The controller could render a view on the server-side which returns a view but needs to be processed by the **View Resolver**.

The View Resolver will locate the view and create an instance.

### Locale Resolver
### Exception Resolver
### Content Type Manager



# How can we get started?

Controllers are the only components that need to be created by developers, common Message Converters are automatically setup if found on the classpath.

[[1 - Create a simple RESTful controller to handle GET requests#Request Processing Lifecycle - REST|More Details and Diagram Flow]]

## How to implement a Controller?

Controllers are annotated with `@Controller` which is actually a [[3 - Components#What are Stereotype Annotations?|Stereotype annotation]] so it will be found by the component-scanner.

[[1 - Create a simple RESTful controller to handle GET requests#Controller Implementation|Picture]]

### What is `@ResponseBody`?

It is an annotation that is for the handler methods which helps define a ***REST response***. 

By doing so, it turns off the **View** handling sub-system since we will be returning [[#Message Converters|simple objects]].

### Is there an easier way to implement a REST Controller?

Yes, by using the "composed" annotation `@RestController` which incorporates `@Controller` and `@ResponseBody`, it assumes all methods will return return REST response-data.

## How to implement a handler method?

You create your method within the Controller class which is annotated with `@Controller` and for the method itself, you annotate it with:
- `@GetMapping(..)` 
- `@PostMapping(..)` 
- `@PutMapping(..)` 
- `@DeleteMapping(..)` 

[[1 - Create a simple RESTful controller to handle GET requests#Method Signature Examples|A few examples of everything explained]]

[[#How to handle all mappings?]]
### Handler Method Arguments

You pick the arguments you require and Spring will inject them for you, you can inject:
- `HttpServletRequest`
- `HttpSession`
- `Principal`
- `Locale`
- and more

### How can I extract the Request Parameters from the URL?

You can do so by using the annotation `@RequestParam` which:
- Extracts the parameters from the request URL
- Performs type conversion

[[1 - Create a simple RESTful controller to handle GET requests#Extracting Request Parameters|Picture]]

### Is there another way to extract values from the URL?

Yes, values can be extracted from the request URL by using URI Templates.

- `{..}` are used within the Mapping annotation
- `@PathVariable` is used on the method argument

[[1 - Create a simple RESTful controller to handle GET requests#URI Templates|Details]]

#### Naming convention

[[1 - Create a simple RESTful controller to handle GET requests#Naming Conventions|Example]]

# Why do we need Message Converters?

We talked about [[#Message Converters|Message Converters]] previously but we need them because the problem is as such:
- Response requires data to be returned in the response body
	- Usually JSON or XML
- Developers prefer to work with Java objects
- Developers also want to avoid manual conversions

The solution to that is:
- Object to text conversion
- Annotating response data with `@ResponseBody` or by using [[#Is there an easier way to implement a REST Controller?|@RestController]]

### How do they work really?

Spring Boot automatically sets up the HttpMessageConverters but manual configuration is also possible.

[[2 - Message Converters#HttpMessageConverter|Examples]]

### How do we know what needs to be converted?

By looking at the request header `Accept`, it specifies the mime type which usually is:
- `application/json`
- `application/xml`

[[2 - Message Converters#What Return Format? *Accept* Request Header|Example]]
# Can you customise GET responses?

Yes, by using the `ResponseEntity` API. You can responsed explicitly which:
- Gives you more control
- You can set headers or control response content

[[2 - Message Converters#Customising `GET` Responses `ResponseEntity`|Snippet code]]

[[2 - Message Converters#Setting Response Data with Domain Object|Another Snippet code example]]

# How to handle all Requests?

## HTTP Status Code Support

Web apps use a handful of status codes:
- Success - 200 OK
- Redirect - 30x for Redirects
- Client Error (Invalid URL) - 404 Not Found
- Server Error - 500 (unhandled Exceptions)

[[1 - Implementing HTTP GET and PUT#HTTP Status Code Support|RESTful applications may use...]]

### `@ResponseStatus`

You can use `@ResponseStatus` annotation to return a status code other than 200. The annotation is used on void methods which return a response with an empty body (no-content).

**NOTE:** Use `HttpStatus` enumerator for more status codes

[[1 - Implementing HTTP GET and PUT#`@ResponseStatus`|Example]]

### `@RequestBody`

You can use the `@RequestBody` annotation to extract incoming request data. The correct message converter is chosen automatically with the help of the **request header** `Content-Type`.

[[1 - Implementing HTTP GET and PUT#`@RequestBody`|Snippet]]

### Building URIs

HTTP 201 must return the full URI of the newly created resource in the **location header**.

You can create a URI with:
- `UriComponentsBuilder`
	- Allows explicit creation of URI
	- Requires hard-coded URLs
- `ServletUriComponentsBuilder` - [[2 - Implementing HTTP POST and DELETE#`ServletUriComponentsBuilder`|Example Code]]
	- Subclass of `UriComponentsBuilder`
	- Provides access to the URL that invoked the current controller method

## `@GetMapping`

`@GetMapping("/store/orders")` is a "composed" annotation, it is equivalent to:
- `@RequestMapping(path="/store/orders", method=RequestMethod.GET)`
- RequestMethod enumerators
	- GET
	- POST
	- PUT
	- PATCH
	- DELETE
	- HEAD
	- OPTIONS
	- TRACE

**NOTE:** For HEAD, OPTIONS, TRACE must use `@RequestMapping`

### Example: Retrieving a Representation: `GET`

[[1 - Implementing HTTP GET and PUT#Retrieving a Representation `GET`|GET Example]]

## `@PutMapping`

The PUT method is only used when updating an **existing resource**. The requirements for a PUT mapping is:
- Responds only to PUT requests
- Be able to access data in the HTTP Request - [[#`@RequestBody`]]
- Return an empty response with the **status code 204** - [[#`@ResponseStatus`]]

[[1 - Implementing HTTP GET and PUT#HTTP `PUT` Update a Resource|Request example]]

[[1 - Implementing HTTP GET and PUT#Updating Existing Resource `PUT`|Request and code example]]
## `@PostMapping`

The POST method is only used when creating a **new resource**. The requirements for a POST mapping is:
- Responds only to POST requests
- Be able to access data in the HTTP Request
- Return a "created" status with the **status code 201**
- Must generate **Location header** for newly created resource
	- [[#Building URIs|How do we generate the URI location?]]

[[2 - Implementing HTTP POST and DELETE#HTTP `POST` Create a new Resource|Request Example]]

[[2 - Implementing HTTP POST and DELETE#Creating a new Resource `POST`|Request and code example]]

## ## `@DeleteMapping`

The DELETE method is only used when deleting an existing resource. The requirements for a DELETE mapping is:
- Responds only to DELETE requests
- Return an empty response with the **status code 204**
- Must generate **Location header** for newly created resource

[[2 - Implementing HTTP POST and DELETE#Deleting a Resource `DELETE`|Request and code example]]

# What's the difference between a embedded web container and an external one?

In regards to Spring Boot, by default an embedded web container (Tomcat is the default) is started up which allows you application to be run from the command line.

You must configure the application to be run within an external container. 

## How can I run the app within an external container?

Your application class must extend `SpringBootServletInitializer` and must specify the configuration classes to use.

[[3 - Configure for WAR or JAR deployment#Running Within a Web Container (traditional)|Example app class and of the configuration]]

## Can you configure the app to run in both scenarios?

Yes, by having a `main()` and `configure()` you can configure the application to be package in a JAR or WAR files respectively.

### Details about running JAR or WAR

By default, both JAR and WAR files can run from the command line with the help of the embedded container Tomcat, though it is not that common for the WAR file (since its purpose is to be deployed within an external container).

#### Best Practise

Mark Tomcat dependencies as **provided** when building WARs.
## Are there alternative embedded web containers?

Instead of Tomcat, you can use:
- Jetty
- Undertow

[[3 - Configure for WAR or JAR deployment#Alternative Web Containers Jetty, Undertow|Jetty Example]]


# Are there any tools to help with development?

Yes, by adding `spring-boot-devtools` in your dependencies it helps with:
- Automatic restarts
- Automatically disabled in production
# What is needed to get a Spring Web app running?

- pom.xml file
	- [[4 - Spring MVC Quick Start#1a Maven Descriptor|Example of Maven Descriptor]]
	- [[4 - Spring MVC Quick Start#1b Maven Descriptor|Example of Maven Descriptor continued]]
	- [[4 - Spring MVC Quick Start#1c Spring Boot sets up Spring MVC|Since dependencies are defined..]]
- controller class
	- [[4 - Spring MVC Quick Start#2 Implement the Controller|Example of Controller implementation]]
- application.properties file
	- [[4 - Spring MVC Quick Start#3 Setting Properties|Example of common properties being set..]]
- application class
	- [[4 - Spring MVC Quick Start#4 Application Class|Example of Application class]]

Then package the application using `mvn package` and deploy/run the application using `java - jar` using the "fat" JAR. Test the application is running by visiting the URL in the browser.

[[4 - Spring MVC Quick Start#5 Deploy and Test|Example of running the Application]]

[[4 - Spring MVC Quick Start#Quick Start|Details]]


# 

#
#