# Observability

[Observability](https://lightstep.com/blog/opentelemetry-101-what-is-observability/) generally refers to the ability to understand an application’s performance based on output data. This telemetry data is divided into three types: metrics, tracing and logging.

Microsoft Graph SDKs should provide the ability for application developers to gather data about the calls made via the SDKs. No telemetry data is captured and transmitted to Microsoft beyond what is included in the HTTP request headers.

## OpenTelemetry

[OpenTelemetry](https://opentelemetry.io) is an industry standard for capturing observability data from applications. The OpenTelemetry project has a set of libraries that contain the interfaces that an application interacts with to capture data. The primary primitive of OpenTelemetry is a span. An application...

## Why not just write to logs?

As application developers we are used to injecting a logger into our code and using log statements to add useful messages to a log file. Our log messages are often used to signal a particular code path has been taken.  A common question is whether to log the start of a section of code, or the end of a section of code. Sometimes it makes sense to log both to understand if a section has been entered and whether it was completed successfully.  There are times when it is useful to understand the time it took for a section of code.  Where code is reused it is often useful to understand the calling context of the code.  All of this can be done using log statements.  However, OpenTelemetry offers better solution.

OpenTelemetry provides different model for capturing information about the execution of a program.  It uses a span to surround a section of code.  A span always has a start and an end, and spans can be nested. When instrumenting library code, spans are created to identify interesting sections of code.  Spans also allow attaching information that provides context about the currently executing section of code.  

As a library author, adding a log statement comes with an expectation that a string will be written to a log file (or some other log storage) if the application developer has enabled the appropriate level level of logging.  The OpenTelemetry model does not make the same commitments to the library author. The library author suggests that a span might be of interest to the application developer, but how that information is processed and recorded is the responsibility of the `collector` component that is configured by the application developer.  The collector decides if the start and the end of a span will be recorded. It decides if the data elements are stored. It decides if the span should be timed.  Some collectors are sufficiently advanced that they may buffer the `trace` of a set of nested spans and based on the outcome decide if they are going to store the information. For example, if a HTTP call fails, then record the span details. Or, if a call takes more than some configured duration, then the span details are recorded.  A collector can be configured to either serialize events based on the timeline of when they happen regardless of the thread that is executing, or they can be collected to show just the events related to a single thread of execution.  This is one of the major distinctions between logging and tracing. Tracing shows activity related to a single inbound request to the library, whereas logging show all activity performed by the libary.

Spans also have more advanced concepts like a status to indicate if the span completed successfully. It is also possible to link spans together. Spans can also have events attached to them to indicate point in time events similar to how log events are captured.

While OpenTelemetry uses terms like `spans`, `baggage`, `traces`, `metrics`, not all of the libraries that support OpenTelemetry collectors use the same terms.  For example, in .NET span is represented by an Activity in the DiagnosticSource library. The OpenTelemetry ecosystem is a bit messy in its design because it is result of a set of pragmatic compromises between a large number of existing APM (Application Peformance Monitoring) vendors that already had existing solutions.  The benefit is broad operability and future that is far more aligned than the past.

The important take-away from this for us as SDK developers, is that with this slightly different model for capturing information about our running SDKs, we open up a wide range of possibilities for developers writing apps with our SDKs to get very clear visibility into what is happening inside our library.

## What to capture

Open Telemetry spans are nested and can have attributes attached to each span. The following spans should be created for each request with the indicated attributes.

```
Application Request: { }
  Convert RequestInformation to Native Request Object: { }
    [Serialize Request Payload]: { size, type }
  Send Request: { method, uriTemplate, !url }
    Obtain Token: { optionalClaims }
    [Redirect Request]: { statusCode }
    [Retry Request]: { statusCode, retry-after }
  Handle Response: { statusCode }
    [Deserialize Response Payload]: { size, type }
```

Attributes marked with ! should only be captured if the application has indicated the EUII can be captured.
Error messages should never include EUII unless it is explicitly indicted by the application.

