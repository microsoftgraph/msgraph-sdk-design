# Delta Response Handler

Provide a mechanism to discover changes returned via a delta response. Delta responses can have properties set to null. Not all platforms provide a way to discover properties that have been 'unset' to their default state. For example, in .NET, our models default to not serialize null values.

## Requirements

- Provide an ability to discover changed property values, including changes to null or an empty string.
- Changed property MUST be returned as an array of tokens that identify the name of the property returned in a delta response.
- Changed property tokens SHOULD be in dot notation form. The token form SHOULD be in a form that makes it easy to identify the changed property in code. For example, the JSON object below would be represented as `["name", "address.city"]`.

```json
{
	"value" :
		{
			"name": null,
			"address" :
				{
					"city": null
				}
		}
}
```
- The changed property array SHOULD be set on a `Changed` property in the response object.

## Example usage and implementation

### .NET

We will make the following changes to support this feature:
* BaseRequest.cs/IBaseRequest.cs - Add a private setter for responseHandler.
* Create a **DeltaResponseHandler** class that will encapsulate a serializer that will deserialize the changed properties into an array of tokens in a property named `changed` in the AdditionalData property bag.
* BaseRequestExtensions.cs - Add WithResponseHandler<T>(ResponseHandler rh) request builder where T : IBaseRequest. This will replace the default response handler set on BaseRequest. It will only be applicable to the single request.
<!-- Should this target IBaseRequest, or should we specifically target I*DeltaRequest? It would be a pain to update this every time a new Delta scenario is supported. -->

```CSharp
// Developer experience
var userDeltaCollectionPage = await graphClient.Users
                                               .Delta()
                                               .Request()
                                               .WithResponseHandler(new DeltaResponseHandler())
                                               .GetAsync();

userDeltaCollectionPage.AdditionalData.TryGetValue["changed"](out object changedPropertyList);
```