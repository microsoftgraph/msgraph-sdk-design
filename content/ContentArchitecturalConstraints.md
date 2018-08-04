# Graph Content Classes

## Objectives

To create classes that encapsulate standardized patterns that are used to define HTTP request and response payloads across the Microsoft Graph.  

## Requirements

- Content classes MUST allow serializing to a provided stream if streams exists on the platform.
- Content classes MUST be able to generate wire content as byte array.
- The behavior of content classes MUST be independent of the request URL.
- Content classes MAY provide ability to update request headers with content related headers.

## Performance Considerations

Being able to serialize to a stream is important for several scenarios:

- allows writing large content that will be sent using chunk encoding
- elimnating the double memory usage where request payload is transferred into client side buffer before sending
- recreating request bodies when retrying or redirecting

## Security Considerations

There are no security issues specific to content classes.