# How Spring DI Container Works

Your classes and the Configuration for the whole application reference the DI container which has the application context.

![[Pasted image 20240805161651.png]]

# Application Classes as POJO's with Dependencies

![[Pasted image 20240805161943.png]]

# Configuration Class with Dependencies

There are two ways of setting up the Application Configuration, the two ways are:

- (Cross Reference) Method Invocation
```java
@Configuration
public class ApplicationConfig {
	@Bean
	public TransferService transferService(){
		return new TransferServiceImpl(accountRepository());
	}
	@Bean
	public AccountRepository accountRepository(){
		return new JdbcAccountRepository(dataSource());
	}
	@Bean
	public DataSource dataSource(){
		....
		return dataSource;
	}
}
```

- Providing Arguments
```java
@Configuration
public class ApplicationConfig {
	@Bean
	public TransferService transferService(AccountRepository repository){
		return new TransferServiceImpl(repository);
	}
	@Bean
	public AccountRepository accountRepository(DataSource dataSource){
		return new JdbcAccountRepository(dataSource);
	}
	@Bean
	public DataSource dataSource(){
		....
		return dataSource;
	}
}
```

# Creating and Using the Application


![[Pasted image 20240805162333.png]]

- `SpringApplication.run()` - is a Spring Boot class (vanilla Spring is used only though). Spring Boot will auto-configure a lot of components but it is not being triggered.

# Accessing a Bean Programmatically

![[Pasted image 20240805162925.png]]


# Summary

- Spring separates application configuration from application objects (beans)
- Spring manages you application objects
	- Creating them in the correct dependency order
	- Ensuring they are fully initialized before use
- Each bean is given a unique id / name
