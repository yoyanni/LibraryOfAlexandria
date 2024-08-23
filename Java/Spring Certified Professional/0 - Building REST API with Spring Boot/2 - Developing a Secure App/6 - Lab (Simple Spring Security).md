# 1: Understand our Security Requirements

Who should be allowed to manage any given Cash Card?

In our simple domain, let's state that the user who _created_ the Cash Card "owns" the Cash Card. Thus, they are the "card owner". Only the card owner can view or update a Cash Card.

The logic will be something like this:
- IF the user is _authenticated_
- AND they are _authorized_ as a "card owner"
- AND they own the requested Cash Card
- THEN complete the users's request
- BUT don't allow users to access Cash Cards they do not own.

# 2: Add the Spring Security Dependency

We can add support for Spring Security by adding the appropriate dependency.

1. Add the dependency
```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	// Add the following dependency 
	implementation 'org.springframework.boot:spring-boot-starter-security' ...
}
```
2. Run test (7 failed tests)
	Why do you think all tests of our Cash Card API are failing after adding the Spring Security dependency?
3. Understand why everything is broken
	When we added the Spring Security dependency to our application, _security was enabled by default._
	
	Since we haven't specified how authentication and authorization are performed within our Cash Card API, Spring Security has completely locked down our API.

# 3: Satisfy Spring Security's Dependencies

Next, we'll focus on getting our tests passing again by providing the minimum configuration needed by Spring Security.

We've provided another file on our behalf: `example/cashcard/SecurityConfig.java`. This will be the Java Bean where we'll configure Spring Security for our application.

1. Uncomment `SecurityConfig.java` and review.
```java
package example.cashcard;
... 
class SecurityConfig {
	SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		return http.build(); 
	} 
	@Bean 
	PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder(); 
	}
	...
```
2. Enable Spring Security
	Let's turn `SecurityConfig` into our configuration Bean for Spring Security.
```java
// Add this Annotation
@Configuration
class SecurityConfig {
// Add this Annotation
	@Bean SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		return http.build(); 
	}
	...
```
3. Understand the Annotations.
	* `@Configuration`
		The `@Configuration` annotation tells Spring to use this class to configure Spring and Spring Boot itself. Any Beans specified in this class will now be available to Spring's Auto Configuration engine.
	* `@Bean`
		Spring Security expects a Bean to configure its **Filter Chain**, which you learned about in the Simple Spring Security lesson. Annotating a method returning a `SecurityFilterChain` with the `@Bean` satisfies this expectation.
4. Run tests (fails with 403 - FORBIDDEN)

# 4: Configure Basic Authentication

Thus far we've bootstrapped Spring Security, but not actually secured our application.

Now let's secure our application by configuring **basic authentication**.

1. Configure basic authentication
	Update `SecurityConfig.filterChain` with the following:
```java
@Bean 
SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
	http 
		.authorizeHttpRequests(request -> request
			.requestMatchers("/cashcards/**")
			.authenticated())
		.httpBasic(Customizer.withDefaults())
		.csrf(csrf -> csrf.disable()); return http.build(); 
}
```
2. Understand the Spring Security configuration
	Here, if we explain Spring Security's builder pattern in more understandable language, we see:
		All HTTP requests to `cashcards/` endpoints are required to be authenticated using HTTP Basic Authentication security (username and password). Also, do not require CSRF security.
3. Run tests
	When you run the tests, you'll notice that most tests fail with a `401 UNAUTHORIZED` HTTP status code.
	
	We've enabled basic authentication, requiring that requests must supply a username and password.

# 5: Testing Basic Authentication

There are many ways of providing user authentication and authorization information for a Spring Boot application using Spring Security.

For our tests, we'll configure a test-only service that Spring Security will use for this purpose: An `InMemoryUserDetailsManager`.

