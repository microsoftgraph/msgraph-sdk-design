# Service Discovery Handler

## Objective

Depending on where client code is located, it may be optimal to select between different instances of a service.  The service locator handler can inspect a request and choose to replace the host of the request URL with an alternative one.

This approach allows a generated service library to assume the default service location and the target location can be changed per request based on some application defined heuristics.

## Requirements

- Service Discovery Handler must accept a function that will provide an alternate URL based on an input URL
- If the discovery function returns returns a non null URL then the request URL will be updated. 
- This handler should be configured to execute after the redirect handler to ensure redirect URLs are also updated.


## Example Usage


```CSharp

var factory = new GraphClientFactory()
        .With(new ServiceDiscoveryHandler((Uri requestUrl) => {
            if (requestUrl.Path.StartsWith("/people")) {
                return ChangeHost(requestUrl,"https://localhost:3000");
            }
            return null; // Don't touch URL
        }));
factory.CreateClient()
```
