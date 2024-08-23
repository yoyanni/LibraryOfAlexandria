The Application Context is the heart of Spring, it is a dependency injection container that will take care of the lifecycle of the components.

- Spring application context represents Spring DI container
	- Spring beans are managed through the application context
- Spring application context can be created in any environment
	- Standalone app
	- Web app
	- JUnit test

# Application Context Example

## Creating Application Context in a System Test

How to explicitly bootstrap the application context in a test environment:

![[Pasted image 20240805164035.png]]

* `@BeforeEach` - Executes before each test in the class