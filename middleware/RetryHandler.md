# Retry Handler

## Objectives

Provide a reusuable component that provides application developers with effective handling of failed requests that can be retried.

## Requirements

- Retried Requests should be equivalent to the initial request made.
- A `retry-attempt` header SHOULD be added which contains a numeric value representing the retry number.  This value will be used for diagnostics and determining the effectiveness of the retry handler.
- Retries MUST respect the `retry-after` response header if provided.
- Where no `retry-after` header is provided by the server, an exponential backoff with random offset hueristic should be used to determine the retry delay.
- Retries should be limited to a maximum delay value. The default value for this is set at 180 seconds.
- The client code can specify a custom value for the maximum delay.
- Retries will be based on the cumulative retry delays compared against the maximum delay specified by the client code, cumulatively added across all retries within the context of a single call. This is applicable to both 429 and 503/504.
- Upon expiry of the maximum delay, when the cumulative retry delay is greater than the specified maximum delay, the `Task` should be cancelled and an `exception` thrown with a relevant message.
- In the case whereby the client code specifies a maximum delay that is less than the received `retry-after` header value from the server, the cumulative retry delay will run for the duration of the `retry-after` value.
- Only requests with payloads that are buffered/rewindable are supported.  Payloads with forward only streams will be have the responses returned without any retry attempt.

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