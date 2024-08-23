# What is a Component?

A component/bean is an annotated class that is managed by the Spring IoC/DI container.

# How are they defined?

Spring beans can be defined:
- Explicitly using `@Bean` methods inside a configuration class
- Implicitly using `@Component` and component-scanning

# What are Scopes?

Scopes define the life-cycle and visibility of a component within the DI container.

## What is the Default Scope for a component?

**Singleton** is the default scope in Spring. In this scope, the Spring IoC container creates exactly one instance of the bean, and this single instance is shared across the entire Spring application context.

[[3 - Bean Scopes#Bean Default Scope - "singleton"|Picture]]

#### Implications of the Singleton Scope

Be careful with the Singleton Beans because they will be accessed by multiple threads at the same time. Must make sure that they are handled with **Thread Safety**.

Three ways to achieve Thread Safety is:

- Use *Stateless* or *Immutable* Beans
- Use **synchronised** (which is difficult)
- Use a different scope
## How do you set a Scope?

Using the `@Scope()`, you can explicitly set the scope to singleton or use another scope.

## Bean Scope - "prototype"

Every time the Bean with scope "prototype" is referenced, a new instance is created.

[[3 - Bean Scopes#Bean Scope - "prototype"|Picture]]

## What are some Common Scopes?

Commonly used scopes are:

- Singleton - A single instance is used
- Prototype - A new instance is created each time the bean is referenced
- Session - A new instance is created once per user session (web environment only)
- Request - A new instance is created once per request (web environment only)

## Are there other Scopes?

[[3 - Bean Scopes#Other Scopes|Answer]]


# How to add Startup and Shutdown Behaviour?

By using `@PostConstruct` you can add behaviour during startup, and by using `@PreDestroy` you can add behaviour during shutdown.

***NOTE:*** Annotated methods can have any visibility but must take no parameters and only return void.

[[2 - Adding Startup and Shutdown Behaviours#`@PostConstruct` and `@PreDestroy`|More Details]]

## `@PostConstruct`

[[2 - Adding Startup and Shutdown Behaviours#`@PostConstruct`|Details/Picture]]

## `@PreDestroy`

***NOTE:*** `@PreDestroy` methods are called if app shuts down normally, **NOT** if the process dies or is killed.

[[2 - Adding Startup and Shutdown Behaviours#`@PostConstruct`|Details/Picture]]

# What are Stereotype Annotations?

They are annotations that are used to define and classify components, making them easier to manage and understand within the context of a Spring application.

When [[2 - Configuration#What is `@Component-Scanning(arg)`?|component scanning]], it also checks for annotations that are themselves annotated with `@Component`. 

That is because `@Component` is the most generic Stereotype annotation.

[[3 - Stereotype and Meta Annotations#Stereotype Annotations|Picture]]

## What are Predefined Stereotype Annotations?

[[3 - Stereotype and Meta Annotations#Predefined Stereotype Annotations|Picture]]

## What are Meta-annotations?

They are annotations that can be applied to other annotations. They provide metadata about the annotations they are applied to and are used to create custom annotations with specific behaviours and properties.

[[3 - Stereotype and Meta Annotations#Predefined Stereotype Annotations|Picture]]