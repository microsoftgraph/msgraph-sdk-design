# Microsoft Graph SDKs - Requirements and Design

This repository holds documents related to current and on-going work on Microsoft Graph SDKs.  The following diagram shows a high level view of the SDK component architecture. The goal is to enable developers to opt-into functionality that they wish to use.  Over time all the SDKs will be adapted to follow this pattern.

![Component Architecture](images/componentArch.png)

## SDK Features Support

| Component |Feature| .net | Java | JS | ObjC | Powershell | PHP | Ruby | Python | Go |  
|--|--|--|--|--|--|--|--|--|--|--|
|[Middleware](middleware/middleware.md)
| | Pipeline                |✓|✓|[✓][js_middleware]|[✓][objc_middleware]|✓| | |[✓][python_middleware]| [✓][go_middleware]|
| | [Authorization Handler](middleware/AuthorizationHandler.md)   |[✓][dotnet_authhandler] |[✓][java_authhandler]|[✓][js_authhandler]|[✓][objc_authhandler]|[✓][dotnet_authhandler]| | |[✓][python_authhandler] | N/A |
| | [Retry Handler](middleware/RetryHandler.md)              |[✓][dotnet_retryhandler]|[✓][java_retryhandler]|[✓][js_retryhandler]|[✓][objc_retryhandler]|[✓][dotnet_retryhandler]| | | [✓][python_retryhandler]| [✓][go_retryhandler]
| | [Redirect Handler](middleware/RedirectHandler.md)        |[✓][dotnet_redirecthandler]|[✓][java_redirecthandler]|[✓][js_redirecthandler]|[✓][objc_redirecthandler]|[✓][dotnet_redirecthandler]| | |✓ | [✓][go_redirecthandler]|
| | [Request compression Handler](middleware/CompressionHandler.md) | | | | | | |
| | [Response decompression Handler](middleware/DecompressionHandler.md) |[✓][dotnet_decompressionhandler]|N|N|N|[✓][dotnet_decompressionhandler]| |
| | [Logging Handler](middleware/LoggingHandler.md) |O| | | |[✓][powershell_logginghandler]| |
| | [Telemetry Handler](middleware/TelemetryHandler.md) |[✓][dotnet_telemetryhandler]|[✓][java_telemetryhandler]|✓|✓|[✓][powershell_telemetryhandler]| | |[✓][python_telemetryhandler] | [✓][go_telemetryhandler]|
| | Connection Management | | | | | | |
| | Long Running Operations | | | | | | |
| | Chaos Handler | |[✓][java_chaoshandler] |O| | | |
| | Service Discovery Handler | | | | | | |
| [Content](content/ContentArchitecturalConstraints.md)
|| [Batch Request Content](content/BatchRequestContent.md)     |[✓][dotnet_batchrequestcontent]|[✓][java_batchrequestcontent]|[✓][js_batchrequestcontent]|[✓][objc_batchrequestcontent] | | |
|| [Batch Response Content](content/BatchResponseContent.md)   |[✓][dotnet_batchrequestcontent]|[✓][java_batchresponsecontent]|[✓][js_batchresponsecontent] |[✓][objc_batchresponsecontent] | | |
|| [Multipart Content](content/MultipartContent.md)            |✓|[✓][java_multipartcontent]| | | | |
|| [Error Content](content/ErrorContent.md)            | | | | |N| |
| Graph Components
|| [Client Factory](GraphClientFactory.md)           |[✓][dotnet_clientfactory]|[✓][java_httpclients]|[✓][js_graphclientfactory]|[✓][objc_graphclientfactory]|[✓][dotnet_clientfactory]| | |[✓][python_graphclientfactory] | [✓][go_graphclientfactory]
|| [Response Handling](ResponseHandler.md) |[✓][dotnet_responsehandler]| |[✓][js_responsehandler]||✓||
| Tasks
|| [File Upload](tasks/FileUploadTask.md)                | |[✓][java_largefileupload]|[✓][js_fileuploadtask] |✓|[✓][powershell_fileupload] | |
|| [Page Iterator](tasks/PageIteratorTask.md)            |[✓][dotnet_pageiteratortask]||[✓][js_pageiteratortask] |✓|[✓][powershell_pageiterator]| |
| [Providers](providers/providers.md)
|| [Authentication](providers/AuthenticationProvider.md)              |[✓][dotnet_authprovider]|[✓][java_authprovider]|[✓][js_authprovider]|[✓][objc_authprovider]|[✓][powershell_authprovider]| | | | [✓][go_authprovider]|
|| [Logging](providers/LoggingProvider.md)                     | | | | |N| |

✓ - Completed  
O - In progress
N - Native library support

## Supported Languages

