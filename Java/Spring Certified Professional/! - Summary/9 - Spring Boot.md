# What is Spring Boot?

Spring boot is an extension of the Spring framework/platform. It simplifies the process of building production-ready applications by providing a set of tools and conventions for quick setup and deployment. Spring Boot eliminates much of the boilerplate configuration and reduces the complexity of developing Spring applications

# Why Spring Boot?

- A radically fast getting-started experience for Spring Development
- Opinionated out of the box but easy to opt out
- Provides a range of common features
	- Embedded servers
	- Metrics
	- Health Checks
	- Configuration
	- Containerization

# How are the Dependencies managed?

[[0 - Spring Boot Feature Introduction#How do you manage Dependencies?|Managing Dependencies]]

## What is the Parent POM?

It defines versions of key dependencies:
- It uses a **dependencyManagement** section internally
- Through `spring-boot-dependencies` as a parent

It also defines Maven plugins and sets up the Java version.

[[0 - Spring Boot Feature Introduction#Spring Boot Parent POM|Snippet]]

## What are the Spring Boot "Starter" Dependencies?

It is an easy way to bring in multiple coordinated dependencies which include "Transitive" dependencies.

[[0 - Spring Boot Feature Introduction#Spring Boot "Starter" Dependencies|Picture]]

## What is the Test "Starter" Dependencies?

`spring-boot-starter-test`

[[0 - Spring Boot Feature Introduction#Test "Starter" Dependencies|Snippet]]

## What about some other "Starter" Dependencies?

- `spring-boot-starter-jdbc`
- `spring-boot-starter-data-jpa`
- `spring-boot-starter-web`
- `spring-boot-starter-batch`

[[0 - Spring Boot Feature Introduction#Many Starters are available out of the box|More details]]

# Spring boot has Auto-Configuration!

## What is the Auto-Configuration?

Spring Boot automatically creates beans it thinks you need based on some conditions.

## How is it enabled?

If the `@EnabledAutoConfiguration` annotation is on a Spring Java configuration class (`@SpringBootConfiguration` which simply extends `@Configuration`) enables auto-configurations

[[0 - Spring Boot Feature Introduction#Auto-configuration enabled by `@EnableAutoConfiguration`|Snippet]]

## Is there an easier way to achieve the Auto-configuration?

Yes, by using `@SpringBootApplication`, you set the following annotations:
- `@EnableAutoConfiguration`
- `@SpringBootConfiguration`
- `@ComponentScan`

[[0 - Spring Boot Feature Introduction#Shortcut `@SpringBootApplication`|Snippet and details]]

## How does Auto-Configuration actually work?

It works by extensively using Spring-provided [[#What are Auto-Configuration Classes?|auto-configuration classes]] which are annotated with `@Configuration` ***with conditions***.

[[1 - Using Auto Configuration#How Does Auto-Configuration Work?|Conditions include...]]
### Examples of Auto-configuration for DataSource, JdcbTemplate

[[1 - Using Auto Configuration#Examples of Auto-Configuration DataSource, JdbcTemplate|Examples]]
### How can you conditionally create Beans?

With the use of `@Conditional` annotations which there are many of, such as:
- `@ConditionalOnBean`
- `@ConditionalOnMissingBean`
- `@ConditionalOnClass`
- `@ConditionalOnMissingClass`
- many more...

### What are Auto-Configuration Classes?

They are classes that are annotated explicitly with `@AutoConfiguration` which are then processed by the `@EnableAutoConfiguration`.

[[1 - Using Auto Configuration#What are Auto-Configuration Classes?|Example of Autoconfiguration classes]]

[[1 - Using Auto Configuration#Where are these Auto-Configuration classes specified?|Some more details]]

#### Where are these Classes specified?

`@EnableAutoConfiguration` processes autoconfiguration 

## How can I control Spring Boot Auto-Configuration?

### Spring Boot is designed to make overriding easy you can achieve this by...
#### Setting some of Spring Boot's properties

There are hundreds of pre-defined properties which are used by Spring-provided `@ConfigurationProperties`/AutoConfiguration classes.

[[2 - Override Default Configuration#Example External Database|Example of an External Database]]

[[2 - Override Default Configuration#Example Controlling Logging Level|Example of an Controlling Logging Level]]

##### Where to Define Spring Boot Specific Properties

The ***application.properties/yml*** is best used to define Spring Boot properties as they can get read early enough to affect all autoconfiguration possibilities.

In some cases, you are not even able to set specific properties (logging levels) in other property files.

#### Explicitly defining beans yourself (so Boot does not have to)

When you explicitly define beans it disables auto-configured ones. Generally the auto-configuration is based on bean type and not the name.

[[2 - Override Default Configuration#2 Explicitly define beans yourself|Example]]

#### Explicitly disabling some auto-configuration

You can disable some auto-configuration classes via:
- Annotations
	- `@EnableAutoConfiguration` and `@SpringBootApplication` both have the `exclude` attribute
- Configurations
	- `spring.autoconfigure.exclude` in application.properties file

#### Change dependencies or their versions

[[2 - Override Default Configuration#4a Override Dependency Versions|Details - 1]]
[[2 - Override Default Configuration#4a Override Dependency Versions|Details - 2]]
[[2 - Override Default Configuration#4a Override Dependency Versions|Details - 3]]

## How to run Startup Tasks after the context is initialised?

They are specifically designed for tasks that need to happen after the application has fully started, such as:

- **Initializing resources** that depend on the entire application context
- **Processing command-line arguments**
- **Running scheduled tasks** immediately upon application startup.
- **Performing one-time startup logic**

[[2 - Override Default Configuration#`CommandLineRunner` and `ApplicationRunner`|Details]]
# How are Properties handled?

## What is the `application.properties` file?

You can add properties to the file that can supplement the autoconfiguration or override it.

[[2 - Spring Boot - Getting Started#application.properties|Example of the file]]

### How are properties organised in Spring Boot?

Spring Boot looks for `application.properties` and loads all the properties automatically. They are available to the [[2a - External Properties#What is the Spring's Environment Abstraction?|Environment]] and [[2a - External Properties#Is there alternative? Yes, `@Value`.|@value]] annotation.

Spring Boot will also look for profile-specific properties in files following `application-{profile}.properties` convention.

[[0 - Spring Boot - A Closer Look#Profiles and `application.properties`|Example of convention]]

#### Where does Spring Boot look for the file?

- `/config` sub-directory of the working directory
- The working directory
- `config` package in the classpath
- classpath root
## Is there an alternative for the `application.properties` file?

Yes, you can use YAML with the file `application.yml`. Spring Boot supports YAML configuration which is more concise with a indented text format.

Spring boot looks for the file in the same [[#Where does Spring Boot look for the file?|locations]] as application.properties.

[[0 - Spring Boot - A Closer Look#YAML Support|Picture]]

### How are Profiles handled with YAML?

[[0 - Spring Boot - A Closer Look#Profiles and `application.properties`|Answer]]

## What is the precedence for selecting properties?

[[0 - Spring Boot - A Closer Look#Precedence|Answer]]

## What is the problem with Property Placeholders?

When trying to access properties you have to repeat the prefix ([[0 - Spring Boot - A Closer Look#The Problem with Property Placeholders|example]]) which is cumbersome.

### What is the solution?

The solution is to use the annotation `@ConfigurationProperties(prefix="")` in a dedicated bean. This way, you set the prefix of the properties once and and declare them within the bean that then automatically sets them from corresponding properties.

[[0 - Spring Boot - A Closer Look#Use `@ConfigurationProperties`|Example of the dedicated bean]]

#### How is the `@ConfigurationProperties` enabled?

`@EnableConfigurationProperties(..)` or `@ConfigurationPropertiesScan` is applied to the application class but the property config file (annotated with `@ConfigurationProperties(..)`) must also be annotated with `@Component` since it must be a bean/component that can be scanned.

[[0 - Spring Boot - A Closer Look#Enable `@ConfigurationProperties`|Example of configuration]]

#### What is "relaxed binding" on `@ConfigurationProperties`

[[0 - Spring Boot - A Closer Look#Relaxed Binding on `@ConfigurationProperties`|Answer]]
# What is a Fat JAR?

A "fat" JAR contains all dependencies including Tomcat for web application. This allows you to run the JAR directly with `java -jar`.

## How can I create the Fat JAR?

You can use both Maven or Gradle plugins to create the fat JAR.

[[1 - Package and Integration Testing#Spring Boot Plugin - Maven|Example of Maven plugin]]

You actually create two JAR files:
- `my-app.jar` - "fat" JAR
- `my-app.jar.original` - your-application-details-only JAR

[[1 - Package and Integration Testing#Packaging Result|Packaging Results]]

# How can I test with Spring Boot?

Instead of using [[6 - Testing#Is there a faster/easier way to connect them?|@SpringJUnitConfig]], you can use the alternative `@SpringBootTest`. 

You can pass the application class to the annotation to allow to use the same configurations and defaults.

[[1 - Package and Integration Testing#Test `@SpringBootTest`|Code Snippet]]

## How does it work?

`@SpringBootTest` searches for the `@SpringBootConfiguration` annotation and creates the application context for the tests.

You can avoid explicitly providing a app class if the `@SpringBootConfiguration` is in a package above the test file.

[[1 - Package and Integration Testing#Testing `@SpringBootConfiguration`|Example]]

# How can I get started with Spring Boot?

You can use the web-based tool **Spring Initializr** that simplifies the setup and creation of new Spring Boot projects. It provides a user-friendly interface that allows developers to quickly generate a new Spring Boot application with preconfigured dependencies, project structure, and build files (Maven or Gradle).

[[2 - Spring Boot - Getting Started#What is its value?|More details on Spring Initializr]]

## What are the three files you need to start?

To get a running Spring application:
- pom.xml - [[2 - Spring Boot - Getting Started#Maven Descriptor - 1|Maven POM file example]] | [[2 - Spring Boot - Getting Started#Maven Descriptor - 2|Maven POM  continued]]
	- Setup Spring Boot and other dependencies
- application.properties - [[2 - Spring Boot - Getting Started#application.properties|Example of the file]]
	- General Config
- Application Class - [[2 - Spring Boot - Getting Started#Application Class|Example of the class]]
	- Launcher

Then by packaging the application, generating the "fat" JAR and running it with the command `java - jar`, you can get the application to run.

[[2 - Spring Boot - Getting Started#Putting it all together|Example]]

