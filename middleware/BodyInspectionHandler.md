# Body Inspection Handler

A middleware handler that allows the client application to inspect request and response bodies.

## Requirements

- MUST export the request and response bodies on the original option value.
- MUST make in memory copies of the streams and rewind the original streams to avoid exhausting payloads before they are sent/parsed.
- MUST rewind the stream copies so the application does not need to.
- Provide an option to enable request body inspection. (default disabled)
- Provide an option to enable response body inspection. (default disabled)
- MUST make clear in documentation comments that callers are responsible for disposing the stream.
- MUST make clear in documentation comments copies are being made, which will lead to memory pressure on the application, use adequately.
- Additional observability requirements in [Observability](../Observability.md)

## Example

### CSharp

```CSharp

var bodyInspectionOption = new BodyInspectionHandlerOption {
    InspectResponseBody = true
};
var result = await client.Me.GetAsync(c => c.Options.Add(bodyInspectionOption));
using var reader = new StringReader(bodyInspectionOption.ResponseBody, Encoding.UTF8);
Console.WriteLine("The response content is {content}", await reader.ReadToEndAsync());
```
