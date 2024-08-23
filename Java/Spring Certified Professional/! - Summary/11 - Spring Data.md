
# What is Spring Data?

It is part of the Spring Framework that simplifies data access and persistence in Java applications. It abstracts the complexities of data access, allowing developers to focus more on business logic and less on boilerplate code for database operations.

[[1 - Create Spring Data Repositories for JPA#What is Spring Data?|Picture]]

# Why use Spring Data?

It provides similar support for NoSQL databases that Spring does for RDBMS:
- Template classes to hide low-level, repetitive code
- Data-access exceptions
- Can transfer code to different storage technologies

***It also implements repositories for you***

# How can Spring Data implement Repositories?

Spring Data implements the repositories at run-time, it will:
- Scan for interfaces extending the Spring Data Common `Repository<T,K>`
- CRUD methods are auto-generated if using the `CrudRepository<T,K>`
- It supports paging, custom queries and sorting
- Variations exist for most [[1 - Create Spring Data Repositories for JPA#What is Spring Data?|Spring Data sub-projects]]

## Step 1: Annotate Domain Class (JPA)

[[1 - Create Spring Data Repositories for JPA#Step 1 Annotate Domain Class (JPA)|Annotate the JPA domain classes]]

### Other Data Stores

[[1 - Create Spring Data Repositories for JPA#Domain Classes Other Data Stores|Details on other stores]]

## Step 2: Define your Repositories as an Interface

### Choose a Repository Interface to extend

[[1 - Create Spring Data Repositories for JPA#Step 2a Choose a Repository Interface to extend|Code Snippet of CrudRepository and PagingAndSortingRepository Interfaces]]

### Define your Repository Interface

#### Option #1: 

You can extend the CRUD/PagingAndSorting Repo and add finder methods which must obey naming convention.

[[1 - Create Spring Data Repositories for JPA#Step 2b Define your Repository Interface - Option 1|Details and Code Snippet]]

#### Option #2:

You can extend the base Repository Interface (Marker Interface), which does not have any methods, and build your own interface.

[[1 - Create Spring Data Repositories for JPA#Step 2b Define your Repository Interface - Option 2|Code Snippet]]

## How to Enable your Repositories?

Spring Boot will automatically scan for repository interfaces, it starts in the package of the `@SpringBootApplication` class and scans all sub-packages.

You can also enable it manually by adding the `@EnableJpaRepositories` in a configuration file.

[[1 - Create Spring Data Repositories for JPA#Finding your Repositories|Example of Manual scanning]]

### What happens internally?

Spring Data implements your repositories at run-time creating instances as Spring Beans/Component, which means they are [[1 - Application Context#Case 2 Your Bean is a Proxy|proxies]].

[[1 - Create Spring Data Repositories for JPA#Internal Behaviour - Another Spring Proxy|Picture]]

# How to access the Repository?

[[1 - Create Spring Data Repositories for JPA#Accessing the Repository - It is a regular bean during runtime|Just like a regular bean.]]

