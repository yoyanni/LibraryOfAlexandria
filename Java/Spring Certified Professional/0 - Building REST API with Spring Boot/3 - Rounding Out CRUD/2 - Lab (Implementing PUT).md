# 1: Write the Test First

What's the functionality that we want our application to have? How do we want our application to behave?

Let's define this now, then work our way towards satisfying our aspirations.

1. Write the Update test
	We'll use `PUT` to update `CashCards`.
	
	Note that we'll expect a `204 NO_CONTENT` response instead of a `200 OK`. The `204` indicates that the action was successfully performed and no further action is needed by the caller.
	
	Add the following test, which updates Cash Card `99` and sets its `amount` to `19.99`.
```java
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
... 

@Test
@DirtiesContext
void shouldUpdateAnExistingCashCard() {
	CashCard cashCardUpdate = new CashCard(null, 19.99, null);
	HttpEntity<CashCard> request = new HttpEntity<>(cashCardUpdate);
	ResponseEntity<Void> response = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.exchange("/cashcards/99", HttpMethod.PUT, request, Void.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT); 
}
```
	
## Learning Moment: what's up with `restTemplate.exchange()`?

Did you notice that we're not using `RestTemplate` in the same way we have in our previous tests?

All other tests use `RestTemplate.xyzForEntity()` methods such as `getForEntity()` and `postForEntity()`. 

So, why aren't we following the same pattern of utilizing `putForEntity()`?

**Answer**: `putForEntity()` doesn't exist! Read more about it here in the [GitHub issue](https://github.com/spring-projects/spring-framework/issues/15256) about the topic. Luckily `RestTemplate` supports multiple ways of interacting with REST APIs, such as `RestTemplate.exchange()`.
	
2. Understand `RestTemplate.exchange()`
	The `exchange()` method is a more general version of the `xyzForEntity()` methods we've used in other tests: `exchange()` requires the verb and the request entity (the body of the request) to be supplied as parameters.
	
	Using `getForEntity()` as an example, you can imagine that the following two lines of code accomplish the same goal:
	
	`.exchange("/cashcards/99", HttpMethod.GET, new HttpEntity(null), String.class);`
	
	The above line is functionally equivalent to the following line:
	
	`.getForEntity("/cashcards/99", String.class);`
	Now let's explain the test code.
	
	* First we create the `HttpEntity` that the `exchange()` method needs:
	    `HttpEntity<CashCard> request = new HttpEntity<>(existingCashCard);`
	
	* Then we call `exchange()`, which sends a `PUT` request for the target ID of `99` and updated Cash Card data:
		`.exchange("/cashcards/99", HttpMethod.PUT, request, Void.class);`
3. Run the tests
	It's time to watch our tests _fail for the right reasons._ For what reason will our new test fail?
	
	*Fails with 403 - FORBIDDEN*
	
	It's time to watch our tests _fail for the right reasons._
	
	With no Controller endpoint, this `PUT` call is forbidden! Spring Security automatically handled this scenario for us.


# 2: Implement @PutMapping in the Controller

1. Add a minimal `@PutMapping`
	This Controller endpoint is fairly self-explanatory:
	* The `@PutMapping` supports the `PUT` verb and supplies the target `requestedId`.
	* The `@RequestBody` contains the updated `CashCard` data.
	* Return an HTTP `204 NO_CONTENT` response code for now, just to get started.

```java
@PutMapping("/{requestedId}")
private ResponseEntity<Void> putCashCard(@PathVariable Long requestedId, @RequestBody CashCard cashCardUpdate) {
	// just return 204 NO CONTENT for now.
	return ResponseEntity.noContent().build(); 
}
```
2. Run tests (Pass)
3. Enhance the test to verify a successful update
	Similar to other verifications we've performed in our test suite, let's assert that the update was successful.
```java
void shouldUpdateAnExistingCashCard() {
... 
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT); 
	
	ResponseEntity<String> getResponse = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.getForEntity("/cashcards/99", String.class);
	assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);
	
	DocumentContext documentContext = JsonPath.parse(getResponse.getBody());
	Number id = documentContext.read("$.id"); 
	Double amount = documentContext.read("$.amount"); 
	assertThat(id).isEqualTo(99); 
	assertThat(amount).isEqualTo(19.99); 
}
```
4. Understand the test updates.

Here, we fetch `CashCard` `99` again, so we can verify that it was updated.

