# What is a Transaction?

A set of tasks which take place as a single, indivisible action.

- **A**tomic - Each unit of work is an all-or-nothing operation
- **C**onsistent - Database integrity constraints are never violated
- **I**solated - Isolating transactions from each other
- **D**urable - Commited changes are permanent

[[0 - Why Transactions and how Java supports them#Transactions in the RewardNetwork|Example of a Transaction]]

[[0 - Why Transactions and how Java supports them#Naive Approach|Naive Approach]]

[[0 - Why Transactions and how Java supports them#Correct Approach|Correct Approach]]

# How to use Transactions?

There are 3 steps you need to take:
- Declare a `PlatformTransactionManager` bean
- Declare the transactional methods
	- A declarative approach 
		- Using Annotations (recommended)
	- A programmatic approach
		- using `TransactionTemplate` %% Addition from Udemy Practise exam %% 
- Add `@EnableTransactionManagement` to a config class

## How to configure Transactions?

With the help of `@Transactional` within the component/bean and adding `@EnableTransactionManagement` in the config file.

[[1 - Spring Transaction Management#`@Transactional` Configuration|Code Example]]

## How can I implement the PlatformTransactionManager?

PlatformTransactionManager is the base interface for the abstraction. Several other implementations are available:
- DataSourceTransactionManager
- JpaTransactionManager
- JmsTransactionManager
- JtaTransactionManager
- more...

## How is the Transaction Management implement really?

The targeted service is wrapped within a proxy which uses an "around" advice.

[[1 - Spring Transaction Management#Declarative Transaction Management|Details and Diagram]]

[[1 - Spring Transaction Management#What Happens Exactly?|More Details]]

## How can I use the Transaction Manager?

Create the required implementation, just like any other Spring bean and configure it as appropriate.

[[1 - Spring Transaction Management#Deploying the Transaction Manager|Code Example]]

[[1 - Spring Transaction Management#Accessing a JTA Transaction Manager|JTA Transactional Manager Example]]

## Transactions are bound to the current thread.

The Transaction context is bound to the current thread, which holds the underlying Jdbc connection.

JdbcTemplate used in an `@Transactional` method which uses that connection automatically.

[[1 - Spring Transaction Management#Transaction Bound to Current Thread|Details]]

### You can also use the `@Transactional` at the Class level.

Every method within the class will use a Transaction.

### You can also combine Class and Method level declaration.

[[1 - Spring Transaction Management#Class and method level|Example code]]


# Java's `@Transactional`

Do not use Java's annotation since it has few options. Best to always use Spring's `@Transactional`.

# What is Transaction Propagation?

It is how transactions should behave when one transaction-related method is called from another transaction-related method.

[[2 - Configure Transaction Propagation#Understanding Transaction Propagation|Problem example]]

[[2 - Configure Transaction Propagation#Understanding Transaction Propagation|Problem example continued]]

## How does Spring handle Transaction Propagation?

There are 7 levels of propagation, the below examples will only show `REQUIRED` and `REQUIRES_NEW`.

**NOTE:** Propagation Rules are Enforced by Proxy so if there are internal calls the rules will not be enforced.

[[2 - Configure Transaction Propagation#Propagation Rules are Enforced by a Proxy|More Details]]

### REQUIRED (Default)

[[2 - Configure Transaction Propagation#REQUIRED|Details]]

### REQUIRES_NEW

[[2 - Configure Transaction Propagation#REQUIRES_NEW|Details]]

%% Addition from Udemy Exam Practises %%
# What are Isolation levels?

Transaction isolation levels are essential in database systems to manage concurrent access to data. When multiple transactions run simultaneously, they can interact with each other in unintended ways, leading to various anomalies and inconsistencies. Isolation levels help control these interactions by defining the visibility and accessibility of data across transactions.

The isolation levels are:
- **READ_UNCOMMITTED (Lowest Isolation, Highest Concurrency)**:
    - Allows all anomalies (dirty reads, non-repeatable reads, phantom reads).
    - Prioritizes performance over consistency.
- **READ_COMMITTED**:
    - Prevents dirty reads but allows non-repeatable and phantom reads.
    - Balances performance and consistency, commonly used as a default in many databases.
- **REPEATABLE_READ**:
    - Prevents dirty reads and non-repeatable reads but allows phantom reads.
    - Offers stronger consistency guarantees with a moderate impact on concurrency.
- **SERIALIZABLE (Highest Isolation, Lowest Concurrency)**:
    - Prevents all anomalies (dirty reads, non-repeatable reads, phantom reads).
    - Provides the strictest consistency but significantly reduces concurrency and performance.

### What are the reasons we need these levels?

- **Dirty Reads**: Reading uncommitted changes made by another transaction.
- **Non-repeatable Reads**: Reading the same data multiple times yields different results due to changes made by other transactions.
- **Phantom Reads**: A query executed multiple times returns a different set of rows due to insertions or deletions by other transactions.
- **Lost Updates**: Concurrent transactions overwrite each other's updates.

# Rollback Rules

## What is the Default rule?

A transaction is rolled back only if a `RuntimeException` has been thrown.

[[3 - Setup Rollback Rules and Testing support#Default Behaviour|More Details]]

## How can the Default rule be overridden?

You can override the default rule by using the `rollbackFor` and/or `noRollbackFor` attributes.

[[3 - Setup Rollback Rules and Testing support#rollbackFor and noRollbackFor|Example code]]


# How can I test the Transactional methods?

You can test by adding to annotated test methods/classes the annotation `@Transactional`.

This achieves transactional behaviour but also rolls back after finishing testing so you don't need to clean up your database afterwards.

[[3 - Setup Rollback Rules and Testing support#`@Transactional` within Integration Test|Example code]]

## Can I better control the Transactional tests? 

Yes you can! You can do so with the help of the `@Commit` which commits the transactions within the test class.

[[3 - Setup Rollback Rules and Testing support#Controlling Transactional Tests|Example]]
