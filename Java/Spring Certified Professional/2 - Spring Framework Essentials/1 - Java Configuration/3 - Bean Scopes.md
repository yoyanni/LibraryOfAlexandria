# Bean Default Scope - "singleton"

The default scope of a Bean is singleton. The component will be initialised once and cached in the application context.

Using the `@Scope()`, you can explicitly set the scope to singleton or use another scope.

![[Pasted image 20240805185236.png]]

# Implications for Singleton Beans

Be careful with the Singleton Beans because they will be accessed by multiple threads at the same time. Must make sure that they are handled with **Thread Safety**.

Three ways to achieve Thread Safety is:

- Use *Stateless* or *Immutable* Beans
- Use **synchronised** (which is difficult)
- Use a different scope

# Bean Scope - "prototype"

Every time the Bean with scope "prototype" is referenced, a new instance is created.

![[Pasted image 20240805190210.png]]

# Common Scopes

Commonly used scopes are:

- Singleton - A single instance is used
- Prototype - A new instance is created each time the bean is referenced
- Session - A new instance is created once per user session (web environment only)
- Request - A new instance is created once per request (web environment only)

# Other Scopes

There are more scopes, such as:

- More specialised scopes:
	- Web Socket scope
	- Refresh scope
	- Thread scope
- Custom scopes (rare)
	- You define a factory for creating bean instances
	- Register to define a custom scope name

# Summary

- Objects are given what they need to work
	- Does not think about finding dependencies
	- Simplifies code and improves reusability
- Promotes programming to interfaces
	- Conceals implementation details of dependencies
- Improves testability
	- Dependencies are easily swapped with stubs for unit testing
- Better control over object lifecycles

