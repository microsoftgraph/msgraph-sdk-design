# GraphResponse Object

## Objectives

Provide a decorator for the native response message to enable attaching Graph specific behaviour and state to a native HTTP response object.

## Requirements

- Maintain reference to corresponding GraphRequest object
- Provide access to HTTP response headers, status codes, and the raw response body.
- Enable transformation from GraphResponse object to native response object.  This may be lossy.
- Enable transformation from native response object to GraphResponse object
- Provide access to response content as domain object.
- Provide a method to lazy deserialize the response body into our generated models or another object type based on whether a custom deserializer is provided.
- Possilbly provide support providing a custom deserializer to override the default deserializer.

## Performance Considerations

Custom deserializer support can enable customers to craft a deserializer optimized for a specific response.

## Background

The generated .Net client library either returns: 
 1. the response content as an object or collection object as the result of a successful call to Microsoft Graph, or 
 2. a ServiceException in the case the call fails.

We consider this a design flaw as the client library swallows useful information such as the response headers and the http status code. Mitigation such as stuffing the information in AdditionalData is insufficient for all scenarios. 
For example, there is no AdditionalData on Stream objects, and Delete operations return void.

Related to:

- https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/457
- https://github.com/microsoftgraph/msgraph-sdk-dotnet/issues/361


## Current Implementations

### Javascript

The Javascript SDK exposes the native [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) object when selecting the RAW ResponseType option.

### .Net

> Currently not supported

### Java

> Currently not supported. Should be able to use the same pattern as .Net.

### PHP

The PHP SDK returns a Graph model (or collection) or a GraphResponse object, depending on whether a returnType is specified. This means that the returnType information is not supplied to the GraphResponse. The response body is set as a JSON array on GraphResponse. The only work I'd suggest here is that we add an option to pass the returnType to the GraphResponse and add a lazy deserialize method so it is easy to get the Graph models.

## The GraphResponse Object

The GraphResponse object can wrap the native HttpResponseMessage object so that customers can use the familiar HttpResponseMessage members. 
GraphResponse will be part of Microsoft.Graph.Core and it will expose the headers,Content and status code while also keeping reference to the Request object as below.


### GraphResponse
```csharp

    /// <summary>
    /// The GraphResponse Object
    /// </summary>
    public class GraphResponse : IDisposable
    {
        /// <summary>
        /// The GraphResponse Constructor
        /// </summary>
        /// <param name="iBaseRequest">The Request made for the response</param>
        /// <param name="httpResponseMessage">The response</param>
        public GraphResponse(IBaseRequest iBaseRequest,HttpResponseMessage httpResponseMessage)
        {
            this.httpResponseMessage = httpResponseMessage;
            this.BaseRequest = iBaseRequest;
        }

        private readonly HttpResponseMessage httpResponseMessage;
        
        /// <summary>
        /// The Response Status code
        /// </summary>
        public HttpStatusCode StatusCode => httpResponseMessage.StatusCode;
        
        /// <summary>
        /// The Response Content
        /// </summary>
        public HttpContent Content => httpResponseMessage.Content;
        
        /// <summary>
        /// The Response Headers
        /// </summary>
        public HttpResponseHeaders HttpHeaders => httpResponseMessage.Headers;

        /// <summary>
        /// The reference to the Request
        /// </summary>
        public IBaseRequest BaseRequest;

        /// <summary>
        /// Get the native Response Message
        /// </summary>
        /// <returns></returns>
        public HttpResponseMessage ToHttpResponseMessage()
        {
            return httpResponseMessage;
        }

        /// <summary>
        /// Cleanup
        /// </summary>
        public void Dispose()
        {
            httpResponseMessage?.Dispose();
        }
    }

```

### GraphResponse< T >

This derives from non generic GraphResponse and provides a method of derialization using a function called `GetResponseObjectAsync()`

```csharp

    /// <summary>
    /// The GraphResponse Object
    /// </summary>
    public class GraphResponse<T> : GraphResponse
    {
        /// <summary>
        /// The GraphResponse Constructor
        /// </summary>
        /// <param name="iBaseRequest">The Request made for the response</param>
        /// <param name="httpResponseMessage">The response</param>
        public GraphResponse(IBaseRequest iBaseRequest, HttpResponseMessage httpResponseMessage)
            : base(iBaseRequest, httpResponseMessage)
        {
        }

        /// <summary>
        /// Gets the deserialized object 
        /// </summary>
        public async Task<T> GetResponseObjectAsync()
        {
            return await this.BaseRequest.ResponseHandler.HandleResponse<T>(this);
        }
    }

```

