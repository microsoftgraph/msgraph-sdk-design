# Graph Client Factory [In Progress]

## Objectives

Provide a class that can be used to create an instances of platform native HTTP client library that are pre-configured for making requests to Graph APIs.

## Requirements

- Enable developer to create a native HTTP client instance that is configured with a pipeline of middleware for Graph requests
- Allow developer to provide a custom pipeline that future create requests will respect
- Configure default HTTP handling behavior of native library
- Configure default request headers

## Performance Considerations

## Security Considerations

Authorization tokens MUST not be added to the default request headers.
