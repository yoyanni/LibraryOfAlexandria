In this lesson, you’ll add the Create endpoint to the API.

Four questions we’ll need to answer while doing this are:

1. Who specifies the ID - the client, or the server?
2. In the API Request, how do we represent the object to be created?
3. Which HTTP method should we use in the Request?
4. What does the API send as a Response?

# Idempotence and HTTP

An **idempotent** operation is defined as one which, if performed more than once, results in the same outcome. In a REST API, an idempotent operation is one that even if it were to be performed several times, the resulting data on the server would be the same as if it had been performed only once.

For each method, the HTTP standard specifies whether it is idempotent or not. `GET`, `PUT`, and `DELETE` are idempotent, whereas `POST` and `PATCH` are not.

Every Create request will generate a new ID, thus no idempotency.

![[Pasted image 20240726211720.jpg]]

# The POST Request and Response

Now let’s talk about the content of the `POST` Request, and the Response.

## The Request

The `POST` method allows a Body, so we'll use the Body to send a JSON representation of the object:

**Request:**

- Method: `POST`
- URI: `/cashcards/`
- Body:

    ```java
    {
        amount: 123.45
    }
    ```

## The Response

On successful creation, what HTTP Response Status Code should be sent? We could use `200 OK` (the response that Read returns), but there’s a more specific, more accurate code for REST APIs: `201 CREATED`.

A response code of `200 OK` does _not_ answer the question “Was there any change to the server data?”. By returning the `201 CREATED` status, the API is specifically communicating that data was added to the data store on the server.

A Response also contains **Headers**. Headers have a name and a value. The HTTP standard specifies that the `Location` header in a `201 CREATED` response should contain the URI of the created resource.

**Response:**

- Status Code: `201 CREATED`
- Header: `Location=/cashcards/42`

## Spring Web Convenience Methods

Spring Web provides methods which are geared towards the recommended use of HTTP and REST.

An example :
```java
return  ResponseEntity
        .created(uriOfCashCard)
        .build();
```

Compared to: 
```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .header(HttpHeaders.LOCATION, uriOfCashCard.toASCIIString())
        .build();
```

