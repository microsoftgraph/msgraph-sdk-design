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

## Performance Considerations

## Security Considerations

## References

1. [Graph Documentation]( https://developer.microsoft.com/en-us/graph/docs/concepts/json_batching)
1. [OData](https://www.oasis-open.org/committees/download.php/60365/odata-json-format-v4.01-wd02-2017-03-24.docx)
