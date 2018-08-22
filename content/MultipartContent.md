# Multipart Content

## Objectives

- Provide support for request and response payloads using multipart formats.  These include `multipart/form-data`, `multipart/mixed` and `multipart/alternative`.

## Requirements

See [constraints](ContentArchitecturalConstraints.md) related to all content objects.

- Allow adding a part containing a set of HTTP headers and array of bytes.
- Provide helper for adding form-data part with name, content-type and byte array.
- Provide helper for adding part with filename, content-type and byte-array.
- Allow user to specific content type of multi-part payload
- Allow user to override default boundary
- Serialize all headers and boundaries using US-ASCII charset.

## Performance Considerations

## Security Considerations

