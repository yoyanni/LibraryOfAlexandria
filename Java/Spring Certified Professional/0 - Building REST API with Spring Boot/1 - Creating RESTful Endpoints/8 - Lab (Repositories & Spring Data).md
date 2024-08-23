# 1: Review the Current Data Management Pattern

Our Family Cash Card REST API currently relies upon `CashCard` data hard-coded directly into our `CashCardController`. Our tests in `CashCardApplicationTests` assert that this data is present.

We know that a web Controller shouldn't manage data. This is a violation of Separation of Concerns.

1. Review `CashCardController`
	Has hard-coded details which is a violation of Separation of Concerns.
2. Review `CashCardApplicationTests`

# 2: Add Spring Data Dependencies

1. Add dependencies for Spring Data and a database.
2. Understand the dependencies
	1. `implementation 'org.springframework.data:spring-data-jdbc'`
		Spring Data has several abstractions on top of those technologies. These are commonly called an **Object-Relational Mapping** framework, or `ORM`.
		
		Spring Data JDBC is our ORM
	2. `implementation 'com.h2database:h2'`
		Database management frameworks only work if they have a linked database. H2 is a "very fast, open source, JDBC API" SQL database implemented in Java.
3. Run tests

# 3: Create the CashCardRepository

1. Create the `CashCardRepository`
2. Understand `extends CrudRepository`
	1. `CrudRepository` is an interface supplied by Spring Data. Spring Boot and Spring Data work together to automatically generate the CRUD methods.
3. Run test
	1. Application crashes because we have not indicated which data object the `CashCardRepository` should manage. The "domain type" of this repository will be the `CashCard`.
4. Configure the `CashCardRepository`
5. Configure the `CashCard`
	1. `record CashCard(@Id Long id, Double amount) {}` - we still need to tell Spring Data which field is the ID.  We do that by editing the `CardCard`'s id with the annotation `@Id`
6. Run tests

# 4: Inject the CashCardRepository

Although we've configured our `CashCard` and `CashCardRepository` classes, we haven't utilized the new `CashCardRepository` to manage our `CashCard` data.

1. Inject the `CashCardRepository` into `CashCardController`.
```
@RestController
@RequestMapping("/cashcards")
class CashCardController {
	private final CashCardRepository cashCardRepository;
	
	private CashCardController(CashCardRepository cashCardRepository) {
		this.cashCardRepository = cashCardRepository;
	}
```

2. Run test
	If you run the tests now, they'll all pass, despite no other changes to the codebase utilizing the new, _required_ constructor
3. Behold Auto Configuration and Construction Injection!
	Spring's Auto Configuration is utilizing its dependency injection (DI) framework, specifically _constructor injection_, to supply `CashCardController` with the correct implementation of `CashCardRepository` at runtime.

## Learning Moment: Remove the DI

1. Temporarily change the `CashCardRepository` to remove the implementation of `CrudRepository`.
2. Compile the project and note the failure.
	Clues such as `NoSuchBeanDefinitionException`, `No qualifying bean`, and `expected at least 1 bean which qualifies as autowire candidate` tell us that Spring is trying to find a properly configured class to provide during the dependency injection phase of Auto Configuration1, but none qualify.
    
    We can satisfy this DI requirement by extending the `CrudRepository`.
3. Undo all the above.

# 5: Use the CashCardRepository for Data Management


You're finally ready to use the `CashCardRepository`!

1. Find the `CashCard` using `findById`
	1. Using also the Optional API
2. Understand the changes
	1. `Optional<CashCard> cashCardOptional = cashCardRepository.findById(requestedId);` - this returns an Optional which _might or might not_ contain the `CashCard`
	2. `cashCardOptional.isPresent()` - determines if the CashCard was found or not
	3. `cashCardOptional.get()` - if found, this would be how you retrieve it
3. Run tests
	1. `showStandardStreams=true` - when true, the error messages within the console will be verbose
4. Rerun tests
	RESULT: No Table found

# 6: Configure the Database

 Providing a `schema.sql` and `data.sql` is one of many ways Spring provides to easily initialize a database.

1. Edit `schema.sql`
	Spring Data will automatically configure a database for tests if we provide the correct file in the correct location.
2. Understand `schema.sql`
	A database schema is a "blueprint" for how data is stored in a database. Our database schema reflects the `CashCard` object that we understand.
3. Rerun tests
4. Load test data from `data.sql`.
	Not only can Spring Data create our test database, it can also _load data into it_, which we can use in our tests.
5. Rerun tests
	Pass!

## Learning Moment: `main` vs `test` resources

Spring has provided a powerful feature for us: it allows us to separate our test-only resources from our main resources when needed.

Our scenario here is a common example of this: our database schema is always the same, but our _data_ is not!