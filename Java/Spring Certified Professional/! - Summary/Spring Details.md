
* Java Configuration
	* App classes
	* Config classes
		* (Cross Reference) Method Invocation (DI?)
		* Providing Arguments (DI?)
	* Creating and Using the app
		* Accessing a Bean Programmatically
		* What is a Bean? (needs adding)
* Application Context
	* Representation of DI container
	* Created in any environment
* Multiple Config Classes
	* Java Config with DI (Providing Arguments?)
	* Tramp data
- Bean Scopes
	- Singleton scope
		- `@Scope`
		- Implications
			- Thread Safety
	- Prototype Scope
	- Common Scopes
	- Other Scopes

More Java Config:
- Property Files
	- Environment Bean - Spring's Environment Abstraction
		- JVM System Properties - `System.getProperty()`
		- System Environment Variables - `System.getenv()`
		- Java Property Files
	- Property Sources
	- `@Value` (SpEL)
- Profiles - Bean Grouping
	- Definition of Profiles
		- Class level
		- Method level
	- Activate Profiles
	- Property Source Selection
- SpEL
	- Examples
	- Accessing Properties
	- Fallback Values

Component Scanning:
- Annotation-based Config
	- explicit (java) vs implicit (component-scanning) config
	- `@Autowired`
		- Constructor-injection
		- Method-injection
		- Field-injection
		- required=false||true
	- Constructor vs Setter DI
	- Autowiring and Disambiguation
		- `@Qualifier`
		- Autowiring Rules
		- Component Naming
	- Delayed Initialisation
- Config choices
	- Autowiring Constructor Rules
	- About COmponent Scanning
		- Best Practises
	- When to use Java vs Annotation Config
- Startup and Shutdown Behaviour
	- `@PostConstruct` and `@PreDestroy`
	- `@Bean` lifecycle method attributes
	- JVM Shutdown Hook
- Stereotype and Meta Annotations
	- Predefined Stereotype annotations
		- Service
		- Repository
		- Controller
		- etc..
	- Meta-Annotations



# How do I start the Spring Application?

By creating the context with `SpringApplication.run()` and supplying it with the config class, we now have the DI container reference.

With the container, we can `getBean()` which will fetch the Bean and then we can execute.

![[Pasted image 20240805162333.png]]
## Accessing a Bean Programmatically

As we saw above, we were able to retrieve a Bean but there are multiple ways you can achieve this.

![[Pasted image 20240805162925.png]]
### What is a Bean?

A "bean" is a reusable, self-contained component that encapsulates data and behaviour.

In the Spring Framework, beans are objects that form the backbone of a Spring application. They are managed by the Spring IoC (Inversion of Control) container, which takes care of instantiating, configuring, and assembling beans.

# How to create an Application from Multiple files?

