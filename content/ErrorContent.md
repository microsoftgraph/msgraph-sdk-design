# Error Content

## Objectives

- Provide access to error information included in the response body of a 4XX or 5XX response. 
- Define standard class names for error scenarios.

## Requirements

- Match the specification as defined by the [Microsoft API Guidelines](https://github.com/microsoft/api-guidelines/blob/07a1e42e3e33f94df782abfcbaec24b63968c120/Guidelines.md#5-taxonomy) for errors returned in API responses.
- Service errors SHOULD be defined in a class named GraphServiceException or GraphServiceError depending on the platform.
- Service errors SHOULD contain the response status code and response headers.
- Client errors SHOULD be defined in a class named GraphClientException or GraphClientError depending on the platform.
- Client and service errors should use common properties according to the Microsoft API Guidelines.

GraphServiceException or GraphServiceError MUST support all potential OData error properties. See [Microsoft Graph error JSON](https://docs.microsoft.com/en-us/graph/errors#json-representation) for more information. The following is an example GraphServiceException response payload.

```json
{
    "error": {
        "code": "BadRequest",
        "message": "Resource not found for the segment 'mef'.",
        "innerError": {
            "date": "2021-07-02T01:40:19",
            "request-id": "1a0ffbc0-086f-4e8f-93f9-bf99881c65f6",
            "client-request-id": "225aed2b-cf4a-d456-b313-16ab196c2364"
        }
    }
}
```

## Reference service error implementations

JavaScript - [GraphError](https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/GraphError.ts)
.NET - [ServiceException](https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/feature/2.0/src/Microsoft.Graph.Core/Exceptions/ServiceException.cs)
Java - [GraphServiceException](https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/http/GraphServiceException.java)

## Reference client error implementations

JavaScript - [GraphClientError](https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/GraphClientError.ts)
.NET - [ClientException](https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Exceptions/ClientException.cs), everything had been a ServiceException.
Java - [ClientException](https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/core/ClientException.java)
