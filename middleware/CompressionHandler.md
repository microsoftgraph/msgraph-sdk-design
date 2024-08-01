# Compression Handler

A middleware component that compresses request bodies and falls back to an uncompressed body if the API doesn't support request body compression.

## Requirements

- Add `Content-encoding: gzip` to request headers for requests with a body.
- Compresses request bodies.
- Should the handler receive a [415 Unsupported Media Type](https://httpwg.org/specs/rfc7231.html#status.415) response, the handler will retry sending the request without compressing it a maximum of one time before passing on the error.
- Compression handler should be installed as a default handler by [GraphClientFactory](../GraphClientFactory.md)
- The compression handler should accept a `CompressionOptions` object with a property `EnableCompression`. That object can be passed when instantiating the handler, if nothing is passed a default value is created with `EnableCompression: true`. That object can also be passed on a per request base from the fluent-style API by the SDK used. If a value is passed for the request, it takes precedence over the value that was set with the handler. Compression is only performed when the computed value of `EnableCompression` is `true`.
- MUST NOT compress request bodies when a Content-Range header is present with a "bytes" range unit to avoid "corrupting" the set of requests in the range. (e.g. Content-Range: bytes 0-9/20)
- MUST NOT compress request bodies when a Content-Encoding header is already present to avoid double compression.
- MUST NOT attempt to send the original uncompressed payloads if the response status code is `415` and the `Content-Encoding` request header was already present outside of the middleware handler.
- Additional observability requirements in [Observability](../Observability.md)

## Remarks

For response payload decompression, refer to the [decompression handler](./DecompressionHandler.md) specification.

This handler MUST be inserted BEFORE the [Retry handler](./RetryHanlder.md) to avoid compressing the request payload multiple times over.

This handler is part of the Graph handlers collection and not the Kiota handler collection because of how it handles bad requests being specific to Microsoft Graph APIs.

Microsoft Graph environments which do not support request body compression yet return a 400 response code instead of 415.

## Example

### HTTP request with compressed body

```http
POST https://graph.microsoft.com/v1.0/me/microsoft.graph.sendMail HTTP/1.1
Host: graph.microsoft.com
SdkVersion: Graph-dotnet-2.0.5
FeatureFlag: 00000047
Cache-Control: no-store, no-cache
Authorization: Bearer {token}
Accept-Encoding: gzip
Transfer-Encoding: chunked
Content-Type: application/json
Content-Encoding: gzip
…compressed body….
```

### HTTP response from Microsoft Graph when request compression is not supported

```http
HTTP/1.1 400 Bad Request

Date: Thu, 09 Dec 2021 03:37:07 GMT

Content-Type: application/json

Vary: Accept-Encoding

Strict-Transport-Security: max-age=31536000

request-id: 38b4ee5c-1168-4281-863b-920860a4eebe

client-request-id: 38b4ee5c-1168-4281-863b-920860a4eebe

x-ms-ags-diagnostic: {"ServerInfo":{"DataCenter":"Canada East","Slice":"E","Ring":"2","ScaleUnit":"001","RoleInstance":"QB1PEPF0000111E"}}

Content-Length: 313

 

{"error":{"code":"BadRequest","message":"Unable to read JSON request payload. Please ensure Content-Type header is set and payload is of valid JSON format.","innerError":{"date":"2021-12-09T03:37:08","request-id":"38b4ee5c-1168-4281-863b-920860a4eebe","client-request-id":"38b4ee5c-1168-4281-863b-920860a4eebe"}}}
```
