# Telemetry Handler

## Objectives

Provide a middleware component that attaches metadata to a Graph request in order help improve the SDK team improve the developer experience.

## Requirements

- Add `SdkVersion` request header to each request to identify the language and version of the client SDK library.  The product identifier should be of the format `graph-{lang}`. The `SdkVersion` version can contain multiple values, or appear multiple times in a request. Values should be comma delimited as normal header lists are. e.g.

    `SdkVersion: MyApp/1.0, spfx/1.0, graph-javascript-1.0`
- Add a `client-request-id` header with GUID value to request if one is not present.  If the [RequestContext](../middleware/middleware.md) has a `ClientRequestId` property set then that value should be used.

## Remarks

Ideally we would use the `User-Agent` header to identify the libraries that were involved in making the request.  Unfortunately, this does not work for Javascript because despite allowing `User-Agent` to be changed in fetch() there is a [bug in Chromium](https://bugs.chromium.org/p/chromium/issues/detail?id=571722) that remains unfixed for more than 3 years.

The SdkVersion borrows much of the syntax from `user-agent` but instead uses a comma delimited list.

ABNF Syntax for SdkVersion:

    SdkVersion = 1#( product [ RWS comment ] )
    product         = token ["/" product-version]

where `product`, `product-version`, `comment` and `token` are defined in [RFC 7230](https://tools.ietf.org/html/rfc7230) and [RFC 7231](https://tools.ietf.org/html/rfc7231).

Currently, many SDKs embed the version number into the `product` using a hypen as a separator.  Over time we should migrate these to use the `/` to make parsing the version easier.

The order of `SdkVersion` values is significant.

## Performance

## Security

