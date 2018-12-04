# Graph Client Factory

## Objectives

Provide a class that can be used to create an instances of platform native HTTP client library that are pre-configured for making requests to Graph APIs.  

There are two primary usage scenarios for the client factory.  Developers who wish to make HTTP requests using the native HTTP library can use the factory to create an instance of the native library that is preconfigured to access the Graph API.  The second scenario is where the service library client uses the factory for creating its native HTTP client library internally.

## Requirements

- Enable developer to create a native HTTP client instance that is configured with a pipeline of middleware for Graph requests
- Allow developer to provide a custom pipeline that future create requests will respect
- Configure default HTTP handling behavior of native library
  - Define default request timeout (100 secs)
  - If a platform supports connection timeouts, then allow configuration of that.  (default 30 secs)
  - Override the default base address for requests
- Configure default request headers
  - Set the SdkVersion header with the appropriate moniker based on the following structure `graph-{lang}-{version}`.
- Enable the developer to select a supported sovereign cloud using an enumerated list.  Selecting the sovereign cloud should ensure that the AuthenticationProvider uses the appropriate Authentication Endpoint.
- Enable a developer to configure a HTTP proxy that will be used for outgoing requests.

## Performance Considerations

- If available on the platform, enable gzip compression of requests and responses.

## Security Considerations

- If available on the platform, configure for TLS 1.2

Authorization tokens MUST not be added to the default request headers.

## Open Issues

  - Should we define a response stream read timeout?
  - Can the factory detect from the environment which is the appropriate sovereign cloud?
