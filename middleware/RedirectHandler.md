# Redirect Handler

A piece of client-side middleware designed to handle 3XX responses transparently so that application code doesn't need to.

## Objectives

- Remove the need for application developers to handle redirect responses.

## Requirements

- Additional observability requirements in [Observability](../Observability.md)

### Status Codes Handled

|Status Code | Description | Notes|
|--|--|--|
| 301 | Moved Permanently | |
| 302 | Found | Temporary |
| 303 | See Other | POST changes to GET |
| 307 | Temporary Permanently |Method does not change |
| 308 | Moved Permanently |Method does not change |

For all status codes listed, redirect handler should use the `Location` header from the redirect response to construct a new request to the new URL that is the same as the original request except for where noted otherwise.

### Max redirects

Requests should be able to include a hint as to how many redirects are allowed before the response should be returned.  Allowing zero maximum redirects indicates that no redirects are allowed.  Handler should have a default max redirects if request does not provide one.

### Redirected Request

For requests with payloads, handler must ensure the payloads are buffered prior to making the request, so that request can be re-issued if necessary.  If max redirects is zero then buffering is not necessary.  This allows a client who wishes to stream a request body to stop the buffering from occuring.

The redirected request URL should be derived from the Location header of the redirect response.  If the Location header is a relative reference, the final URL should be relative to the original target URL.

The redirected request should contain the same headers and body as the original request, except for scenarios noted in the security considerations section.  If the original request object contains other state that is not transmitted over the wire, it should also be preserved in the redirected request to ensure consistent middleware behavior.

## Performance Considerations

For permanent redirects, redirection MAY be cached and reused on future requests to avoid round trip.
Care should be taken to try and avoid duplicating request payloads when making the redirected request.

## Security Considerations

Authorization header MUST not be resent if authority (scheme+host) of the Location header is different than the original target URL.

## Open Issues

- What should be the behaviour if a Location header is not provided?