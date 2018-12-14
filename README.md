# Microsoft Graph SDKs - Requirements and Design

This repository holds documents related to current and on-going work on Microsoft Graph SDKs

## SDK Features Support 

| Component |Feature| .net | Java | Javascript | Objective C | PHP | Ruby |  
|--|--|--|--|--|--|--|--|
|[Middleware](middleware/middleware.md)
| | Pipeline                |X| | |[X][ios_middleware]|||
| | [Authorization Handler](middleware/AuthorizationHandler.md)   | | | | | | |
| | [Retry Handler](middleware/RetryHandler.md)           |[X][dotnet_retryhandler]| | | | | |
| | [Redirect Handler](middleware/RedirectHandler.md)        |[X][dotnet_redirecthandler]| | | | | |
| | Compression Handler | | | | | | |
| | Logging Handler | | | | | | |
| | Telemetry Handler | | | | | | |
| [Content](content/ContentArchitecturalConstraints.md)       
|| [Batch Request Content](content/BatchRequestContent.md)    | | | | | | |
|| [Batch Response Content](content/BatchResponseContent.md)   | | | | | | |
|| [Multipart Content](content/MultipartContent.md)        | | | | | | |
| Graph Components 
|| [Client Factory](GraphClientFactory.md)           |x| | |x| | |
|| Graph Request            | | | | | | |
|| [Graph Response](GraphResponse.md)           | | | | | | |
| Tasks
|| [File Upload](FileUploadTask.md)              | | | | | | |
|| [Page Iterator](PageIteratorTask.md)            | | | | | | |
| [Providers](providers.md)
|| Authentication              |[X][dotnet_authprovider]| | | | | |
|| Logging                     | | | | | | |

## Repositories

|Language| Repo | Packages |
|--|--|--|
|.net|[msgraph-sdk-dotnet](https://github.com/microsoftgraph/msgraph-sdk-dotnet)|[Nuget](https://www.nuget.org/packages/Microsoft.Graph/)|
|Java|[msgraph-sdk-java](https://github.com/microsoftgraph/msgraph-sdk-java)||
|Javascript Core|[msgraph-sdk-javascript](https://github.com/microsoftgraph/msgraph-sdk-javascript)||
|Typescript Types|[msgraph-typescript-typings](https://github.com/microsoftgraph/msgraph-typescript-typings)||
|Objective C|[msgraph-sdk-objc](https://github.com/microsoftgraph/msgraph-sdk-objc)||
|PHP|[msgraph-sdk-php](https://github.com/microsoftgraph/msgraph-sdk-php)|
|Ruby|[msgraph-sdk-ruby](https://github.com/microsoftgraph/msgraph-sdk-ruby)|


## Issues

View or log issues on the [Issues](https://github.com/microsoftgraph/msgraph-sdk-design/issues) tab in the repo.

## Copyright and license

Copyright (c) Microsoft Corporation. All Rights Reserved. Licensed under the MIT [license](LICENSE).

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.


[ios_middleware]: https://github.com/microsoftgraph/msgraph-sdk-objc/blob/master/MSGraphCoreSDK/MSGraphCoreSDK/Middleware/Protocols/MSGraphMiddleware.h
[dotnet_authprovider]: https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth
[dotnet_retryhandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/RetryHandler.cs
[dotnet_redirecthandler]: https://github.com/microsoftgraph/msgraph-sdk-dotnet/blob/dev/src/Microsoft.Graph.Core/Requests/RedirectHandler.cs
