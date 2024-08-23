# Lifecycle of a Spring Application Context

## The Use Phase

This is what happens in the "use phase" when you invoke a bean obtained from the context:

![[Pasted image 20240806195310.png]]

### Case 1: Your Bean is just a Bean

They bean is just your raw object, simply invoked directly.

![[Pasted image 20240806195438.png]]

### Case 2: Your Bean is a Proxy

If you require extra behaviour, Spring might decide to wrap your component in a **Proxy***.

![[Pasted image 20240806195525.png]]

This is the case with:
- Transactions
- Security
- Exception Handling

The **Proxy*** is created during the initialisation phase by a ***BeanPostProcessor***.

![[Pasted image 20240806195921.png]]


#### Proxy Power Example: Transactions

The **BeanPostProcessor*** may wrap your bean in a **proxy**.

![[Pasted image 20240806200344.png]]
#### Kinds of Proxies

Spring supports both JDK or CGLib proxies

![[Pasted image 20240806200500.png]]

***Note:*** Spring Boot always will prefer CGLib Proxies

#### JDK vs CGLib Proxies

![[Pasted image 20240806201032.png]]


## The Destruction Phase

The destruction phase starts when the context is closed or a shutdown hook was invoked.

![[Pasted image 20240806201304.png]]

### Bean Clean Up

- All beans are cleaned up
	- Any registered `@PreDestroy` methods are invoked
	- Beans are released for the Garbage Collector to destroy

The destruction process ***cannot*** happen for prototype beans because we do not:
* When they will be destroyed
* When they are out of scope

***NOTE:*** This can ***ONLY*** happen for applications that have shut down gracefully, not if they have been killed or failed.

