Inspired by the Expression Language used in Spring WebFlow and based on Unified Expression Language used in JSP (Java Server Pages) and JSF (Java Server Faces).

It is used/extended by other Spring-based frameworks.

# SpEL Examples - Using `@Value`

![[Pasted image 20240806140311.png]]

The 
- The `#` expects an expression
- `systemProperties` is an implicit variable provided by Spring

The evaluation of the expression then gets injected into the variable.

# Accessing Properties

You can access properties via the *environment* and a conversion may occur implicitly if it is required.

Properties are Strings by default.

![[Pasted image 20240806142543.png]]

**NOTE:** 
- These are equivalent and implicitly convert the String into an Int.
- When using the "$" we want to resolve something from the environment. 

# Fallback Values

You can provide a fall-back value by doing the below:

![[Pasted image 20240806143026.png]]

For SpEL, use the **Elvis** operator `?:` :

![[Pasted image 20240806143113.png]]

# Summary

EL attributes can be:
- Spring beans
- IMplicit references
	- Spring's environment, systemProperties, systemEnvironment available by default
	- Others depending on context
- SpEL allows to create custom functions and references
	- Widely used in Spring projects
		- Spring Security and WebFlow
		- Spring Batch and Integration
	- Each may add their own implicit references
