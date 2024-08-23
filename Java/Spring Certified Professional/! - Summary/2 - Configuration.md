# How to define your application configuration?

There are two ways:

- Java-based - Explicitly defining the beans required in a separate config file [[0 - Annotation-based Configuration#Before - Explicit Bean Definition|(example)]]
- [[#Avoid "Tramp Data"|Annotation-based]] - Implicitly defining with annotations [[0 - Annotation-based Configuration#After - Implicit Configurations|(example)]]

When using the explicit approach (Java-based), there are two ways of setting up the configuration:

- Method Invocation (Cross Reference) 
- Providing Arguments

[[0 - Quick Start with Java Configuration#Configuration Class with Dependencies|Examples of both]]

# How to use Multiple config files?

With the help of `@Import`, you can use multiple files to adhere to:

- Separation of Concerns principle
	- Keep related Beans within the same `@Configuration`
- Best Practise - Separate "application" & "infrastructure"
	- Infrastructure often changes between environments

[[2 - Handling Multiple Configurations#Creating an Application Context from Multiple files|Pictures]]

# Avoid "Tramp Data"

[[2 - Handling Multiple Configurations#But Avoid "Tramp Data"|About Tramp Data]]

# What is Constructor Injection?

It is another way to pass dependencies to the main configuration file with the help with `@Autowired`.

[[2 - Handling Multiple Configurations#Java Configuration with Dependency Injection|Picture]]
## Usage of `@Autowired`

You can dependency inject using the annotation at the:

- Constructor level (recommended)
	- [[1 - Configuration Choices#Autowiring Constructors|Constructor Config options]]
- Method level
- Field level
	- [[0 - Annotation-based Configuration#Usage of `@Autowired`|Why you shouldn't use Field Level injection]]

### Are the `@Autowired` dependencies required or not?

By default, they are required but if the dependency isn't found, it will through an exception.

The `required` attribute is used to override default behaviour.

[[0 - Annotation-based Configuration#`@Autowired` Dependencies Required or Not?|Pictures]]

### What is Disambiguate when trying to inject?

It is when the `@Autowired` constructor does not know what to inject because the dependency is referenced by type and when you have multiple same types, this issue occurs.

[[0 - Annotation-based Configuration#Autowiring and Disambiguation|Picture]]

The work around would be to use the name. The `@Qualifier` annotation helps with that along with the explicit naming of the dependency.

[[0 - Annotation-based Configuration#Autowiring and Disambiguation|Picture]]

#### Be careful with Component Names.

When not specified:
- Names are auto-generated
	- De-capitalised non-qualified class name by default
- ***Recommendation:*** never rely on generated names!

When specified:
- Allow disambiguation when 2 bean classes implement the same interface

## Autowiring Rules

- Look for unique bean of required type
- Use `@Qualifier`
- Try to find a matching bean by name

## When are Components/Beans initialised?

Beans normally are created on start-up when the application context is created.

### What does the `@Lazy` Annotation do?

It defines a delayed (lazy) initialisation of a component/bean by using the `@Lazy` annotation.
#### What is Delayed Initialisation?

It is when a component is created after the above mentioned timing, it is useful when the component's dependencies are not available at start-up. 

Lazy Components are used in this order:
- Dependency injected
- `ApplicationContext.getBean` Method is used

# Which is better? Constructor vs Setter DI?

[[0 - Annotation-based Configuration#Constructor vs Setter DI|Spring can work with both!]]

# What is `@Component-Scanning(arg)`?

It is used when we want to scan all the components in a package `(arg)` that have the annotation `@Component`.

[[0 - Annotation-based Configuration#After - Implicit Configurations|Picture]]

## When are they scanned?

Components are scanned at start-up along with the JAR dependencies which could result in slower start-up times if too many files are scanned.

## Example of Practises

The general idea is to avoid scanning more files than necessary.

Really Bad:
	- `@ComponentScan({"org", "com"})`
- Still bad:
	- `@ComponentScan("com")`
- OK:
	- `@ComponentScan("com.bank.app")`
- Optimised:
	- `@ComponentScan({"com.bank.app.repository", "com.bank.app.service", "com.bank.app.controller"})`

# When to use Java vs Annotation Config?

People use the Annotation config when:
- Spring does not give you any other choice.
- When you need Stereotype annotations

Java config is chosen when:
- When it is desired or required to keep beans decoupled from Spring (Legacy code)
- When managing configurations in a single logical location is an important issue
- Can be used from all classes

# What are the Lifecycle method attributes of `@Bean`?

The annotation has options to define life-cycle methods:
- `initMethod=""`
- `destroyMethod=""`

[[3 - Components#How to add Startup and Shutdown Behaviour?|Annotations that do the same thing]]


# Configuration Lifecycle

[[1 - Initialisation Phase (Part 2)#Configuration Lifecycle|Picture]]

