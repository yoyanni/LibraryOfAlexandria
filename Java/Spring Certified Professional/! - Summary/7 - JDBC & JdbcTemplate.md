# What is JDBC?

JDBC (Java Database Connectivity) is a Java API that enables Java applications to interact with relational databases. It provides a set of classes and interfaces for connecting to a database, sending SQL queries, and retrieving results.

## What are the problems with JDBC?

- Redundant code
- Error Prone code
- Generic Low-Level SQLExceptions that do not help

[[0 - Problems with traditional JDBC and into JdbcTemplate#Redundant, Error Prone Code in Traditional JDBC code|Example]]

##  How do we resolve this problem?

By using a Template, specifically `JdbcTemplate`.

# What is the JdbcTemplate?

It is an implementation of the ***Template Design Pattern*** which greatly simplifies the use of the JDBC API.

[[0 - Problems with traditional JDBC and into JdbcTemplate#Spring's JdbcTemplate|It helps with..]]

## What can the JdbcTemplate do?

- `queryForObject(sql, type)` - [[0 - Problems with traditional JDBC and into JdbcTemplate#JdbcTemplate in a Nutshell|example]]
- `query(sql, rowMapper)` - [[0 - Problems with traditional JDBC and into JdbcTemplate#Using Callbacks|With the help of callbacks]]
- more... 

# How can I create a JdbcTemplate?

The template only requires a dataSource to acquire a connection.

[[0 - Problems with traditional JDBC and into JdbcTemplate#Creating a JdbcTemplate|More Information]]

# How do I use the JdbcTemplate?

You implement a Repository component based off JdbcTemplate.

[[1 - JdbcTemplate Basic Usage#Implementing a JDBC-based Repository|Example code]]

## What can JdbcTemplate query for?

- Simple Types (int, long, String, Date, ...)
- Generic Maps - Maps, Lists
- Domain Objects
### How can I query for Simple Types?

You mainly use the `queryForObject()` to query for simple types.

**NOTE:** You can also use `queryForObject()` for a single Domain object.  

[[1 - JdbcTemplate Basic Usage#Query for Simple Java Types|Example code]]

### How can I query for Maps and Lists?

JdbcTemplate can return each row of a `ResultSet`:
- When expecting a single row
	- Use `queryForMap()` - [[2 - Working with ResultSets using Callbacks#Querying for Generic Maps - 1|Example code]]
- When expecting multiple rows
	- Use `queryForList()` - [[2 - Working with ResultSets using Callbacks#Querying for Generic Maps - 2|Example code]]

These can be used also when testing use cases.

**NOTE:** The data fetched is already mapped to a Java object.

### How can I map a Domain Object?

Spring's JdbcTemplate supports mapping for a Domain object by using a ***callback approach***.

You may prefer to use an ORM for this, you could use:
- JdbcTemplate queries
- JPA mappings

[[2 - Working with ResultSets using Callbacks#Domain Object Queries|Some more details]]

#### What is the RowMapper?

Spring provides a `RowMapper` interface for mapping a single row of a ResultSet to an object.
- It can be used for both single and multiple row queries
- Parameter can be provided to define the return-type.

[[2 - Working with ResultSets using Callbacks#RowMapper for mapping a row|Example]]

##### How do I use the RowMapper?

##### For Single Rows: [[2 - Working with ResultSets using Callbacks#Querying for Domain Objects - 1|Example]]

##### For Multiple Rows: [[2 - Working with ResultSets using Callbacks#Querying for Domain Objects - 2|Example]]

#### What is a ResultSetExtractor?

It is an interface that Spring provides for processing an entire ResultSet at once.

[[2 - Working with ResultSets using Callbacks#ResultSetExtractor|More Details]]

##### How do I use the ResultSetExtractor?

[[2 - Working with ResultSets using Callbacks#Using a ResultSetExtractor|Code Example]]

#### When should I use RowMapper vs ResultSetExtractor?

Use RowMapper when:
- Each row of a ResultSet maps to a domain object

Use ResultSetExtractor when:
- Multiple rows of a ResultSet map to a single object

### How can I pass Variables to the Sql Query String?

With the help of **bind variables**, specifically by using the `?`, you can add variables to the query. 

[[1 - JdbcTemplate Basic Usage#Query with Bind Variables|Example code]]

## How can JdbcTemplate write to the DB?

### How can JdbcTemplate insert?

It can insert into a db with the help of `update()` and then pass the insert sql script to the method.

The return value is the number of rows created/modified.

**NOTE:** For any non-SELECT SQL query, the `update()` is used to run them.
### How can JdbcTemplate update?

Actually its again with the help of `update()`, you also pass the specific sql script to the method.

**NOTE:** For any non-SELECT SQL query, the `update()` is used to run them.

## Handling Exceptions

### Difference between Checked and Unchecked Exceptions?

Checked Exceptions are ***bad*** because:
- Forced to handle the errors and if you can't:
	- The exception must be declared and they must be done so in every method between
- Form of tight-coupling

Unchecked Exceptions are good because:
- Can throw the exception up the call hierarchy to the best place to handle
- Methods in between don't know about it
- Spring always throws Runtime (unchecked) exceptions

### What are Data Access Exceptions?

- SQLExceptions
- DataAccessException - [[3 - Exception Handling#Spring Data Access Exceptions|Picture]]

[[3 - Exception Handling#Data Access Exceptions|More Details]]

[[3 - Exception Handling#Example BadSqlGrammarException|Example of an error]]

