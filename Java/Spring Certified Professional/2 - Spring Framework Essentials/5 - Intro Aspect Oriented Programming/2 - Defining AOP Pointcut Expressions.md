# Defining Pointcuts

Spring AOP uses AspectJ's pointcut expression language for selecting where to apply advice.


# Common Pointcut Designators

When you define a Pointcut, you define `Designators`. In Spring AOP, we focus on the `execution` designator.

- `execution(<method pattern>)`
	- The method must match the pattern
- Can chain together to create composite pointcuts
	- &&, ||, !
	- `execution(<pattern1>) || execution(<pattern2>)`
- Method Pattern
	- `[Modifiers] ReturnType [ClassType] MethodName (Arguments) [throws ExceptionType]`
# Example Expression

![[Pasted image 20240807133106.png]]

## More Examples

![[Pasted image 20240807133537.png]]

## Implementation vs Interface

![[Pasted image 20240807133751.png]]

## Using Annotations
 
![[Pasted image 20240807134002.png]]
## Working with Packages

***NOTE:*** Expressions below go from strict to looser expressions.

![[Pasted image 20240807134329.png]]

