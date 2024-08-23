Spring Data works with Spring Boot to make database integration simple.

# Controller-Repository Architecture

The **Separation of Concerns** principle states that well-designed software should be modular, with each module having distinct and separate concerns from any other module.

A common software architecture pattern that we can use to enforce data management separation is the **Repository** pattern.

A common architectural framework that divides these layers, typically by function or value, such as business, data, and presentation layers, is called **Layered Architecture**. In this regard, we can think of our Repository and Controller as two layers in a Layered Architecture.

The Repository is the interface between the application and the database, and provides a **common abstraction** for any database, making it easier to switch to a different database when needed.

![[Pasted image 20240726161012.png]]

# Choosing a Database

We’ll use an **embedded, in-memory** database. Specifically, it uses JDBC (the standard Java library for database connectivity) and SQL (the standard database query language).

**Embedded** simply means that it’s a Java library, so it can be added to the project just like any other dependency. 

**In-memory** means that it stores data in memory only, as opposed to persisting data in permanent, durable storage.

![[Pasted image 20240726161553.png]]

We’ll use H2 for **convenience for local development**, but we want to recognize the tradeoffs.

Since H2 is an in-memory database instead of a persistent database, it ensures the same state (empty) when testing running. However, "production" environments require a _persistent_ database which can lead to **Dev-Prod Parity** mismatch.

# Auto Configuration

All we need for full database functionality is to add two dependencies which wonderfully showcases one of the most powerful features of Spring Boot: **Auto Configuration**.

# Spring Data's CrudRepository

We’ll use a specific type of Repository: Spring Data’s `CrudRepository`.

The following is a complete implementation of all CRUD operations by extending `CrudRepository`:

```
interface CashCardRepository extends CrudRepository<CashCard, Long> {}
```

With just the above code, a caller can call any number of predefined `CrudRepository` methods, such as `findById`:

```ini
cashCard = cashCardRepository.findById(99);
```

`CrudRepository` and everything it inherits from is an Interface with no actual code! Well, based on the specific Spring Data framework used (which for us will be Spring Data JDBC) Spring Data takes care of this implementation for us during the IoC container startup time. The Spring runtime will then expose the repository as yet another bean that you can reference wherever needed in your application.