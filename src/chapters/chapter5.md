## Adding a custom field to AspNet Identity

I want to add a biography. 

### Update IdentityModel.cs

- add public string Biography { get; set; } to ApplicationUser class

### Update IdentityConfig.cs
- Add async accessor method to ApplicationUserManager class GetBiographyAsync(string userId);

### Build the Update Biography

- update the IndexViewModel to include the Biography property
- update Index.cshtml, add HTML to display the update biography links

- add class to ManageViewModels.cs
- create UpdateBiography.cshtml
- copy paste the HTML
- add GET controller action
- locate ManageMessageId enum in the hidden Helpers area, add the UpdateBiographySuccess value to the enum
- add POST controller action

- update the Index GET function to display the correct update message and to load in the Biography via the user manager

### test it!

- works
- note that the tables are automatically upgraded to account for the changes we've made