1. Configure a test-only `UserDetailsService`
	Let's provide a test-only `UserDetailsService` with the user `sarah1`.
	
	This `UserDetailsService` configuration should be understandable: configure a user named `sarah1` with password `abc123`.
	
	 Spring's IoC container will find the `UserDetailsService` Bean and Spring Data will use it when needed.
	 
	Add the following Bean to `SecurityConfig`:
```java
@Bean 
UserDetailsService testOnlyUsers(PasswordEncoder passwordEncoder) {
	User.UserBuilder users = User.builder();
	UserDetails sarah = users
		.username("sarah1")
		.password(passwordEncoder.encode("abc123"))
		.roles() // No roles for now
		.build(); 
	return new InMemoryUserDetailsManager(sarah); 
}
```
2. Configure Basic Auth in HTTP tests
	Select one test method that uses `restTemplate.getForEntity`, and update it with basic authentication for `sarah1`.
```java
void shouldReturnACashCardWhenDataIsSaved() {
	ResponseEntity<String> response = restTemplate
		.withBasicAuth("sarah1", "abc123") // Add this
		.getForEntity("/cashcards/99", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
...
```
3. Run tests 
	The updated test that provides the basics should now pass!
4. Update all remaining `CashCardApplicationTests` tests and rerun tests. (everything passes)
5. Verify Basic Auth with additional tests.
	Now let's add tests that expect a `401 UNAUTHORIZED` response when incorrect credentials are submitted using basic authentication.
```java
@Test
void shouldNotReturnACashCardWhenUsingBadCredentials() {
	ResponseEntity<String> response = restTemplate
		.withBasicAuth("BAD-USER", "abc123")
		.getForEntity("/cashcards/99", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.UNAUTHORIZED);
	response = restTemplate
		.withBasicAuth("sarah1", "BAD-PASSWORD")
		.getForEntity("/cashcards/99", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.UNAUTHORIZED);
}
```

# 6: Support Authorization

Spring Security supports many forms of authorization, here we'll implement Role-Based Access Control (RBAC).

1. Add users and roles to the `UserDetailsService` Bean
	To test authorization, we need multiple test users with a variety of roles. Update `SecurityConfig.testOnlyUsers` and add the `CARD-OWNER` role to `sarah1`.
	
	Also, let's add a new user named "hank-owns-no-cards" with a role of `NON-OWNER`.
```java
@Bean 
UserDetailsService testOnlyUsers(PasswordEncoder passwordEncoder) {
	User.UserBuilder users = User.builder();
	UserDetails sarah = users
		.username("sarah1")
		.password(passwordEncoder.encode("abc123"))
		.roles("CARD-OWNER") // new role
		.build();
	UserDetails hankOwnsNoCards = users
		.username("hank-owns-no-cards")
		.password(passwordEncoder.encode("qrs456"))
		.roles("NON-OWNER") // new role
		.build();
	return new InMemoryUserDetailsManager(sarah, hankOwnsNoCards); 
}
```
2. Test for Role verification
	We'll assert that user "hank-owns-no-cards" shouldn't have access to a `CashCard` since that user is not a `CARD-OWNER`.
```java
@Test 
void shouldRejectUsersWhoAreNotCardOwners() {
	ResponseEntity<String> response = restTemplate
		.withBasicAuth("hank-owns-no-cards", "qrs456")
		.getForEntity("/cashcards/99", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.FORBIDDEN); 
}
```
3. Run tests (fails)
	Why was `hank-owns-no-cards` able to access a `CashCard` as indicated by the `200 OK` response?
	
	Although we have given the test users roles, we are _not enforcing_ role-based security.
4. Enable role-based security
	`.hasRole("CARD-OWNER"))` - to enable RBAC, replace the .authenticated() in the `SecurityConfig.java`
5. Run test (pass)

# 7: Cash Card ownership: Repository Updates

Any authenticated user with role `CARD-OWNER` can view anyone else's Family Cash Cards!

To fix this, we'll update our tests, `CashCardRepository`, and `CashCardController`:

