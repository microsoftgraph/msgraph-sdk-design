### Graph OpenAPI specification

- OpenAPI v1 specification:https://raw.githubusercontent.com/microsoftgraph/msgraph-metadata/master/openapi/v1.0/openapi.yaml
- OpenAPI beta specification:https://raw.githubusercontent.com/microsoftgraph/msgraph-metadata/master/openapi/beta/openapi.yaml

### Graph libraries using Kiota

#### Graph Service library:

The Graph Service libraries which contain the request builders will be generated using Kiota

Generation command:
```
kiota.exe -d https://raw.githubusercontent.com/microsoftgraph/msgraph-metadata/master/openapi/v1.0/openapi.yaml --language typescript -o path\msgraph-sdk-typescript\src -c GraphServiceClient -n MicrosoftGraph
```
- GraphServiceClient generated.
- Request builders generated

**Graph Core library**:

 - Graph core library takes a dependency on Kiota core.
 - Kiota core contains code for a generic HTTP API. That is:
    - code to call make the native HTTP client call to the API
    - any generic middleware.
 - Graph specific code stays in Graph core. Example: Graph Tasks.

#### Design


Graph Core

// Following is for typescript only
``` typescript
GraphCoreClient {
    httpClient:  KiotaHttpClient // importing the HTTPClient from Kiota core library
}
```

GraphServiceClient
```
class GraphServiceClient{
    httpCoreInstance : HttpCore // From Kiota

    UserRequestBuilder();
    MailRequestBuilder();
}

```

#### Graph core library specifications

This library contains the
- Factory method/classes returning the:
    - Core client with the user configurations such as authprovider, custom middleware, request options.
    - HttpCore instance for the service library with user configurations such as authprovider, custom middleware, request options.

- Tasks
    - PageIteratorTask
    - LargeFileUploadTask
    - Batching

- Authentication Providers
    - Use the `IAuthProvider` and  auth provider class from the Kiota Auth abstractions.


#### Graph Service library specifications


#### Graph Middlewares

- Import middlewares from Kiota core library
- How to add graph specific checks in the middleware. Example: The JS SDK checks if the url is a Graph url or a custom host passed by the user before appending telemetry headers or auth headers. If not, the headers are removed.


#### Use cases

- Graph core client

```
var response = new coreClient.requestURL("/me").get();
```

- Graph service client

```
var response = new serviceClient.me().get();
```

- Graph service client

```
var response = new serviceClient.requestURL("/me").get();
```

- Graph core client with a task

```
var response = new coreClient.requestURL("/me").get();

PageIterator(coreclient, response)
```

- Graph service client with a task

```
var response = new serviceClient.me().get();
PageIterator(service, response)
```


- The service library should extend the capabilities of the core library, that is, a user using service client should not require to create or use a core client to access any functionality. 
- This can be achieved by maintaining a parent abstract class for the core and the service client.
