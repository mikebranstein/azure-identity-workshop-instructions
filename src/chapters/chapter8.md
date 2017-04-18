## Processing Profile Pictures with Azure Functions

> *NOTE:* This chapter was adapted from Microsoft's Technical Community Content Github repository. To find out more and see the original content, visit [Github](https://github.com/Microsoft/TechnicalCommunityContent/tree/master/Cloud%20Computing/Azure%20Functions/Session%202%20-%20Hands%20On).

Functions have been the basic building blocks of software since the first lines of code were written and the need for code organization and reuse became a necessity. Azure Functions expand on these concepts by allowing developers to create "serverless", event-driven functions that run in the cloud and can be shared across a wide variety of services and systems, uniformly managed, and easily scaled based on demand. In addition, Azure Functions can be written in a variety of languages, including C#, JavaScript, Python, Bash, and PowerShell, and they're perfect for building apps and nanoservices that employ a compute-on-demand model.

In this chapter, you'll create an Azure Function that monitors a blob container in Azure Storage for new images, and then performs automated analysis of the images using the Microsoft Cognitive Services [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Specifically, The Azure Function will analyze each image that is uploaded to the container for inappropriate content and create a copy of the image in another container. Images that contain inappropriate content will be copied to one container (the *rejected* container), and images that are acceptable copied to another (the *profile-pics* container). In addition, the analysis data returned by the Computer Vision API will be stored in blob metadata.

### Creating an Azure Function App

The first step in writing an Azure Function is to create an Azure Function App. In this exercise, you'll create an Azure Function App using the Azure Portal. Then you'll add the *rejected* blob container to the storage account you've used throughout this workshop. The *rejected* container will contain images classified as inappropriate content. 

If you're wondering about the other two containers mentioned above, don't worry. You've already created them as part of previous chapters. We'll continue to use them in this chapter. 

<h4 class="exercise-start">
    <b>Exercise</b>: Create an Azure Function App
</h4>

