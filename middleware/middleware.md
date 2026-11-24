# Middleware

## Objective

In order to enable a flexible way to support cross cutting features, client libraries should support a pipeline of pluggable middleware.  Users should be able to remove existing middleware, replace it and add new piece of middleware at any location in the pipeline.  

## Requirements

- A Middleware pipeline should be configured so that all requests are passed through the chain of middleware in the same order and responses are processed in reverse order to requests.  
- A default pipeline should be configured by the Graph Client Factory for providing standard Graph behavior.
- A pipeline can be customized at the time of creation of a HTTP client.  Pipelines should not be reconfigured at runtime.
- Middleware should accept a native or wrapped request object and also process the corresponding response.
- Middleware components should be as cohesive as possible with an effort to minimize dependencies on other pieces of middleware.
- A [RequestContext](./RequestContext.md) object should be passed along with the request object, to allow the behavior of middleware to be adjusted based on the specifics of the request and state aggregated by other pieces of middleware.
- When middleware assigns HTTP header values to requests or responses they SHOULD only update a single valued header if no value already exists.

## Recommended Middleware Order

For native HTTP adapters, use the following order when the handlers are available and enabled. This is the outbound request order; responses travel back through the same handlers in reverse order.

1. [URL replacement](./UrlReplaceHandler.md)
2. [Query parameter name decoding](./ParametersNameDecodingHandler.md)
3. [User-Agent](./UserAgentHandler.md)
4. [Authorization](./AuthorizationHandler.md) (for arbitrary requests)
5. [Request compression](./CompressionHandler.md)
6. [Response decompression](./DecompressionHandler.md) (if the HTTP client does not already decompress responses)
7. [Redirect](./RedirectHandler.md)
8. [Sunset](./SunsetHandler.md)
9. [Retry](./RetryHandler.md)
10. [Headers inspection](./HeadersInspectionHandler.md)
11. [Body inspection](./BodyInspectionHandler.md)

URL replacement and query parameter name decoding prepare the initial request; they are not reapplied to a server-provided redirect target. Generated SDKs authenticate in the request adapter before the request enters the pipeline, so the authorization header is already present and authorization middleware is not needed. For arbitrary requests that use this middleware, run it early, just after User-Agent.

Compress the request before redirect and retry so the encoded body can be reused rather than recompressed on every send. Body-preserving redirects and retries must replay bytes that match the request's content headers. A redirect that drops the body must also drop its content encoding and other body-specific headers.

Redirect wraps retry so only the failed hop is retried and each hop has its own retry budget. Sunset sits between them so it can observe each hop's response, including intermediate redirects, before the redirect handler follows the next URL. It does not see responses consumed by retry.

Put inspection closest to the transport so it captures the headers and body handed to the HTTP client for each attempt, after transformations, rather than the original request. The transport can still make its own changes. If browser `fetch` follows redirects internally, omit redirect middleware: manual redirect responses do not expose the redirect status or `Location` to the SDK. Middleware cannot observe or retry those individual hops; an eligible retry starts at the original request.

See [Observability](../Observability.md#what-to-capture---tracing) for the corresponding span nesting.

## Performance Considerations

Due to the fact that every request and response pass through each and every piece of middleware it is important that processing is efficient.  Middleware pipelines can be used on both application servers as well as clients so they need to be able to support a high volume of request and be thread safe.

Middleware should be cautious about preserving state between the processing of the request and the handling of the response as this can put significant memory pressure on high load servers.

## Security Considerations


## Example Usages

Here's a straw man proposal for how Middleware control objects could be used to influence the behavior of the request.

```csharp

    var client = new HttpClientFactory().CreateClient(new DelegatingHandler[] {
                                                        new AuthHandler(new AuthOptions()),
                                                        new RedirectHandler(new RedirectOptions()),
                                                        new RetryHandler(new RetryOptions())
        });

    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://graph.microsoft.com/v1.0/me"),
        Method = HttpMethod.Get
    };

    request.AddMiddlewareControl(new IMiddlewareControl[] {
        new RetryOptions()
        {
            ShouldRetry = (req) => { return false; }
        },
        new RedirectOptions()
        {
            MaxRedirects = 0
        },
        new AuthOptions()
        {
            SelectedUser = "bob"
        }});

    var response = client.SendAsync(request);

```

## Open Issues

- Should we create a MiddlewareException? Should we have RequestProcessingException and a ResponseProcessingException so a user knows if the call was actually made?