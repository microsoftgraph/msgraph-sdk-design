# BatchResponseContent

## Objectives

Provide component to simplify the processing of batch responses.

## Requirements

See [constraints](ContentArchitecturalConstraints.md) related to all content objects.

- Deserialize batch response payload into a collection of GraphResponse objects
- Allow users to enumerate collection of GraphResponse objects
- Allow users to retrieve a specific response object by using the same unique identifiers used in making the request.
- Allow users to retrieve a specific deserialized response content value by using the same unique identifiers used in making the request.
- Allow users to retrieve a specific stream response content value by using the same unique identifiers used in making the request.
- Allow users to retrieve a dictionary of the unique identifiers used in making the request corresponding to response status code to enable the re-emitting/retrying of failed individual requests.

## Performance Considerations

## Security Considerations

## Usage Examples

```cs
var batchRequestContent = new BatchRequestContent(graphServiceClient);

// add step 1 using requestInformation
var requestInformation = graphServiceClient.Users.ToGetRequestInformation();
var requestId = await batchRequestContent.AddBatchRequestStepAsync(requestInformation);

// add step 2 using requestInformation
var eventsRequestInformation = graphServiceClient.Me.Events.ToGetRequestInformation();
var eventsRequestId = await batchRequestContent.AddBatchRequestStepAsync(eventsRequestInformation);

// add step 2 using requestInformation
var imageRequestInformation = graphServiceClient.Me.Photo.Content.ToGetRequestInformation();
var imageRequestId = await batchRequestContent.AddBatchRequestStepAsync(eventsRequestInformation);

// send and get back response
var batchResponseContent = await graphServiceClient.Batch.PostAsync(batchRequestContent);

// get back specific native response message
HttpResponseMessage responseMessage = await batchResponseContent.GetResponseByIdAsync(requestId);

// get back specific response using identifier and deserialize it
EventCollectionResponse events = await batchResponseContent.GetResponseByIdAsync<EventCollectionResponse>(eventsRequestId);

// get back specific response using identifier and get the stream
Stream imageStream = await batchResponseContent.GetResponseStreamByIdAsync(imageRequestId);

// enumerate list of response status codes
var statusCodes = await batchResponseContent.GetResponsesStatusCodesAsync();
// filter the requests to retry
var rateLimitedResponses = statusCodes.Where(x => x.Value == HttpStatusCode.TooManyRequests).ToDictionary(x => x.Key, y => y.Value);
if (rateLimitedResponses.Any())
{
    var retryBatch = batchRequestContent.NewBatchWithFailedRequests(rateLimitedResponses);
    // send and get back response
    var retryBatchResponseContent = await graphServiceClient.Batch.PostAsync(retryBatch);
}
```

## References

1. [Graph Documentation]( https://learn.microsoft.com/graph/json-batching)
1. [OData](https://www.oasis-open.org/committees/download.php/60365/odata-json-format-v4.01-wd02-2017-03-24.docx)
