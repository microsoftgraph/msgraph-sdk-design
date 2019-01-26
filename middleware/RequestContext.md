# Request Context

## Objectives

## Requirements

- A request context object is attached to the native Http Request object, or Graph Request Object, to control the behavior of middleware and allow the calling app to react to events that occur during the execution of middleware.
- The request context object should provide a dictionary of optional middleware options objects that can be accessed by middleware components to override the options provided during middleware initialization.  
- The request context object MUST contain a property `ClientRequestId` which can be set to correlate all actions related to the current request.

### Feature Usage Flags

The FeatureUsage flag is set on the Request Context so that telemetry could be sent in a header.
This object should be more broadly available so that we can use this instrument other parts of the core library, and not just the middleware pipeline.
For example, if we want to understand whether customers are using a custom HttpProvider object in the GraphServiceClient, we could capture a flag for that scenario.
We propose a factory method for RequestContext object that can ensure the appropriate feature flags are preset for session level features.

The request context object should contain a property `FeatureUsage` which is a bitmap value that is used to flag feature usage.
This feature is not used to actually enable behavior, but to aggregate a value that is used by the telemetry handler to capture the presence of a handler.


| Flag | Feature |    
|--|--|
|  0x00000000 |  None  |
|  0x00000001 |  Redirect Handler Enabled  |
|  0x00000002 |  Retry Handler Enabled  |
|  0x00000004 |  Auth Handler Enabled  |
|  0x00000008 |  Default HttpProvider Enabled  |
|  0x00000010 |  Logging Handler Enabled  |
|  0x00000020 |  Service Discovery Handler Enabled  |
|  0x00000040 |  Compression Handler Enabled  |
|  0x00000080 |  Connnection Pool Manager Enabled  |
|  0x00000100 |  Long Running Operation Handler Enabled  |

### Middleware Control Map

Each type of middleware that is installed in a pipeline should have the option to receive a Middleware control object that has properties to affect the behavior of the middleware.  These control objects are registered on the request context object so as to be able to provide request specific behavior. 

### Event Callbacks
Middleware controls need the ability to notify the calling application of significant actions that may be taken by the middleware. Potential callbacks include:

- ShouldRetryAfter(int delaySeconds, Request request, Response response)
- ShouldRedirect(Response response)
- AuthenticationChallenge(Response response)

## Security Requirements

## Performance Requirements
