# REST, CRUD, and HTTP

`REST` stands for Representational State Transfer. In a RESTful system, data objects are called Resource Representations. The purpose of a RESTful API (Application Programming Interface) is to manage the state of these Resources.

![[Pasted image 20240725165357.png]]

A frequently mentioned concept when speaking about REST is **CRUD** which stands for “Create, Read, Update, and Delete”.

Another common concept associated with REST is the **Hypertext Transfer Protocol**. In **HTTP**, a caller sends a `Request` to a URI. A web server receives the request, and routes it to a request handler. The handler creates a `Response`, which is then sent back to the caller.

The components of the Request and Response are:

**Request**

- Method (also called Verb)
- URI (also called Endpoint)
- Body

**Response**

- Status Code
- Body

The chart below has more details about RESTful CRUD operations.

| Operation | API Endpoint      | HTTP Method | Response Status  |
| --------- | ----------------- | ----------- | ---------------- |
| Create    | `/cashcards`      | `POST`      | 201 (CREATED)    |
| Read      | `/cashcards/{id}` | `GET`       | 200 (OK)         |
| Update    | `/cashcards/{id}` | `PUT`       | 204 (NO CONTENT) |
| Delete    | `/cashcards/{id}` | `DELETE`    | 204 (NO CONTENT) |

# REST in Spring Boot

## Spring Annotations and Component Scan

What Spring does is to configure and instantiate objects which are called **Spring Beans**. Spring can create Beans in several ways.

You’ll annotate a class with a `Spring Annotation`, which directs Spring to create an instance of the class during Spring’s Component Scan phase. This happens at application startup. The Bean is stored in Spring’s IoC container. From here, the bean can be injected into any code that requests it.

## Spring Web Controllers

In Spring Web, Requests are handled by **Controllers**. We will be using the more specific `RestController`

![[Pasted image 20240725172846.jpg]]

```java
@RestController
class CashCardController {
  @GetMapping("/cashcards/{requestedId}")
  private ResponseEntity<CashCard> findById(@PathVariable Long requestedId) {
     CashCard cashCard = /* Here would be the code to retrieve the CashCard */;
     return ResponseEntity.ok(cashCard);
  }
}
```

1. The above is a handler method in a Controller
2. `@GetMapping` annotation - tells Spring to respond only to **GET** requests for that specific URI path
3. `@PathVariable` annotation - tells Spring what to populate (inject) the parameter with by using the same name for the parameter and the URI endpoint.
4. Spring Web provides the `ResponseEntity` class and also provides several utility methods to produce Response Entities. Here, you can use `ResponseEntity` to create a `ResponseEntity` with code **200 (OK)**, and a body containing a `CashCard`.