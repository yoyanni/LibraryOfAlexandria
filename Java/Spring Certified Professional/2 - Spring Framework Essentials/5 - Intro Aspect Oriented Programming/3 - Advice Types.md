# Advice Type: Before

![[Pasted image 20240807134637.png]]

## Example

Using the `@Before` annotation

![[Pasted image 20240807135112.png]]

***NOTE:*** If the advice throws an exception, the target will not be called - this is a valid use of a **Before Advice**.

# Advice Type: After Returning

![[Pasted image 20240807135358.png]]

## Example

Use the `@AfterReturning` annotation with the `returning` attribute.

![[Pasted image 20240807135458.png]]

***NOTE:*** Returning attribute must match the method argument.

# Advice Type: After Throwing

The Proxy delegates to the Advice **ONLY** after the Target throws an exception.

![[Pasted image 20240807135934.png]]

## Example

Use `@AfterThrowing` annotation with the throwing attribute.
- Only invokes advice if the right exception type is thrown

![[Pasted image 20240807140244.png]]

## Propagation

The `@AfterThrowing` advice will not stop the exception from propagating, however, it can throw a different type of exception.

![[Pasted image 20240807140614.png]]

# Advice Type: After

![[Pasted image 20240807140720.png]]

## Example

Use `@After` annotation

![[Pasted image 20240807140853.png]]

# Advice Type: Around

![[Pasted image 20240807141046.png]]

## Example

Use `@Around` annotation and a ***ProceedingJoinPoint***

![[Pasted image 20240807141247.png]]


# Limitations of Spring AOP

- Can only advise non-private methods
- Can only apply aspects to Spring Beans
- Limitations of weaving with proxies
	- When using proxies, suppose method a() calls method b() on the same class/interface
		- Advice will never be executed for method b()


# Summary

![[Pasted image 20240807141926.png]]