# Creating Dependencies

Beans have to be created in the right order, that would be, first must be the dependencies and ***AFTER*** that the beans are created.

There are two steps:
- Evaluate the dependencies for each bean
- Get each dependency needed
	- Create any if needed (which is recursive)

# Force Dependency order

You can force the dependency order by using the `@DependsOn` annotation.

![[Pasted image 20240807110526.png]]


# Determining Bean Name and Type

![[Pasted image 20240807111032.png]]

- Java Config - returns an interface
- Annotation-based Config - returns an actual implementation class


# Why wont this work?

![[Pasted image 20240807111843.png]]

- `transferService` Bean only return the Type `TransferService`
- `bankingService` Bean expects a Type `BankService` but no method return that

## Solution 1 - Return the Implementation Type (Recommended)

![[Pasted image 20240807112813.png]]

- `tranferService` now return Type `BankTransferServiceImpl` which does implement `BankService`

## Solution 2 - Return Composite Interface

![[Pasted image 20240807113104.png]]

- An interface is created extending the both services
- Then that is the interface that is implemented in the `BankTransferServiceImpl`
- We use that interface as the return Type in the Bean method `transferService`

# Defining Spring Beans - Best Practise

- Aim to be "sufficiently expressive"
	- Return interfaces except:
		- Where multiple interfaces exist
		- **AND** they are needed for dependency injection
	- Writing to interfaces is good practise
- **WARNING**: Event if you return implementation types
	- Still use interfaces when injecting dependencies
	- Injecting implementation types is brittle
		- Dependency injection may fail if the bean is proxied or a different implementation is returned

