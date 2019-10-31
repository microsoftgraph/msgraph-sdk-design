# File Upload Task

## Objectives

Uploading a large file can be done by creating an upload session which allows the upload of slices of the file in sequential API requests. This therefore enables the possibility mechanisms such as resuming the upload or cancelling the upload.
This task aims to provide a fluent and easy to use mechanism for the consumer to control, monitor and cancel the handling of large uploads.

## Requirements

- Accept a Microsoft.Graph.UploadSession instance to be used for the upload which is created and managed by the user and handed over to the task. The task should use the instance to start/resume the upload.
- The task should validate the expiration time and expected byte range of the upload session on resume by querying the upload URL(thereby updating its instance of the Microsoft.Graph.UploadSession).
- Accept a file/stream to be uploaded
- The task should provide a mechanism of configuring the upload properties i.e. the upload slice size
- The task should provide a mechanism of monitoring the upload status i.e the progress and failures through a callback/Delegate
- Using the RequestContext, the feature flag for the FileUploadTask can be set for telemetry purposes.
- In the event that a slice fails to upload, the task should retry to re-upload the slice for a configurable number of times(default of 3) before giving up and throwing an error/exception for the user to handle.

## Performance Considerations

- The FileUploadTask should not create buffers for more than the current slice of data being uploaded.

## Telemetry considerations

- Using the RequestContext, the flag for the FileUploadTask can be set to enable monitoring of the use of the task.

## Example usage

### CSharp

Create a handler for the progress of the upload

```CSharp

public class ProgressCallback : IProgressCallback
{
    public void UpdateProgress(long current, long max)
    {
        //Check progress  
        Console.WriteLine("Processed " + current + "of" + max);
    }

    public void OnSuccess( DriveItem result )
    {
        //Handle the successful response
        Console.WriteLine("Upload complete");
    }

    public void OnFailure( ClientException ex )
    {
        //Handle the failed upload
        Console.WriteLine("Upload failed");
    }
}
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
var largeFileUploadTask = new LargeFileUpload(uploadSession, graphClient, stream, maxSliceSize);

// upload away with relevant callback
DriveItem itemResult = await largeFileUploadTask.UploadAsync( new ProgressCallback );

```

### Java

```java

// Define a callback for the upload progress
IProgressCallback<DriveItem> callback = new IProgressCallback<DriveItem> () {
    @Override
    public void progress(final long current, final long max) {
        //Check progress
    }
    @Override
    public void success(final DriveItem result) {
        //Handle the successful response
        String finishedItemId = result.id;
        Assert.assertNotNull(finishedItemId);
    }

    @Override
    public void failure(final ClientException ex) {
        //Handle the failed upload
        Assert.fail("Upload session failed");
    }
};
```

Create an upload session using the request builders

```java
// create an upload session
UploadSession uploadSession = testBase
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
ChunkedUploadProvider<DriveItem> chunkedUploadProvider = new ChunkedUploadProvider<DriveItem>(
        uploadSession,
        graphClient,
        uploadFile,  //the file to upload
        fileSize,    //size of the slices
        DriveItem.class);

// upload with the provided callback mechanism
chunkedUploadProvider.upload(callback);

```

### TypeScript

Create an upload session using the request builders

```typescript

// specify the options
let options = {
    path: "/Documents",
    fileName: file.name,
    rangeSize: 1024 * 1024,
};

// create an upload session
const uploadTask = await MicrosoftGraph.OneDriveLargeFileUploadTask.create(client, file, options);
```
Use the upload task to run the upload

```typescript
// upload
const response = await uploadTask.upload();

```
