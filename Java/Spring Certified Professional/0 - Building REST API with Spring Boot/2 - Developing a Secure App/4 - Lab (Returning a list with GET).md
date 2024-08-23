# 1: Testing the New Data Contract

Since we are introducing a new data contract, we'll start by testing it!

1. Look at the new data fixtures.
```json
[ 
	{ "id": 99, "amount": 123.45 }, 
	{ "id": 100, "amount": 1.0 }, 
	{ "id": 101, "amount": 150.0 } 
]
```

2. Add a serialization test for the Cash Card list.
```java
@Test 
void cashCardListSerializationTest() throws IOException {
	assertThat(jsonList.write(cashCards)).isStrictlyEqualToJson("list.json"); 
}
```

The test code is self-explanatory: It serializes the `cashCards` variable into JSON, then asserts that `list.json` should contain the same data as the serialized `cashCards` variable.

3. Run tests (failed as expected)
4. Rerun tests (resolved)
5. Add a deserialization test.
```java
@Test void cashCardListDeserializationTest() throws IOException {
	String expected=""" [ { "id": 99, "amount": 123.45 }, { "id": 100, "amount": 100.00 }, { "id": 101, "amount": 150.00 } ] """;
	assertThat(jsonList.parse(expected)).isEqualTo(cashCards); 
}
```

We've intentionally asserted an incorrect value to make it obvious what the test is testing.

6. Run the tests. (testing Deserialization test, successful)

# 2: Test for an Additional GET Endpoint

1. Write a failing test for a new `GET` endpoint.
	Here we're making a request to the `/cashcards` endpoint. Since we're getting the entire list of cards, we don't need to specify any additional information in the request.
2. Run the tests and observe the failure.
	We've already implemented a `/cashcards` endpoint, but not for a `GET` verb.
	
	This is Spring's process:
	
	1. Spring receives a request to the `/cashcards` endpoint.
	2. There's no mapping for the HTTP `GET` verb at that endpoint.
	3. There _is_, however, a mapping to that endpoint for the HTTP `POST` verb. It's the endpoint for the **Create** operation that we implemented in a previous lesson!
	4. Therefore, Spring reports a `405 METHOD_NOT_ALLOWED` error instead of `404 NOT FOUND` -- the route was indeed found, but it doesn't support the `GET` verb.

3. Implement the `GET` endpoint in the Controller.
	To get past the `405` error, we need to implement the `/cashcards` endpoint in the Controller using a `@GetMapping` annotation:

```java
@GetMapping()
    private ResponseEntity<Iterable<CashCard>> findAll() {
        return ResponseEntity.ok(cashCardRepository.findAll());
    }
```

4. Understand the handler method
	Once again we're using one of Spring Data's built-in implementations: `CrudRepository.findAll()`. Our implementing Repository, `CashCardRepository`, will automatically return all `CashCard` records from the database when `findAll()` is invoked.
5. Rerun the tests. (success)

# 3: Enhance the List Test

Let's enhance our tests and make sure the correct data is returned from our HTTP request.

1. Enhance the test.
```java
@Test 
void shouldReturnAllCashCardsWhenListIsRequested() { 
	ResponseEntity<String> response = restTemplate.getForEntity("/cashcards", String.class); 
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK); 
	
	DocumentContext documentContext = JsonPath.parse(response.getBody()); int cashCardCount = documentContext.read("$.length()"); 
	assertThat(cashCardCount).isEqualTo(3);
	
	JSONArray ids = documentContext.read("$..id"); assertThat(ids).containsExactlyInAnyOrder(99, 100, 101);
	
	JSONArray amounts = documentContext.read("$..amount"); assertThat(amounts).containsExactlyInAnyOrder(123.45, 100.0, 150.00); 
}
```

2. Understand the test
	Check out these JsonPath expressions
	1. `documentContext.read("$.length()")` - calculates the length of the array.
	2. `.read("$..id")` - retrieves the list of all `id` values returned
	3. `.read("$..amount")` - collects all `amounts` returned.
	4. `assertThat(...).containsExactlyInAnyOrder(...)`
	    We haven't guaranteed the order of the `CashCard` list -- they come out in whatever order the database chooses to return them. Since we don't specify the order, `containsExactlyInAnyOrder(...)` asserts that while the list must contain everything we assert, the order does not matter.
3. Run the tests (failed)
4. Correct tests and rerun

# 4: Test Interaction and @DirtiesContext

Let's take a moment now to talk about the `@DirtiesContext` annotation.

You'll see two uses of this annotation in the `CashCardApplicationTests` class: One on the class definition, one on the `shouldCreateANewCashCard` test method definition, which is commented-out for now.

1. Comment out the class-level annotation
2. Run tests (test does not pass)

Our new `shouldReturnAllCashCardsWhenListIsRequested` test didn't pass this time! Why?

The reason is that one of the other tests is interfering with our new test by creating a new Cash Card. `@DirtiesContext` fixes this problem by causing Spring to start with a clean slate, as if those other tests hadn't been run. Removing it (commenting it out) from the class caused our new test to fail.

## Learning Moment

Although you can use `@DirtiesContext` to work around inter-test interaction, you shouldn't use it indiscriminately; you should have a good reason. Our reason here is to clean up after creating a new Cash Card.

Leave `DirtiesContext` commented out at the class level, and _uncomment_ it on the method which creates a new Cash Card

# 5: Pagination

We have 3 `CashCards` in our database. Let's set up a test to fetch them one at a time (page size of 1), then have their amounts sorted from highest to lowest (descending).

1. Write the pagination test.
	Note that we are adding parameters to the HTTP request of `?page=0&size=1`. We will handle these in our Controller later.
