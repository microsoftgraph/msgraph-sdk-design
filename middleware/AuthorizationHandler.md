# Authorization Handler [In Progress]

## Objectives

A piece of client-side middleware intended to allow the use of an authorization provider service as part of the middleware pipeline.

- Transparently authorize requests
- Reuse previously obtained tokens for same host
- Refresh expired tokens
- Obtain new token for new scopes

## Requirements

### Accept AuthorizationProvider

Maintain reference to instance of authorization provider.  

### Update Scopes

Obtain scopes from inbound request and compare with current scopes.  If new scopes are present during a request then intiate request for consent, and use new token with request.

### Update Authorization Header

Take a request object and use authorization provider to add Authorization header.  

### 401 Handling

Trap 401 responses and pass information in the `www-authenticate` header to help generate a valid authorization token and re-issue the request.  

## Performance Considerations

How do we deal with streamed bodies?

## Security Considerations

## Existing Implementations