```java
ResponseEntity<String> getResponse = restTemplate
	.withBasicAuth("sarah1", "abc123")
	.getForEntity("/cashcards/99", String.class);
```

Next, we assert that we've retrieved the correct `CashCard` -- `99` -- and that its `amount` was successfully updated to `19.99`.

```java
    ...
    assertThat(id).isEqualTo(99);
    assertThat(amount).isEqualTo(19.99);
    ```
5. Run tests
	Our expectations are legit, but we haven't updated the `CashCardController` to match. The test should fail with valuable error messages, right?
	
	*Fails*
	
	Excellent! We expect an amount of `19.99`, but without any changes we're still returning `123.45`.
6. Update `CashCardController` to perform the data update
	As with our other handler methods, let's ensure that we guarantee that only the `CashCard` `owner` can perform the updates -- the logged-in `Principal` must be the same as the Cash Card `owner`:
```java
@PutMapping("/{requestedId}")
private ResponseEntity<Void> putCashCard(@PathVariable Long requestedId, @RequestBody CashCard cashCardUpdate, Principal principal) {
	CashCard cashCard = cashCardRepository.findByIdAndOwner(
		requestedId, principal.getName()
	);
	CashCard updatedCashCard = new CashCard(
		cashCard.id(),
		cashCardUpdate.amount(),
		principal.getName()
	);
	cashCardRepository.save(updatedCashCard);
	return ResponseEntity.noContent().build(); 
}
```
7. Understand the `CashCardController` updates

We're following a similar pattern as in the `@PostMapping createCashCard()` endpoint.

* We've added the `Principal` as a method argument, provided automatically by Spring Security.

```java
@PutMapping("/{requestedId}")
private ResponseEntity<Void> putCashCard(@PathVariable Long requestedId, @RequestBody CashCard cashCardUpdate, Principal principal) {
```

* Here, we scope our retrieval of the `CashCard` to the submitted `requestedId` and `Principal` (provided by Spring Security) to ensure only the authenticated, authorized `owner` may update this `CashCard`.

```java
cashCardRepository.findByIdAndOwner(requestedId, principal.getName());
```

* Finally, build a `CashCard` with updated values and save it.

```java
CashCard updatedCashCard = new CashCard(cashCard.id(), cashCardUpdate.amount(), principal.getName());
cashCardRepository.save(updatedCashCard);
```

8. Run the tests. (Pass)

We've successfully implemented updating a `CashCard`.

Now let's turn our attention to security. Specifically, What happens if we attempt to update a `CashCard` that we don't own, or one which does not exist?

# 3: Additional Testing and Spring Security's Influence

We've got two remaining scenarios to test: The case where a user tries to update a Cash Card which doesn't exist; and the case where a user tries to update a Cash Card they don't own.

## Case 1: Attempt to update a Cash Card which does not exist.

Start by adding a test.

1. Try to update a Cash Card that doesn't exist
	Here we'll attempt to update a Cash Card with ID `99999`, which doesn't exist.
	
	The test should expect a generic `404 NOT_FOUND` error.
```java
@Test void shouldNotUpdateACashCardThatDoesNotExist() {
	CashCard unknownCard = new CashCard(null, 19.99, null);
	HttpEntity<CashCard> request = new HttpEntity<>(unknownCard);
	ResponseEntity<Void> response = restTemplate
		.withBasicAuth("sarah1", "abc123")
		.exchange("/cashcards/99999", HttpMethod.PUT, request, Void.class); 
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND); 
}
```
2. Run tests
	 *Fails with 403 - FORBIDDEN*
	 
	 Well that's...interesting. Why did we get a `403 FORBIDDEN`? Before we run them again, let's edit `build.gradle` to enable additional test output.
	 
	 *Fails with NullPointerException*
	 
	 A `NullPointerException`! Why a `NullPointerException`?
	Looking at `CashCardController.putCashCard`, we can see that if we don't find the `cashCard`, then method calls to `cashCard` will result in a `NullPointerException`. That makes sense.
	
	**Remember**: We learned in the Spring Security module that in order to avoid "leaking" information about our application, Spring Security has configured Spring Web to return a generic `403 FORBIDDEN` in most error conditions.
3. Dont crash
	We shouldn't allow our code to throw a `NullPointerException`.
	
	Update `CashCardController.putCashCard` to return `404 NOT_FOUND` if no existing `CashCard` is found.
