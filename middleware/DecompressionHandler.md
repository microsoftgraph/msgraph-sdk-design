# Decompression Handler

A middleware component that requests, detects and decompresses response bodies.

## Requirements

- Add `Accept-encoding: gzip` to request headers.
- Decompress response bodies that have the header `Content-Encoding: gzip`
- Compression handler should be installed as a default handler by [GraphClientFactory](../GraphClientFactory.md)

## Remarks

For request payload compression, refer to the [compression handler](./CompressionHandler.md) specification.

This middleware is currently not required in JavaScript as the Fetch API already supports decompressing responses automatically and automatically adds the accept-encoding header.  If Microsoft Graph accepts compressed responses at some point in the future then we should add request compression middleware to Javascript also.

## Example

```csharp

    public class DecompressionHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Declare compression support to server
            var gzipQString = new StringWithQualityHeaderValue("gzip");
            if (!request.Headers.AcceptEncoding.Contains(gzipQString))
            {
                request.Headers.AcceptEncoding.Add(gzipQString);
            }

            // Record feature usage for telemetry
            var requestContext = request.GetRequestContext();
            requestContext.FeatureUsage |= FeatureFlag.CompressionHandler;

            // Send Request
            var response = await base.SendAsync(request, cancellationToken);

            // Decompress gzipped responses
            if (response.Content != null && response.Content.Headers.ContentEncoding.Contains("gzip"))
            {
                response.Content = new StreamContent(new GZipStream(await response.Content.ReadAsStreamAsync(), CompressionMode.Decompress));
            }
            return response;
        }
    }
```
