
# What is AOP?

It is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns.

It does this by adding additional behaviour to existing code without modifying the code itself, typically by using a proxy mechanism.

# # What are Cross-Cutting Concerns?

They are generic functionalities that is needed in many places in your application.

Examples are:
- Logging and Tracing
- Transaction Management
- Security
- Caching
- Error Handling
- Performance Monitoring
- Custom Business Rules

## Example

Perform a role-based security check before ***every*** application method.

***NOTE:*** the word "every" is a sign that it is a cross-cutting concern.

## What two problems arise when you don't use AOP?

Failing to modularise cross-cutting concerns leads to two problems:
- Code Tangling - a coupling of concerns
- Code Scattering - the same concern spread across modules

### Code Tangling

[[0 - What Problems Does AOP Solve#Symptom 1 - Code Tangling|Picture]]

### Code Scattering

[[0 - What Problems Does AOP Solve#Symptom 2 - Code Scattering|Picture]]

## How to use AOP in your application

- Implement your mainline application logic
	- Focus on the core problem
- Write aspects to implement your cross-cutting concerns
	- Spring provides many aspects out-of-the-box
- Weave the aspects into your application
	- Adding the cross-cutting behaviours to the right places

## How can you implement AOP?

- AspectJ
- Spring AOP

[[0 - What Problems Does AOP Solve#Leading AOP Technologies|Details]]

# What are the core concepts of AOP?

- Join Point
	- A point in the execution of a program such as a method call or exception thrown
		- Spring AOP only allows method call points
- Pointcut
	- An expression that selects one or more Join Points
- Advice
	- Code to be executed at each selected Join Point
- Aspect
	- A module that encapsulates pointcuts (where) and advice (what)
- Weaving
	- Technique by which aspects are combined with the main code

## What is an AOP Proxy?

An **enhanced** class that stands in place of your original with extra behaviour (Aspect) added (woven) into it.

# How to enable Aspects?

For the AspectJ config style, you can use `@EnableAspectJAutoProxy` in the Aspect config file to enable the aspects but you must also import your config to the main config file.

[[1 - Core AOP Concepts and Quick Start#Configure Aspect as a Bean|Details]]

# How to implement...

## How to implement an Aspect?

You must annotate the Component with the `@Aspect` annotation. 

## How to implement an Advice?

The Advice is just a method that performs the functionality.

## How to implement a Pointcut? 

You must annotate the method (Advice) with one of the annotations below:
- `@Before` - [[3 - Advice Types#Advice Type Before|Example]]
- `@AfterReturning` - [[3 - Advice Types#Advice Type After Returning|Example]]
- `@AfterThrowing` - [[3 - Advice Types#Advice Type After Throwing|Example]]
- `@After` - [[3 - Advice Types#Advice Type After|Example]]
- `@Around` - [[3 - Advice Types#Advice Type Around|Example]]

### How do you define Pointcuts?

Spring AOP uses AspectJ's pointcut expression language and with the help of the above annotations, they help you select where to apply the advice.

### What is a Pointcut Designators?

When you define a Pointcut, you define `Designators`. In Spring AOP, we focus on the `execution` designator.

[[2 - Defining AOP Pointcut Expressions#Common Pointcut Designators|Details]]

### Should you use Implementations or Interfaces?

In your pointcuts, it is best if you select the **Interfaces** as they are more flexible compared to the Implementations (they are ignored if a different implementation is used).

### Can you use Annotations within the expression?

[[2 - Defining AOP Pointcut Expressions#Using Annotations|Yes you can!]]

### Can you use Packages within the expression?

[[2 - Defining AOP Pointcut Expressions#Working with Packages|Yes you can!]]
### Can you give me an example of an expression?

[[2 - Defining AOP Pointcut Expressions#Example Expression|Yes, this picture will help.]]

[[2 - Defining AOP Pointcut Expressions#More Examples|And these will help.]]


# How are Aspects applied?

[[1 - Core AOP Concepts and Quick Start#How are Aspects Applied|Picture Diagram]]

[[1 - Core AOP Concepts and Quick Start#AOP - Quick Start|Full Example]]

# Can I access details of the JoinPoint within the Aspect definition?

The `JoinPoint point` is automatically inject by Spring to provide information about where/what invoked the `Advice` (What to execute) and other information about the point.

[[1 - Core AOP Concepts and Quick Start#Tracking Property Changes - With Context|Example]]

# What are some limitations of Spring AOP?

- Can only advise non-private methods
- Can only apply aspects to Spring Beans
- Limitations of weaving with proxies
	- When using proxies, suppose method a() calls method b() on the same class/interface
		- Advice will never be executed for method b()