Open the [Azure Portal](https://portal.azure.com) in your browser. If asked to log in, do so using your Microsoft account. 

Click *+ New*, followed by *Compute* and *Function App*.

<img src="images/chapter8/new-function-app.png" class="img-medium" />

You'll be presented with a Function App creation blade.

<img src="images/chapter8/create-function-app.png" class="img-small" />

Enter an app name that is unique within Azure. Under *Resource Group*, select *Use existing* and select the resource group your created earlier. Choose *App Service plan/Location* for the *Hosting Plan*, and select the same storage account you created earlier. Make sure the *Pin to dashboard* is checked.

Click *Create* to create a new Function App. The function app will start it's deployment process. 

<img src="images/chapter8/deploying-function-app.png" class="img-small" />

While you're waiting for the function app to be created, open the resource group created earlier and locate your storage account. Open the storage account in the portal. 

Click *Blobs* to view the contents of blob storage.

![Opening blob storage](images/chapter8/open-blob-storage.png)

You should see several containers inside of your blob storage: *profile-pics*, *uploaded*, and *azure-webjobs-hosts*. You previously created *profile-pics* and *uploaded* during previous chapters. The *azure-webjobs-hosts* container is created by the function app provisioning process, so you may not see it if the function app you just created hasn't been fully provisioned.

Click *+ Container* to add a container.

<img src="images/chapter8/add-container.png" class="img-large" />

Type *rejected* into the *Name* box. Select *Blob* for the *Access type*. Then click the *Create* button to create the container.

![Naming the container](images/chapter8/name-container.png)

Repeat the previous steps to add containers named *profile-pics* and *uploaded* if they don't exist.

<div class="exercise-end"></div>

Great work! You've created the function app and have the three containers needed to store uploaded, rejected, and valid profile pictures. The next step is to add an Azure Function.

### Creating an Azure Function

Once you have created an Azure Function App, you can add Azure Functions to it. In this exercise, you'll add a function to the Function App you created in the previous exercise and write C# code that uses the [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) to analyze images added to the "uploaded" container for inappropriate content.

<h4 class="exercise-start">
    <b>Exercise</b>: Create an Azure Function
</h4>

Return to the workshop dashboard and click the Azure Function App that you created and pinned in the previous exercise. 

<img src="images/chapter8/open-function-app.png" class="img-small" />

Click the *+* symbol next to *Functions*, as shown in the image below.

<img src="images/chapter8/create-azure-function.gif" class="img-large" />

Scroll past the *Get started quickly...* heading and click *Custom function* under the *Get started on your own* heading. Click *BlobTrigger-CSharp*. 

Enter *BlobImageAnalysis* for the function name and "uploaded/{name}.{ext}" into the *Path* box. (The latter applies the blob storage trigger to the "uploaded" container that you created earlier in this workshop.) *{name}* and *{ext}* refer to function app parameters that will be passed in by the Azure function app API automatically. For example, when a profile picture is uploaded with the name mypicture.jpeg, it will be parsed into two variables: *name* (with a value of *mypicture*) and *ext* (with a value of *jpeg*). 

Click the *Create* button to create the Azure Function.

When the Azure Function is created, a code editor window will appear. 

<img src="images/chapter8/code-editor.png" class="img-large" />

Replace the code shown in the code editor with the following statements:

```csharp
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using System.Net.Http.Headers;
using System.Configuration;

public async static Task Run(Stream myBlob, string name, string ext, TraceWriter log)
{       
    log.Info($"Analyzing uploaded image {name} for appropriate content...");

    var array = await ToByteArrayAsync(myBlob);
    var result = await AnalyzeImageAsync(array, log);
    
    log.Info("Is Adult: " + result.adult.isAdultContent.ToString());
    log.Info("Adult Score: " + result.adult.adultScore.ToString());
    log.Info("Is Racy: " + result.adult.isRacyContent.ToString());
    log.Info("Racy Score: " + result.adult.racyScore.ToString());

    // Reset stream location
    myBlob.Seek(0, SeekOrigin.Begin);
    if (result.adult.isAdultContent || result.adult.isRacyContent)
    {
        // profile picture is NOT acceptable - copy blob to the "rejected" container
        StoreBlobWithMetadata(myBlob, "rejected", name, ext, result, log);
    }
    else
    {
        // profile picture is acceptable - copy blob to the "profile-pics" container
        StoreBlobWithMetadata(myBlob, "profile-pics", name, ext, result, log);
    }
}

private async static Task<ImageAnalysisInfo> AnalyzeImageAsync(byte[] bytes, TraceWriter log)
{
    HttpClient client = new HttpClient();

    var key = ConfigurationManager.AppSettings["SubscriptionKey"].ToString();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

    HttpContent payload = new ByteArrayContent(bytes);
    payload.Headers.ContentType = new MediaTypeWithQualityHeaderValue("application/octet-stream");
    
    var results = await client.PostAsync("https://westus.api.cognitive.microsoft.com/vision/v1.0/analyze?visualFeatures=Adult", payload);
    var result = await results.Content.ReadAsAsync<ImageAnalysisInfo>();
    return result;
}

// Writes a blob to a specified container and stores metadata with it
private static void StoreBlobWithMetadata(Stream image, string containerName, string blobName, string ext, ImageAnalysisInfo info, TraceWriter log)
{
    log.Info($"Writing blob and metadata to {containerName} container...");
    
    var connection = ConfigurationManager.AppSettings["AzureWebJobsStorage"].ToString();
    var account = CloudStorageAccount.Parse(connection);
    var client = account.CreateCloudBlobClient();
    var container = client.GetContainerReference(containerName);

    try
    {
        var blob = container.GetBlockBlobReference($"{blobName}.{ext}");
    
        if (blob != null) 
        {
            // Upload the blob
            blob.UploadFromStream(image);

            // Set the content type of the image
            blob.Properties.ContentType = "image/" + ext;
            blob.SetProperties();

            // Get the blob attributes
            blob.FetchAttributes();
            
			// Write the blob metadata
            blob.Metadata["isAdultContent"] = info.adult.isAdultContent.ToString(); 
            blob.Metadata["adultScore"] = info.adult.adultScore.ToString("P0").Replace(" ",""); 
            blob.Metadata["isRacyContent"] = info.adult.isRacyContent.ToString(); 
            blob.Metadata["racyScore"] = info.adult.racyScore.ToString("P0").Replace(" ",""); 
            
			// Save the blob metadata
            blob.SetMetadata();
        }
    }
    catch (Exception ex)
    {
        log.Info(ex.Message);
    }
}

// Converts a stream to a byte array 
private async static Task<byte[]> ToByteArrayAsync(Stream stream)
{
    Int32 length = stream.Length > Int32.MaxValue ? Int32.MaxValue : Convert.ToInt32(stream.Length);
    byte[] buffer = new Byte[length];
    await stream.ReadAsync(buffer, 0, length);
    return buffer;
}

public class ImageAnalysisInfo
{
    public Adult adult { get; set; }
    public string requestId { get; set; }
}

public class Adult
{
    public bool isAdultContent { get; set; }
    public bool isRacyContent { get; set; }
    public float adultScore { get; set; }
    public float racyScore { get; set; }
}
```

*Run* is the method called each time the function is executed. The *Run* method uses a helper method named *AnalyzeImageAsync* to pass each blob added to the "uploaded" container to the Computer Vision API for analysis. Then it calls a helper method named *StoreBlobWithMetadata* to create a copy of the blob in either the "profile-pics" container or the "rejected" container, depending on the scores returned by *AnalyzeImageAsync*. 

Click the *Save* button at the top of the code editor to save your changes. Then click *View Files*.

<img src="images/chapter8/save-run-file.png" class="img-medium" />

Click *+ Add* to add a new file, and name the file *project.json*.

![image](images/chapter8/add-project-file.png)  

Add the following statements to *project.json*:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "WindowsAzure.Storage": "8.1.1"
            }
        }
    }
}
```

Click the *Save* button to save your changes. Then click *run.csx* to go back to that file in the code editor.

<img src="images/chapter8/save-project-file.png" class="img-large" />

<div class="exercise-end"></div>

An Azure Function written in C# has been created, complete with a JSON project file containing information regarding project dependencies. The next step is to add an application setting that Azure Function relies on.

### Adding a Subscription Key to Application Settings

The Azure Function you created in the previous exercise loads a subscription key for the Microsoft Cognitive Services Computer Vision API from application settings. This key is required in order for your code to call the Computer Vision API, and is transmitted in an HTTP header in each call. In this exercise, you will add an application setting containing the subscription key to the Function App.

<h4 class="exercise-start">
    <b>Exercise</b>: Add a Subscription Key to Application Settings
</h4>

Open a new browser window and navigate to https://www.microsoft.com/cognitive-services/en-us/subscriptions. If you haven't signed up for the Computer Vision API, do so now. (Signing up is free.) Then click *Copy* under *Key 1* in your Computer Vision subscription to copy the subscription key to the clipboard.

<img src="images/chapter8/computer-vision-key.png" class="img-large" />

Return to your Function App in the Azure Portal and click on the function app name on the left (as shown in the image below).

<img src="images/chapter8/goto-app-settings.gif" class="img-large" />

On the right, select the *Platform features* tab at top. Under *General Settings*, click *Application settings*. 

Scroll down until you find the *App settings* section. Add a new app setting named *SubscriptionKey*, and paste the Cognitive Services API subscription key that is on the clipboard into the *Value* box. Then click *Save* at the top of the blade.

<img src="images/chapter8/add-key.png" class="img-medium" />

The app settings are now configured for your Azure Function. 

<div class="exercise-end"></div>

### Testing the Azure Function

Your function is configured to listen for changes to the blob container named "uploaded" that you created earlier in this workshop. Each time an image appears in the container, the function executes and passes the image to the Computer Vision API for analysis. To test the function, you simply upload images to the container. In this exercise, you will use the Azure Portal to upload images to the "uploaded" container and verify that copies of the images are placed in the "accepted" and "rejected" containers.

<h4 class="exercise-start">
    <b>Exercise</b>: Uploading an Image to Blob Storage
</h4>

In the Azure Portal, go to the resource group created for your Function App. Then click the storage account that was created for it.

Click *Blobs* to view the contents of blob storage.

<img src="images/chapter8/open-blob-storage.png" class="img-large" />

Click *uploaded* to open the "uploaded" container.

<img src="images/chapter8/open-uploaded-container.png" class="img-large" />

Click *Upload*.

<img src="images/chapter8/upload-images-1.png" class="img-large" />

Click the button with the folder icon to the right of the *Files* box. Select one or more image files. Then click the *Upload* button to upload the files to the "uploaded" container.

> **WARNING:** Due to the nature of this exercise, your Azure Function can detect adult and racy images. Please, **DO NOT** upload inappropriate images during the lab. Be respectful of those around you. 

![Uploading images to the "uploaded" container](images/chapter8/upload-images-2.png)

Return to the blade for the "uploaded" container and verify that the images you uploaded were uploaded. eight images were uploaded.

> **NOTE:** These screenshots may be slightly different due to the number and names of images you uploaded.

<img src="images/chapter8/uploaded-images.png" class="img-medium" />

Close the blade for the "uploaded" container and open the "profile-pics" container.

<img src="images/chapter8/open-accepted-container.png" class="img-large" />

Verify that the "profile-pics" container holds the images you uploaded. *These are the images that were classified as appropriate images by the Computer Vision API*.

> It may take a minute or more for all of the images to appear in the container. If necessary, click *Refresh* every few seconds until you see all the images you expect.

<img src="images/chapter8/accepted-images.png" class="img-large" />

Close the blade for the "profile-pics" container and open the blade for the "rejected" container.

<img src="images/chapter8/open-rejected-container.png" class="img-large" />

Verify that the *rejected* container holds the number of images you would expect to be rejected. *These images were classified as inappropriate by the Computer Vision API*.

The presence of all images in the *profile-pics* and *rejected* containers is proof that your Azure Function executed each time an image was uploaded to the "uploaded" container. If you would like, return to the BlobImageAnalysis function in the portal and click *Monitor*. You will see a log detailing each time the function executed.

<div class="exercise-end"></div>

### Viewing Blob Metadata

What if you would like to view the scores for adult content and raciness returned by the Computer Vision API for each image uploaded to the "uploaded" container? The scores are stored in blob metadata for the images in the "profile-pics" and "rejected" containers, but blob metadata can't be viewed through the Azure Portal.

In this exercise, you will use the cross-platform [Microsoft Azure Storage Explorer](http://storageexplorer.com) to view blob metadata and see how the Computer Vision API scored the images you uploaded.

<h4 class="exercise-start">
    <b>Exercise</b>: View Blob Metadata with Storage Explorer
</h4>

Start Storage Explorer. Find your storage account you've bene working with and expand the list of blob containers underneath it. Then click the container named *profile-pics*.

Right-click an image in the "profile-pics" container and select *Properties* from the context menu.

Inspect the blob's metadata. *IsAdultContent* and *isRacyContent* are Boolean values that indicate whether the Computer Vision API detected adult or racy content in the image. *adultScore* and *racyScore* are the computed probabilities.

<img src="images/chapter8/profile-pic-metadata.gif" class="img-large" />

You can probably imagine how this might be used in the real world. Suppose you were building a photo-sharing site and wanted to prevent adult images from being stored. You could easily write an Azure Function that inspects each image that is uploaded and deletes it from storage if it contains adult content.

<div class="exercise-end"></div>

#### Summary 

In this chapter you learned how to:
- Create an Azure Function App
- Write an Azure Function that uses a blob trigger
- Add application settings to an Azure Function App
- Use Microsoft Cognitive Services to analyze images and store the results in blob metadata

This is just one example of how you can leverage Azure Functions to automate repetitive tasks. Experiment with other Azure Function templates to learn more about Azure Functions and to identify additional ways in which they can aid your research or business.

In the next chapter, we'll take the web app to the next level by adding in a SignalR hub, and automatically updating a profile picture if it's approved.