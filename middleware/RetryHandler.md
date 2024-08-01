# Retry Handler

## Objectives

Provide a reusuable component that provides application developers with effective handling of failed requests that can be retried.

## Requirements

- Retried Requests should be equivalent to the initial request made.
- A `retry-attempt` header SHOULD be added which contains a numeric value representing the retry number.  This value will be used for diagnostics and determining the effectiveness of the retry handler.
- Retries MUST respect the `retry-after` response header if provided.
- Where no `retry-after` header is provided by the server, an exponential backoff with random offset hueristic should be used to determine the retry delay.
- Customers can specify a custom value for the `RetriesTimeLimit` greater than 0 to introduce time-based evaluated request retries alongside the default count-based request retry.
- If the `RetriesTimeLimit` value has been specified, the cumulative retry time and `retry-after` value for each request retry will be evaluated against this value; if the cumulative retry time plus the `retry-after` value is greater than the `RetriesTimeLimit`, the failed response will be immediately returned, else the request retry continues. This is applicable to both 429 and 503/504.
- Only requests with payloads that are buffered/rewindable are supported. Payloads with forward only streams will be have the responses returned without any retry attempt.
- Additional observability requirements in [Observability](../Observability.md)

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

## Open Issues
- New requests made while an existing request has been queued for retry, should be considered for adding to the queue.  
- Requests with the exact same URL should be queued.
- Requests with the same path should be queued.
- Requests with n matching initial segments should be queued after 5-n throtted requests. e.g. If a there have been 3 throttled requests for requests with the first two segments matching, then the next matching request should be queued.  
