# What is the Spring Boot Testing Framework?

It provides a set of annotations and utilities for testing which built on top of the Spring Testing Framework. Some of the annotations are:
- `@SpringBootTest`
- `@WebMvcTest`
- `@DataJpaTest`
- `@MockBean`
- [[0 - Spring Boot Testing#What is Spring Boot Testing Framework?|and more...]]

# Starter Dependency

The dependency `spring-boot-starter-test` is already included with the default project. It adds the following dependencies:
- JUnit 5
- Spring and Spring Boot Test - Testing Annotations
- AssertJ - A fluent assertion library
- Hamcrest - a library of matcher
- Mockito - A Java mocking framework
- JSONassert - An assertion library for JSON
- JsonPath - XPath for JSON

# How can you do Integration Testing with Spring Boot?

It automatically searches for `@SpringBootConfiguration`, an alternative is the `@ContextConfiguration` annotation to create the context for testing.

Use:
- `@SpringBootConfiguration` when integration testing
- `@ContextConfiguration` when slice testing

Provides support for different `webEnvironment` modes, modes such as:
- RANDOM_PORT - start the web container at a random port that is free
- DEFINED_PORT - start the web container at a specified port
- MOCK - web container wont be started but components are available
- NONE

The embedded server is started by the testing framework when RANDOM_PORT or DEFINED_PORT are used.

Finally, it also auto-configures a `TestRestTemplate`.

### What is the TestRestTemplate?

It is an alternative of RestTemplate suitable for integration tests, it:
- Takes a relative path (instead of an absolute)
- Fault Tolerant: it does not throw an exception when it receives an error such as 404 from the server
- Configured to ignore cookies and redirects

You can also customise by using the **RestTemplateBuilder**.

[[0 - Spring Boot Testing#Example with TestRestTemplate|Example of TestRestTemplate]]

[[0 - Spring Boot Testing#Example with TestRestTemplate Test code|Example of TestRestTemplate continued]]


# What is MockMVC?

It is a MVC Testing that is a part of the Spring Framework.

The goal is to provide first-class support for testing Spring MVC code, it:
- Processes requests through [[12 - Spring MVC & Web#The heart of MVC Dispatcher Servlet|Dispatcher Servlet]]
- Does **not** require running Web container to test

[[1 - Perform MockMVC Testing#Example MockMvc Test|Example of a MockMVC Test]]

## What imports are required?

[[1 - Perform MockMVC Testing#Static Imports|These STATIC imports]]

### What does the `MockMvcRequestBuilder` do?

It can perform:
- Standard HTTP operations
- `fileUpload`also supported
- Argument is usually a URI template string
- Returns a [[1 - Perform MockMVC Testing#`MockHttpServletRequestBuilder` - Examples|MockHttpServletRequestBuilder]] instance (for chaining other methods)

### What are the Static methods you can chain along?

[[1 - Perform MockMVC Testing#Static Methods|Answer]]

### What static methods are there for `MockMvcResultMatchers`?

[[1 - Perform MockMVC Testing#`MockMvcResultMatchers` - Static Methods|Answer]]
# Why is MockMVC needed?

[[1 - Perform MockMVC Testing#Need for Spring MVC Testing|How can you verify the below?]]
# How to use MockMvc?

By using the method `perform(arg)`, it dictates the action it is going to take. The argument could be:
- `get()`
- `put()`
- `post()`

These are provided by one of the static imports we mentioned above, specifically `MockMvcRequestBuilders`

You then append method from `MockHttpServletRequestBuilders` to further test.

[[1 - Perform MockMVC Testing#Testing RESTful Controllers|Example]]

The `perform()` method returns a `ResultActions` object which can chain `andExpect()` methods together for testing. 

The `content()` and `jsonPath()` methods (from `MockMvcResultMatchers`) go inside the `andExpect()` methods

[[1 - Perform MockMVC Testing#Testing RESTful Controllers|Example of these methods]]

## How can you debug?

- `andDo()` performs action on `MvcResult`
- `print()` sends the `MvcResult` to output stream
- or use `andReturn()` to get the `MvcResult` object

[[1 - Perform MockMVC Testing#Printing Debug Information|Example of debugging]]

# What is "Slice" Testing?

Performs isolated testing within a slice of an application, for example:
- Web Slice
- Repository Slice
- Caching Slice

The dependencies need to be mocked to do so.

# What happens when you are Web Slice testing with `@WebMvcTest`?

Full auto-configuration is disabled and instead apply only configurations relevant to MVC tests. The MVC testing framework is auto-configured along with the **MockMvc** bean and optionally Spring Security as well.

`@WebMvcTest` is used in combination with `@MockBean` for mocking its dependencies.

[[2 - Perform Slice Testing#Code Example with `@WebMvcTest`|Example of @WebMvcTest]]
[[2 - Perform Slice Testing#Complete Example|Example of @WebMvcTest continued]]


## `@MockBean` vs `@Mock` for Dependencies

`@Mock`:
- From the Mockito Framework
- Use it when Spring context is not needed
	- Can be applied to: %% Addition from Udemy exam practise %%
		- Fields
		- Parameters

`@MockBean`:
- From Spring Boot Framework
- Use it when Spring context is needed
	- Can be applied to: %% Addition from Udemy exam practise %%
		- Classes
		- Fields
- Creates a new mock bean when it is not present in the context or replaces it with a mock bean when it is present


# What happens when you are Repository Slice Testing with `@DataJpaTest`?

Should be used when a test focuses only on JPA components since it only loads `@Repository` beans and excludes the other `@Components`.

`TestEntityManager` is auto-configured which is an alternative to `EnitityManager` for use in JPA tests. It provides a subset of `EntityManager` methods, such as:
- Those which are useful for tests
- Helper methods for common testing tasks such as:
	- `persistFlushFind()`
	- `persistAndFlush()`

It uses an embedded in-memory database which:
- Replaces any explicit or auto-configured DataSource
- The `@AutoConfigureTestDatabase`annotation can be used to override these settings

