
Spring is an Open Source, Lightweight, DI Container and Framework for building Java enterprise applications.

# Lightweight

- Spring apps do not require a Java EE app server
- Spring is not invasive
	- Does not require you to extend framework classes or implement framework interfaces
	- Write code as POJO (Plain old java object)
- Low overhead
	- Spring jars are relatively small

# Dependency Injection

- Spring serves as a DI container for your app objects
	- Objects do not have to worry about connecting to each other
- Spring instantiates and injects dependencies into your objects
- Spring also serves as a lifecycle manager

# Spring Framework is more than just a DI container

- Wide variety of technologies/architectures/deployment-platforms:
	- Containerization, Cloud, Microsevices
	- JDBC, Transactions, ORM / JPA, NoSQL
	- Events, Streaming, Reactive, Messaging, JMS, Tasks, Scheduling
	- Security, OAuth2, OpenID Connect
	- Monitoring, Observability
- Spring provides framework classes, interfaces and annotations to help work with lower-level tech
- Extensible and customizable