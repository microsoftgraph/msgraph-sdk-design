# URL Replacement Handler

## Objectives

Resolve application-provided placeholders in an outgoing request URL after URI template expansion. For example, an application can map `/me-token-to-replace` to `/me` so that `https://graph.microsoft.com/v1.0/me-token-to-replace/messages` is sent as `https://graph.microsoft.com/v1.0/me/messages`.

## Requirements

- The handler MUST support configuring whether replacement is enabled and which strings to replace, with client-wide defaults and a per-request override.
- When enabled, the handler MUST apply the configured match and replacement pairs to the request URL before forwarding the request. When disabled, when no pairs are configured, or when no match is found, the URL MUST be left unchanged.
- The handler MUST NOT change the HTTP method, headers, or body. URL replacement is separate from URI template expansion and [query parameter name decoding](./ParametersNameDecodingHandler.md).
- In the [recommended pipeline](./middleware.md#recommended-middleware-order), the handler runs before query decoding and optional authorization. It applies to the initial request, not to a server-provided redirect target.
- Additional observability requirements are described in [Observability](../Observability.md).

## Implementation Considerations

Existing implementations differ in whether they match against the path or the serialized URL, and whether they replace the first or all occurrences of each match. Implementations should document the matching scope, escaping, and repeated-match behavior. For portable configuration, use distinct, non-overlapping placeholders that appear once in the path. Do not rely on a common replacement order for overlapping pairs.

## Security Considerations

Replacement rules should come from trusted application configuration. Implementations that allow matching outside the path can change the request origin. Authorization for the outgoing request must be scoped to the rewritten destination, and a rewrite must not forward credentials intended for a different origin.
