# Decompression Handler

A middleware component that requests, detects and decompresses response bodies.

## Requirements

- Add `Accept-encoding: gzip` to request headers.
- Decompress response bodies that have the header `Content-Encoding: gzip`
- Compression handler should be installed as a default handler by [GraphClientFactory](../GraphClientFactory.md)
- Additional observability requirements in [Observability](../Observability.md)

## Remarks

For request payload compression, refer to the [compression handler](./CompressionHandler.md) specification.

## State of automatic decompression

The following table describes the state of automatic decompression of responses. When automatic decompression is supported, we don't need to implement it manually.

| Language | Client | Automatic decompression | Source | Notes |
| -------- | ------ | ----------------------- | ------ | ----- |
| CSharp | HttpClient | Yes | [link](https://learn.microsoft.com/en-us/dotnet/api/system.net.decompressionmethods?view=net-8.0) | Only if the handler `AutomaticDecompression` is set to `All` (not the default!) |
| Go | net/http | Yes | [link](https://webscraping.ai/faq/go/how-do-i-process-compressed-http-responses-in-go) | Only gzip is supported. Ensure `DisableCompression` is false (default) on the transport. |
| Java | OkHttp | Yes | [link](https://medium.com/tech-insider/okhttps-gzip-compression-904919638458) | |
| PHP | Guzzle | Yes | [link](https://docs.guzzlephp.org/en/stable/request-options.html#decode-content) | Does NOT add the accept encoding header automatically. |
| Python | HTTPX | Yes | None | Couldn't find any source, but tested with 0.27.0 and httpbin |
| TypeScript/JavaScript | fetch | Yes | [link](https://stackoverflow.com/questions/70092469/node-fetch-automatic-gzip-decompression/78779242#78779242) | Automatically adds the accept encoding header and decompresses the response. Make sure `compress` is set to `true` (default) in the request options. |

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
