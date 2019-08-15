# Logging Handler [Draft 2]

## Objectives

This feature is a middleware component that allows developers to plug in their own 3rd party logging platform to their application, currently it enables developers to log items in the graph SDK related to: request to graph service latency, authentication latency, redirect count, retry count, exceptions, request, application id's, and client id’s. These items will be provided to a third-party logging framework through various exporters. List of items is open-ended and can be changed.

## Requirements
•	Allow developer to trace items related to exceptions (auth, client, service) by configuring areas where there could be a potential exception with logger. Exception logs will contain information about the request, response, and type of exception.

•	Enable developer to monitor redirect and retry counts with stats by tracing redirect operations through calls and collecting custom metrics.

•	Configure default handlers with mechanism to capture latencies. Allow developer to provide optional custom items to log, provide base class that customers can use to configure custom handlers to use logger.

•	Enable developer to log full graph end to end latency by logging starting at the auth handler as the root span until the response from graph service. All operations after auth handler will be sub spans of the same tracer. Future scope-- enable latency to start with logging handler instead of auth handler.

•	The Microsoft Graph .NET Core Client Library will have a dependency to OpenCensus.

•	The items that will potentially be logged will have an instrumentation to be able to be turned on an off per indivisual developer’s usage. The logging provider will be enabled if the user has at least one item they want to log and have set up an exporter.

•	The logging handler will be created in the Graph Client Factory when a new http client is configured.

•	The developer will be required to provide information about which items they would like to log. They will be required to configure their own exporters. 

•	Allow developer to log client-request-id and application-id’s with stats.


## Example Usage
```cs

//Create tracer object to record ditributed tracing information from ms graph .net core client library
private static ITracer tracer = Tracing.Tracer;

//Configure exporter to export traces to Azure Application Insights
TelemetryConfiguration.Active.InstrumentationKey = "InstrumentationKey";
var appInsightsexporter = new ApplicationInsightsExporter(Tracing.ExportComponent, Stats.ViewManager, TelemetryConfiguration.Active);

//Start exporter
appInsightsexporter.Start()

//Configure 100% sample rate so that all traces will be sampled and exported
var spanBuilder = tracer
.SpanBuilder("incoming request")
.SetRecordEvents(true)
.SetSampler(Samplers.AlwaysSample);

// 4. Create a scoped span. It will end automatically when using statement ends
using (var scopedSpan = spanBuilder.StartScopedSpan())

{
    tracer.CurrentSpan.AddAnnotation("Latency of getting user's profile.");
    //Get the current user's profile.
    User me = await graphClient.Me.Request(requestOptions).WithUserAccount(ClaimsPrincipal.Current.ToGraphUserAccount()).GetAsync();

 }

//Shutdown the exporter so it will flush queued traces to Application Insights.
Tracing.ExportComponent.SpanExporter.Dispose();


```

## Performance Considerations
## Security Considerations

## Open Issues

How to link stats within a span to that trace?

What is the default behaviour of OpenCensus when an exporter is not configured?

Can we get an instance of an exporter using OpenCensus static classes?

How should we configure the tracer within our client library?
