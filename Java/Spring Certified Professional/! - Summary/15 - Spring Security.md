# Do you know these basic security concepts?

- Principal 
	- Is a user, device, or system that performs an action
- Authentication
	- Is establishing that a principal's credentials are valid
- Authorization
	- Deciding if a principal is allowed to access a resource
- Authority
	- Permission or credential enabling access (such as a role)
- Secured Resource
	- Resource that is being secured

## Authentication

There are many authentication mechanisms:
- Basic
- Digest
- Form
- OAuth 2.0 / OIDC
- and others..

There are many storage options for credentials and authority data:
- in-memory (development only)
- Database
- LDAP (Lightweight Directory Access Protocol)

## Authorisation

Authorisation depends on **authentication**, before deciding if a user is permitted to access a resource a user identity must be established.

Authorisation determines if you have the required **Authority**. The decision process is often based on roles:
- ADMIN role can cancel orders
- MEMBER role can place orders
- GUEST role can browse the catalog

# What does Spring Security provide?

- Portability
	- Can be used on any Spring project
- Separation of Concerns
	- Business logic is decoupled from security concerns
	- Authentication and Authorization are decoupled
		- Changes to authentication have no impact on authorization
- Flexible & Extensible
	- [[#Authentication|all the options listed here]]
	- Highly customisable

# The Big Picture

- Secured Resource protected by config attributes
- When there is a new thread of execution (in other word, a new request from a client):
	- An new empty **Security Context** is placed in the thread local
		- Always accessible by `SecurityContextHolder`
- When a user wants to be authenticated details are sent to the **Authentication Manager**
	- Auth. Manager checks with providers if details are correct
		- If successful, it will populate the Security Context with the **Authentication Object**
- The Auth. Object contains the:
	- Principal
		- Username
		- Password
		- and more..
	- Authorities - set of authorities
- User must then be **authorized** to access the secure resource
- The **Security Interceptor** secures the resource by checking the config attributes
	- It receives all the security information of the user from the Security Context
	- Delegates to the **Authorization Manager**
- Authz. Manager is responsible for the authorization process and grants or denies access to the resource

[[0 - Securing REST Application with Spring Security#The Big Picture|Diagram flow of process]]

# How can we use Spring Security?

There are three steps:
- Setup Filter chain
- Configure security (authorization) rules
- Setup Web Authentication

## Filter Chain

The implementation is a chain of Spring configured filters which require a `DelegatingFilterProxy` (auto-configured by Spring Boot).

- Within the Servlet container, we have the `DelegatingFilterProxy` which all requests go through
	- the `DelegatinFilteProxy` is not a Spring Security class but Spring MVC
		- Configured by Spring boot
- When a request comes in, the DFP will pass the request to the **springSecurityFilterChain** which is inside of the application context
	- The filter chain will pass the request through all filters that are configured
- If successful, the request finally goes back to DFP and to the **Dispatcher Servlet** which delegates to controllers
- Once its done with the controller, it goes back through the DFP, the filterChain, back to DFP and then to the user.

[[0 - Securing REST Application with Spring Security#Spring Security Filter Chain - 2|Diagram flow example]]

### What sort of Spring Security Filters are there?

[[0 - Securing REST Application with Spring Security#Spring Security Filters|Answer]]

[[0 - Securing REST Application with Spring Security#Example - `SecurityContextPersistenceFilter`|Example Diagram flow]]

# What is the Default Security Setup?

- Set up a single in-memory user called "user"
- Auto-generates a UUID password
- Depending on Spring Security's content negotiation either one of the below are used:
	- HTTP Basic
	- form Login
- All URL's are protected

# How can we configure Spring Security?

[[1 - URL Authorisation#Spring Security Configuration|Answer]]

## How to specify what authorisation is required?

Within the filter Chain config, you define specific authorisation restrictions for URLs.

It uses Spring MVC match rules to specify the URLs.

[[1 - URL Authorisation#Authorising URLs|Code Snippet]]

You can chain multiple restrictions which order evaluated in the order that they are listed. You must put the most specific matches first (first come, first served).

[[1 - URL Authorisation#More on `authorizeRequests()`|Example]]

**NOTE:** [[1 - URL Authorisation#Warning URL Mapping|Use requestMatchers..]]

## How to by-pass Security?

Since some URLs do not need to be secured, we can use `permitAll()` which allows open-access but still goes through the Spring Security Filter chain.

[[1 - URL Authorisation#By-passing Security|Code Snippet]]

# What is the general flow of Authentication?

- The request comes in and starts going through the filter chain
- The **Basic AuthenticationFilter** will extract the username and password from the request
	- This will create an authentication object of `UsernamePasswordAuthenticationToken`
		- It is not authenticated yet
		- No authorities associated
- The object is then sent to the **AuthenticationManager**
	- The implementation of the **ProviderManager** delegates to one or more than one **AuthenticationProvider**
- The **AuthenticationProvider(s)** will try to authenticate the unauthenticated object
- The AuthenticationProvider delegates to **UserDetailsService** which is an interface that contains a method called `loadUserByUsername()`.
	- the method will bring back the user if it exists
		- if it does not, it will through an exception
- Once the user is found, the **UserDetailsService** will return a **UserDetails** object which will then pass back to the **AuthenticationProvider**, back to the **ProviderManager** and back to the **Basic AuthenticationFilter**
- The filter will populate the **SecurityContext** with the authentication object, which is now populated and contain the authorities associated with the user.

[[2 - Configuring Web Authentication#Spring Security Authentication Flow|Diagram flow]]

## What sort of implementations are there for the AuthenticationProvider?

Several built-in implementations are:
- JaasAuthenticationProvider
- LdapAuthenticationProvider
- OpenIDAuthenticationProvider
- DaoAuthenticationProvider (Used in example)
- RememberMeAuthenticationProvider

## What sort of implementations are there for the UserDetailsService?

Several built-in implementations are:
- InMemoryUserDetailsManager
- JdbcUserDetailsManager
- LdapUserDetailsManager

### In-Memory UserDetailsService

[[2 - Configuring Web Authentication#In-Memory UserDetailsService|Example]]

### Database UserDetailsService

It queries RDBMS for users and their authorities with the help of:
- [[2 - Configuring Web Authentication#Database UserDetailsService - 2|Default queries]]
- Groups, with tables like:
	- `groups`
	- `group_members`
	- `group_authorities`

[[2 - Configuring Web Authentication#Database UserDetailsService - 1|Example of setup]]

## Can you implement custom authentication?

Yes, there are two options you can choose:
- Custom **UserDetailsService**
- Custom **AuthenticationProvider**

[[2 - Configuring Web Authentication#Implementing custom authentication|Code snippet for both]]


# Can you talk about Password Encoding?

- Password must be stored in an encoded form
- It is a one-way transformation
	- You cannot decode it back
	- Authentication process compares user-provided password against the encoded one in the storage
- [[2 - Configuring Web Authentication#Password Encoding|Multiple encoding schemes]] supported

## What challenges are there for these Encoding Schemes?

- Should be future-proof
- Should accommodate old password formats
- Should allow multiple password formats

[[2 - Configuring Web Authentication#Challenges of Password Encoding Schemes|Some more details..]]

## What does Spring Security use for Password Encoding?

It uses the [[2 - Configuring Web Authentication#DelegatingPasswordEncoder to the Rescue|DelegatingPasswordEncoder]]. 




# How is the HTTP Authentication enabled?

There are two ways that we [[#What is the Default Security Setup?|previously mentioned]] 

## httpBasic

[[2 - Configuring Web Authentication#Enabling HTTP Authentication - 1|An example of enabling authentication with httpBasic]]

## formLogin

[[2 - Configuring Web Authentication#Enabling HTTP Authentication - 2|An example of enabling authentication with formLogin]]

[[2 - Configuring Web Authentication#Example|Example of HTML form]]
# How is Method Security actioned?

Usually security is applied at the service layer level where all your business logic and method reside. Attempting to communicate with other layers directly will ***by-pass*** security.

With the help of Spring Security and AOP,  annotations are used to secure resources at the method-level.

## How does it work?

- Spring will notice you require security and so it wraps your Service Layer implementation inside a proxy
	- This Spring Security Proxy will implement the same Service Layer interface
- Every request that goes to this proxy will first go through the **Spring SecurityInterceptor**
- It then delegates to the **Authorization Manager** and determines if you have the authority (`hasPermission()`) to action the method
	- Throws `AccessDeniedException` if not allowed
- If successful, everything works as usual
	- If not, exception will be handled in the web layer

[[3 - Method Security and testing#How it Works?|Flow Diagram]]

## How is Method Security applied?

It is applied using SpEL, specifically with the:
- `PreAuthorize(..)`
- `PostAuthorize(..)`

Spring Security extends the SpEL by adding new methods and variable, such examples are:
- `hasRole(..)`
- `principal` variable
- and more..

To use Method Security, you must annotate the configuration with `@EnableMethodSecurity`.

[[3 - Method Security and testing#Method Security with SpEL - `@EnableMethodSecurity`|Example of the above]]
