# Response Handler

Implement default behaviour for Graph calls.  These can be used either by generated Request classes or called directly by developers using native libraries.

## Requirements

- Provide the ability to take a response payload from a successful request and deserialize it into a strong type.
- Provide the ability to take a response payload from a failed request and deserialize it into a ErrorContent object.
- Responses with 4XX or 5XX should attempt to translate response body into ErrorContent, otherwise a default ErrorContent should be created.
- Calls to the native Http library should not throw ServiceExceptions.
- Service Library calls should throw a ServiceException with a ErrorContent object.
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