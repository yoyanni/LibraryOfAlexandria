In the associated lesson, you learned about implementing the Delete operation in a REST API. In this lab, we'll implement hard delete in our Cash Card API, using the API specifications we defined in the associated lesson.

### Credentials for test user Kumar

Credentials were added for the user `kumar2` to the `testOnlyUsers` bean.

# 1: Test the Happy Path

Let's start with the simplest happy path: Successfully deleting a `CashCard` which exists.

We need the Delete endpoint to return the `204 NO CONTENT` status code.

1. Write the test
	Notice that we've added the `@DirtiesContext` annotation. We'll add this annotation to all tests which change the data. If we don't, then these tests could affect the result of other tests in the file.

```java
@Test
@DirtiesContext
void shouldDeleteAnExistingCashCard() {
	ResponseEntity<Void> response = restTemplate
			.withBasicAuth("sarah1", "abc123")
			.exchange("/cashcards/99", HttpMethod.DELETE, null, Void.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
}
```

## Why not use `RestTemplate.delete()`?

Notice that we're using `RestTemplate.exchange()` even though `RestTemplate` supplies a method that looks like we could use: `RestTemplate.delete()`. However, let's look at the signature:

```java
public class RestTemplate ... {
	public void delete(String url, Object... uriVariables)
```

The other methods we've been using (such as `getForEntity()` and `exchange()`) return a `ResponseEntity`, but `delete()` doesn't. Instead, it's a `void` method. Why is this?

The Spring Web framework supplies the `delete()` method as a convenience, but it comes with some assumptions:

1. A response to a `DELETE` request will have no body.
2. The client shouldn't care what the response code is unless it's an error, in which case, it'll throw an exception.

Given those assumptions, no return value is needed from `delete()`.

But, the second assumption makes `delete()` unsuitable for us: We need the `ResponseEntity` in order to assert on the status code! So, we won't use the convenience method, but rather let's use the more general method: `exchange()`.

2. Run tests
	The test failed because the `DELETE /cashcards/99` request returned a `403 FORBIDDEN`. 
	
	At this point you probably expected this result: Spring Security returns a `403` response for any endpoint which isn't mapped.
3. Implement the Delete endpoint in the Controller
```java
@DeleteMapping("/{id}") 
private ResponseEntity<Void> deleteCashCard(@PathVariable Long id) {
	return ResponseEntity.noContent().build(); 
}
```
4. Run tests
	They pass! 
	
	Does that mean that we're done? Not yet!
	
	We haven't written the code to actually _delete_ the item.
5. Test that we're actually deleting the `CashCard`
	Add the following assertions to the `shouldDeleteAnExistingCashCard()` test:
```java
@Test
@DirtiesContext
void shouldDeleteAnExistingCashCard() {
	ResponseEntity<Void> response = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.exchange("/cashcards/99", HttpMethod.DELETE, null, Void.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
	
	// Add the following code:
	ResponseEntity<String> getResponse = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.getForEntity("/cashcards/99", String.class);
	assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
}
```
6. Understand the test code
	We want to test that the deleted Cash Card is actually deleted, so we try to `GET` it, and assert that the result code is `404 NOT FOUND`.

# 2: Implement the DELETE Endpoint

Now we need to write a Controller method which will be called when we send a `DELETE` request with the proper URI.

Add code to the Controller to delete the record.

Change the `CashCardController.deleteCashCard()` method:

```java
@DeleteMapping("/{id}")
private ResponseEntity<Void> deleteCashCard(@PathVariable Long id) {
	cashCardRepository.deleteById(id); // Add this line
	return ResponseEntity.noContent().build();
}
```

The change is straightforward:

