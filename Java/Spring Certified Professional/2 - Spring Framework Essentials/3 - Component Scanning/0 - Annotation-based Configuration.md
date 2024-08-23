# Before - Explicit Bean Definition

Configuration is external to bean-class
- Helps with Separation of concerns
- Java-based dependency injection

![[Pasted image 20240806144052.png]]


# After - Implicit Configurations

- Annotation-based configuration within bean-class
- Component-scanning

![[Pasted image 20240806144418.png]]

# Usage of `@Autowired`

You can dependency inject using annotations at the:

- Constructor level
- Method level
- Field level

Constructor-injection (recommended practise):

![[Pasted image 20240806144714.png]]

Method-injection:

![[Pasted image 20240806144915.png]]

Field-injection (best not used due to being handled by framework and wont be available during testing):

![[Pasted image 20240806145006.png]]

# `@Autowired` Dependencies: Required or Not?

- Default behaviour: **REQUIRED** 

![[Pasted image 20240806145437.png]]

- Use *required* attribute to override default behaviour

![[Pasted image 20240806145505.png]]


# Constructor vs Setter DI

Spring does not care which is used.

![[Pasted image 20240806145843.png]]

Follow the same rules as standard Java:
- Prefer Constructor injection
- Be consistent across your project team

# Autowiring and Disambiguation

The below example shows an ambiguous situation, the `@Autowired` Constructor does not know which Component to inject. It is attempting to inject by type but both components are of the same type which produces the exception (blue area).

![[Pasted image 20240806150441.png]]

## Workaround

A workaround would be to autowire by name. We do that with the help with the `@Qualifier` annotation and then explicitly provide the name of the component to inject. 

![[Pasted image 20240806150816.png]]

## Autowiring Rules

- Look for unique bean of required type
- Use `@Qualifier`
- Try to find a matching bean by name

# Component Names

When not specified:
- Names are auto-generated
	- De-capitalised non-qualified class name by default
- ***Recommendation:*** never rely on generated names!

When specified:
- Allow disambiguation when 2 bean classes implement the same interface

# Delayed Initialisation

- Beans normally are created on startup when the application context is created.
- Lazy beans are first used when:
	- Dependency injected
	- `ApplicationContext.getBean` Method is used
- Useful if bean's dependencies are not available at startup

![[Pasted image 20240806152239.png]]

**NOTE:** Lazy beans are often misused.

# Annotation vs Java Config Syntax

![[Pasted image 20240806152348.png]]