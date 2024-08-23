# 1: Write a Spring Boot Test for the GET endpoint

1. Write the test using `@SpringBootTest`
2. Understand the test:
	1. `@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)`
	2.  `@Autowired`
	3. `ResponseEntity<String> response = restTemplate.getForEntity("/cashcards/99", String.class);`
3. Run test
4. Understand test failure

```
package example.cashcard;

import com.jayway.jsonpath.DocumentContext;
import com.jayway.jsonpath.JsonPath;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;  

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)

class CashCardApplicationTests {

    @Autowired
    TestRestTemplate restTemplate;
    
    @Test
    void shouldReturnACashCardWhenDataIsSaved() {
        ResponseEntity<String> response = restTemplate.getForEntity("/cashcards/99", String.class);
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
    }
}
```

# 2: Create a REST Controller

1. Create the Controller
2. Add the handler method
	1. Implement a `findById()` method to handle HTTP requests
3. Rerun test

```
// CashCardController.java

package example.cashcard;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

class CashCardController {

    private ResponseEntity<String> findById() {
       return ResponseEntity.ok("{}");
    }

 }
```

# 3: Add the GET endpoint

1. Update the Controller
2. Understand the Spring Web annotations
	1. `@RestController` - tells Spring that this class is a `Component` of type `RestController` and capable of handling HTTP requests.
	2. `@RequestMapping("/cashcards")` - is a companion to `@RestController` that indicates which address requests must have to access this Controller.
	3. `@GetMapping("/{requestedId}")` - `@GetMapping` marks a method as a _handler method._ `GET` requests that match `cashcards/{requestedID}` will be handled by this method.
3. Run test

```
// CashCardController.java

@RestController
@RequestMapping("/cashcards")
class CashCardController {
  
   @GetMapping("/{requestedId}")
   private ResponseEntity<String> findById() {
         return ResponseEntity.ok("{}");
   }
}
```

# 4: Complete the GET endpoint

1. Update test
2. Understand the additions
	1. `DocumentContext documentContext = JsonPath.parse(response.getBody());` - converts the response String into a JSON-aware object.
	2. `Number id = documentContext.read("$.id"); assertThat(id).isNotNull();` - when we request a Cash Card with `id` of `99`, a JSON object will be expected with _something_ in the `id` field
3. Run test (note failure)
4. Return a Cash Card from the Controller
```
@GetMapping("/{requestedId}")
private ResponseEntity<CashCard> findById() {
    CashCard cashCard = new CashCard(1000L, 0.0);
    return ResponseEntity.ok(cashCard);

}
```
5. Run test again
	1. It's important that tests pass or fail for _for the right reason._
6. Update test
7. Rerun test, note the new error
	1. the test is failing for the right reason: We didn't return the correct `id`.
8. ... rest of steps is repetitive information

# 5: Using the @PathVariable

1. Add a new test method
	1. Add a new test for handling unknown ids
2. Run test
3. Add `@PathVariable`
	1. `private ResponseEntity<CashCard> findById(@PathVariable Long requestedId)` - makes Spring Web aware of the `requestedId` supplied in the HTTP request
4. Utilize `@PathVariable`
```
@GetMapping("/{requestedId}")
private ResponseEntity<CashCard> findById(@PathVariable Long requestedId) {
	if (requestedId.equals(99L)) {
		CashCard cashCard = new CashCard(99L, 123.45);
		return ResponseEntity.ok(cashCard);
	} else {
		return ResponseEntity.notFound().build();
	}

}
```
