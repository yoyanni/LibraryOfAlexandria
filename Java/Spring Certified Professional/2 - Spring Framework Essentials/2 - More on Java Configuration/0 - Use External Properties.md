# Setting property values

Consider the bean definition from the previous module:

![[Pasted image 20240806124426.png]]

Hard coding these properties is a **bad practise**. It is better practise to "externalise" these properties by using **property files**.

# Spring's Environment Abstraction

**Environment** bean represents loaded properties from the runtime environment.

Properties are derived from various sources, particularly in this order:

- JVM System Properties - `System.getProperty()`
- System Environment Variables - `System.getenv()`
- Java Property Files

Below you will see the **Environment** variable is being injected and the usage of `getProperty()`:

![[Pasted image 20240806125105.png]]

# Property Source

The Environment bean obtains values from **Property Sources**. 

As mentioned previously, there are multiple ways to provide property sources but when you are using **Java Property Files** you will need to use the annotation `@PropertySource` in your configuration file to provide those properties to your application.

**Note:** Env. variables and Java System Properties are always automatically populated.

![[Pasted image 20240806125703.png]]

Available resource prefixes are:
- `classpath:`
- `file:`
- `http:`

# Accessing Properties using `@Value`

Instead of passing/injecting the Environment (provided by Spring) directly into the data source, you can use the annotation `@Value` to inject values from the environment.

**Note:** When using the "$" we want to resolve something from the environment.

![[Pasted image 20240806135255.png]]