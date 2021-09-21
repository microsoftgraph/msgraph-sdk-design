### What is Kiota? 
Learn [Kiota](https://github.com/microsoft/kiota#readme)

### APIs that can use Kiota 
- Any HTTP API with an Open API specification (For generators). Not limited to Microsoft Graph.

### Kiota libraries

Common for all languagues:
- Kiota generator: Generates the request builders
The generated code contains the APIClient class and the request builders. 

```
class APIClient {
    httpCore : HttpCore;

    getResourceById(): ResourceRequestBuilder;
}
```

Libraries for each language:

- Kiota abstractions
    - This library contains only abstract classes and interfaces which can be implemented to connect the generated request builders with a client library that makes the call to the API.
    [Kiota abstractions dotnet](https://github.com/microsoft/kiota/tree/main/abstractions/dotnet)
    [Kiota abstractions java](https://github.com/microsoft/kiota/tree/main/abstractions/java)
    [Kiota abstractions typescript](https://github.com/microsoft/kiota/tree/main/abstractions/typescript)

    - Contains the following:
        - HttpCore interface

        ```
        interface HttpCore{
            getSerializationWriterFactory(): SerializationWriterFactory;
   
            sendAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType>;
            sendCollectionAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType[]>;
            sendPrimitiveAsync<ResponseType>(requestInfo: RequestInformation, responseType: "string" | "number" | "boolean" | "Date" | "ReadableStream", responseHandler: ResponseHandler | undefined): Promise<ResponseType>;
            sendNoResponseContentAsync(requestInfo: RequestInformation, responseHandler: ResponseHandler | undefined): Promise<void>;
            enableBackingStore(backingStoreFactory?: BackingStoreFactory | undefined): void;
        }
        ````
        - Serialization interfaces
        - Authentication: `IAuthProvider`, abstract classes- `anonymousAuthenticationProvider` and `BearerTokenAuthneticationProvider`
        - Utils
        - ResponseHandler interface
        - MiddlewareOption class
        - RequestInformation
        - HttpMethod Enum

- Kiota core
    - This is generic core client library (functionality or purpose similar to Microsoft Graph core libraries)
    [Kiota core dotnet](https://github.com/microsoft/kiota/tree/main/http/dotnet/httpclient)
    [Kiota core java](https://github.com/microsoft/kiota/tree/main/http/java/okhttp)
    [Kiota core typescript](https://github.com/microsoft/kiota/tree/main/http/typescript/fetch)
    - This library contains the following:
        -  HttpCore class implementing the abstraction HttpCore interface.
        -  HttpClientbuilder (.NET, Java) or HTTPClient class (TS) (processes and records the middleware chain used while making requests.)
        -  Middlewares: AuthHandler, Retry, Redirect, Compression, Caching, Telemetry
    
```
class HttpCore implements kiota.abstractions.HttpCore {
    constructor(authProvider, parseNodeFactory, serializationWriteFactory, httpClient);

    sendAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType>;
    sendCollectionAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType[]>;
    sendPrimitiveAsync<ResponseType>(requestInfo: RequestInformation, responseType: "string" | "number" | "boolean" | "Date" | "ReadableStream", responseHandler: ResponseHandler | undefined): Promise<ResponseType>;
    sendNoResponseContentAsync(requestInfo: RequestInformation, responseHandler: ResponseHandler | undefined): Promise<void>;
    enableBackingStore(backingStoreFactory?: BackingStoreFactory | undefined): void;
}

class HttpClient{
    middlewares : Middlewares();
    send() //calls the MiddlewareChain to make a  request with the native HttpClient;
}
```

Kiota Generated API Client (This will be the GraphServiceClient)

```
class APIClient {

    httpCoreInstance : HttpCore

    SampleRequestBuilder(httpCoreInstance);
    ExampleRequestBuilder(httpCoreInstance);
}
```

- Kiota serializations 
    - This library contains implementation of the kiota abstractions serialization interfaces
    - Currently targets `JSON` format.
    - Other formats to consider:
        - Multipart payload
        - Base64 encoded string
        - text/plain
        - xml

- Kiota auth providers

### Example of how we use the Kiota libraries:

1. Clone the Kiota repo, build the project and navigate the path containing the `kiota.exe`
2. Generate the request builder using the command:
```
kiota.exe -d ../msgraph-sdk-powershell/openApiDocs/v1.0/mail.yml --language csharp -o ../somepath -n namespaceprefix
```
3. The generation should contain the `APIClient` class and request builders.

5. Create a demo class to test the `APIClient`.

4. Import the `KiotaCore` library
- [dotnet](https://github.com/microsoft/kiota/packages/826853)
- [TypeScript](https://github.com/microsoft/kiota/packages/827040)

Example:
```
import { HttpCore } from '@microsoft/kiota-http-fetch';
```

5. Import the serialization library
- [dotnet](https://github.com/microsoft/kiota/packages/826855)
- [TypeScript](https://github.com/microsoft/kiota/packages/827041)

```
import {JsonParseNodeFactory, JsonSerializationWriterFactory} from '@microsoft/kiota-serialization-json';
```

Example:

6. Import the AuthProvider library or implement the IAuthProvider interface to create a custom AuthProvider class.

Find a suitable Kiota Authentication library in https://github.com/microsoft/kiota/packages


7. Create a HTTPCore instance:

```
var httpCore = new HTTPCore(authProvider, JsonParseNodeFactory, JsonSerializationWriterFactory)
```

8. Create a APIClient instance:

```
var apiClient = new APIClient(httpCore);
var response = apiClient.users("<ID>").get();
```

### Kiota Middlewares

#### AuthHandler
#### Retry

```
Retry-After: http-date
Retry-After: delay-seconds
```

#### Redirect

#### Compression
#### Caching

#### Telemetry

- Introduce a callback option so that user can add custom telemetry code.
- Discuss: Why introduce a callback instead user can have a custom middleware chain with telemetry  handler implementation?
- Discuss: Should the callback pattern be used for every middleware?