- We'll add functionality to `CashCardRepository` to restrict or "scope" queries to the correct `OWNER`.
- Next, we'll update our `CashCardController` to guarantee that only the correct `OWNER` is used.

Now, let's update our tests and `CashCardRepository`.

1. Add a new `CashCard` for a user named `kumar2`.
	`INSERT INTO CASH_CARD(ID, AMOUNT, OWNER) VALUES (102, 200.00, 'kumar2');`
2. Test that users cannot access each other's data.
	Let's add a test that explicitly asserts that our API returns a `404 NOT FOUND` when a user attempts to access a Cash Card they do not own.
	
	**_Note:_** You might wonder why we want to return a `404 NOT FOUND` response instead of something else, like `401 UNAUTHORIZED`. One argument in favor of choosing to return `NOT FOUND` is that it's the same response that we'd return if the requested Cash Card doesn't exist. It's safer to err on the side of not revealing **any** information about data which isn't authorized for the user.
```java
@Test
void shouldNotAllowAccessToCashCardsTheyDoNotOwn() {
	ResponseEntity<String> response = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.getForEntity("/cashcards/102", String.class); // kumar2's data
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
}
```
3. Run tests (fails)
	What's going on here?
	
	Answer: Currently, user `sarah1` is able to view `kumar2`'s data because:
	* `sarah1` is _authenticated_.
	* `sarah1` is an _authorized_ `CARD-OWNER`.
