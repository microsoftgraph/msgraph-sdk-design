# Sunset Handler

## Objectives

Let application operation teams know that a given operation depends on an API which is slated for removal so they can proactively update the application before a breakage occurs.

## Requirements

- MUST provide an option to disable the behaviour for a given request with a settable default for all requests.
- Additional observability requirements in [Observability](../Observability.md)
- MUST create an event under the span (see Observability) whenever an [HTTP sunset header](https://datatracker.ietf.org/doc/html/rfc8594) is encountered in the response.
- The additional event MUST contain the sunset date value provided by the header as an attribute.
- The span and event MUST NOT contain the URI since parent spans already offer that tag/attribute and it could contain sensitive information.
- The additional event MUST contain the sunset link value (see RFC) if present as an attribute.
