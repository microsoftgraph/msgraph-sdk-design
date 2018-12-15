# Authentication Provider

## Objective

The AuthenticationProvider is an implementation of a public interface that is used as an adapter to a external authentication library.  The goal is to simplify the set of options provided by the OAuth2 library by limiting the functionality to just capabilities available on the Graph.

## Graph Requirements
- Allow selecting National Clouds
- Handle conditional access challenges
- Map external exceptions to MSGraph ServiceException and ClientException

## National Cloud Endpoints

|Country | Authorization Server|
|---|---|
| China | https://login.chinacloudapi.cn |
| Germany | https://login.microsoftonline.de |
| US Government | https://login.microsoftonline.us |
| Global service (default) | https://login.microsoftonline.com |

## OAuth2 Library Requirements 

- Accept native request object and add an Authorization header with a valid bearer token. 
- Reuse previously obtained tokens for same host
- Refresh expired tokens
- Obtain scopes from inbound request and compare with current scopes.  If new scopes are present during a request then intiate request for consent, and use new token with request.

## Security Requirements

## Performance Requirements

## Open Issues

