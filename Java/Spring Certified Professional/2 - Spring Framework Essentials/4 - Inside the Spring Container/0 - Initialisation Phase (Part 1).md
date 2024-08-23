
# Container Lifecycle

Spring Bean container runs through three distinct phases:
- Initialisation - the application context is created and so is the components (beans)
	- Spring Beans are created
	- Dependency Injection occurs
- Usage - retrieving beans from app context (executing business logic)
	- Beans are available for use in the application
- Destruction - when the app context is closed
	- Beans are released for Garbage Collection

# Lifecycle of a Spring Application Context

## The Initialisation Phase

When a context is created, the initialisation phase has completed.

![[Pasted image 20240806183943.png]]

In more detail, these are the steps that occur:
- **Step A:** Load & Process Bean Definitions
- **Step B:** Perform Bean Creation

### Bean Initialisation Steps

![[Pasted image 20240806184542.png]]

### Step A: Load & Process Bean Definition

1. The `@Configuration` classes are processed
	1. And/or `@Component` annotated classes are scanned for
2. Bean definitions added to a **BeanFactory**
	1. Each indexed under its id and type
3. Special **BeanFactoryPostProcessor** beans invoked
	1. Can modify the definition of any bean

![[Pasted image 20240806185041.png]]

### Load Bean Definitions

![[Pasted image 20240806185328.png]]

### BeanFactoryPostProcessor

#### Internal Extension Point

* Application transformation to bean definitions
	* Before the objects are created
* Several useful implementations provided in Spring
	* Reading properties
	* Registering a custom scope
* Write your own BeanFactoryPostProcessor
	* Implement BeanFactoryPostProcessor interface

#### Most Common Example

An example of a BeanFactoryPostProcessor is **PropertySourcesPlaceholderConfigurer** which evaluates *Property Sources*.

***NOTE:*** Recall the `PropertySource(...)` and `@Value` annotations

![[Pasted image 20240806190448.png]]

#### Declaration

Simply create as a bean in the usual way, define using the `@Bean` method.

![[Pasted image 20240806190617.png]]

#### Considerations

**BeanFactoryPostProcessor** is an internal bean invoked by Spring, which is not your code. It needs to run before any beans are created.

The use of `static` is recommended for `@Bean` methods.