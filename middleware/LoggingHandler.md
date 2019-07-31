# Logging Handler [Draft 1]

## Objectives

This feature is a middleware component that allows developers to plug in their own 3rd party logging platform to there application, and monitor items relating to the latency of the requests and responses of the handlers in the middleware pipeline. These items will be provided to a thrid party logging framework. This feature also allows you to configure a mechanism to track request that go over a specified time and provide that information to 3rd party apropriate log level. 

## Requirements 
 <ul>
 <li>The logger will allow users to trace various spans that occur during the authentication and the call to graph service but will have the potential to trace other operations and requests.</li> 
 <li>Each span is identified by it's name, span ID, start and end time (latency), the trace id it's assoicated with, as well as other           fields.</li>
  <li> Each span must have a unique name and Id if within the same trace.</li>
  <li> Each trace must have a unique name an ID.</li>
  <li> If a trace or span exceeds the set time given for that operation then it will send...</li>
 </ul>
 
## Security Considerations

## Performance Considerations
<ul>
 <li>While waiting for the logger to finish a trace or span..after a certain amount of time quit the process...<li>
</ul>