### .NET

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph/)|
|Core|[msgraph-sdk-dotnet-core](https://github.com/microsoftgraph/msgraph-sdk-dotnet-core)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Core/)|
|Auth|[msgraph-sdk-dotnet-auth](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Auth/)|
|Beta|[msgraph-beta-sdk-dotnet](https://github.com/microsoftgraph/msgraph-beta-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Beta/)|

### Go

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-go](https://github.com/microsoftgraph/msgraph-sdk-go)|[pkg.go.dev](https://pkg.go.dev/github.com/microsoftgraph/msgraph-sdk-go/)|
|Core|[msgraph-sdk-go-core](https://github.com/microsoftgraph/msgraph-sdk-go-core)|[pkg.go.dev](https://pkg.go.dev/github.com/microsoftgraph/msgraph-sdk-go-core/)|
|Auth|[azure-identity](https://github.com/Azure/azure-sdk-for-go/tree/main/sdk/azidentity)|[pkg.go.dev](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity)|
|beta|[msgraph-beta-sdk-go](https://github.com/microsoftgraph/msgraph-beta-sdk-go)|[pkg.go.dev](https://pkg.go.dev/github.com/microsoftgraph/msgraph-beta-sdk-go/)|

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
|Auth|[azure-identity](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)||
|beta|[msgraph-beta-sdk-java](https://github.com/microsoftgraph/msgraph-beta-sdk-java)||

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
|Core|[msgraph-sdk-python-core](https://github.com/microsoftgraph/msgraph-sdk-python-core)|[PyPI](https://pypi.org/project/msgraph-core/)

## Issues

View or log issues on the [Issues](https://github.com/microsoftgraph/msgraph-sdk-design/issues) tab in the repo.

## Copyright and license

Copyright (c) Microsoft Corporation. All Rights Reserved. Licensed under the MIT [license](LICENSE).

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

[java_authprovider]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity
[java_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/AuthenticationHandler.java
[java_chaoshandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/ChaosHttpHandler.java
[java_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/RedirectHandler.java
[java_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/RetryHandler.java
[java_telemetryhandler]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/TelemetryHandler.java
[java_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/content/BatchRequestContent.java
[java_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/content/BatchResponseContent.java
[java_multipartcontent]: https://github.com/microsoftgraph/msgraph-sdk-java/blob/dev/src/main/java/com/microsoft/graph/models/extensions/Multipart.java
[java_largefileupload]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/tasks/LargeFileUploadRequest.java
[java_httpclients]: https://github.com/microsoftgraph/msgraph-sdk-java-core/blob/dev/src/main/java/com/microsoft/graph/httpcore/HttpClients.java


[objc_middleware]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Protocols/MSGraphMiddleware.h
[objc_authprovider]:https://github.com/microsoftgraph/msgraph-sdk-objc-auth
[objc_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/Authentication/MSAuthenticationHandler.h
[objc_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RetryHandler
[objc_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RedirectHandler
[objc_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchRequestContent.h
[objc_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchResponseContent.h
[objc_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/HTTPClient/MSClientFactory.h

[dotnet_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth
[dotnet_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/RetryHandler.cs
[dotnet_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/RedirectHandler.cs
[dotnet_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/AuthenticationHandler.cs
[dotnet_decompressionhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Requests/Middleware/CompressionHandler.cs
[dotnet_clientfactory]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-core/blob/dev/src/Microsoft.Graph.Core/Requests/GraphClientFactory.cs
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

[python_middleware]: https://github.com/microsoftgraph/msgraph-sdk-python-core/blob/dev/msgraph/core/middleware/middleware.py
[python_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-python-core/blob/dev/msgraph/core/middleware/authorization.py
[python_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-python-core/blob/dev/msgraph/core/middleware/retry.py
[python_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-python-core/blob/dev/msgraph/core/_client_factory.py
[python_telemetryhandler]: https://github.com/microsoftgraph/msgraph-sdk-python-core/blob/dev/msgraph/core/middleware/telemetry.py

[go_middleware]: https://github.com/microsoft/kiota/blob/main/http/go/nethttp/pipeline.go
[go_authprovider]: https://github.com/microsoft/kiota/tree/main/authentication/go/azure
[go_retryhandler]: https://github.com/microsoft/kiota/blob/main/http/go/nethttp/retry_handler.go
[go_redirecthandler]: https://github.com/microsoft/kiota/blob/main/http/go/nethttp/redirect_handler.go
[go_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-go-core/blob/main/graph_client_factory.go
[go_telemetryhandler]: https://github.com/microsoftgraph/msgraph-sdk-go-core/blob/main/graph_telemetry_handler.go

[powershell_telemetryhandler]: https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/src/Authentication/Authentication/Helpers/HttpHelpers.cs
[powershell_logginghandler]: https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/tools/Custom/Module.cs
[powershell_fileupload]: https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/tools/Custom/FileUploadCmdlet.cs
[powershell_pageiterator]: https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/tools/Custom/ListCmdlet.cs
[powershell_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/src/Authentication/Authentication/Cmdlets/ConnectMgGraph.cs
