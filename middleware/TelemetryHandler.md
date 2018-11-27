# Telemetry Handler

## Objectives

Provide a middleware component that attaches metadata to a Graph request in order help improve the SDK team improve the developer experience.

## Requirements

- Add `SdkVersion` request header to each request to identify the language and version of the client SDK library.  The header value should be of the format `graph-{lang}-{version}`.
- Add a `client-request-id` header with GUID value to request if one is not present.  If the [RequestContext](../middleware/middleware.md) has a `ClientRequestId` property set then that value should be used.
- Add a `FeatureUsage` header using the value of the FeatureUsage property on RequestContext.  

## Performance

## Security

