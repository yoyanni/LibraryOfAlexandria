# 1: Test the HTTP POST Endpoint

1. Add a test for the POST endpoint.
	The simplest example of success is a non-failing HTTP POST request to our Family Cash Card API. We'll test for a `200 OK` response
2. Understand the test
3. Run tests

# 2: Add the POST endpoint

The `POST` endpoint is similar to the `GET` endpoint in our `CashCardController`, but uses the `@PostMapping` annotation from Spring Web.

The `POST` endpoint must accept the data we are submitting for our new `CashCard`, specifically the `amount`.

1. Add the `POST` endpoint without accepting `CashCard` data.
	Note that by returning nothing at all, Spring Web will automatically generate an HTTP Response Status code of `200 OK`.
2. Run tests.

```java
import org.springframework.web.bind.annotation.PostMapping; 
... 

@PostMapping 
private ResponseEntity<Void> createCashCard() { 
	return null; 
}
```

# 3: Testing based on semantic correctness

We want our Cash Card API to behave as semantically correctly as possible.

Let's refer to the official Request for Comments for HTTP Semantics and Content ([RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)) for guidance as to how our API should behave.

For our `POST` endpoint, review this section about [HTTP POST](https://www.rfc-editor.org/rfc/rfc9110#name-post); note that we've added emphasis:
	If one or more resources has been created on the origin server as a result of successfully processing a POST request, **_the origin server SHOULD send a 201 (Created) response containing a Location header field that provides an identifier for the primary resource created ..._**

1. Update the `shouldCreateANewCashCard` test.
2. Understand the test updates.
	1. `assertThat(createResponse.getStatusCode()).isEqualTo(HttpStatus.CREATED);` - now we expect the HTTP response status code to be `201 CREATED`
	2. `URI locationOfNewCashCard = createResponse.getHeaders().getLocation();` - when a `POST` request results in the successful creation of a resource, such as a new `CashCard`, the response should include information for how to retrieve that resource.
	3. `ResponseEntity<String> getResponse = restTemplate.getForEntity(locationOfNewCashCard, String.class); assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);` - fetches the newly created CashCard with the Location header's information
3. Run the tests

```java
@Test
void shouldCreateANewCashCard() {
	CashCard newCashCard = new CashCard(null, 250.00);
	ResponseEntity<Void> createResponse = restTemplate.postForEntity("/cashcards", newCashCard, Void.class);
	assertThat(createResponse.getStatusCode()).isEqualTo(HttpStatus.CREATED);

	URI locationOfNewCashCard = createResponse.getHeaders().getLocation();
	ResponseEntity<String> getResponse = restTemplate.getForEntity(locationOfNewCashCard, String.class);
	assertThat(getResponse.getStatusCode()).isEqualTo(HttpStatus.OK);

}
```

# 4: Implement the POST Endpoint

1. Return a `201 CREATED` status
```java
@PostMapping 
private ResponseEntity<Void> createCashCard(@RequestBody CashCard newCashCardRequest) { 
	return ResponseEntity.created(URI.create("/what/should/go/here?")).build();
}
```
2. Run the tests. (fails because it cant find the object)
3. Save the new `CashCard` and return its location.
```java
@PostMapping
private ResponseEntity<Void> createCashCard(@RequestBody CashCard newCashCardRequest, UriComponentsBuilder ucb) {
   CashCard savedCashCard = cashCardRepository.save(newCashCardRequest);
   URI locationOfNewCashCard = ucb
            .path("cashcards/{id}")
            .buildAndExpand(savedCashCard.id())
            .toUri();
            
   return ResponseEntity.created(locationOfNewCashCard).build();
}
```

# 5: Understand CrudRepository.save

This line in `CashCardController.createCashCard` is deceptively simple:

```java
CashCard savedCashCard = cashCardRepository.save(newCashCardRequest);
```

As learned in previous lessons and labs, Spring Data's `CrudRepository` provides methods that support creating, reading, updating, and deleting data from a data store. `cashCardRepository.save(newCashCardRequest)` does just as it says: it saves a new `CashCard` for us, and returns the saved object with a unique `id` provided by the database. Amazing!

# 6: Understand the other changes to CashCardController


  ```java
  createCashCard(@RequestBody CashCard newCashCardRequest, ...)
```

Unlike the `GET` we added earlier, the `POST` expects a request "body". This contains the data submitted to the API. Spring Web will deserialize the data into a `CashCard` for us.

```java
URI locationOfNewCashCard = ucb
.path("cashcards/{id}")
.buildAndExpand(savedCashCard.id())
.toUri();
```

This is constructing a URI to the newly created `CashCard`. This is the URI that the caller can then use to GET the newly-created `CashCard`.
Note that `savedCashCard.id` is used as the identifier, which matches the `GET` endpoint's specification of `cashcards/<CashCard.id>`.


#### Where did `UriComponentsBuilder` come from?

We were able to add `UriComponentsBuilder ucb` as a method argument to this `POST` handler method and it was automatically passed in. How so? It was injected from our now-familiar friend, Spring's IoC Container. Thanks, Spring Web!


```java
return ResponseEntity.created(locationOfNewCashCard).build();
```

Finally, we return `201 CREATED` with the correct `Location` header.

# 7: Final Testing and Learning Moment

1. Run tests (success)
2. Add more test assertions. (if you want)

## Learning moment

What would happen if we provided an `id` for our new, unsaved `CashCard`?

1. Update the test to submit a `CashCard.id`
	`CashCard newCashCard = new CashCard(44L, 250.00);`
2. Run tests (500 - Internal Server Error)
3. Find and understand the database failure

```shell
Failed to update entity [CashCard[id=44, amount=250.0]]. Id [44] not found in database.
```

The Repository is trying to **_find `CashCard` with `id` of `44` and throwing an error when it cannot find it._** Interesting! Can you guess why?

Supplying an `id` to `cashCardRepository.save` is supported when an _update_ is performed on an existing resource.

In this Learning Moment you learned that the API requires that you _not_ supply a `CashCard.id` when creating a new `CashCard`.