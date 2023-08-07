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

// send and get back response
var batchResponseContent = await graphServiceClient.Batch.PostAsync(batchRequestContent);

// get back specific native response message
HttpResponseMessage responseMessage = await batchResponseContent.GetResponseByIdAsync(requestId);

// get back specific response using identifier and deserialize it
EventCollectionResponse events = await batchResponseContent.GetResponseByIdAsync<EventCollectionResponse>(eventsRequestId);

// get back specific response using identifier and get the stream
Stream imageStream = await batchResponseContent.GetResponseStreamByIdAsync(imageRequestId);

// enumerate list of failed requests and create a new batch request
var statusCodes = await batchResponseContent.GetResponsesStatusCodesAsync();
if(statusCodes.Any())
{
    var retryBatch = batchRequestContent.NewBatchWithFailedRequests(rateLimitedResponses);
    // send and get back response
    var retryBatchResponseContent = await graphServiceClient.Batch.PostAsync(retryBatch);
}

```

## References

1. [Graph Documentation]( https://learn.microsoft.com/en-us/graph/json-batching)
1. [OData](https://www.oasis-open.org/committees/download.php/60365/odata-json-format-v4.01-wd02-2017-03-24.docx)
