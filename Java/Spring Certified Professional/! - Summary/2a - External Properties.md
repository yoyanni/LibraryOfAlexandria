
# Why use External Properties?

Harding coding details such as, database username and password, into your direct code is **bad practise**. It is better practise to set these properties within **property files**.

[[0 - Use External Properties#Setting property values|Picture]]
# What is the Spring's Environment Abstraction?

The **Environment** bean represents loaded properties from the runtime environment.

Properties are derived from various sources, particularly in this order:

- JVM System Properties - `System.getProperty()`
- System Environment Variables - `System.getenv()`
- Java Property Files

## How to use?

[[0 - Use External Properties#Spring's Environment Abstraction|Picture]]

## Is there alternative? Yes, `@Value`.

Instead of passing/injecting the Environment (provided by Spring) directly into the data source, you can use the annotation `@Value` to inject values from the environment.

**Note:** When using the "$" we want to resolve something from the environment. 

[[2 - Spring Expression Language (SpEL)#SpEL Examples - Using `@Value`|What is the "$"?]]

# What is a Property Source?

The Environment bean obtains values from **Property Sources**. 

There are multiple ways to provide property sources but when you are using **Java Property Files** you will need to use the annotation `@PropertySource`.

**Note:** Env. variables and Java System Properties are always automatically populated.

Available resource prefixes are:
- `classpath:`
- `file:`
- `http:`

[[0 - Use External Properties#Property Source|Picture]]

