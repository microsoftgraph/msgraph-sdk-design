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

### Additional requirements for Kiota clients

- MUST NOT attempt to update the authorization header if it is already present (unless we ran into an authentication challenge). This is to ensure customers can use the same http client for arbitrary requests and with a generated client/request adapter without running into double authentication issues.
- MUST accept a [BaseBearerAuthenticationProvider](https://github.com/microsoft/kiota-dotnet/blob/main/src/abstractions/authentication/BaseBearerTokenAuthenticationProvider.cs) and use the associated AccessTokenProvider to obtain the access token. This is to ensure the client initialization remains simple.
- MUST be implemented in the kiota http layer. This ensures maximum reusability without mixing concerns with the authentication implementations.
- MUST rely on the AllowedHostValidator provided by the access token provider to determine whether or not to perform authorization on a given request. This is to ensure configuration remains consistent and simple, as well as to ensure bearer tokens are not sent to untrusted hosts.
- MUST provide an additional HTTP client factory which accepts a BaseBearerAuthenticationProvider and will setup the default middleware handlers in addition to the authorization one.
- Additional observability requirements in [Observability](../Observability.md)

### 401 Handling

Trap 401 responses and pass information in the `www-authenticate` header to help generate a valid authorization token and re-issue the request.  

## Performance Considerations

## Security Considerations

## Examples

### C#

```csharp
var deviceCodeCredentials = new DeviceCodeCredentials();
// simplest case MS Graph pre-packaged SDK
var client = new GraphServiceClient(deviceCodeCredentials);
var me = await client.Me.GetAsync();
// self-served client
var authenticationProvider = new AzureIdentityAuthenticationProvider(deviceCodeCredentials);
var requestAdapter = new HttpRequestAdapter(authenticationProvider);
var client = new MyCustomGraphClient(requestAdapter);
var me = await client.Me.GetAsync();
var httpClient = KiotaClientFactory.GetHttpClient(authenticationProvider); //this method is the http package
//... make an arbitrary request
```
