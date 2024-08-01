# Response Handler

Implement default behaviour for Graph calls.  These can be used either by generated Request classes or called directly by developers using native libraries.

## Requirements

- Provide the ability to take a response payload from a successful request and deserialize it into a strong type.
- Successful requests that MAY return a response payload MUST also support an empty response body.
- Provide the ability to take a response payload from a failed request and deserialize it into a ErrorContent object.
- Responses with 4XX or 5XX should attempt to translate response body into ErrorContent, otherwise a default ErrorContent should be created.
- Calls to the native Http library should not throw ServiceExceptions.
- Service Library calls SHOULD throw an exception with a ErrorContent object. Ideally a different exception should be thrown based on a) the call could not be made b) the server returned an error. See [ErrorContent Requirements](./ErrorContent.md#requirements) for recommended exception names.
- For responses with an ErrorContent payload, attempt to update the ErrorContent with X-ThrowSite header if the the header exists and the ErrorContent does not have a payload.

## Example Usage

```CSharp

  var responseHandler = new ResponseHandler(new Serializer());

  var response = await httpClient.SendAsync(request);
  
  if (response.IsSuccessCode()) {
      User user = responseHandler.HandleSuccessfulResponse<User>(response);
      return user;
  } else {
      ErrorContent error = responseHandler.HandleErrorResponse(response);
      throw new ServiceException(error);
  }
```

## Discussion

All response handlers MUST implement the HTTP specification correctly. Specifically this portion of the specification

> HTTP status codes are extensible.  HTTP clients are not required to
   understand the meaning of all registered status codes, though such
   understanding is obviously desirable.  However, **a client MUST
   understand the class of any status code, as indicated by the first
   digit, and treat an unrecognized status code as being equivalent to
   the x00 status code of that class**, with the exception that a
   recipient MUST NOT cache a response with an unrecognized status code.

https://datatracker.ietf.org/doc/html/rfc7231#section-6

A HTTP request MUST not fail with an exception specifically because it received an HTTP status code that is was not expecting.

### Content vs No Content

Response handlers cannot assume that a request that normally returns a response body, that can be deserialized into an entity, will always return a response body. The response may be empty.  As an empty body is not a valid JSON payload it will need to be handled explicitly.

For successful responses, only requests that use HEAD, or return 204/205 can a client assume the response body will be empty. All other responses may, or may not have a non-zero length body. The presence of a body in a response can be detected by the existence of a content-length with a non-zero value or a Transfer-Encoding header including the term chunked and a payload with a non-zero value in the first line.  Most language native client libraries abstract away chunk-encoding by default and therefore content-length is usually sufficient to detect a body.  The presence of Content-Type should not be used as an indicator of a body.

Examples of where the presence of a body is ambiguous:

| Method | Status Code | Notes |
|--|--|--|
|POST | 200 | According to RFC 7231, the body contains the results or status of the action. In theory, this could be the created resource representation.  However, it is debatable whether it would be better to return a 201 with the actual response body.  |
|POST | 201 | According to RFC 7231, if this returns a body, it should contain a link and information about newly created object, not the object itself. Some APIs do return the created object. |
|POST | 204 | This MUST not return a body. |
|PATCH | 200 | According to RFC 7231, this should return the updated body. In practice, that isn't always the case.|
|PATCH | 201 | Same as POST.  |
|PATCH | 204 | This MUST not return a body. |
|PUT | 200 | According to RFC 7231, responses to PUTs are not cacheable, indicating that it is not expected to return the response body. However, returning the updated response does not seem to be prohibited. |
|PUT | 201 | Same rules as POST. |
|PUT | 204 | This MUST not return a body. |
|DELETE | 200 | The response body, if present should describe the status of the operation. In theory this could be a representation of the resource that was deleted. |
|DELETE | 204 | This MUST not return a body. |
|GET | 200 | This MUST return a body that represents the identified resource. If a representation doesn't exist, then the response SHOULD be a 404. |
|GET | 202 | A 202 may be used if the GET request will take a long time. In this case the body might be empty. |

