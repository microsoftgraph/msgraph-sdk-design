### Graph OpenAPI description

- [OpenAPI v1 specification](https://raw.githubusercontent.com/microsoftgraph/msgraph-metadata/master/openapi/v1.0/openapi.yaml)
- [OpenAPI beta specification](https://raw.githubusercontent.com/microsoftgraph/msgraph-metadata/master/openapi/beta/openapi.yaml)

### Graph libraries using Kiota

#### Graph Service library:

The Graph Service libraries which contain the request builders will be generated using Kiota

Generation command:
```Shell
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
    requestAdapterInstance : RequestAdapter  // From Kiota

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
    - Use the `IAuthenticationProvider` and  authentication provider class from the Kiota abstractions.

- BaseRequest/IBaseRequest
    - These classes/interfaces would be dropped as they are rendered redundant by the existence of `RequestInformation` from the Kiota core libraries and provide better support for conversion to native http request objects.
    - Existing extensions/features/enrichments to the classes/interfaces would be moved to the `RequestInformation` through the graph core library.(e.g. one can use `BaseRequest` instances to create a batch request, now we would use `RequestInformation` instances)

- HttpProvider/IHttpProvider
    - These classes/interfaces would be dropped as they are rendered redundant by the existence of `IRequestAdapter` from the Kiota core libraries and provide the necessary functionality to perform http requests

- Response Handler
    - Any existing/needed response handlers to now implement the `IResponseHandler` (or equivalent) from kiota core libraries

#### Graph Service library specifications

- Discuss about ways to add custom code or custom configurations to the generated `GraphServiceClient` code.
- The custom configurations that can go here are as follows: 
 - Setting the default for `HttpCore` with a Graph specific `HttpCore` instance. 
 - Adding multiple constructors to the `GraphServiceClient`. 

#### Graph Middlewares

- Import middlewares from Kiota core library
- How to add graph specific checks in the middleware. Example: The JS SDK checks if the url is a Graph url or a custom host passed by the user before appending telemetry headers or auth headers. If not, the headers are removed.

- AuthHandler - none - This avoids having to configure both the middleware and the provider and the authentication provider implementations are available across any http client
- RetryHandler - Kiota core
- RedirectHandler - Kiota core
- CompressionHandler - Kiota core
- CachingHandler - Kiota core
- ChaosHandler - Graph core 
#### Use cases

- Graph core client 
```TypeScript
var response = new coreClient.requestURL("/me").get();
```

- Graph service client
```
var response = new serviceClient.me().get();
```

- Graph service client calling a core functionality

- The service library should extend the capabilities of the core library, that is, a user using service client should not require to create or use a core client to access any functionality. 
```
var response = new serviceClient.requestURL("/me").get(); // Example : a user of the JS SDK should be able to access GraphServiceClient.api(/me) without initialising the core.
```

- Graph core client with a task

```
var response = new coreClient.requestURL("/me").get();

PageIterator(coreclient, response)
```

- Graph service client with a task

```
var response = new serviceClient.me().get();
PageIterator(serviceClient, response)
```

- The service library should extend the capabilities of the core library, that is, a user using service client should not require to create or use a core client to access any functionality.
-  Some options to  achieve this (TBD):
 -  Maintain a parent abstract class for the core and the service client.
 -  `GraphServiceClient` extends the `GraphCoreClient`.

### More questions

- Does the generated ApiClient have to be tightly coupled with the generated request builders? Should it always be tightly coupled? [MEM] I ask as there could be scenarios where the request builders are not necessary and the client developers just wants the Kiota.Core functionality in the client, and will use client.requestUrl<Response>.Get();

