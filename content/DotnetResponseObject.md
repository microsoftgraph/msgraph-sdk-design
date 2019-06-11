# GraphResponse object for .Net

The generated .Net client library either returns: 1) the response content as an object or collection object as the result of a successful call to Microsoft Graph, or 2) a ServiceException in the case the call fails.

We consider this a design flaw as the client library swallows useful information such as the response headers and the http status code. Mitigation such as stuffing the information in AdditionalData is insufficient for all scenarios. For example, there is no AdditionalData on Stream objects, and Delete operations return void.

Related to:
https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/457
https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/361

## Proposed solution

We propose that we create parallel *Async functions to enable the GraphResponse object functionality. We will generate requests with the following public API signatures:

GetWithResponseObjectAsync(): GraphResponse<T>, where T is the return type
CreateWithResponseObjectAsync(NewObject: Entity) : GraphResponse<T>, where T is the return type
PostWithResponseObjectAsync(NewObject: Entity) : GraphResponse<T>, where T is the return type
UpdateWithResponseObjectAsync(UpdatedObject: Entity) : GraphResponse<T>, where T is the return type
DeleteWithResponseObjectAsync() : GraphResponse<T>, where T is the return type

GraphResponse.Content
GraphResponse.HttpHeaders
GraphResponse.StatusCode


Why not on BaseRequest?
* All of the new methods would be on all the Request objects.
* Would require generics to define the return type. This is fine until we get to collections, ex *Entity*CollectionPage, which then would require users to understand our conventions.
* Pros: Write in one place. No T4 template code to maintain.

Why in the generated files?
* Use generator to get capa annotation generation support.
* Cons: T4 template code to maintain.


Things we considered.
- We can't just use AdditionalData as getting Stream and DeleteAsync don't have a Graph content response.

* We add overload to existing generated functions. GetAsync(WithResponseObject: boolean); where we would deprecate existing overloads to state that this is the new way. *
* Can't use the existing AdditionalData as DeleteAsync() doesn't return an object.




## Opportunities

We realize that this is an op


Opportunity to implement stuff from our proposed "breaking moment" a simplified http provider. This would also allow us to tryout enabling custom serlializer as well.

We could release this as an experimental preview.
After prview, we de-emphasize *Async classic methods and update the snippet generator to use this form. Deemphasis will be done via blog post and /// comment.
We instrument the generated methods so we discover usage.
We deprecate when we hit a threshold. Deprecation will be done via blog post and /// comment.
We can delete at another threshold. The cool thing is that they will still get the latest functionality in the service libraries since they are decoupled.

## Deemphasis to deprecation to breaking change plan