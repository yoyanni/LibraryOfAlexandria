# `@PostConstruct` and `@PreDestroy`

Adds behaviour at startup and shutdown

![[Pasted image 20240806163110.png]]

***NOTE:*** Annotated methods can have any visibility but must take no parameters and only return void.

Beans are created in the usual way:
- Returned from @Bean methods
- Found and created by the component-scanner

Spring then invokes these methods ***automatically*** during the bean-creation process.

These are ***NOT*** Spring annotations:
- Defined by JSR-250, since Java 6
- Supported by Spring and by Java EE


# `@PostConstruct`

Called after setter injections are performed.

![[Pasted image 20240806163747.png]]

# `@PreDestroy`

Called when a ***ConfigurableApplicationContext*** is closed.
- Useful for releasing resources & cleaning up
- Not called for prototype beans

***NOTE:*** `@PreDestroy`  methods are called if app shuts down normally, **NOT** if the process dies or is killed.

![[Pasted image 20240806164118.png]]


# Lifecycle Method Attributes of `@Bean` Annotation

Alternatively, `@Bean` has options to define these life-cycle methods.

![[Pasted image 20240806164319.png]]

When should you use the `@PostConstruct`/`@PreDestroy`?

- Use the annotations for your own classes
- Use Lifecycle Method attributes of `@Bean` for classes you didnt write and cant annotate.

# Use a JVM Shutdown Hook

Shutdown Hooks automatically run when JVM shuts down.

![[Pasted image 20240806164924.png]]

`SpringApplication.run` method does this automatically and returns a `ConfigurableApplicationContext`.

