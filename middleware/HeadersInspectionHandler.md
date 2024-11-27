# Headers Inspection Handler

A middleware handler that allows the client application to inspect request and response headers.

## Requirements

- MUST expose the request and response headers on the original option value.
- MUST make copies of the headers and their values.
- MUST perform case insensitive lookups on headers values.
- Provide an option to enable request headers inspection. (default disabled)
- Provide an option to enable response headers inspection. (default disabled)
- Additional observability requirements in [Observability](../Observability.md)

## Example

### CSharp

```CSharp

var headersInspectionOption = new HeadersInspectionHandlerOption {
    InspectResponseHeaders = true,
};
var result = await client.Me.GetAsync(c => c.Options.Add(headersInspectionOption));
Console.WriteLine("The response content type is {contentType}", headersInspectionOption.ResponseHeaders["Content-Type"]);
```
