## Building a Staging Blob Container Profile Pictures

This is a short chapter, but sets the stage for something big. Imagine the web app we've been building was going to be used in a production capacity. Do you really want to give users the ability to upload *any* image are their profile image? Some images just aren't suitable for a work enviornment. 

Wouldn't it be nice to add a step into the profile image upload process where images were screened? But, who's going to do the screening? Do you really have time to screen *every* images uploaded. Maybe it makes sense for a small site, but what happens when there's thousands of users? 

For me, there's no chance that I would introduce a manual screen step. Instead, I'd look for every opportunity to automate this process so I can spend the time to write code once, and use it everytime someone changes their profile picture. 

Over the next several chapters, we'll be building an automated image screening process using [Microsoft's Cognitive Services Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). We'll combine various Azure services: blob storage containers, an Azure function listening for changes to a blob container, and REST service calls to the computer vision API. The computer vision API will process each uploaded image, indicating whether it is work appropriate. Images that are acceptable will be moved to the profile picture blob container. Invalid images will be moved to a separate container.  

In this chapter, we'll start building the autoamted process by modifying our web app to place the uploaded images in a staging blob container. Let's get started.

### Creating a Profile Picture Staging Blob Container

<h4 class="exercise-start">
    <b>Exercise</b>: Extending ASP.NET Identity with a Profile Picture
</h4>

This is a fairly short exercise, where you'll do 2 things to create a staging blob container for profile pictues:
* **Step 1:** Create a web.config app setting for the upload/staging blob contianer name 
* **Step 2:** Update the upload image function to upload images to the staging blob container

#### **Step 1:** Create a web.config app setting for the upload/staging blob contianer name

Add a new app setting to the *web.config* file named `ProfilePicUploadBlobContainer`.

```xml
<add key="ProfilePicUploadBlobContainer" value="uploaded" />
```

#### **Step 2:** Update the upload image function to upload images to the staging blob container

Update the `UploadImageAsync()` function to change the blob container images are uploaded to. You can find this function in *ManageController.cs*.

```csharp
public async Task<string> UploadImageAsync(string currentBlobUrl, HttpPostedFileBase imageToUpload)
{
    string imageFullPath = null;
    if (imageToUpload == null || imageToUpload.ContentLength == 0)
    {
        return null;
    }
    try
    {
        // connect to our storage account
        var cloudStorageAccount = CloudStorageAccount.Parse($"DefaultEndpointsProtocol=https;AccountName={ConfigurationManager.AppSettings["StorageAccountName"]};AccountKey={ConfigurationManager.AppSettings["StorageAccountKey"]};");
        var cloudBlobClient = cloudStorageAccount.CreateCloudBlobClient();
        var cloudBlobContainer = cloudBlobClient.GetContainerReference(ConfigurationManager.AppSettings["ProfilePicUploadBlobContainer"]);

        // create the blob storage container, if needed
        if (await cloudBlobContainer.CreateIfNotExistsAsync())
        {
            await cloudBlobContainer.SetPermissionsAsync(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                }
            );
        }

        var imageName = $"{Guid.NewGuid()}{Path.GetExtension(imageToUpload.FileName)}";

        // upload image blob
        var cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(imageName);
        cloudBlockBlob.Properties.ContentType = imageToUpload.ContentType;
        await cloudBlockBlob.UploadFromStreamAsync(imageToUpload.InputStream);

        // get URL of uploaded image blob - replacing the staging/upload container name 
        // with the valid blob container name
        imageFullPath = cloudBlockBlob.Uri.ToString().Replace(ConfigurationManager.AppSettings["ProfilePicUploadBlobContainer"], ConfigurationManager.AppSettings["ProfilePicBlobContainer"]);
    }
    catch (Exception ex)
    {
        // in reality, you should handle this...
    }
    return imageFullPath;
}
```

The changes made are straight-forward, but we'd like to call attention to the `imageFullPath` variable and it's value: 

```csharp
imageFullPath = 
    cloudBlockBlob.Uri.ToString()
        .Replace(
            ConfigurationManager.AppSettings["ProfilePicUploadBlobContainer"], 
            ConfigurationManager.AppSettings["ProfilePicBlobContainer"]);
```

From the previous chapter, you'll recall that `cloudBlockBlob` is a reference to the uploaded blob image. After this change, the blob container we'll be upload to is the staging/upload container named *uploaded* (stored in the `ProfilePicUploadBlobContainer` app setting). This staging/upload container should not be returned to the app, because images in this container have not been processed with the computer vision API. 

When images get processed, valid profile pictures will be placed in the *profile-pics* container (stored in the `ProfilePicBlobContainer` app setting). As a result, the URL returned from the upload function returns the URL of the valid image. 

Let's test it out! Compile and run the web app. When you upload a new image, you should expect the image to be placed into the *uploaded* blob container, and the profile management page to not display an image (because we haven't created the Azure function to validate the pictures with the computer vision API).

<img src="images/chapter7/profile-pics-uploaded.gif" class="img-large" />

Use Storage Explorer to check out the blob containers. You should see the *profile-pics* and *uploaded* containers. The *profile-pics* container will have the original profile picture inside, and the *uploaded* container will have the new profile picture.

<img src="images/chapter7/storage-explorer.gif" class="img-large" />

<div class="exercise-end"></div>

That concludes this chapter. We said it would be a short one. 

#### Summary

In this chapter, you:
* Learned about the automated profile picture analysis process we'll be implementing
* Updated the web app to upload images to a different blob container that will be used as an upload/staging area
* Verified the creation of the upload/staging blob container and verified images were added to the correct blob container

In the next chapter, we'll finish building the automated profile picture analysis process.