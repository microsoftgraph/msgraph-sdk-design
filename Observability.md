# Observability

[Observability](https://lightstep.com/blog/opentelemetry-101-what-is-observability/) generally refers to the ability to understand an application’s performance based on output data. This telemetry data is divided into three types: metrics, tracing and logging.

Microsoft Graph SDKs should provide the ability for application developers to gather data about the calls made via the SDKs. No telemetry data is captured and transmitted to Microsoft beyond what is included in the HTTP request headers.

## OpenTelemetry

[OpenTelemetry](https://opentelemetry.io) is an industry standard for capturing observability data from applications. The OpenTelemetry project has a set of libraries that contain the interfaces that an application interacts with to capture data. Observability can be divided in four main different goals:

- Telemetry: allows the owners of a product to understand better its usage.
- Tracing: allows the administrators of an application to understand the details of an action end to end, across multiple systems.
- Logging: allows the administrators of an application to understand what is happening for the application globally, this aspect is orthogonal to the tracing aspect.
- Metrics: allows the administrators to measures specific events or facets of the application. (e.g. number of requests per minute, average latency...)

The design guidelines already provide separate details about the telemetry aspect and it'll be out of scope for the current document. The logging specification for Open Telemetry is currently in a draft state, and sparsely implemented, for that reason we'll revisit logging requirements at a later time. For metrics, we need to get a better understanding of what elements applications administrators care about in their day to day activities, more user research is required.

## Why not just write to logs?

As application developers we are used to injecting a logger into our code and using log statements to add useful messages to a log file. Our log messages are often used to signal a particular code path has been taken.  A common question is whether to log the start of a section of code, or the end of a section of code. Sometimes it makes sense to log both to understand if a section has been entered and whether it was completed successfully.  There are times when it is useful to understand the time it took for a section of code.  Where code is reused it is often useful to understand the calling context of the code.  All of this can be done using log statements.  However, OpenTelemetry offers better solution.

OpenTelemetry provides different model for capturing information about the execution of a program.  It uses a span to surround a section of code.  A span always has a start and an end, and spans can be nested. When instrumenting library code, spans are created to identify interesting sections of code.  Spans also allow attaching information that provides context about the currently executing section of code.  

As a library author, adding a log statement comes with an expectation that a string will be written to a log file (or some other log storage) if the application developer has enabled the appropriate level level of logging.  The OpenTelemetry model does not make the same commitments to the library author. The library author suggests that a span might be of interest to the application developer, but how that information is processed and recorded is the responsibility of the `collector` component that is configured by the application developer.  The collector decides if the start and the end of a span will be recorded. It decides if the data elements are stored. It decides if the span should be timed.  Some collectors are sufficiently advanced that they may buffer the `trace` of a set of nested spans and based on the outcome decide if they are going to store the information. For example, if a HTTP call fails, then record the span details. Or, if a call takes more than some configured duration, then the span details are recorded.  A collector can be configured to either serialize events based on the timeline of when they happen regardless of the thread that is executing, or they can be collected to show just the events related to a single thread of execution.  This is one of the major distinctions between logging and tracing. Tracing shows activity related to a single inbound request to the library, whereas logging show all activity performed by the library.

Spans also have more advanced concepts like a status to indicate if the span completed successfully. It is also possible to link spans together. Spans can also have events attached to them to indicate point in time events similar to how log events are captured.

While OpenTelemetry uses terms like `spans`, `baggage`, `traces`, `metrics`, not all of the libraries that support OpenTelemetry collectors use the same terms.  For example, in .NET span is represented by an Activity in the DiagnosticSource library. The OpenTelemetry ecosystem is a bit messy in its design because it is result of a set of pragmatic compromises between a large number of existing APM (Application Performance Monitoring) vendors that already had existing solutions.  The benefit is broad operability and future that is far more aligned than the past.

The important take-away from this for us as SDK developers, is that with this slightly different model for capturing information about our running SDKs, we open up a wide range of possibilities for developers writing apps with our SDKs to get very clear visibility into what is happening inside our library.

## What to capture - tracing

Open Telemetry spans are nested and can have attributes attached to each span. The following spans should be created for each request with the indicated attributes.

```JSONC
{
  "ApplicationRequest?": { // this span is owned by the application itself in the calling code that calls the chained API surface or equivalent
    "SetContentFromParsable?": { // request information or equivalent when a request body is used from a model, also applies to setContentFromScalar and from stream
      "com.microsoft.kiota.request.type": "Fully qualified name of the content model"
    },
    "SendAsync - <URI template>": { // http request adapter or equivalent, also applies to the other overloads like sendPrimitiveAsync etc...
      "http.response.body.size?": "int value for the response content length",
      "http.response.status_code": "http response status code",
      "network.protocol.name": "1.1 or 2 or 3",
      "http.response.header.content-type?": "value from the response content type header",
      "http.request.method": "http method for the request",
      "url.scheme": "http or https",
      "url.full!": "URI of the request",
      "server.address": "request host",
      "http.request.body.size?": "int value for the request content length",
      "http.request.header.content-type?": "value from the request content type header",
      "url.uri_template": "URI template for the request",
      "com.microsoft.kiota.response.type": "The fully qualified name of the deserialized model",
      "getHttpResponseMessage": {
        "GetAuthorizationToken": { // comes from the authentication provider
          "com.microsoft.kiota.authentication.is_url_valid": "true if the URL was valid, false otherwise (https, host)",
          "com.microsoft.kiota.authentication.additional_claims_provided": "true when CAE/POP claims were provided by the request adapter, false otherwise",
          "com.microsoft.kiota.authentication.scopes": "comma separated value of the scopes for the request"
        },
        "getRequestFromRequestInformation": {
          // collection of all the http attributes from the parent span might be done here, this span is here to measure errors/timing of the conversion to a native object
        },
        "RetryHandler_intercept?": { // single span when the request is successful, multiple when retries occurred
          "com.microsoft.kiota.handler.retry.enable": "boolean, true if the handler is present and enabled",
          "http.request.resend_count": "int number of retries that occurred before the request was successful",
          "http.request.resend_delay?": "int duration in seconds before the request was retried",
          "http.response.status_code": "status code of the response",
          "RedirectHandler_intercept?": { // single span when the request is successful, multiple when retries occurred
            "com.microsoft.kiota.handler.redirect.enable": "boolean, true if the handler is present and enabled",
            "com.microsoft.kiota.handler.redirect.count": "int, the number of redirects that occurred",
            "http.response.status_code": "status code of the response",
            "CompressionHandler_intercept?": {
              "com.microsoft.kiota.handler.compression.enable": "boolean, true if the handler is present and enabled",
              "http.request.body.compressed": "boolean, true if the response was compressed by the handler",
              "http.request.body.size": "long, byte size of the request body",
              "DecompressionHandler_intercept?": {
                "com.microsoft.kiota.handler.decompression.enable": "boolean, true if the handler is present and enabled",
                "ParametersNameDecodingHandler_intercept?": {
                  "com.microsoft.kiota.handler.parameters_name_decoding.enable": "boolean, true if the handler is present and enabled",
                    "SunsetHandler_intercept?": {
                      "com.microsoft.kiota.handler.sunset.enable": "boolean, true if the handler is present and enabled",
                        "Event - com.microsoft.kiota.sunset_header_received?": {
                          // raised only if a sunset header is present in the response
                          "sunset_date": "Date at internet date format from the sunset header value",
                          "sunset_link?": "link header with a sunset value for rel (only the URI)"
                        },
                        "HeadersInspectionHandler_intercept?": {
                          "com.microsoft.kiota.handler.headersInspection.enable": "boolean, true if the handler is present and enabled",
                          "BodyInspectionHandler_intercept?": {
                          "com.microsoft.kiota.handler.bodyInspection.enable": "boolean, true if the handler is present and enabled",
                            "AuthorizationHandler_intercept?": {
                              "com.microsoft.kiota.handler.authorization.enable": "boolean, true if the handler is present and enabled",
                              "com.microsoft.kiota.handler.authorization.token_present?": "boolean, true if an authorization header was already present",
                              "com.microsoft.kiota.handler.authorization.token_obtained?": "boolean, true if obtaining the token was successful",
                              "Event - com.microsoft.kiota.handler.authorization.challenge_received?" : {
                                // raised only if a www-authenticate header has been received and we're obtaining a new token
                                "com.microsoft.kiota.handler.authorization.token_obtained": "boolean, true if obtaining the token was successful",
                                "http.request.resend_count": "int number of retries that occurred before the request was successful"
                              },
                              "Request_transport?": {
                                // this span is present only to measure network latency and deduct it from the middleware pipeline
                              }
                            }
                          }
                        }
                    }
                }
              }
            }
          }
        },
        "retryCAEResponseIfRequired": {
          "http.request.resend_count?": "1 of we had to retry for CAE challenge, not set otherwise",
          "Event - com.microsoft.kiota.authenticate_challenge_received?": {
            // raised only if the application is receiving a CAE challenge
          }
        },
      },
      "throwFailedResponses": {
        "com.microsoft.kiota.error.mapping_found": "boolean, whether the client has error mappings for that status code",
        "com.microsoft.kiota.error.body_found": "boolean, whether the client found an error body in the response",
        "status": "error", // use the set status method for those two, they are a special kind of attribute
        "status_message": "received_error_response"
      },
      "Event - com.microsoft.kiota.response_handler_invoked?": {
        // raised only if the application has provided a custom response handler
      },
      "getRootParseNode": {
        // to measure the time it gets to pull the stream
      },
      "GetObjectValue": { 
        // or the equivalent deserialization method name, this is mainly to measure deserialization errors/time 
      }
    }
  }
}
```

Legend:

- Objects braces describe a span.
- Properties describe attributes/tags.
- Spans prefixed with a `Event` represent an event.
- Spans starting with an uppercase letter represent a public method and should always be present.
- Spans starting with a lowercase letter represent a private method and might be structured differently depending on implementation details.
- ? sign describes something optional (depending on the request or configuration).
- ! should only be captured if the application has indicated the EUII can be captured.

> Note: Error messages should never include EUII unless it is explicitly indicated by the application.
> Note: spans that have the same parent span are described sequentially.
> Note: the nesting of the middleware handlers depends on how they were configured by the client application.
