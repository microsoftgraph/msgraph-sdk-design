# File Upload Task

## Objectives

Uploading a large file can be done by creating an upload session which allows the upload of slices of the file in sequential API requests. This therefore enables the possibility mechanisms such as resuming the upload or cancelling the upload.
This task aims to provide a fluent and easy to use mechanism for the consumer to control, monitor and cancel the handling of large uploads.

## Requirements

- Accept a Microsoft.Graph.UploadSession instance to be used for the upload which is created and managed by the user and handed over to the task. The task should use the instance to start/resume the upload.
- The task should validate the expiration time and expected byte range of the upload session on resume by querying the upload URL(thereby updating its instance of the Microsoft.Graph.UploadSession).
- Accept a file/stream to be uploaded.
- The task should provide a mechanism of configuring the upload properties i.e. the upload slice size.
- The task should provide a mechanism of monitoring the upload progress through a callback/delegate.
- The task should signal upload failures via standard exception mechanism.
- The task should signal upload completion via by returning the status or returning a completed **Task**/**Promise**/**Future** for async APIs.
- Using the RequestContext, the feature flag for the **FileUploadTask** can be set for telemetry purposes.
- The task should not retry to upload failed slices as any retry should already be done by the retry-handler which the task should be using.
- Upload task cancellation - 
  - The task should provide cancellation capabilities through native task cancellation sources when using async APIs.
  - For Outlook, OneDrive and PrintDocument APIs, send a delete request to the upload session URL to cancel an upload task. 
- The task should provide api to access the upload session object containing its URL, expiry date and the cancellation status.
  - Implement `getUploadSession()` which returns a `LargeFileTaskUploadSession` object.