```java
@Test
void shouldReturnAPageOfCashCards() {
    ResponseEntity<String> response = restTemplate.getForEntity("/cashcards?page=0&size=1", String.class);
    assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);

    DocumentContext documentContext = JsonPath.parse(response.getBody());
    JSONArray page = documentContext.read("$[*]");
    assertThat(page.size()).isEqualTo(1);
}
```
2. Run the tests. (error)
3. Implement pagination in the `CashCardController`
	Don't delete the existing `findAll()` method
```java
@GetMapping 
private ResponseEntity<List<CashCard>> findAll(Pageable pageable) {
	Page<CashCard> page = cashCardRepository.findAll( 
		PageRequest.of( 
			pageable.getPageNumber(),
			pageable.getPageSize()
		)
	);
	return ResponseEntity.ok(page.getContent());
}
```
4. Understand the pagination code
	1. `findAll(Pageable pageable)`
	    `Pageable` is yet another object that Spring Web provides for us. Since we specified the URI parameters of `page=0&size=1`, `pageable` will contain the values we need.
	2. `PageRequest.of(...)`
		`PageRequest` is a basic Java Bean implementation of `Pageable`. Things that want paging and sorting implementation often support this, such as _some types of_ Spring Data Repositories.
5. Try to compile (error)
6. Extend `PagingAndSortingRepository` and rerun tests (errors again)
7. Understand and resolve the failure
	The problem is that we have _two methods mapped to the same endpoint_. Spring detects this error at runtime, during the Spring startup process.

# 6: Sorting

We'd like the Cash Cards to come back in an order that makes sense to humans. So let's order them by amount in a descending order with the highest amounts first.

1. Write a test (which we expect to fail).
```java
@Test 
void shouldReturnASortedPageOfCashCards() {
	ResponseEntity<String> response = restTemplate.getForEntity("/cashcards?page=0&size=1&sort=amount,desc", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
	
	DocumentContext documentContext = JsonPath.parse(response.getBody());
	JSONArray read = documentContext.read("$[*]");
	assertThat(read.size()).isEqualTo(1);
	
	double amount = documentContext.read("$[0].amount");
	assertThat(amount).isEqualTo(150.00); 
}
```
2. Understand the test
	The URI we're requesting contains both pagination and sorting information: 
	
	`/cashcards?page=0&size=1&sort=amount,desc`
	
	- `page=0`: Get the first page. Page indexes start at 0.
	- `size=1`: Each page has size 1.
	- `sort=amount,desc`
3. Run the tests (error)
	The reason is that since we didn't specify a sort order, the cards are returned in the order they are returned from the database. And this happens to be the same as the order in which they were inserted. 
	
	_An important observation_: Not all databases will act the same way. Now, it should make even more sense why we specify a sort order (instead of relying on the database's default order).
4. Implement sorting in the Controller
	In the `CashCardController` class, add an additional parameter to the `PageRequest.of()` call: 
	
	`pageable.getSort()` - the method extracts the sort query parameter from the request URI.
	
5. Learn by breaking things
	To get a little more confidence in the test, let's do an experiment.
	
	In the test, change the sort order from descending to ascending:
	
	`ResponseEntity<String> response = restTemplate.getForEntity("/cashcards?page=0&size=1&sort=amount,asc", String.class);`
	
	This should cause the test to fail because the first Cash Card in ascending order should be the $1.00 card.
	
	**(Test fails)**
	
	Correct! This result reinforces our confidence in the test.

# 8: Paging and Sorting defaults

We now have an endpoint which requires the client to send four pieces of information: The page index and size, the sort order, and direction. This is a lot to ask, so let's make it easier on them.

1. Write a new test which doesn't send any pagination or sorting parameters.
	The defaults will be:
	* Sort by amount ascending.
	* A page size of something larger than 3, so that all of our fixtures will be returned.
```java
@Test 
void shouldReturnASortedPageOfCashCardsWithNoParametersAndUseDefaultValues() {
	ResponseEntity<String> response = restTemplate.getForEntity("/cashcards", String.class);
	assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
	
	DocumentContext documentContext = JsonPath.parse(response.getBody());
	JSONArray page = documentContext.read("$[*]");
	assertThat(page.size()).isEqualTo(3);
	
	JSONArray amounts = documentContext.read("$..amount"); assertThat(amounts).containsExactly(1.00, 123.45, 150.00); 
}
```
2. Run the test
	The test failure shows:
	- All the Cash Cards are being returned, since the `(page.size()).isEqualTo(3)` assertion succeeded.
	- BUT: They are not sorted since the `(amounts).containsExactly(1.00, 123.45, 150.00)` assertion fails:
3. Make the test pass
	In the Controller, swap:
	
	`pageable.getSort()` 
	
	for:
	
	`pageable.getSortOr(Sort.by(Sort.Direction.ASC, "amount"))` 
4. Understand the implementation
	The `getSortOr()` method provides default values for the `page`, `size`, and `sort` parameters.
	
	The default values come from two different sources:
	1. Spring provides the default `page` and `size` values (they are 0 and 20, respectively). A default of 20 for page size explains why all three of our Cash Cards were returned. Again: we didn't need to explicitly define these defaults. Spring provides them "out of the box".
	2. We defined the default `sort` parameter in our own code, by passing a `Sort` object to `getSortOr()`:
		`Sort.by(Sort.Direction.ASC, "amount")`
	
	The net result is that if any of the three required parameters are _not_ passed to the application, then reasonable defaults will be provided.
5. Run test again (pass)

# 9: Summary

In this lab, we implemented a "GET many" endpoint and added sorting and pagination. These accomplished two things:

1. Ensured that the data received from the server is in a predictable and understandable order.
2. Protected the client and server from being overwhelmed by a large amount of data (the page size puts a cap on the amount of data that can be returned in a single response).