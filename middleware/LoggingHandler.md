# Logging Handler [Draft 2]

## Objectives
<ul>
 <li>This feature is a middleware component that allows developers to plug in their own 3rd party logging platform to their application, currently it enables developers to log items in the graph SDK related to capturing individual default handler latency’s, graph client latency, redirect count, exceptions, request and client ID’s. These items will be provided to a third-party logging framework through various exporters.</li>
</ul>

## Requirements 
 <ul>
 <li> Allow developer to trace items related to exceptions (auth, client, service) by configuring areas where there could be a potential exception with logger. Exception logs will contain information about the request, response, and type of exception.</li> 
 <li>	Enable developer to monitor redirect counts with logger by tracing redirect operations through calls.</li>
  <li> Configure default handlers with mechanism to capture latencies. Allow developer to provide optional custom items to log, provide base class that customers can use to configure custom handlers to use logger</li>
  <li> Enable developer to log full graph end to end latency by logging starting at the auth handler as the root span until the response from the graph service. All operations after auth handler will bu sub spans of the same tracer. Future scope- enable latency to start with logging handler instead of auth handler.</li>
  <li>Allow developer to log client-request-id and application-id’s</li>
 </ul>
 
## Security Considerations

## Performance Considerations
<ul>
 <li>While waiting for the logger to finish a trace or span..after a certain amount of time quit the process...<li>
</ul>
