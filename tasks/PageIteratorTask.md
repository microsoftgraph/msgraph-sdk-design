# Page Iterator Task

## Objectives

For a variety of reasons, collections of entities are often split into pages and each page is returned with a URL to the next page.  Sometimes, page granularity provided by the API does not match the requirements of the consumer.  This task aims to simplify the life of consumers of paged collections.

## Requirements

- Accept any collection of entities that contains a link to a next page
- Accept a callback function that will be called for each entity in each page of the collection.  The callback function should provide a way to indicate to the iterator to stop iterating.
- Dereference the next page link, when each of the the entities of the current page have been passed to the callback.


## Performance Considerations

The PageIteratorTask should not maintain references to pages that it has already iterated over.  An optimization could be to allow the Iterator to request future pages before it has completed walking the current page.  If this is implemented then there needs a way for a consumer to disable this optimization.   
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
```

