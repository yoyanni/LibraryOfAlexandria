 
# Bean Creation Sequence of Events - Singleton

1. Bean creation
	1. Created with dependencies injected
	2. Each singleton bean eagerly instatiated
		1. Unless marked as lazy
2. Next each bean goes through a post-processing phase
	1. BeanPostProcessors
3. Now bean is fully initialised and ready to use
	1. Tracked by id until the context is destroyed

![[Pasted image 20240806192421.png]]

# BeanPostProcessor
## The Initialiser Extension Point

- Special case of a bean post-processing
	- Causes initialisation methods to be called
		- `@PostConstruct`
		- `init-method`
- Internally, Spring uses several initialiser bean post-processors
	- **CommonAnnotationBeanPostProcessor** enables:
		- `@PostConstruct`
		- `@Resource`

![[Pasted image 20240806193129.png]]

## After and Before Init - Extension Points

Important extension point in Spring, it can:
- Modify bean instances in any way
- Powerful enabling feature
- Will run against every bean
- Can modify a bean before and/or after initialisation
	- **BeforeInit** - runs before the initialiser
	- **AfterInit** - runs after the initialiser

![[Pasted image 20240806193543.png]]

## Interface

Bean Post Processors implement a known interface:
- Spring provides several implementations
- You can write you own (not common)
	- Typically, the ***After Initialisation*** method

![[Pasted image 20240806193925.png]]
## Example - CustomBeanPostProcessor

![[Pasted image 20240806194118.png]]


# Configuration Lifecycle

![[Pasted image 20240806194309.png]]