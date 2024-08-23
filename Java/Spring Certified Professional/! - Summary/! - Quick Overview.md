# Spring

## What is Spring?

Spring is an Open Source, Lightweight, Modular, DI Container and Framework for building Java enterprise applications.

## Why is Spring lightweight?

- Spring apps do not require a Java EE app server
- Spring is not invasive
	- Does not require you to extend framework classes or implement framework interfaces
	- Write code as POJO (Plain old java object)
- Low overhead
	- Spring jars are relatively small

## What Modules are there?

- **Spring MVC** - for the web application
- **Spring Data** - for data access
- **Spring Security** - for authentication and authorization
- Many more...

## What is a Dependency Injection Container?

It provides the foundation for managing the lifecycle and configuration of application objects (beans).

### What is Inversion of Control (IoC)?

It is a principle in which the control of object creation and management is transferred from the application code to a container or framework. 

In the case of Spring, the IoC container manages the lifecycle of beans and their dependencies.

### What is Dependency Injection?

Dependency Injection is a design pattern used to implement IoC. It involves injecting dependencies (objects) into a class, rather than the class creating the dependencies itself. 

This promotes loose coupling and enhances testability and maintainability.

## What else can the Spring Framework do?

Provide integration and implementation with a wide variety of technologies/architectures/deployment-platforms:
- Containerization, Cloud, Microservices
- JDBC, Transactions, ORM / JPA, NoSQL
- Events, Streaming, Reactive, Messaging, JMS, Tasks, Scheduling
- Security, OAuth2, OpenID Connect
- Monitoring, Observability


## What is Spring's Goal?

To provide comprehensive infrastructural support for developing enterprise Java App:
- Spring deals with the "plumbing"
- You focus on solving the business domain problems

### What Key Principles are there?

* Don't repeat yourself (DRY)
- Separation of Concerns
- Convention over Configuration
- Testability


## More Details on Spring?

Visit [[Spring Details]]
# Spring Boot
