# Parameters Name Decoding

## Objectives

Provide support for characters that are reserved for RFC6570 Uri Template so they can be used by clients. Example `$` is a reserved character that is commonly used by OData services for query parameters like `$select`. Since it is reserved, the code generation projects the following URI template `{+baseUrl}/users{?%24select}` which will get expanded to `https://graph.microsoft.com/v1.0/users?%24select=firstName`. The service won't automatically decode it, so this reserved character needs to be decoded by a middleware handler before the request is sent to the service.

## Requirements

- MUST provide an option to enable/disable the handler per request. With a default value for all requests.
- MUST provide a way to set the default option for all requests from the application. With a default value in the handler.
- The option MUST provide a way to select which characters will be decoded. With a default of `$`, `.`, `-`, `~`. (note: those last 3 MIGHT already be decoded by the HTTP client pipeline, in which case only the dollar sign is required as a default)
- MUST ONLY decode the query parameters names, not the values, not the path/host/scheme.
- The handler MUST ONLY URI decode the provided characters, and no other characters. (%XX)
- Additional observability requirements in [Observability](../Observability.md)
