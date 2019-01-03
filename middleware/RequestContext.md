# Request Context

## Objectives

## Requirements

- A request context object is attached to the native Http Request object to control the behaviour of middleware and allow the calling app to react to events that occur during the execution of middleware.
- The request context object should provide a dictionary of optional middleware control objects that can be accessed by middleware components to allow customized behavior.  
- The request context object MUST contain a property `ClientRequestId` which can be set to correlate all actions related to the current request.

### Feature Usage Flags

The request context object should contain a property `FeatureUsage` which is a bitmap value that is used to flag feature usage. 

| Flag | Feature |    
|--|--|
|  0x00000001 |  Redirect Handler Enabled  |
|  0x00000002 |  Retry Handler Enabled  |
|  0x00000003 |  Auth Handler Enabled  |

### Middleware Control Map

Each type of middleware that is installed in a pipeline should have the option to receive a Middleware control object that has properties to affect the behavior of the middleware.  These control objects are registered on the request context object so as to be able to provide request specific behavior. 

### Event Callbacks
Middleware controls need the ability to notify the calling application of significant actions that may be taken by the middleware. Potential callbacks include:

- ShouldRetryAfter(int delaySeconds, Request request, Response response)
- ShouldRedirect(Response response)
- AuthenticationChallenge(Response response)

## Security Requirements

## Performance Requirements
