# Page Iterator Task

## Objectives

For a variety of reasons, collections of entities are often split into pages and each page is returned with a URL to the next page.  Sometimes, page granularity provided by the API does not match the requirements of the consumer.  This task aims to simplify the life of consumers of paged collections.

## Requirements

- Accept any collection of entities that contains a link to a next page
- Accept a callback function that will be called for each entity in each page of the collection.
- Dereference the next page link, when each of the the entities of the current page have been pass to the callback.

## Performance Considerations

The PageIteratorTask should not maintain references to pages that it has already iterated over.

## Security Considerations

## Example usage

```CSharp
var pageIterator = new PageIterator<User>(somePagedCollection, 
    (user) => { 
        Console.Debug(user.DisplayName);
    }
 );

pageIterator.Iterate();

```

