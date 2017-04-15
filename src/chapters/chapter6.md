## Adding a profile picture to the app

In this chapter you will learn:
* How to extend ASP.NET Identity by adding an image to a user profile
* How to upload files to Azure blob storage

In the last chapter, you learned how to extend ASP.NET Identity by adding a biography to a user's profile. You also saw that by adding a public property to the `ApplicationUser` class, ASP.NET Identity managed the data sotre's schema.

We'll continue extending ASP.NET Identity in this chapter by adding an image to the user profile management page.

### Visualizing What You're Building

Before we jump in, let's take a minute to visualize what you'll be building in this chapter.

<img src="images/chapter6/upload-picture.gif" class="img-large" />

We'll be adding the *Profile Picture* field. Once added, you'll be able to see your uploaded picture on the main page and navigate to a second page to update it.

Let's get started! We'll begin by modifying ASP.NET Identity to include the new field, then move on to modifying the MVC code.

### Extending ASP.NET Identity

You'll recall that we previously updated the `ApplicationUser` and `ApplicationUserManager` classes to extend ASp.NET Identity. We'll be doing something similar for the profile picture. 

<h4 class="exercise-start">
    <b>Exercise</b>: Extending ASP.NET Identity with a Profile Picture
</h4>

To extend ASP.NET Identity, add two things:
1. public property to the `ApplicationUser` class
2. async accessor function for the property to the `ApplicationUserManager` class

#### Updating the `ApplicationUser` class

Add a public property to the `ApplicationUser` class, located in the the *IdentityModel.cs* file. You can find this file in the *Models* folder.

You'll remember this is the class that inherits from `IdentityUser`, the class ASP.NET Identity uses to represent a user. 

```csharp
public string ProfilePicUrl { get; set; }
```

You may have noticed something interesting about the property you just added: it's a string, not an image. But why?

> **NOTE:** We could add a binary property to store the actual image, but that would store the images as a column in the *AspNetUsers* table in Azure table storage. Table storage is great for semi-structured text data, but not so good for blob (binary large object) data, like an image. Instead, we'll be using a different type of Azure storage to store our images, called blob storage. We're not going to cover the details of blob storage right now, but it's important you understand why we're not adding a binary property to the `ApplicationUser` class.

So, if we're not storing binary data in the `ApplicationUser` class, why are we storing a URL? You may recall from a previous chapter that data stored in an Azure storage account can be accessed via URL in the form of *https://{storage-account-name}.{storage-type}.core.windows.net*. 

After uploading an image to blob storage, we'll save the image URL to our user profile.  

#### Updating the `ApplicationUserManager` class

We also need to add an asynchronous accessor function to the `ApplicationUserManager` class (jus tlike we did for the biography property). The method will take a `userId` and return the user's profile picture URL. You can find the `ApplicationUserManager` class in the *IdentityConfig.cs* file, located in the *App_Start* folder.

Add this code after the constructor.

```csharp
public async Task<string> GetProfilePicUrlAsync(string userId)
{
    var user = await this.Store.FindByIdAsync(userId);
    return (user != null) ? user.ProfilePicUrl : string.Empty;
}
``` 

This function uses the `Store` object, which is of type `IUserStore<ApplicationUser>`, which (simply put) is the object that manages storing and retrieving user data from our Azure table. 

The `this.Store.FindByIdAsync(userId)` call gets a reference to our user. We then return the user's profile picture URL, or an empty string if the user isn't found.

<div class="exercise-end"></div>

That's it. Let's move on to updating our MVC app to support the profile picture property.










### Add Profile Pic to to the web app

- update the IndexViewModel to include the Profile Picture property
- update Index.cshtml, add HTML to display the update profile picture and a hiddenFor field links

- ManageViewModels.cs, add System.Web to usings
- update UpdateBiographyViewModel class in ManageViewModels.cs

- update UpdateBiography.cshtml, adding profile picture div

- update GET controller action for UpdateBiography
- update POST controller action for UpdateBiography
- add method UploadImageAsync
- add usings to support UploadImageSync code

- add app settings: StorageAccountName, StorageAccountKey, ProfilePicBlobContainer
- replace values for storage account name, storage account key

- update the Index GET function to display the correct update message and to load in the ProfilePicUrl via the user manager

### test!

- run web site
- check out storage explorer


