# How do we test Spring/Spring Boot?

JUnit 5 is used to test the applications.

## What is Spring's Support for Testing?

Spring has a rich testing support which is based on the `TestContext` Framework. It defines a `ApplicationContext` for your tests and the `@ContextConfiguration` annotation helps with defining the Spring configuration.

**NOTE:** All the above is packaged as a separate module called `spring-test.jar`.

# What are the components of JUnit 5?

The 3 components are:
- **JUnit Platform**
	- The Foundation for testing on the JVM
- **JUnit Jupiter**
	- An extension model for writing tests and extensions in JUnit 5
- **JUnit Vintage**
	- A TestEngine for running JUnit 3 & 4 on the platform

# What are some of the Annotations in JUnit 5?

Some of the old Annotations are:
- `@BeforeEach`
- `@BeforeAll`
- `@AfterEach`
- `@AfterAll`
- `@Disabled`

[[0 - JUnit 5#JUnit 5 New Programming Models|Have a look at the old annotations!]]

New Annotations were introduced in JUnit 5:
- `@DisplayName`
- `@Nested`
- `@ParameterizedTest`

**NOTE:** Do keep in mind that JUnit 5 ignores all JUnit 4 annotations.

# Example of a JUnit 5 Test

[[0 - JUnit 5#Writing Test - JUnit 5 Style|Example]]

# What is done with a Unit Test?

With Unit Testing, you do not need Spring to perform, just JUnit 5. What happens is:
- The testing of one unit of functionality
- Keeping dependencies minimal
- Isolated from the environment (including Spring)
- Uses Stubs and/or Mocks for dependencies

# What is done with an Integration Test?

With Integration Testing, you do need Spring because you are testing the interactions of all the working units. Obviously unit testing must be performed to know they work in isolation first.

What happens is:
- Out-of-container testing, which means not running the app server
- Infrastructure is scaled down
- Test Containers
	- Use Lightweight instances of common databases

[[1 - Write Integration Tests using Spring#Example|Integration Testing Example]]


# What is Spring's Support for Testing?

Spring has a rich testing support which is based on the `TestContext` Framework. It defines a `ApplicationContext` for your tests and the `@ContextConfiguration` annotation helps with defining the Spring configuration.

**NOTE:** All the above is packaged as a separate module called `spring-test.jar`.

## How is Spring and JUnit connected?

With the help of JUnit 5's `@ExtendWith` annotation and Spring's extension point `SpringExtension.class`. The `@ContextConfiguration` annotation is also set up with the config file.

[[1 - Write Integration Tests using Spring#Using Spring's Test Support|Example]]

%% Addition from Udemy exam practise %%
### The superseded `@RunWith` and SpringRunner.class

Instead of using `@RunWith(SpringRunner.class)`, you use `@ExtendWith(SpringExtension.class)` in JUnit 5 tests.

%% addition end %%
### Is there a faster/easier way to connect them?

Yes, there is! By using `@SpringJUnitConfig` composed annotation, it combines `@ExtendWith` from JUnit and  `@ContextConfiguration` from Spring.

[[1 - Write Integration Tests using Spring#`@SpringJUnitConfig`|Example]]

## How to configure/setup Tests classes and methods?
### Is there an alternative way of Injecting a component for testing?

[[1 - Write Integration Tests using Spring#Alternative Autowiring for Tests|Yes, using method autowiring!]]

### Is there a more specific way of setting the config for a particular test?

Yes, by including the `@Configuration` and `@Import` annotations at the bottom of the test file. You can then override a specific bean (if already available) for a specific test/test file.

[[1 - Write Integration Tests using Spring#Including Configuration as an Inner Class|Example]]

### How can I add Custom Properties for testing?

[[1 - Write Integration Tests using Spring#Test Property Sources|Answer]]

### How can we set a Profile for Tests?

By using the `@ActiveProfile` annotation you can set a profile or more in a particular test class.

The components that are labelled with the profile (or not) will be included. 

[[2 - Configure Tests using Spring Profiles and Working with Databases#Activating Profiles for a Test|Example]]

#### Setting Profiles - Java Config

[[2 - Configure Tests using Spring Profiles and Working with Databases#Profile Activation with JavaConfig|Example]]

#### Setting Profiles - Annotations

[[2 - Configure Tests using Spring Profiles and Working with Databases#Profile Activation with Annotations|Example]]

### How to set up Testing with Databases?

Since a common practise is integration testing against an SQL db, an ***in-memory*** db is usually used since it is easy to set up and no prior installs are required.

A common requirement to do so is to populate the db before running tests and we can do that with the help of the `@Sql` annotation.

[[2 - Configure Tests using Spring Profiles and Working with Databases#`@Sql` Examples|Examples]]

#### What options are there for the `@Sql` annotation?

[[2 - Configure Tests using Spring Profiles and Working with Databases#Options|Options]]

## What happens when there are multiple test methods within a file?

Nothing really, both tests share the same cached Application Context and use the same Bean that has been injected.

[[1 - Write Integration Tests using Spring#Multiple Test Methods|Picture]]

### What is `@DirtiesContext`?

[[1 - Write Integration Tests using Spring#DirtiesContext|Answer]]

# What are the benefits of testing with Spring?

The benefits are:
- No need to deploy to an external container to test application functionality
	- Runs quickly within IDE
	- Supports Continuous Integration testing
- Allows reuse of config between environments
	- Application config is reused
	- Infrastructure config is environment-specific
		- Databases



