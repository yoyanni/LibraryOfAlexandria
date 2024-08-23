Aspect-Oriented Programming (AOP) enables modularization of cross-cutting concerns
- The code for a cross-cutting concern is in a single place, in a module
- In Java, a class represents a module

# What are Cross-Cutting Concerns?

They are generic functionalities that is needed in many places in your application.

Examples are:
- Logging and Tracing
- Transaction Management
- Security
- Caching
- Error Handling
- Performance Monitoring
- Custom Business Rules

# Example

Perform a role-based security check before ***every*** application method.

***NOTE:*** the word "every" is a sign that it is a cross-cutting concern.

# Implementing Cross-Cutting Concerns without Modularisation

Failing to modularise cross-cutting concerns leads to two problems:
- Code Tangling - a coupling of concerns
- Code Scattering - the same concern spread across modules

# Symptom #1 - Code Tangling

![[Pasted image 20240807115324.png]]
# Symptom #2 - Code Scattering

![[Pasted image 20240807115503.png]]

# System Evolution without Modularisation

![[Pasted image 20240807115627.png]]

# Aspect Oriented Programming (AOP)

Aspect Oriented Programming enables modularisation of cross-cutting concerns
- TO avoid code tangling
- TO eliminate code scattering

## How to use AOP in your application

- Implement your mainline application logic
	- Focus on the core problem
- Write aspects to implement your cross-cutting concerns
	- Spring provides many aspects out-of-the-box
- Weave the aspects into your application
	- Adding the cross-cutting behaviours to the right places

## System Evolution: AOP based

![[Pasted image 20240807120212.png]]

# Leading AOP Technologies

AspectJ:
- Original AOP technology (1995)
- A full-blown AOP language
	- Uses bytecode modification for aspect weaving

Spring AOP:
- Java-based AOP framwork with AspectJ integration
	- Uses dynamic proxies for aspect weaving
- Focuses on using AOP to solve enterprise problems