- We use the `@DeleteMapping` with the `"{id}"` parameter, which Spring Web matches to the `id` method parameter.
- `CashCardRepository` already has the method we need: `deleteById()` (it's inherited from `CrudRepository`).

Run the tests, and watch them pass!

# 3: Test case: The Cash Card doesn't exist

Our contract states that we should return `404 NOT FOUND` if we try to delete a card that doesn't exist.

1. Write the test
```java
@Test
void shouldNotDeleteACashCardThatDoesNotExist() {
	ResponseEntity<Void> deleteResponse = restTemplate
			.withBasicAuth("sarah1", "abc123")
			.exchange("/cashcards/99999", HttpMethod.DELETE, null, Void.class);
	assertThat(deleteResponse.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
}
```
2. Run the tests
	*Fails with 204 - NO_CONTENT*
	
	We need to enforce the security of our app by checking that the user trying to delete a card is the owner.

# 4: Enforce Ownership

We need to check whether the record exists. If not, we should _not_ delete the Cash Card, and return `404 NOT FOUND`.

1. Make the following changes to `CashCardController.deleteCashCard`:
	We're using the Principal in order to check whether the Cash Card exists, _and_ at the same time, enforce ownership.
```java
private ResponseEntity<Void> deleteCashCard( @PathVariable Long id, Principal principal) {
// Add the following 3 lines: 
	if (!cashCardRepository.existsByIdAndOwner(id, principal.getName())) {
		return ResponseEntity.notFound().build(); 
	} 
	... 
}
```
2. Add the `existsByIdAndOwner()` method to the Repository
```java
interface CashCardRepository extends CrudRepository<CashCard, Long>, PagingAndSortingRepository<CashCard, Long> {
	... 
	boolean existsByIdAndOwner(Long id, String owner); 
	... 
}
```
3. Understand the Repository code
	We added logic to the Controller method to check whether the Cash Card ID in the request actually exists in the database. The method we'll use is `CashCardRepository.existsByIdAndOwner(id, username)`.
	
	This is another case where Spring Data will generate the implementation of this method as long as we add it to the Repository.
	
	So why not just use the `findByIdAndOwner()` method and check whether it returns `null`? We could absolutely do that! But, such a call would return extra information (the content of the Cash Card retrieved), so we'd like to avoid it as to not introduce extra complexity.
4. Watch the test pass

# 6: Refactor

At this point, we have an opportunity to practice the _Red, Green, Refactor_ process. We've already done _Red_ (the failing test), and _Green_ (the passing test). Now we can ask ourselves, should we _Refactor_ anything?

```java
... 
if (!cashCardRepository.existsByIdAndOwner(id, principal.getName())) {
	return ResponseEntity.notFound().build(); 
} 
cashCardRepository.deleteById(id);
return ResponseEntity.noContent().build();
```

You might find the following version, which is logically equivalent but slightly simpler, to be easier to read:

```java
... 
if (cashCardRepository.existsByIdAndOwner(id, principal.getName())) {
	cashCardRepository.deleteById(id);
	return ResponseEntity.noContent().build();
} 
return ResponseEntity.notFound().build(); 
```

The differences are slight, but removing a not-operator (`!`) from an `if` statement often makes for easier-to-read code, and _readability is important_!

# 6: Hide Unauthorized Records

There's one more case that we have yet to test: What if the user attempts to delete a Cash Card owned by someone else? We decided in the associated lesson that the response should be `404 NOT FOUND` in this case. Thats enough information for us to write a test for that case:

1. In `CashCardApplicationTests.java`, add the following test method at the end of the class:
```java
@Test
void shouldNotAllowDeletionOfCashCardsTheyDoNotOwn() {
	ResponseEntity<Void> deleteResponse = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.exchange("/cashcards/102", HttpMethod.DELETE, null, Void.class);
	assertThat(deleteResponse.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
}
```
2. Run the test
	They all passed! That's great news.
	- We've written a test for a specific case in our API.
	- The test passed without any code changes!
	
	Why do I need that test, since it passes without having to make any code changes? Isn't the purpose of TDD to use tests to guide the implementation of the application? If that's the case, why did I bother to write that test?
	
	Answers:
	- Yes, that is _one_ of many benefits that TDD provides: A process to guide the creation of code in order to arrive at a desired outcome.
	- Tests themselves, though, have another purpose, separate from TDD: Tests are a powerful safety net to enforce correctness. Since the test you just wrote tests a different case than those already written, it provides value. If someone were to make a code change which caused this new test to fail, then you'll have caught the error before it could become an issue! Yay for Tests.
3. One more test
	Shouldn't we test that the record that we tried to delete still exists in the database - that it didn't get deleted?
	
	Yes, that's a valid test.
```java
@Test
void shouldNotAllowDeletionOfCashCardsTheyDoNotOwn() {
	ResponseEntity<Void> deleteResponse = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.exchange("/cashcards/102", HttpMethod.DELETE, null, Void.class);
	assertThat(deleteResponse.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);  

	ResponseEntity<String> getResponse = restTemplate
		.withBasicAuth("kumar2", "xyz789")
		.getForEntity("/cashcards/102", String.class);
	assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);
}
```
4. Run the test (Pass)

# 7: Summary

In this lab, you implemented a RESTful Delete endpoint which doesn't "leak" security information to would-be attackers. You also had a chance to do a small, but useful refactoring to practice the Red, Green, Refactor process.