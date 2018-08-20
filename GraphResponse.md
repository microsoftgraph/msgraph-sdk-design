# GraphResponse Object

## Objectives

Provide a decorator for the native response message to enable attaching Graph specific behaviour and state to a native HTTP response object.

## Requirements

- Maintain reference to corresponding GraphRequest object
- Enable transformation from GraphResponse object to native response object.  This may be lossy.
- Enable transformation from native response object to GraphResponse object
- Provide access to response content as domain object.