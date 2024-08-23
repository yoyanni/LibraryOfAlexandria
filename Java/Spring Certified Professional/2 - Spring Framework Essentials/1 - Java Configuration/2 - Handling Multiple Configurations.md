# Creating an Application Context from Multiple files

Your `@Configuration` class can get too big, instead use multiple config files combined with `@Import`.

This defines a **single** Application Context and the Beans are sourced from multiple files

![[Pasted image 20240805165027.png]]

Keep in mind:
- Separation of Concerns principle
	- Keep related Beans within the same `@Configuration`
- Best Practise - Separate "application" & "infrastructure"
	- Infrastructure often changes between environments

![[Pasted image 20240805165738.png]]

# Java Configuration with Dependency Injection

Another way to pass dependencies is to use Constructor Injection by using the `@Autowired` annotation with a constructor of the Configuration class.

![[Pasted image 20240805175535.png]]

# But Avoid "Tramp Data"

Be careful when using both methods of setting up the application configuration.

![[Pasted image 20240805184824.png]]