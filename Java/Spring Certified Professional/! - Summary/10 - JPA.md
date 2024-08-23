# What is JPA?

**Java Persistence API (JPA)** is a specification within the Java ecosystem that provides a standard way to map Java objects to relational databases. It simplifies database operations by abstracting the complexities of database interactions and providing a framework for managing relational data in Java applications.

Core concepts are:
- Entities
- EntityManager
- Persistence Context
- Persistence Unit
- Transactions
- Querying

# Starter Dependencies

[[0 - Spring Boot - Spring Data JPA#Spring JPA "Starter" Dependencies|Dependency]]

# Does Spring Boot auto-configure JPA?

Yes, Spring Boot automatically configures:
- A **DataSource**
- An **EntityManagerFactoryBean**
- A **JpaTransactionManager**

But you can also customise:
- EntityManagerFactoryBean
- JpaTransactionManager

## You can customise the EntityManagerFactoryBean with configuration properties

[[0 - Spring Boot - Spring Data JPA#Customise EntityManagerFactoryBean - Configuration Properties|Configuration Properties]]

# How to setup JPA?

By default, Spring Boot looks in the same package as the class that is annotated with `@EnableAutoConfiguration` but it can be overridden with `@EntityScan(..)`

Some properties ([[0 - Spring Boot - Spring Data JPA#Replaced by|properties]]) need to be set in the application.properties and lots of defaults.

[[0 - Spring Boot - Spring Data JPA#JPA Configuration without Spring Boot|Config without Spring Boot]]
