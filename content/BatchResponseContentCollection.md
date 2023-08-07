# BatchResponseContentCollection

## Objectives

Provide component to simplify the processing of multiple batch responses made via `BatchRequestContentCollection`.

## Requirements

See [constraints](ContentArchitecturalConstraints.md) related to all content objects.

- Hold multiple batch response objects (i.e. `BatchResponseContent`) made from a collection of batch request objects(i.e. `BatchRequestContentCollection`)
- Allow users to enumerate collection of individual response objects.
- Allow users to retrieve a specific response object by using the SAME unique identifiers used in making the request (the unique identifiers will be unique across the `BatchResponseContentCollection` as they were unique across the `BatchRequestContentCollection` when making the request).
- Allow users to retrieve a specific deserialized response content value by using the same unique identifiers used in making the request.
- Allow users to retrieve a specific stream response content value by using the same unique identifiers used in making the request.
- Allow users to retrieve a dictionary of the unique identifiers used in making the request corresponding to response status code to enable the re-emitting/retrying of failed individual requests.

## Performance Considerations

## Security Considerations

## Usage Examples

```cs
var batchRequestContentCollection = new BatchRequestContentCollection(graphServiceClient);

// send and get back response
var batchResponseContentCollection = await graphServiceClient.Batch.PostAsync(batchRequestContentCollection);

// get back specific native response message
HttpResponseMessage responseMessage = await batchResponseContentCollection.GetResponseByIdAsync(requestId);

// get back specific response using identifier and deserialize it
EventCollectionResponse events = await batchResponseContentCollection.GetResponseByIdAsync<EventCollectionResponse>(eventsRequestId);

// get back specific response using identifier and get the stream
Stream imageStream = await batchResponseContentCollection.GetResponseStreamByIdAsync(imageRequestId);

// enumerate list of failed requests and create a new batch request
var statusCodes = await batchResponseContentCollection.GetResponsesStatusCodesAsync();
if(statusCodes.Any())
{
    var retryBatch = batchRequestContentCollection.NewBatchWithFailedRequests(rateLimitedResponses);
    // send and get back response
    var retryBatchResponseContentCollection = await graphServiceClient.Batch.PostAsync(retryBatch);
}

```

## References

1. [Graph Documentation]( https://learn.microsoft.com/en-us/graph/json-batching)
1. [OData](https://www.oasis-open.org/committees/download.php/60365/odata-json-format-v4.01-wd02-2017-03-24.docx)
