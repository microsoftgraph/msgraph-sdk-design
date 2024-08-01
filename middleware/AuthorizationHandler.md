# Authorization Handler

## Objectives

A piece of client-side middleware intended to allow the use of an authorization provider service as part of the middleware pipeline.


## Requirements

- Transparently authorize requests
- Accept AuthorizationProvider instance in constructor
- Update Authorization Header
- Certain workloads error out when an unexpected header is present in the request. The middlewares should check:
  - If the request URL is a Graph serve endpoint or a custom url provided by the developer, then append or update the authorization header.
  - Else the middleware should delete authorization header.
 

Take a request object and use authorization provider to add Authorization header.  

### 401 Handling

Trap 401 responses and pass information in the `www-authenticate` header to help generate a valid authorization token and re-issue the request.  

## Performance Considerations

## Security Considerations

