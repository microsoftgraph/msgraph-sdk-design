# Retry Handler

## Objectives

Provide a reusuable component that provides application developers with effective handling of failed requests that can be retried.

## Requirements

- Retried Requests should be equivalent to the initial request made.
- A `retry-attempt` header SHOULD be added which contains a numeric value representing the retry number.  This value will be used for diagnostics and determining the effectiveness of the retry handler.
- Retries MUST respect the `retry-after` response header if provided.
- Retries should be limited by maximum elapsed time and maximum retry attempts
- Where no `retry-after` header is provided by the server, perform retry with the retry handler specified by the library consumer, or fall back to the default handler implemented as exponential backoff handler.
- Allow library consumer to set the parameters for the exponential backoff, and also provide sane defaults, e.g.:
  - initial retry interval: 3 seconds
  - retry interval multiplier: (retry number)^2
  - maximum interval: 180 seconds
  - maximum elapsed time: 1800 seconds (10 retries ^ 2 * 180 seconds)
  - maximum retry attempts: 10
- Only requests with payloads that are buffered/rewindable are supported. Payloads with forward only streams will be have the responses returned without any retry attempt.

### Supported Status Codes

|Status Code | Description | Notes|
|--|--|--|
| 429 | Too many requests | |
| 503 | Server Unavailable |  |
| 504 | Gateway Timeout |  |

## Future Potential Requirements

- Individual requests should support a policy mechanism that determines if a retry should occur.

## Performance Considerations

The thread processing a request should not be blocked while waiting for a retry delay to expire.

## Security Considerations

## References

[Graph Throttling](https://developer.microsoft.com/en-us/graph/docs/concepts/throttling)
