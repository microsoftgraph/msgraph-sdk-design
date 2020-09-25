# Chaos Handler
A piece of middleware that helps to mock different kinds of responses.

## Objectives
Provide a component, so that application developers can test their application with different kinds of responses (different response codes).

## Requirements
* Should be included in middleware using Custom Middleware Chain.
* Should support Manual Mode of operation, where application developer can specify what response code they want for which API request.
* Manual Mode should be supported at Client Level/ Global Level where they declare a Map and pass this to ChaosHandler.
* This manually declared Map should work on exact matching as well as Regex Matching (Eg - "/me/messages/.*").
* Manual Mode should be supported at Request Level as well, where they can override the response code for a particular request.
* Should support Random mode where application developers receive Random response codes on the basis of API method from the list of
predefined response code for each method.
* Should allow developers to pass custom Response Body mocking Graph responses from the API.

#### Random Mode Response Codes List

| API Method |                              Status Code List                             |
|:----------:|:-------------------------------------------------------------------------:|
|     GET    | 200, 301, 307, 400, 401, 403, 404, 405, 429, 500, 502, 503, 504           |
|    POST    | 200, 201, 204, 307, 400, 401, 403, 404, 405, 429, 500, 502, 503, 504, 507 |
|     PUT    | 200, 201, 400, 401, 403, 404, 405, 409, 429, 500, 502, 503, 504, 507      |
|    PATCH   | 200, 204, 400, 401, 403, 404, 405, 429, 500, 502, 503, 504                |
|   DELETE   | 200, 204, 400, 401, 403, 404, 405, 429, 500, 502, 503, 504, 507           |

## Future Potential Requirements
* Support for having a default Middleware chain, which can be used as Custom Middleware chain by doing some manipulations.


