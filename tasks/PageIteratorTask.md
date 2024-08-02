# Page Iterator Task

## Objectives

For a variety of reasons, collections of entities are often split into pages and each page is returned with a URL to the next page. Sometimes, page granularity provided by the API does not match the requirements of the consumer. This task aims to simplify the life of consumers of paged collections.

## Requirements

1. Accept any collection of entities that contains a link to a next page. This must include support for a collection of entities returned from a batch response.
2. Accept a callback function that will be called for each entity in each page of the collection. The callback function should provide a way to indicate to the iterator to stop as well as resume iterating. The callback function should be passed to the Iterate method. The consumer should drive the use of the API by fetching the next page results whenever they require.
3. Dereference the next page link, when each of the the entities of the current page have been passed to the callback.
4. Expose the state of the PageIterator (NotStarted, Started, Delta etc.) to the user. This proves to be helpful in the scenario where a deltaLink is returned at which point the paging will be paused, however the user should be informed that it is safe to follow the delta link if they wish to examine the changes to the resource.  
5. Accept or provide the ability to pass information like headers, middleware options in the request for the next page. For example, when using a PageIteratorTask for processing a large number of events, consumer should be able to specify the timezone preferance in the headers.
6. RequestOptions should be configurable outside of construction for flexibility with each call to **Iterate**.
7. Provie a **hasNext** method that returns true if there's a next item and false otherwise
8. Provide a **Next** method that returns the next item from the current page or the next page.

## Performance Considerations

The PageIteratorTask should not maintain references to pages that it has already iterated over. An optimization could be to allow the Iterator to request future pages before it has completed walking the current page. If this is implemented, then there needs to be a way for a consumer to disable this optimization.

## Security Considerations

## Example usage

C#

```CSharp
// Walk all pages
var pageIterator = new PageIterator<User>(client, somePagedCollection,
    (user) => {
        Console.Debug(user.DisplayName);
        return true;
    }
 );

pageIterator.IterateAsync();

```

C#

```CSharp
// Search for something
User foundUser = null;
var pageIterator = new PageIterator<User>(client, somePagedCollection,
    (user) => {
        if (Match(user)) {
            foundUser = user;
            return false;
        };
        return true;
    }
 );

pageIterator.IterateAsync();

```

C#

```CSharp
// Create complete collection
List<User> allUsers = new List<User>();
var pageIterator = new PageIterator<User>(client, somePagedCollection,
    (user) => {
        allUsers.Add(user);
        return true;
    }
 );

pageIterator.IterateAsync();

```

TypeScript

```typescript
let response: PageCollection = await client.api("/me/messages").get();
let callback = (data) => {
  console.log(data);
  return true;
};
let pageIterator = new PageIterator(client, response, callback);
pageIterator.iterate();

// Example of passing headers with a PageIterator
let response: PageCollection = await client
  .api("/me/events")
  .headers({ Prefer: 'outlook.timezone= "utc"' })
  .get();
let callback = (data) => {
  console.log(data);
  return true;
};
var requestOptions = { headers: { Prefer: 'outlook.timezone= "utc"' } };
let pageIterator = new PageIterator(client, response, callback, requestOptions);
pageIterator.iterate();
```

Go

```go
pageIterator, err := msgraphgocore.NewPageIterator(graphResponse, *reqAdapter, ParsableCons)
pageIterator.SetHeaders(map[string]string{"Content-Type": "application/json"})

err := pageIterator.Iterate(func(pageItem interface{}) bool {
      item := pageItem.(graph.User)
      res = append(res, *item.GetId())
      return true
})

pageIterator.Iterate(func(pageItem interface{}) bool {
      item := pageItem.(graph.User)
      res = append(res, *item.GetId())

      return *item.GetId() != "2" // pauses when id == 2
})

// resumes iteration from user with id 3
pageIterator.Iterate(func(pageItem interface{}) bool {
      item := pageItem.(graph.User)
      res2 = append(res2, *item.GetId())

      return true
})
```

PHP
```php
$pageIterator = new PageIterator($graphResponse, $requestAdapter, $parsableConstructor);
$pageIterator->setHeaders(["Content-Type" => "application/json"]);

$items = [];
$pageIterator->iterate(function ($pageItem) use (&$items) {
      $items []= $pageItem;
      return true;
})
```
PHP
```php
$pageIterator = new PageIterator($graphResponse, $requestAdapter, $parsableConstructor);
$pageIterator->setHeaders(["Content-Type" => "application/json"]);
$items = [];

$pageIterator->iterate(function ($pageItem) use (&$items) {
      return $pageItem->id !== '2';
});

// resumes iteration from user with id 3
$pageIterator->iterate(function ($pageItem) use (&$items) {
      $items []= $pageItem;
      return true;
});
```
Python
```
page_iterator = PageIterator(response, client.request_adapter)
page_iterator.set_headers({"Content-Type": "application/json"})

page_result = page_iterator.convert_to_page(response)
page_result.odata_next_link = "https://graph.microsoft.com/v1.0/users?$top=2&$skip=2"


page_iterator.iterate(lambda page_item: print(f" Page item {page_item}"))

```
## Future Implementations

    - Support 4th requirement mentioned in this file. The progress can be tracked as follows -
    | SDK         | Implementation Status |
    |-------------|----------------------|
    | C#          | Implemented |
    | JAVA        | Implemented |
    | PHP         | Implemented          |
    | Objective-C | -                    |
    | JavaScript  | In Progress          |
    | Go          | Implemented          |
    | Python      | Implemented          |