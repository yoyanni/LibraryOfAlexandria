# Beans can be grouped into Profiles

Profiles represent environment and are used when you want to group specific Beans together for that specific environment.

They can also be used for specific implementations:

- jdbc
- jpa

OR deployment platforms:

- on-premise
- cloud

Beans are included/excluded based on profile membership.

![[Pasted image 20240806133352.png]]

# Defining Profiles

You can use the `@Profile` annotation to specify the profile it belongs to.

You can set it at the class level:

![[Pasted image 20240806133735.png]]

Or even at the method level:

![[Pasted image 20240806134154.png]]

You can also use the `!` when specifying a Profile to state that it should not be used with the specific profile mentioned.

![[Pasted image 20240806134428.png]]

# Ways to Activate Profiles

You have to activate profiles at run-time and you can do that in two ways:

- Using a System property via the command-line
	- `-Dspring.profiles.active=embedded,jpa`
- Using a System property programmatically
```java
System.setProperty("spring.profiles.active", "embedded,jpa");
SpringApplication.run(AppConfig.class);
```

**NOTE:** For integration tests only you can use the `@ActiveProfiles` annotation.

# Property Source Selection

`@Profile` can control which `@PropertySources` are included in the Environment.

![[Pasted image 20240806135415.png]]