- Progress Handler -
  - Provide capabilities to the user to track progress using callback functions.
  - Implement a `IUploadEventHandler`(currently `IProgress` in Java and c#) interface which contains the following callback properties -
    - `progress(rangeOfFileUploaded, additionalParamters):void` - called after each chunk upload.
    - `additionalParameters` should be an optional parameter of any strict type allowing flexibility in the implementation of the callback.      
- The task classes naming should match **LargeFileUploadXXX** (provider, result...) and all the classes should live in a **tasks** subnamespace, and be sharing the same  namespace as the **PageIterator** task.
- The task should be agnostic to the kind of upload being performed so as to support for various fileUpload scenarios e.g. **DriveItem** and **FileAttachment**. An example of the agnostic nature of task is how the task is marked as completed considering different response formats from each API:
  - If the response status is a 201.
  - An additional case for OneDrive is, if the response status is a 200 and the response contains an "id" then the task is complete.
- MUST NOT compress the individual slices when it uploads them. This is because compressing slices with content-encoding header leads to invalid ranges/content length headers. (e.g. for a 100 bytes file of 10*10 bytes uncompressed slices, the content length would be ~7 bytes)
- MUST NOT compress the whole file before sending it. This is because it might lead to memory issues in some client contexts (needs compressed result length to [compute the range header](https://www.rfc-editor.org/rfc/rfc9110.html#section-14.1.2-3)), it's also because the content-encoding header applies to individual requests and not to the overall set of requests and the server would try to decompress each slice independently, leading to a corrupted result.
  
> Note: Outlook and Print API does not allow to update an attachment after it has been uploaded.

Refer to the following documentation for more information:

- [Outlook](https://docs.microsoft.com/en-us/graph/outlook-large-attachments)
- [OneDriveItem](https://docs.microsoft.com/en-us/graph/api/driveitem-createuploadsession?view=graph-rest-1.0&preserve-view=true)
- [Print API](https://docs.microsoft.com/en-us/graph/upload-data-to-upload-session)

### Appendix on the range header for Microsoft Graph

Microsoft Graph large file upload is inspired off [range response syntax](https://www.rfc-editor.org/rfc/rfc7233#section-4.2), [updated in RFC9110](https://www.rfc-editor.org/rfc/rfc9110.html#section-14.1.2).

## Large File Upload Session

Json Schema of the upload session object: 

```json
{
  "properties": {
    "url": { "type": "string"},
    "expiryDate": { "type": "date"},
    "isCancelled": { "type": "boolean"},
  }
}
```
## Large File Upload Result Prototype

Json Schema with a generic type for the object:

- In case only an id is provided by the response, it'll be set as the id of the responseBody.
- In case a location header is returned by the service, the property will be set and the responseBody will be left null.

```json
{
  "type": "object",
  "properties": {
    "location": { "type": "string"},
    "responseBody": { "type":  "generic"},
  }
}
```

> Warning: the location header casing can be different between HTTP/1.1 **Location** and HTTP/2. Implementation should account for this.

## Large Upload Sequence

1. The consumer creates a large upload session using the SDK.
2. The consumer opens a stream to the file that needs to be uploaded (from storage, network, memory...).
3. The consumer creates a large upload task (this object model) passing the upload session, upload parameters, and the stream.
4. The consumer calls the **upload** method which:
    1. Reads the next bytes according to parameters
    2. Performs the upload request.
    3. Reads the response to determine whether a next range of bytes is expected, or the upload is completed, or whether the upload has failed.
    4. If next bytes are expected, the task repeats previous 3 steps.
5. The upload status is returned to the consumer or an exception is thrown if the upload failed.

## Large File Progress Handler Prototype

```json
{
  "properties":{
    "progress":{"type": "callback"},
    "extraCallbackParam": { "type":  "generic"}
  }
}
```
### Example of a response calling for the upload of the next range

```json
{
  "@odata.context":"https://outlook.office.com/api/v2.0/$metadata#Users('<redacted>')/Messages('<redacted>')/AttachmentSessions/$entity",
  "expirationDateTime":"2019-09-25T01:09:30.7671707Z",
  "nextExpectedRanges":["2097152"]
}
```

> Warning: In case the large upload task is targeting Outlook APIs, it is possible the casing of the json properties is different (i.e. **ExpirationDateTime** instead of **expirationDateTime**). Implementation should account for this.

## Performance Considerations

- The **FileUploadTask** should not create buffers for more than the current slice of data being uploaded.

## Telemetry considerations

- Using the RequestContext, the flag for the **FileUploadTask** can be set to enable monitoring of the use of the task.

## Example usage

### CSharp

Create a handler for the progress of the upload

```CSharp
// Setup the progress monitoring
IProgress<long> progress = new Progress<long>(progress =>
{
    Console.WriteLine($"Uploaded {progress} bytes of {stream.Length} bytes");
});
```

Create an upload session using the request builders

```CSharp
// create an upload session
var uploadSession = await graphClient.Drive.Items["01KGPRHTV6Y2GOVW7725BZO354PWSELRRZ"].ItemWithPath("_hamilton.png").CreateUploadSession().Request().PostAsync();
```

Use the upload session and callback handler to run/resume the upload

```CSharp
// create the Large File Upload task with relevant options
var maxSliceSize = 320 * 1024; // 320 KB - Change this to your slice size. 5MB is the default.
// var provider = new ChunkedUploadProvider(uploadSession, graphClient, stream, maxChunkSize); //Old way that is wrapped
var largeFileUploadTask = new LargeFileUploadTask(uploadSession, graphClient, stream, maxSliceSize);

// upload away with relevant callback
LargeFileUploadResult<DriveItem> uploadResult = await largeFileUploadTask.UploadAsync( progress );

```

### Java

```java
// Define a callback for the upload progress
final IProgressCallback callback = new IProgressCallback () {
    @Override
    public void progress(final long current, final long max) {
        //Check progress
    }
};
```

Create an upload session using the request builders

```java
// create an upload session
final IUploadSession uploadSession = testBase
        .graphClient
        .me()
        .drive()
        .items(itemId)
        .itemWithPath("_hamilton.jpg")
        .createUploadSession(new DriveItemUploadableProperties())
        .buildRequest()
        .post();

```

Use the upload session and callback handler to run the upload

```java
// create the upload provider
final LargeFileUploadTask<DriveItem> largeFileUploadTask = new LargeFileUploadTask<DriveItem>(
        uploadSession,
        graphClient,
        uploadFile,  //the file to upload
        fileSize,    //size of the slices
        DriveItem.class);

// upload with the provided callback mechanism
final LargeFileUploadResult<DriveItem> result = largeFileUploadTask.upload(callback); //or uploadAsync to get a future

```

### TypeScript

Create an upload session using the request builders

```typescript

// create the upload session
const uploadSession = LargeFileUploadTask.createUploadSession(client, "REQUEST_URL", payload);

// specify the options
let options = {
    path: "/Documents",
    fileName: file.name,
    rangeSize: 1024 * 1024,
};

// Create an upload session
const uploadTask = new LargeFileUploadTask(client, fileObj, uploadSession, optionsWithProgress);

//Use the upload task to run the upload
const uploadResult:UploadResult = await uploadTask.upload();
```
