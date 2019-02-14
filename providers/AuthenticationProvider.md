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


## Supported Flows

### Interactive Authentication Provider

Shows a UI popup provided by the Auth Server

### UsernamePassword Authentication Provider

Passes username/password over the wire to the AuthServer

### IntegratedWindows Authentication Provider

Grabs credentials from the OS

### DeviceCode Authentication Provider

Outputs a code that needs to be manually entered in a web form at a designated URL. 


## Scenarios

### Multi-User

### Multi-provider

### Cloud Discovery
App asks user for email
We call OpenIdConnect discovery endpoint and identify Auth Server/TokenServer
Pass that to the "ClientApplication"


### PerSession Provider

### PerRequest Provider



### Confidential Client
- Multi-user
- Multi-cloud
- Stateless token cache
- I'm thinking that we don't actually need stateless support because if you attach before access and after Update events to a token cache then it can load what it needs on the fly.  This would mean that a single token cache can satisfy the distributed cache requirement.

### Public Client Application

= Per Session Config
- Multi-user
- Multi-cloud

## OAuth2 Library Requirements

- Accept native request object and add an Authorization header with a valid bearer token. 
- Reuse previously obtained tokens for same host
- Refresh expired tokens
- Obtain scopes from inbound request and compare with current scopes.  If new scopes are present during a request then initiate request for consent, and use new token with request.

## Security Requirements

## Performance Requirements
