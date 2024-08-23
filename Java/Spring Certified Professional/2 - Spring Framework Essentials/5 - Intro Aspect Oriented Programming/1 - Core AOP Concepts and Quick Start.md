
# Core AOP Concepts

- Join Point
	- A point in the execution of a program such as a method call of exception thrown
		- Spring AOP only allows method call points
- Pointcut
	- An expression that selects one or more Join Points
- Advice
	- Code to be executed at each selected Join Point
- Aspect
	- A module that encapsulates pointcuts (where) and advice (what)
- Weaving
	- Technique by which aspects are combined with the main code

## Proxy

AOP Proxy:
- An **enhanced** class that stands in place of your original
	- With extra behaviour (Aspect) added (woven) into it


# AOP - Quick Start

![[Pasted image 20240807121434.png]]

How can you use AOP to meet it?

## An Application Object Whose Properties Could Change

![[Pasted image 20240807121638.png]]

Any time the set method are triggered we want to log a message.

## Implement the Aspect

![[Pasted image 20240807122358.png]]

- `@Aspect` is not a part of Spring but of ***AspectJ***
- The method defines the **Advice** (What to execute)
- `@Before` annotation and the expression within it defines the **Pointcut** (Where to execute)
	- The expression defines all the setter methods and only one argument 

## Configure Aspect as a Bean

You must enable the use of Aspects by using the annotation `@EnableAspectJAutoProxy`.

We use the AspectJ configuration style but for **weaving** we will be using **Spring AOP (proxies)**.

![[Pasted image 20240807123032.png]]

## Include the Aspect Config

![[Pasted image 20240807123228.png]]

## Test the Application

![[Pasted image 20240807123429.png]]

`@Qualifier` annotation helps choose which cache will be injected automatically into the field with the help of `@Autowired`.

When the `setCacheSize()` is called, since the Aspect config was set up, it has weaved the Aspect functionality using proxies and will execute the Aspect, printing in the console.

## How are Aspects Applied

![[Pasted image 20240807124316.png]]

## Which Setter is Proxied

The Cache interface will only have aspects woven into it because of the config. There wasn't a DataSource within the main config file.

![[Pasted image 20240807124530.png]]

## Tracking Property Changes - With Context

![[Pasted image 20240807124818.png]]

The `JoinPoint point` is automatically inject by Spring to provide information about where/what invoked the `Advice` (What to execute) and other information about the point.