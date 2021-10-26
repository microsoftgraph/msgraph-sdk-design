# Telemetry Handler

## Objectives

Provide a mandatory middleware component that attaches metadata to a Graph request in order help the SDK team improve the developer experience.

## Requirements

- Add a `client-request-id` header with GUID value to request if one is not present.  If the [RequestContext](../middleware/RequestContext.md) has a `ClientRequestId` property set then that value should be used.

- Add `SdkVersion` request header to each request to identify the language and version of the client SDK library(s). The product identifier should be of the format `graph-{lang}/{majorVersion}.{minorVersion}.{buildVersion}` for client libraries. If the client SDK library has more than one component i.e. core, v1.0/beta service model library, then the product identifier should capture them as follows based on usage :

    | Client SDK Library Component | Template | Example |
    | --- | --- | --- |
    | **Core** | `graph-{lang}-core/{major}.{minor}.{build}` | `graph-dotnet-core/1.16.0` |
    | **v1.0 Service Model** | `graph-{lang}/{major}.{minor}.{build}` | `graph-dotnet/1.16.0` |
    | **Beta Service Model** | `graph-{lang}-beta/{major}.{minor}.{build}` | `graph-dotnet-beta/1.16.0` |
    
    The `SdkVersion` version can contain multiple values, or appear multiple times in a request. Values MUST be comma delimited as normal header lists are if the `SdkVersion` contains multiple values. e.g.

    ```
    SdkVersion: MyApp/1.0, graph-dotnet/1.15.0, graph-dotnet-core/1.16.0 (featureUsage=0f)
    ```
    
- Client SDK library component names SHOULD be lower case.

- If available, add a the `featureUsage` value contained in the [RequestContext](../middleware/RequestContext.md) to the `SDKVersion` header as key/value pair in the comment after the product identifier. e.g.

    `SdkVersion: graph-javascript/1.0.0 (featureUsage=0f)`

- A `hostOS` value can also be added as a key/value pair in the comment to help us identify the OS on which our client SDK is running on. e.g.

    `hostOS=Microsoft Windows 10.0.18362`

- Add `runtimeEnvironment` key/value pair to capture the runtime framework on which the client SDK is running on. Ideally, we should capture the runtime environment in the form of `runtimeEnvironment=Name/Version`. e.g.

    - `runtimeEnvironment=.NETFramework/1.1` for .NET.
    - `runtimeEnvironment=Node/1.1` for JavaScript.
    - `runtimeEnvironment=JRE/1.1` for Java.

Multiple key/value pairs included in the comment should be delimited by a semi-colon.

- Certain workloads error out when an unexpected header is present in the request. The middlewares should check:
  - If the request URL is a Graph serve endpoint or a custom url provided by the developer, then append or update the telemetry headers.
  - Else the middleware should delete telemetry header.

#### Ideal Metadata Capture
```
SdkVersion: graph-dotnet-beta/0.6.0-preview, graph-dotnet-core/1.16.0 (featureUsage=0f; hostOS=Microsoft Windows 10.0.18362; runtimeEnvironment=.NETFramework/4.7.2)
client-request-id: fdae6861-5916-486d-93d9-9129160b2d79

```

## Remarks

Ideally we would use the `User-Agent` header to identify the libraries that were involved in making the request.  Unfortunately, this does not work for Javascript because despite allowing `User-Agent` to be changed in fetch() there is a [bug in Chromium](https://bugs.chromium.org/p/chromium/issues/detail?id=571722) that remains unfixed for more than 3 years.

The SdkVersion borrows much of the syntax from `user-agent` but instead uses a comma delimited list.

ABNF Syntax for SdkVersion:

    SdkVersion = 1#( product [ RWS comment ] )
    product         = token ["/" product-version]

where `product`, `product-version`, `comment` and `token` are defined in [RFC 7230](https://tools.ietf.org/html/rfc7230) and [RFC 7231](https://tools.ietf.org/html/rfc7231).

Currently, many SDKs embed the version number into the `product` using a hypen as a separator.  Over time we should migrate these to use the `/` to make parsing the version easier.

The order of `SdkVersion` values is significant. Core library SdkVersion header values MUST be specified as the last value in the SdkVersion header. Service library header values MUST precede the Core library value in the SdkVersion header. Microsoft applications header values MUST precede the Service library header value.

> Note: The `SdkVersion` header is intended for Microsoft telemetry purposes. It is used to capture information about Microsoft SDKs and products that use the Microsoft Graph APIs. Non-Microsoft applications that use this header, or alter the value of this header, will gain no benefit from using this header. Use of this header by non-Microsoft applications reduces the telemetry value of this header and reduces the data quality associated with header which reduces the insights that we can gain from this header. In turn, this means fewer data-driven improvements to our SDKs. Users of the SDK or the SDK repository should not alter the value of the SdkVersion header.

## Performance

The SdkVersion header is not used for any performance purposes by Microsoft Graph.

## Security

The SdkVersion header MUST not be used for any security related features unless the security feature use is captured by the `featureUsage` value. 

## Privacy

The SdkVersion header MUST not contain information that identifies users or organizations.
