   

State the requirement with description and example of when to use each of the following IHTTPCore function.
```
sendAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType>;
```

```
sendCollectionAsync<ModelType extends Parsable>(requestInfo: RequestInformation, type: new() => ModelType, responseHandler: ResponseHandler | undefined): Promise<ModelType[]>;
```

```
sendPrimitiveAsync<ResponseType>(requestInfo: RequestInformation, responseType: "string" | "number" | "boolean" | "Date" | "ReadableStream", responseHandler: ResponseHandler | undefined): Promise<ResponseType>;
```

```        
sendNoResponseContentAsync(requestInfo: RequestInformation, responseHandler: ResponseHandler | undefined): Promise<void>;
```        

```        
enableBackingStore(backingStoreFactory?: BackingStoreFactory | undefined): void;
```



Kiota core implementation 

   
```
constructor(authProvider, parseNodeFactory, serializationWriteFactory, httpClient);
```
