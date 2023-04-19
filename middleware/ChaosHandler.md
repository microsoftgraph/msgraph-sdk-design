# Chaos Handler

A piece of middleware that enables developers to inject server failures that are difficult to trigger. The goal of this Chaos Handler is to allow a developer to test their application code with random errors injected into their application so that they can see whether the application handles server generated failures. Additionally, a developer can explicitly control the generated failures to assist with reproducing the failure and aid in fixing the application behaviour under failure conditions. 

## Objectives

Provide a configurable testing handler component that, by default, generates random error responses, and can be configured to generate tester specified errors.

## Requirements

* MUST support request/response pass-through for non-intercepted responses.
* The rate of error responses returned MUST be configurable with a value that represents the percentage of responses intercepted and replaced with a random error response. 
* MUST NOT be a default handler in the midleware chain.
* It SHOULD be easily added to the default middleware chain without affecting the expected middleware functionality.
* It SHOULD be added as the handler immediately before the final handler.
* The default Chaos Handler SHOULD be in random mode by default
* Should be included in middleware using Custom Middleware Chain.
* Should support Manual Mode of operation, where application developer can specify what responses they want for a provided request.
* Manual Mode should be supported at Client Level/ Global Level where they declare a Map and pass this to ChaosHandler.
* This manually declared Map should work on exact matching as well as Regex Matching (Eg - "/me/messages/.*").
* Manual Mode should be supported at Request Level as well, where they can override the response for a particular request.
* Should support Random mode where application developers receive Random responses on the basis of API method from the list of predefined response code for each method.

### Random Mode Response Codes List

| API Method |      Status Code List        |
|:----------:|:----------------------------:|
|     GET    | 429, 500, 502, 503, 504      |
|    POST    | 429, 500, 502, 503, 504, 507 |
|     PUT    | 429, 500, 502, 503, 504, 507 |
|    PATCH   | 429, 500, 502, 503, 504      |
|   DELETE   | 429, 500, 502, 503, 504, 507 |

## Future Potential Requirements

* Support for custom response bodies.
* Conditional responses based on URL and/or header pattern matching.
* Support for having a default Middleware chain, which can be used as Custom Middleware chain by doing some manipulations.