## Exposing the GraphResponse object from the Service Library

We can create parallel *Async functions to enable the GraphResponse object functionality. We will generate requests with the following public API signatures:

* `GetResponseAsync(): : GraphResponse<T>`
* `CreateResponseAsync(NewObject: Entity) : GraphResponse<T>`
* `PostResponseAsync(NewObject: Entity) : GraphResponse<T>`
* `UpdateResponseAsync(UpdatedObject: Entity) : GraphResponse<T>`
* `DeleteResponseAsync() : GraphResponse`  //no generic here 

These would in turn call a function in the core library(BaseRequest) that looks something like

```csharp

    public async Task<GraphResponse> SendAsyncWithGraphResponse() 
    {
        var response = await this.SendRequestAsync(serializableObject, cancellationToken, completionOption).ConfigureAwait(false))
        return new GraphResponse(this,response);
    }

```
### Example Usage

Example calls will look like this.

```csharp

GraphResponse<IUserCollectionPage> response = await graphServiceClient.Users.Request().GetWithGraphResponseAsync<IUserCollectionPage>(cancellationToken);

GraphResponse<User> response = await graphServiceClient.Me.Request().UpdateWithGraphResponseAsync<User>(patchUser, cancellationToken);

```

### Other Notes

* Delete requests can (by default) return GraphResponse objects rather than void
* Pro: Use generator to get capabilty annotation generation support.
* Cons: T4 template code to maintain.
* We can possibly add extension methods to GraphResponse to also return domain objects

## Late and Custom Deserialization

This can be done exposing the `IResponseHandler` interface as below. (This will involve some tweaking of the current ResponseHandlers as well).

```csharp

    /// <summary>
    /// The interface required for all response handlers.
    /// </summary>
    public interface IResponseHandler
    {
        /// <summary>
        /// Process raw HTTP response into the requested domain type.
        /// </summary>
        /// <typeparam name="T">The type to return</typeparam>
        /// <param name="response">The HttpResponseMessage to handle.</param>
        /// <returns></returns>
        Task<T> HandleResponse<T>(HttpResponseMessage response);

        /// <summary>
        /// Process the GraphResponse into the requested domain type.
        /// </summary>
        /// <typeparam name="T">The type to return</typeparam>
        /// <param name="graphResponse">The GraphResponse to handle.</param>
        /// <returns></returns>
        Task<T> HandleResponse<T>(GraphResponse graphResponse);
    }

```

This means that customers could use a custom serializer while implementing the interface and simply throw in a `GraphResponse` object into it. 
If we align our current ResponseHandler to this, they could possibly just use our ResponseHandler as well.

Therefore a developer could possibly follow the following steps.

```csharp

    ISerializer serailizer = new CustomSerializer(); //Custom Serializer
    IResponseHandler responseHandler = new ResponseHandler(serailizer); // Our ResponseHadler with custom Serializer
    
    GraphResponse<User> graphResponse = await graphServiceClient.Me.Request()
                                                    .WithResponseHandler(responseHandler)// customized yay!
                                                    .UpdateWithGraphResponseAsync<User>(patchUser, cancellationToken);//response with no serialization
    
    //
    // Do other stuff here like check status/headers
    //

    User user = graphResponse.GetResponseObjectAsync(); //calls the responsehandler with custom serailizer

```

## Opportunities

* `SendAsync` calls in the core library can be updated/revised to return `GraphResponse` rather than `HttpReponseMessage`

* Opportunity to implement stuff from our proposed "breaking moment" a simplified http provider. This would also allow us to tryout enabling custom serlializer as well.

* We could release this as an experimental preview. After preview, we de-emphasize *Async classic methods and update the snippet generator to use this form. Deemphasis will be done via blog post and /// comment.
We instrument the generated methods so we discover usage.
We deprecate when we hit a threshold. Deprecation will be done via blog post and /// comment.
We can delete at another threshold. The cool thing is that they will still get the latest functionality in the service libraries since they are decoupled.
