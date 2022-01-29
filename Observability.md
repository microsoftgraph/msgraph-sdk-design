# Observability

[Observability](https://lightstep.com/blog/opentelemetry-101-what-is-observability/) generally refers to the ability to understand an application’s performance based on output data. This telemetry data is divided into three types: metrics, tracing and logging.

Microsoft Graph SDKs should provide the ability for application developers to gather data about the calls made via the SDKs. No telemetry data is captured and transmitted to Microsoft beyond what is included in the HTTP request headers.

## OpenTelemetry

[OpenTelemetry](https://opentelemetry.io) is an industry standard for capturing observability data from applications. The OpenTelemetry project has a set of libraries that contain the interfaces that an application interacts with to capture data. The primary primitive of OpenTelemetry is a span. An application...

## What to capture

Open Telemetry spans are nested and can have attributes attached to each span. The following spans should be created for each request with the indicated attributes.

Application Request: { }
  Convert RequestInformation to Native Request Object: { }
    [Serialize Request Payload]: { size, type }
  Send Request: { method, uriTemplate, !url }
    Obtain Token: { optionalClaims }
    [Redirect Request]: { statusCode }
    [Retry Request]: { statusCode, retry-after }
  Handle Response: { statusCode }
    [Deserialize Response Payload]: { size, type }

Attributes marked with ! should only be captured if the application has indicated the EUII can be captured.
Error messages should never include EUII unless it is explicitly indicted by the application.

Current unknown:  Can information collected via spans also be emitted as logs?