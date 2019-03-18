# Microsoft Graph SDKs - Requirements and Design

This repository holds documents related to current and on-going work on Microsoft Graph SDKs

## SDK Features Support

| Component |Feature| .net | Java | JS | ObjC | PHP | Ruby | Python | Go | Powershell |
|--|--|--|--|--|--|--|--|--|--|--|
|[Middleware](middleware/middleware.md)
| | Pipeline                |X|X|[X][js_middleware]|[X][objc_middleware]|||
| | [Authorization Handler](middleware/AuthorizationHandler.md)   |[X][dotnet_authhandler] |[X][java_authhandler]|X|[X][objc_authhandler] | | |
| | [Retry Handler](middleware/RetryHandler.md)              |[X][dotnet_retryhandler]|X|O|[X][objc_redirecthandler]| | |
| | [Redirect Handler](middleware/RedirectHandler.md)        |[X][dotnet_redirecthandler]|X|X?|[X][objc_retryhandler] | | |
| | [Compression Handler](middleware/CompressionHandler.md) | | | | | | |
| | [Logging Handler](middleware/LogggingHandler.md) | | | | | | |
| | [Telemetry Handler](middleware/TelemetryHandler.md) | | | | | | |
| [Content](content/ContentArchitecturalConstraints.md)
|| [Batch Request Content](content/BatchRequestContent.md)     | |X|[X][javascript_batchrequestcontent]|[X][objc_batchrequestcontent] | | |
|| [Batch Response Content](content/BatchResponseContent.md)   | |X|[X][javascript_batchresponsecontent] |[X][objc_batchresponsecontent] | | |
|| [Multipart Content](content/MultipartContent.md)            |X|[X][java_multipartcontent]| | | | |
|| [Error Content](content/ErrorContent.md)            | | | | | | |
| Graph Components
|| [Client Factory](GraphClientFactory.md)           |[X][dotnet_clientfactory]|X|X|[X][objc_graphclientfactory]| | |
|| [Response Handling](responseHandler.md) | | |[X][javascript_responsehandler]||||
| Tasks
|| [File Upload](FileUploadTask.md)                | | |[X][javascript_fileuploadtask] | | | |
|| [Page Iterator](PageIteratorTask.md)            | | |[X][javascript_pageiteratortask] | | | |
| [Providers](providers.md)
|| [Authentication](providers/AuthenticationProvider.md)              |[X][dotnet_authprovider]|[J](java_authprovider) / [A](android_authprovider) |X|[X][objc_authprovider] | | |
|| [Logging](provider/LoggingProvider.md)                     | | | | | | |

X - Completed  
O - In progress

## Supported Languages

### .Net

|Role| Repo | Packages |
|--|--|--|
|Service+Models|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph/)|
|Core|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph.Core/)|
|Auth|[msgraph-sdk-dotnet-auth](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth)|
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
[js_middleware]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/IMiddleware.ts#L15
[objc_middleware]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Protocols/MSGraphMiddleware.h
[objc_authprovider]:https://github.com/microsoftgraph/msgraph-sdk-objc-auth
[objc_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/Authentication/MSAuthenticationHandler.h
[objc_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RetryHandler
[objc_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-objc/tree/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Implementations/RedirectHandler
[objc_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchRequestContent.h
[objc_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/GraphContent/BatchContent/MSBatchResponseContent.h
[objc_graphclientfactory]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/HTTPClient/MSClientFactory.h
[dotnet_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth
[dotnet_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/RetryHandler.cs
[dotnet_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/RedirectHandler.cs
[dotnet_authhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/AuthenticationHandler.cs
[javascript_graphrequest]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/GraphRequest.ts
[javascript_responsehandler]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/ResponseHandler.ts
[javascript_batchrequestcontent]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/content/BatchRequestContent.ts
[javascript_batchresponsecontent]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/content/BatchResponseContent.ts
[javascript_fileuploadtask]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/tasks/LargeFileUploadTask.ts
[javascript_pageiteratortask]: https://github.com/microsoftgraph/msgraph-sdk-javascript/blob/dev/src/tasks/PageIterator.ts
[dotnet_clientfactory]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/GraphClientFactory.cs
[java_multipartcontent]: https://github.com/microsoftgraph/msgraph-sdk-java/blob/dev/src/main/java/com/microsoft/graph/models/extensions/Multipart.java
