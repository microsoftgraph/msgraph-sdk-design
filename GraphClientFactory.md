# Graph Client Factory

## Objectives

Provide a class that can be used to create an instances of platform native HTTP client library that are pre-configured for making requests to Graph APIs.

## Requirements

- Enable developer to create a native HTTP client instance that is configured with a pipeline of middleware for Graph requests
- Allow developer to provide a custom pipeline that future create requests will respect
- Configure default HTTP handling behavior of native library
  - Define default request timeout
  - Override the default base address for requests
- Configure default request headers
  - Set the SdkVersion header with the appropriate moniker based on the following structure `graph-{lang}-{version}`.
- Enable the developer to select a supported sovereign cloud using an enumerated list.
- Enable a developer to configure a HTTP proxy that will be used for outgoing requests.

## Performance Considerations

## Security Considerations

Authorization tokens MUST not be added to the default request headers.
