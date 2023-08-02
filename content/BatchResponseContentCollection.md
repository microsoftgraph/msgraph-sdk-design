# BatchRequestContentCollection

## Objectives

Provide component to simplify the processing of multiple batch responses made via `BatchRequestContentCollection`.

## Requirements

See [constraints](ContentArchitecturalConstraints.md) related to all content objects.

- Hold multiple batch response objects (i.e. `BatchResponseContent`) made from a collection of batch request objects(i.e. `BatchRequestContentCollection`)
- Allow users to enumerate collection of individual response objects
- Allow users to retrieve a specific response object by using the same unique identifiers used in making the request.
- Allow users to retrieve a specific deserialized response content value by using the same unique identifiers used in making the request.
- Allow users to retrieve a specific stream response content value by using the same unique identifiers used in making the request.

## Performance Considerations

## Security Considerations

## References

- [1] [Graph Documentation]( https://developer.microsoft.com/en-us/graph/docs/concepts/json_batching)
- [2] [OData](https://www.oasis-open.org/committees/download.php/60365/odata-json-format-v4.01-wd02-2017-03-24.docx)
