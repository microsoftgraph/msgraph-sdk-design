# User-Agent Handler

## Objectives

Identify the Kiota HTTP library and its version in the outgoing `User-Agent` header without replacing the application's existing identification. This is separate from the [Telemetry Handler](./TelemetryHandler.md), which defines Graph-specific headers such as `SdkVersion`.

## Requirements

- The handler MUST provide an enable/disable option and allow configuring the product name and version, with client-wide defaults and a per-request override.
- By default, when enabled, the handler adds the library's product identifier as `product/version` (for example, `kiota-go/1.7.0`). If no `User-Agent` value is present, the handler sets it. Otherwise, it appends the product identifier to the existing value, separated by a space.
- The handler MUST preserve existing products and comments and MUST NOT add a duplicate of its product identifier when the same request passes through it again. When disabled, it MUST leave the header unchanged.
- In the [recommended pipeline](./middleware.md#recommended-middleware-order), the handler runs after query parameter name decoding and before optional authorization, so it does not need to append the product again for each redirect hop or retry.
- Additional observability requirements are described in [Observability](../Observability.md).

## Implementation Considerations

The HTTP client or browser can control the final `User-Agent` sent on the wire. In particular, browser `fetch` implementations may ignore an SDK-provided `User-Agent`; middleware should not assume the header value it supplies is what the service receives.
