Now that our API can **create** Cash Cards, it’s reasonable to learn how to fetch all (or some!) of the Cash Cards. In this lesson, we’ll implement the “Read Many” endpoint, and understand how this operation differs substantially from the Read endpoint that we previously created.

# Requesting a List of Cash Cards

The API should be able to return multiple Cash Cards in response to a single REST request, we’ll need a new data contract. Instead of a single Cash Card, the new contract should specify that the response is a JSON Array of Cash Card objects:

```json
[
  {
    "id": 1,
    "amount": 123.45
  },
  {
    "id": 2,
    "amount": 50.0
  }
]
```

`CrudRepository`, has a `findAll` method that we can use to easily fetch all the Cash Cards in the database.

```java
@GetMapping()
private ResponseEntity<Iterable<CashCard>> findAll() {
   return ResponseEntity.ok(cashCardRepository.findAll());
}
```

Some questions come to mind:

- How do I return only the Cash Cards that the user owns? (**Great question!** We’ll discuss this in the upcoming Spring Security lesson).
- What if there are hundreds (or thousands?!) of Cash Cards? Should the API return an unlimited number of results or return them in “chunks”? This is known as **Pagination**.
- Should the Cash Cards be returned in a particular order (i.e., should they be sorted?)?
# Pagination and Sorting

To start our pagination and sorting work, we’ll use a specialized version of the `CrudRepository`, called the `PagingAndSortingRepository.` Let’s talk about the “Paging” functionality.

Ideally, an API should **not** be able to produce a response with unlimited size, because this could overwhelm the client or server memory. In order to ensure that an API response doesn’t include an astronomically large number of Cash Cards, let’s utilize **Spring Data’s pagination** functionality.

Pagination in Spring (and many other frameworks) is to specify the page length (e.g. 10 items), and the page index (starting with 0). This brings up another hurdle. In order for pagination to produce the correct page content, the items must be sorted in some specific order.

# Spring Data Pagination API

Thankfully, Spring Data provides the `PageRequest` and `Sort` classes for pagination. Let’s look at a query to get page 2 with page size 10, _sorting by amount in descending order_ (largest amounts first):

```java
Page<CashCard> page2 = cashCardRepository.findAll(
    PageRequest.of(
        1,  // page index for the second page - indexing starts at 0
        10, // page size (the last page might have fewer items)
        Sort.by(new Sort.Order(Sort.Direction.DESC, "amount"))));

```

# The Request and Response

Now let’s use Spring Web to extract the data to feed the pagination functionality:

- **Pagination:** Spring can parse out the `page` and `size` parameters if you pass a `Pageable` object to a `PagingAndSortingRepository find…()`method.
- **Sorting:** Spring can parse out the `sort` parameter, consisting of the field name and the direction separated by a comma – but be careful, no space before or after the comma is allowed! Again, this data is part of the `Pageable` object.

# The URI

Now let’s learn how we can compose a URI for the new endpoint, step-by-step (we've omitted the `https://domain` prefix in the following):

1. Get the second page
    
    `/cashcards?page=1`
    
2. …where a page has length of 3
    
    `/cashcards?page=1&size=3`
    
3. …sorted by the current Cash Card balance
    
    `/cashcards?page=1&size=3&sort=amount`
    
4. …in descending order (highest balance first)
    
    `/cashcards?page=1&size=3&sort=amount,desc`

# The Java Code

Let’s go over the complete implementation of the Controller method for our new “get a page of Cash Cards” endpoint:

```java
@GetMapping
private ResponseEntity<List<CashCard>> findAll(Pageable pageable) {
   Page<CashCard> page = cashCardRepository.findAll(
           PageRequest.of(
                   pageable.getPageNumber(),
                   pageable.getPageSize(),
                   pageable.getSortOr(Sort.by(Sort.Direction.DESC, "amount"))));
   return ResponseEntity.ok(page.getContent());
}
```

- First let’s parse the needed values out of the query string:
    - We use `Pageable`, which allows Spring to parse out the `page` number and `size` query string parameters.
        - Note: If the caller doesn’t provide the parameters, Spring provides defaults: `page=0, size=20`.
- We use `getSortOr()` so that even if the caller doesn’t supply the `sort` parameter, there is a default. Unlike the `page` and `size` parameters, for which it makes sense for Spring to supply a default, it wouldn’t make sense for Spring to arbitrarily pick a sort field and direction.
- We use the `page.getContent()` method to return the Cash Cards contained in the `Page` object to the caller.\

So, what does the `Page` object contain besides the Cash Cards? Here's the `Page` object in JSON format. The Cash Cards are contained in the `content`. The rest of the fields contain information about how this Page is related to other Pages in the query.

```json
{
  "content": [
    {
      "id": 1,
      "amount": 10.0
    },
    {
      "id": 2,
      "amount": 0.19
    }
  ],
  "pageable": {
    "sort": {
      "empty": false,
      "sorted": true,
      "unsorted": false
    },
    "offset": 3,
    "pageNumber": 1,
    "pageSize": 3,
    "paged": true,
    "unpaged": false
  },
  "last": true,
  "totalElements": 5,
  "totalPages": 2,
  "first": false,
  "size": 3,
  "number": 1,
  "sort": {
    "empty": false,
    "sorted": true,
    "unsorted": false
  },
  "numberOfElements": 2,
  "empty": false
}
```