```
CashCardApplicationTests > shouldNotAllowAccessToCashCardsTheyDoNotOwn() FAILED org.opentest4j.AssertionFailedError: 
	expected: 404 NOT_FOUND
	but was: 200 OK
```
4. Update the `CashCardRepository` with new `findBy...` methods.
	The simplest thing we can do is to always filter our data access by `CashCard` `owner`.
	
	Let's do that in our Repository.
	
	We'll need to filter by `owner` when finding both a single `CashCard` or a list of `CashCards`.
	
	------*Code Snippet Insert*------
	
	As mentioned in the Spring Data labs and lessons, Spring Data will take care of the actual implementations (writing the SQL queries) for us.
	
	**_Note:_** You might wonder whether Spring Data allows you to write your own SQL. After all, Spring Data can't anticipate every need, right? The answer is Yes! It's easy for you to write your own SQL code. The [Spring Data Query Methods](https://docs.spring.io/spring-data/relational/reference/repositories/query-methods-details.html) documentation describes how to do so by using the `@Query` annotation.
	
	Spring Data is perfectly capable of generating the SQL for the queries we need, though.
```java
... 
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
... 

interface CashCardRepository extends CrudRepository<CashCard, Long>, PagingAndSortingRepository<CashCard, Long> {
	CashCard findByIdAndOwner(Long id, String owner);
	Page<CashCard> findByOwner(String owner, PageRequest pageRequest); 
}
```

# 8: Cash Card ownership: Controller Updates

The `CashCardRepository` now supports filtering `CashCard` data by `owner`.

But we're not using this new functionality. Let's make those updates to the `CashCardController` now by introducing a concept we explained in the written lesson: the `Principal`.

As with other helpful objects, the `Principal` is available for us to use in our Controller. The `Principal` holds our user's authenticated, authorized information.

1. Update the Controller's `GET` by ID endpoint.
	Update the `CashCardController` to pass the Principal's information to our Repository's new `findByIdAndOwner` method.
```java
import java.security.Principal;
... 

@GetMapping("/{requestedId}") 
private ResponseEntity<CashCard> findById(@PathVariable Long requestedId, Principal principal) {
	Optional<CashCard> cashCardOptional = Optional.ofNullable(
		cashCardRepository.findByIdAndOwner(requestedId, principal.getName())
	);
	if (cashCardOptional.isPresent()) {
...
```
2. Run the tests (GET passes but everything else fails)
3. Update the Controller's `GET` for lists endpoint.
```java
@GetMapping
private ResponseEntity<List<CashCard>> findAll(Pageable pageable, Principal principal) {
	Page<CashCard> page = cashCardRepository.findByOwner(
		principal.getName(), 
		PageRequest.of( pageable.getPageNumber(),
...
```
4. Run tests again (All pass)

# 9: Cash Card ownership: Creation Updates

The authenticated, authorized Principal should be used as the `owner` when creating a new `CashCard`.

**Question**: What would happen if we automatically used the submitted `owner` value?
**Answer**: We risk allowing users to create `CashCards` for _someone else_!

Let's ensure that only the authenticated, authorized Principal owns the `CashCards` they are creating.

1. Update the `POST` test.
	To prove that we don't need to submit an `owner`, let's use `null` as the `owner` for the `CashCard`.
```java
void shouldCreateANewCashCard() {
	CashCard newCashCard = new CashCard(null, 250.00, null); 
...
```
2. Run test (fails with 403 - FORBIDDEN)
	You'll see that the tests fail with a `403 FORBIDDEN` error. But why?
	
	It seems that we tried to create a new `CASH_CARD` in the database, but `NULL` is not allowed as the `OWNER`, which is exactly what we passed in for the `OWNER` in our test.
	
	What does this mean? It means that even though the tests (and users) see a `403 FORBIDDEN` error, the application is actually _crashing!_
	
	So, why aren't we seeing a `500 INTERNAL_SERVER_ERROR`, which is more appropriate for a crashing server?
## Learning Moment: Spring Security and Error Handling

Our Controller is returning `403 FORBIDDEN` instead of an `500 INTERNAL_SERVER_ERROR` because Spring Security is automatically implementing a best practice regarding how errors are handled by Spring Web.

It's important to understand that any information returned from our application might be useful to a bad actor attempting to violate our application's security. For example: knowledge about actions that causes our application to crash -- a `500 INTERNAL_SERVER_ERROR`.

In order to avoid _"leaking"_ information about our application, Spring Security has configured Spring Web to return a generic `403 FORBIDDEN` in most error conditions. If almost everything results in a `403 FORBIDDEN` response then an attacker doesn't really know what's going on.

Now that we understand what's happening, let's get the Controller to properly handle this situation.

3. Update the `POST` endpoint in the Controller.
```java
@PostMapping 
private ResponseEntity<Void> createCashCard(@RequestBody CashCard newCashCardRequest, UriComponentsBuilder ucb, Principal principal) {
	CashCard cashCardWithOwner = new CashCard(
		null, 
		newCashCardRequest.amount(), 
		principal.getName()
	); 
	CashCard savedCashCard = cashCardRepository.save(cashCardWithOwner);
```
4. Run tests (pass)

# 10: About CSRF

Protection against Cross-Site Request Forgery (CSRF, or "sea-surf") is an important aspect of HTTP-based APIs used by web-based applications.

Yet, we've disabled CSRF via the `csrf.disable()` code in `SecurityConfig.filterChain`.

Why have we disabled CSRF?

For the purposes of our Family Cash Card API, we're going to follow the guidance from the Spring Security team [regarding non-browser clients](https://docs.spring.io/spring-security/site/docs/5.0.x/reference/html/csrf.html#when-to-use-csrf-protection):
	*When should you use CSRF protection? Our recommendation is to use CSRF protection for any request that could be processed by a browser by normal users. If you are only creating a service that is used by non-browser clients, you will likely want to disable CSRF protection.*

# 11: Summary

In this lab you learned how to use Spring Security to ensure that only authenticated, authorized users have access to the Family Cash Card API. In addition, you followed best practices in the Controller and Repository layers of our application to ensure that only the _correct_ users have access to their (and only their) Cash Card data. You also learned that Spring Security not only protects our requests, but it also modifies our API's error handling as to avoid "leaking" information about crashes and other internal operations occurring with our application.