```java
@PutMapping("/{requestedId}")
private ResponseEntity<Void> putCashCard(@PathVariable Long requestedId, @RequestBody CashCard cashCardUpdate, Principal principal) {
	CashCard cashCard = cashCardRepository.findByIdAndOwner(
		requestedId, principal.getName()
	);
	if (cashCard != null) {
		CashCard updatedCashCard = new CashCard(
			cashCard.id(),
			cashCardUpdate.amount(),
			principal.getName()
		);
		cashCardRepository.save(updatedCashCard);
		return ResponseEntity.noContent().build(); 
	} 
	return ResponseEntity.notFound().build(); 
}
```
4. Run the tests
	Our tests pass now that we're returning a `404` when no `CashCard` is found.

## Case 2: Attempt to update a Cash Card owned by someone else

1. Write a test for the remaining case
	Let's write a test that asserts that `sarah1` isn't allowed to update one of `kumar2`'s Cash Cards.
```java
@Test
    void shouldNotUpdateACashCardThatIsOwnedBySomeoneElse() {
        CashCard kumarsCard = new CashCard(null, 333.33, null);
        HttpEntity<CashCard> request = new HttpEntity<>(kumarsCard);
        ResponseEntity<Void> response = restTemplate
                .withBasicAuth("sarah1", "abc123")
                .exchange("/cashcards/102", HttpMethod.PUT, request, Void.class);
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.NOT_FOUND);
    }
```
2. Make a hypothesis about what the test result will be, then verify the hypothesis by running the test
	The test passed because the modification to the Controller that we made in the previous step - namely, to check for a `null` result when retrieving the existing Cash Card - also causes this test to pass.
3. Verify the reason for the test success.
	Comment out the changes in the Controller, and run the tests again.
	
	*Fails with 403 - FORBIDDEN*
	
	We're back to the "crashing" state: Both tests result in `403 FORBIDDEN` because of the underlying `NullPointerException`.
4. Undo your experimental changes.

### Learning Moment: Controversy Exists

At this point you may ask yourself: Should I write two different tests which act exactly the same with respect to a single change in the application code? And if I should, why?

One reason why you might wish to keep the two tests is that at some future time, someone might change the implementation of the controller in some way that causes the two tests to _actually act differently_.

After all, we're testing two different scenarios:

- A `CashCard` record with `id`=99999 **_does_** exist in the database.
- A `CashCard` record with `id`=99999 **_does not_** exist in the database.

The truth is that if you bring this up as a conversation topic, you might get different answers from different people under different circumstances. Acknowledging that opinions may differ, we'll make the choice to leave _both_ tests in place.

# 5: Refactor the Controller Code

Let's reinforce your usage of the Red, Green, Refactor development loop.

We've just completed several tests focused on updating an existing Cash Card.

Do we have any opportunities to simplify, reduce duplication, or otherwise refactor our code without changing behavior?

## Simplify the Code

1. Remove the `Optional`
	This might be controversial: Since we're not taking advantage of features of `Optional` in `CashCardController.findById`, and no other Controller methods use an `Optional`, let's simplify our code by removing it.
2. Run tests (pass)

## Reduce Code Duplication

Note that both `CashCardController.findById` and `CashCardController.putCashCard` have nearly identical code that retrieves a target `CashCard` from the `CashCardRepository` using information from a `CashCard` and `Principal`.

Let's reduce code duplication by extracting a helper method named `findCashCard`, and utilizing it in both `.findById` and `.putCashCard`.

This will allow us to update how we retrieve a `CashCard` in one place as the Controller changes over time.

1. Create a shared `findCashCard` method.
```java
private CashCard findCashCard(Long requestedId, Principal principal) {
	return cashCardRepository.findByIdAndOwner(requestedId, principal.getName());
}
```
2. Update `CashCardController.findById` and rerun the tests.
3. Update `CashCardController.putCashCard` and rerun the tests.

# 6: Summary

In this lab, you learned how to implement an HTTP `PUT` endpoint that allows an authenticated, authorized owner to update their Cash Card.

You also learned how Spring Security automatically manages error handling by Spring Web to ensure that security-sensitive information isn't accidentally revealed when a Controller encounters an error.

In addition, you reinforced your understanding and utilization of the Red, Green, Refactor development cycle by refactoring our Controller code without changing its functionality.