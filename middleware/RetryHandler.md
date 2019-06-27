# Retry Handler

## Objectives

Provide a reusuable component that provides application developers with effective handling of failed requests that can be retried.

## Requirements

- Retried Requests should be equivalent to the initial request made.
- A `retry-attempt` header SHOULD be added which contains a numeric value representing the retry number.  This value will be used for diagnostics and determining the effectiveness of the retry handler.
- Retries MUST respect the `retry-after` response header if provided.
- Where no `retry-after` header is provided by the server, an exponential backoff with random offset hueristic should be used to determine the retry delay.
- The client code can specify a custom value for the maximum delay value for time-based retries. The default is set at 0 second.
- Multiple retry attempts will be limited to not exceed the `RetriesTimeLimit` in the case of time-based retries and `RetryCounts` in count-based retries. This is applicable to both 429 and 503/504.
- In the case where the client receives a `retry-after` value that is greater than the remaining `RetriesTimeLimit` the client should return the failed response immediately.
- Customers who intend to use the time-based retry will need to provide a maximum delay value in seconds greater than 0 for `RetriesTimeLimit`, otherwise the count-based retry will be used by default.
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

## Open Issues
- New requests made while an existing request has been queued for retry, should be considered for adding to the queue.  
- Requests with the exact same URL should be queued.
- Requests with the same path should be queued.
- Requests with n matching initial segments should be queued after 5-n throtted requests. e.g. If a there have been 3 throttled requests for requests with the first two segments matching, then the next matching request should be queued.  
