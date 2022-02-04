# Page Iterator Task

## Objectives

For a variety of reasons, collections of entities are often split into pages and each page is returned with a URL to the next page.  Sometimes, page granularity provided by the API does not match the requirements of the consumer.  This task aims to simplify the life of consumers of paged collections.

## Requirements

1. Accept any collection of entities that contains a link to a next page. This must include support for a collection of entities returned from a batch response.
2. Accept a callback function that will be called for each entity in each page of the collection.  The callback function should provide a way to indicate to the iterator to stop as well as resume iterating. The callback function should be passed to the Iterate method. The consumer should drive the use of the API by fetching the next page results whenever they require.
3. Dereference the next page link, when each of the the entities of the current page have been passed to the callback.
4. Accept or provide the ability to pass information like headers, middleware options in the request for the next page. For example, when using a PageIteratorTask for processing a large number of events, consumer should be able to specify the timezone preferance in the headers. 
5. RequestOptions should be configurable outside of construction  for flexibility with each call to **Iterate**.
6. Provide a **Next** method that returns the next item from the current page or the next page.

## Performance Considerations

The PageIteratorTask should not maintain references to pages that it has already iterated over.  An optimization could be to allow the Iterator to request future pages before it has completed walking the current page.  If this is implemented, then there needs to be a way for a consumer to disable this optimization.
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
        let response: PageCollection = await client.api("/me/events").headers({ "Prefer": 'outlook.timezone= "utc"' }).get(); 
        let callback = (data) => { 
            console.log(data); 
            return true; 
        }; 
        var requestOptions = { headers: { "Prefer":  'outlook.timezone= "utc"' } };
        let pageIterator = new PageIterator(client, response, callback, requestOptions); 
        pageIterator.iterate(); 
```
## Future Implementations
    - Support 4th requirement mentioned in this file. The progress can be tracked as follows -
    | SDK         | Implementation Status |
    |-------------|----------------------|
    | C#          | -                    |
    | JAVA        | -                    |
    | PHP         | -                    |
    | Objective-C | -                    |
    | JavaScript  | In Progress          |
    | Go          | Implemented
