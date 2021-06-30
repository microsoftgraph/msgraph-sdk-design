# Error Content

## Objectives

- Provide access to error information included in the response body of a 4XX or 5XX response. 
- Define standard class names for error scenarios.

## Requirements

- Match the specification as defined by the [Microsoft API Guidelines](https://github.com/microsoft/api-guidelines/blob/07a1e42e3e33f94df782abfcbaec24b63968c120/Guidelines.md#5-taxonomy) for errors returned in API responses.
- Service errors SHOULD be defined in a class named ServiceException or ServiceError depending on the platform.
- Client errors SHOULD be defined in a class named ClientException or ClientError depending on the platform.
- Client and service errors should use common properties according to the Microsoft API Guidelines.
