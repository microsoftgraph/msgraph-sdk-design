# Microsoft Graph SDKs - Requirements and Design

This repository holds documents related to current and on-going work on Microsoft Graph SDKs

## SDK Features Support

| Component |Feature| .net | Java | JS | ObjC | Powershell | PHP | Ruby | Python | Go |  
|--|--|--|--|--|--|--|--|--|--|--|
|[Middleware](middleware/middleware.md)
| | Pipeline                |✓|✓|[✓][js_middleware]|[✓][objc_middleware]|||
| | [Authorization Handler](middleware/AuthorizationHandler.md)   |[✓][dotnet_authhandler] |[✓][java_authhandler]|[✓][js_authhandler]|[✓][objc_authhandler] | | |
| | [Retry Handler](middleware/RetryHandler.md)              |[✓][dotnet_retryhandler]|✓|[✓][js_retryhandler]|[✓][objc_retryhandler]| | |
| | [Redirect Handler](middleware/RedirectHandler.md)        |[✓][dotnet_redirecthandler]|✓|[✓][js_redirecthandler]|[✓][objc_redirecthandler] | | |
| | [Compression Handler](middleware/CompressionHandler.md) |[✓][dotnet_compressionhandler]|N|N|N| | |
| | [Logging Handler](middleware/LoggingHandler.md) | | | | | | |
| | [Telemetry Handler](middleware/TelemetryHandler.md) |[O][dotnet_telemetryhandler]| | | | | |
| | [Connection Management]() | | | | | | |
| | [Long Running Operations]() | | | | | | |
| | [Chaos Handler]() | | | | | | |
| | [Service Discovery Handler]() | | | | | | |
| [Content](content/ContentArchitecturalConstraints.md)
|| [Batch Request Content](content/BatchRequestContent.md)     |[✓][dotnet_batchrequestcontent]|✓|[✓][js_batchrequestcontent]|[✓][objc_batchrequestcontent] | | |
|| [Batch Response Content](content/BatchResponseContent.md)   |[✓][dotnet_batchrequestcontent]|✓|[✓][js_batchresponsecontent] |[✓][objc_batchresponsecontent] | | |
|| [Multipart Content](content/MultipartContent.md)            |✓|[✓][java_multipartcontent]| | | | |
|| [Error Content](content/ErrorContent.md)            | | | | | | |
| Graph Components
|| [Client Factory](GraphClientFactory.md)           |[✓][dotnet_clientfactory]|✓|[✓][js_graphclientfactory]|[✓][objc_graphclientfactory]| | |
|| [Response Handling](ResponseHandler.md) |[✓][dotnet_responsehandler]| |[✓][js_responsehandler]||||
| Tasks
|| [File Upload](tasks/FileUploadTask.md)                | | |[✓][js_fileuploadtask] | | | |
|| [Page Iterator](tasks/PageIteratorTask.md)            |[✓][dotnet_pageiteratortask]| |[✓][js_pageiteratortask] | | | |
| [Providers](providers/providers.md)
|| [Authentication](providers/AuthenticationProvider.md)              |[✓][dotnet_authprovider]|[J](java_authprovider) / [A](android_authprovider) |[✓][js_authprovider]|[✓][objc_authprovider] | | |
|| [Logging](providers/LoggingProvider.md)                     | | | | | | |

✓ - Completed  
O - In progress
N - Native library support

## Supported Languages

### .Net

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph/)|
|Core|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Core/)|
|Auth|[msgraph-sdk-dotnet-auth](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Auth/)|
|Beta|[msgraph-beta-sdk-dotnet](https://github.com/microsoftgraph/msgraph-beta-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Beta/)|

### Javascript

|Role| Repo | Packages |
|--|--|--|
|Service+Core|[msgraph-sdk-javascript](https://github.com/microsoftgraph/msgraph-sdk-javascript)||
|Models|[msgraph-typescript-typings](https://github.com/microsoftgraph/msgraph-typescript-typings)|

### Java

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-java](https://github.com/microsoftgraph/msgraph-sdk-java)||
|Core|[msgraph-sdk-java-core](https://github.com/microsoftgraph/msgraph-sdk-java-core)||
|Auth|[msgraph-sdk-java-auth](https://github.com/microsoftgraph/msgraph-sdk-java-auth)||
|Auth|[msgraph-sdk-android-auth](https://github.com/microsoftgraph/msgraph-sdk-android-auth)||

Java Core is based on the [OkHttp](https://github.com/square/okhttp).

### Objective C

|Role| Repo | Packages |
|--|--|--|
|Core|[msgraph-sdk-objc](https://github.com/microsoftgraph/msgraph-sdk-objc)||
|Auth|[msgraph-sdk-objc-auth](https://github.com/microsoftgraph/msgraph-sdk-objc-auth)|
|Models|[msgraph-sdk-objc-models](https://github.com/microsoftgraph/msgraph-sdk-objc-models)|

### PHP

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-php](https://github.com/microsoftgraph/msgraph-sdk-php)|

### Ruby

|Role| Repo | Packages |
|--|--|--|
|All|[msgraph-sdk-ruby](https://github.com/microsoftgraph/msgraph-sdk-ruby)|

### Python

|Role| Repo | Packages |
|--|--|--|
|All|[msgraph-sdk-python](https://github.com/microsoftgraph/msgraph-sdk-python)|

## Issues

View or log issues on the [Issues](https://github.com/microsoftgraph/msgraph-sdk-design/issues) tab in the repo.

## Copyright and license

Copyright (c) Microsoft Corporation. All Rights Reserved. Licensed under the MIT [license](LICENSE).

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

[java_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/AuthenticationHandler.java
[java_multipartcontent]: https://github.com/microsoftgraph/msgraph-sdk-java/blob/dev/src/main/java/com/microsoft/graph/models/extensions/Multipart.java

[objc_middleware]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Protocols/MSGraphMiddleware.h
[objc_authprovider]:https://github.com/microsoftgraph/msgraph-sdk-objc-auth
[objc_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/Authentication/MSAuthenticationHandler.h
[objc_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RetryHandler
[objc_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RedirectHandler
[objc_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchRequestContent.h
[objc_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchResponseContent.h
[objc_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/HTTPClient/MSClientFactory.h

[dotnet_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth
[dotnet_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/RetryHandler.cs
[dotnet_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/RedirectHandler.cs
[dotnet_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/AuthenticationHandler.cs
[dotnet_compressionhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/CompressionHandler.cs
[dotnet_clientfactory]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/GraphClientFactory.cs
[dotnet_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Content/BatchRequestContent.cs
[dotnet_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/Content/BatchResponseContent.cs
[dotnet_responsehandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/ResponseHandler.cs
[dotnet_pageiteratortask]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Tasks/PageIterator.cs
[dotnet_telemetryhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/po/telemetryHandler/src/Microsoft.Graph.Core/Requests/Middleware/TelemetryHandler.cs

[js_middleware]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/middleware/IMiddleware.ts
[js_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/middleware/AuthenticationHandler.ts
[js_responsehandler]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/GraphResponseHandler.ts
[js_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/content/BatchRequestContent.ts
[js_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/content/BatchResponseContent.ts
[js_fileuploadtask]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/tasks/LargeFileUploadTask.ts
[js_pageiteratortask]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/tasks/PageIterator.ts
[js_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/middleware/RedirectHandler.ts
[js_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/middleware/RetryHandler.ts
[js_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/HTTPClientFactory.ts
[js_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/MSALAuthenticationProvider.ts
