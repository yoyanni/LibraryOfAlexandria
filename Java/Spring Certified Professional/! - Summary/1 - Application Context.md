- Spring separates application configuration from application objects (beans)
- Spring manages you application objects
	- Creating them in the correct dependency order
	- Ensuring they are fully initialized before use
- Each bean is given a unique id / name
# What is the Application Context?

The Application Context is created by the DI container and thus represents it. It will take care of the lifecycle of the components/[[#What is a Bean?|beans]].

It can be created in any environment, such as:
- Standalone app
- Web app
- JUnit test

[[1 - The Application Context|Picture]]
# How is the Application Context created?

For it to be created, it requires:
- Your application classes (POJOs)
- Configuration Metadata

These then reference the DI container which creates the application context.

![[Pasted image 20240805161651.png]]

## How to create and use the Application?

[[0 - Quick Start with Java Configuration#Creating and Using the Application|Answer]]

# How is the Application used?

By creating the Application Context, which manages all components/beans, you can use retrieve those components and use them as needed.
# How to access a Bean programmatically?

[[0 - Quick Start with Java Configuration#Accessing a Bean Programmatically|Answer]]

# How to shutdown the Application?

`SpringApplication.run` method registers automatically a shutdown hook and returns a `ConfigurableApplicationContext`.

When invoked, the hook will let the context know that it needs to close.

[[2 - Adding Startup and Shutdown Behaviours#Use a JVM Shutdown Hook|Picture]]

# What is the Lifecycle of the Application Context?

The DI container/Application context runs through three distinct phases:
- Initialisation - the application context is created and so is the components (beans)
	- Spring Beans are created
	- Dependency Injection occurs
- Usage - retrieving beans from app context (executing business logic)
	- Beans are available for use in the application
- Destruction - when the app context is closed
	- Beans are released for Garbage Collection

## The Initialisation Phase

When a context is created, the initialisation phase has completed. 

In more detail, these are the steps that occur:
- **Step A:** Load & Process Bean Definitions
- **Step B:** Perform Bean Creation

[[0 - Initialisation Phase (Part 1)#Bean Initialisation Steps|Initialisation Diagram]]
### Step A: Load & Process Bean Definition

The `@Configuration` classes are processed
	1. And/or `@Component` annotated classes are scanned for
2. Bean definitions added to a **BeanFactory**
	1. Each indexed under its id and type
3. Special **BeanFactoryPostProcessor** beans invoked
	1. Can modify the definition of any bean

[[0 - Initialisation Phase (Part 1)#Step A Load & Process Bean Definition|Picture of Step A]]

[[0 - Initialisation Phase (Part 1)#Load Bean Definitions|Load Bean Definitions - Example]]

#### What is a BeanFactory?

It is the root interface for accessing the Spring IoC (Inversion of Control) container. It is responsible for managing the lifecycle of beans, including creation, initialization, and destruction. The `BeanFactory` provides the basic functionalities of the Spring container, which are extended by the more sophisticated `ApplicationContext`.

Hence the Spring DI container (Application Context) is a Bean Factory.

#### What is a BeanFactoryPostProcessor?

It is an interface in the Spring framework that allows you to modify the bean definitions in the `BeanFactory` before the beans are actually instantiated.

##### How do you declare a BeanFactoryPostProcessor?

[[0 - Initialisation Phase (Part 1)#Declaration|Answer]]
##### What is a common example?

[[0 - Initialisation Phase (Part 1)#Most Common Example|Answer]]

##### What is the Internal Extension Point?

It is the mechanism provided by the Spring framework that allows developers to extend and customise the behaviour of the `BeanFactoryPostProcessors`. 

This extension point is "internal" because it operates within the Spring IoC container, modifying its internal state and bean definitions before the application logic interacts with those beans.

Several useful implementations provided in Spring:
- Reading properties
- Registering a custom scope

##### Considerations

**BeanFactoryPostProcessor** is an internal bean invoked by Spring, which is not your code. It needs to run before any beans are created.

The use of `static` is recommended for `@Bean` methods.

### Step B: Bean Creation

 1. Bean creation
	 1. Created with dependencies injected
	 2. Each singleton bean eagerly instantiated
		 1. Unless marked as lazy
 2. Next each bean goes through a post-processing phase
	 1. BeanPostProcessors
 3. Now bean is fully initialised and ready to use
	 1. Tracked by id until the context is destroyed
 
 [[1 - Initialisation Phase (Part 2)#Bean Creation Sequence of Events - Singleton|Flow Diagram]]

#### What happens when you create the Beans with their dependencies?

Beans have to be created in the right order, that would be:
- First must be the dependencies
	- %% Addition from Udemy tests - [[2 - Configuration#How do you inject dependencies?|How do you inject dependencies?]] %%
- Secondly the beans are created.

There are two steps:
- Evaluate the dependencies for each bean
- Get each dependency needed
	- Create any if needed (which is recursive)

##### Forcing Dependency Order

[[3 - Bean Creation Order and Injection Issues#Force Dependency order|`@DependsOn`]]

##### How is the Bean Name and Type determined?

[[3 - Bean Creation Order and Injection Issues#Determining Bean Name and Type|Details and Picture]]

#### Example - Why wont this work?

[[3 - Bean Creation Order and Injection Issues#Why wont this work?|Example and Solutions]]

#### What are some Best Practises for defining Beans?

Taking into account the previous example, we can:
- Aim to be "sufficiently expressive"
	- Return interfaces except:
		- Where multiple interfaces exist
		- **AND** they are needed for dependency injection
	- Writing to interfaces is good practise
- **WARNING**: Event if you return implementation types
	- Still use interfaces when injecting dependencies
	- Injecting implementation types is brittle
		- Dependency injection may fail if the bean is proxied or a different implementation is returned
#### What is a BeanPostProcessor?

A `BeanPostProcessor` is used to perform operations on beans — such as checking for annotations, wrapping beans with proxies, or performing custom initialization logic — before and after their initialization methods are called.
##### What is the Initialiser Extension Point?

It is a special case of a bean post-processing (internal) which causes these initialisation methods to be called:
	- `@PostConstruct`
	- `init-method`

[[1 - Initialisation Phase (Part 2)#The Initialiser Extension Point|More details and picture]]

##### What is the BeanPostProcessor Extension Point? 

It is an important extension point in Spring, it can:

- Modify bean instances in any way
- Can modify a bean before and/or after initialisation
	- `BeforeInit` - runs before the initialiser
	- `AfterInit` - runs after the initialiser

[[1 - Initialisation Phase (Part 2)#After and Before Init - Extension Points|Diagram Flow]]

[[1 - Initialisation Phase (Part 2)#Interface|More Details]]

[[1 - Initialisation Phase (Part 2)#Example - CustomBeanPostProcessor|Example of CustomBeanPostProcessor]]

## The Use Phase

What happens in the "Use Phase" is:
- The context is gotten from somewhere
- You retrieve a Bean from the app. context
- Action the desired functionality

[[2 - Use and Destroy Phases#The Use Phase|Picture]]

### What happens when you retrieve the Bean from the Context?

#### Case 1: You just get your normal Bean

The bean is just your raw object, simply invoked directly.

[[2 - Use and Destroy Phases#Case 1 Your Bean is just a Bean|Picture]]

#### Case 2: Your Bean is a Proxy

If you require extra behaviour, Spring might decide to wrap your component in a **Proxy***.

This is the case with:
- Transactions
- Security
- Exception Handling

The **Proxy*** is created during the initialisation phase by a ***BeanPostProcessor***.

[[2 - Use and Destroy Phases#Case 2 Your Bean is a Proxy|Pictures]]

[[2 - Use and Destroy Phases#Proxy Power Example Transactions|Example of a Proxy]]

##### What kinds of Proxies are there?

- JDK (dynamic proxies)
- CGLib - Spring Boot will always prefer this one

[[2 - Use and Destroy Phases#Kinds of Proxies|Details/Picture]]

###### JDK vs CGLib Proxies

[[2 - Use and Destroy Phases#JDK vs CGLib Proxies|Flow Diagrams]]

## The Destruction Phase

The destruction phase starts when the context is closed or a shutdown hook was invoked.

[[2 - Use and Destroy Phases#The Destruction Phase|Picture]]

### How are Beans cleaned up?

- Any registered `@PreDestroy` methods are invoked
- Beans are released for the Garbage Collector to destroy

The destruction process ***cannot*** happen for prototype beans because we do not:
* When they will be destroyed
* When they are out of scope

***NOTE:*** This can ***ONLY*** happen for applications that have shut down gracefully, not if they have been killed or failed.