## Adding a profile picture to the app

We want to add a profile picture. It's similar to the biography, and we'll reuse some of the work we've alreayd done to display it.

### Update IdentityModel.cs

- add profile picture url to ApplicationUser

### Update IdentityConfig.cs
- Add async accessor method to ApplicationUserManager class GetProfilePicAsync(string userId);

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


