## Adding a custom field to AspNet Identity

In the previous chapters, you learned how to persist ASP.NET Identity user information in Azure Table Storage. You also learned how to use a locally-hosted storage emulator so you can develop without connecting the cloud. 

In this chapter, we'll continue to update our web app by adding a custom field to the user's profile. 

### Visualizing What You're Building

Before we jump in, let's take a minute to visualize what you'll be building. 

<img src="images/chapter5/manage-biography.gif" class="img-large" />

The ASP.NET MVC template starts with a profile management page that allows you to change your password, manage alternate logins (such as Facebook, Twitter, Microsoft, etc.), and configure multi-factor authentication (if enabled).

We'll be adding the *Biography* field. Once added, you'll be able to see your biography on the main page and navigate to a second page to update the biography. 

You may also notice the status message that is shown when the biography has been updated. 

![image](images/chapter5/message.png)

Let's get started! We'll begin by modifying ASP.NET Identity to include the new field, then move on to modifying the MVC code.

### Extending ASP.NET Identity

If you ever used the old ASP.NET Membership Provider, you'll remember how painful it was to extend it. Extending ASP.NET Identity is different: it's easy.

<h4 class="exercise-start">
    <b>Exercise</b>: Extending ASP.NET Identity with a Biography
</h4>

To extend ASP.NET Identity, add two things:
1. public property to the `ApplicationUser` class
2. async accessor function for the property to the `ApplicationUserManager` class

#### Updating the `ApplicationUser` class

Add a public property to the `ApplicationUser` class, located in the the *IdentityModel.cs* file. You can find this file in the *Models* folder.

You'll remember this is the class that inherits from `IdentityUser`, the class ASP.NET Identity uses to represent a user. 

```csharp
public string Biography { get; set; }
```

#### Updating the `ApplicationUserManager` class

Add an asynchronous accessor function to the `ApplicationUserManager` class. The method will take a `userId` and return the user's biography. You can find the `ApplicationUserManager` class in the *IdentityConfig.cs* file, located in the *App_Start* folder.

Add this code after the constructor.

```csharp
public async Task<string> GetBiographyAsync(string userId)
{
    var user = await this.Store.FindByIdAsync(userId);
    return (user != null) ? user.Biography : string.Empty;
}
``` 

This function uses the `Store` object, which is of type `IUserStore<ApplicationUser>`, which (simply put) is the object that manages storing and retrieving user data from our Azure table. 

The `this.Store.FindByIdAsync(userId)` call gets a reference to our user. We then return the user's biography, or an empty string if the user isn't found.

<div class="exercise-end"></div>

And, that's it. Pretty simple. That's all you need to do to extend ASP.NET Identity with a new property. 

### Updating MVC code to Support the Biography Property

Now that you've added the property, let's update the MVC models, views, and controller actions to support the addition of the biography property.

<h4 class="exercise-start">
    <b>Exercise</b>: Updating the MVC code to support the biography property
</h4>

This exercise is a bit longer than others, because we'll be updating a lot of files. At a high level, we'll be adding the biography property to our web app in several steps. 

We'll start with the profile management page, which will show the biography and a link to update the biography:
* **Step 1:** Update the `IndexViewModel` in *ManageViewModels.cs*
* **Step 2:** Update the Manage\Index view 

Then we'll move on to a new page that updates the biography:
* **Step 3:** Create the `UpdateBiographyViewModel` class in *ManageViewModels.cs*
* **Step 4:** Create the Update Biography view
* **Step 5:** Add a GET controller action for the Update Biography view to *ManageController.cs*
* **Step 6:** Add a "Your biography was updated" message to *ManageController.cs*
* **Step 7:** Add a POST controller action for the Update Biography view to *ManageController.cs*

Finally, we'll return to the profile management page:
* **Step 8:** Update the GET controller action for the Index view in *ManageContoller.cs* to populate the view with the updated biography

There's a lot to do, so let's get moving!

#### **Step 1:** Update the `IndexViewModel` in *ManageViewModels.cs*

Start by updating the `IndexViewModel` class. Add a property for the biography.

```csharp
public class IndexViewModel
{
    public bool HasPassword { get; set; }
    public IList<UserLoginInfo> Logins { get; set; }
    public string PhoneNumber { get; set; }
    public bool TwoFactor { get; set; }
    public bool BrowserRemembered { get; set; }
    public string Biography { get; set; }
}
```

> Adding this property will allow the index view to display the biography when it loads. We'll be setting the value of the biography later in this excercise when we update the index controller's GET action.

#### **Step 2:** Update the Manage\Index view 

Update *Index.cshtml* in the *Views\Manage* folder to display:
* Biography heading
* Biography value
* Link to update the Biography

Add the Ravor markup as the first child element of the `<dl class="dl-horizontal">` element:

```html
<dt>Biography:</dt>
<dd>
    @Model.Biography
    [
    @Html.ActionLink("Update your biography", "UpdateBiography")
    ]
</dd>
```

The HTML action link will render as an HTML link to the Update Biography view (which we'll create next).

#### **Step 3:** Create the `UpdateBiographyViewModel` class in *ManageViewModels.cs*

Add a new view model class named `UpdateBiographyViewModel` in the *ManageViewModels.cs* file. This view model will be used to view and update a user's biography from the Update Biography view.

```csharp
public class UpdateBiographyViewModel
{
    [Display(Name = "Biography")]
    public string Biography { get; set; }
}
```

#### **Step 4:** Create the Update Biography view

Create a view named `UpdateBiography.cshtml` in the *Views\Manage* folder. This view will use the previously created `UpdateBiographyViewModel` to show and update a user's biography.

```html
@model Web.Models.UpdateBiographyViewModel
@{
    ViewBag.Title = "Biography";
}

<h2>@ViewBag.Title</h2>

@using (Html.BeginForm("UpdateBiography", "Manage", FormMethod.Post, new { @class = "form-horizontal", role = "form", enctype = "multipart/form-data" }))
{
    @Html.AntiForgeryToken()
    <h4>Update your biography</h4>
    <hr />
    @Html.ValidationSummary("", new { @class = "text-danger" })
    <div class="form-group">
        @Html.LabelFor(m => m.Biography, new { @class = "col-md-2 control-label" })
        <div class="col-md-10">
            @Html.TextBoxFor(m => m.Biography, new { @class = "form-control" })
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-offset-2 col-md-10">
            <input type="submit" class="btn btn-default" value="Submit" />
        </div>
    </div>
}

@section Scripts {
    @Scripts.Render("~/bundles/jqueryval")
}
```

#### **Step 5:** Add a GET controller action for the Update Biography view to *ManageController.cs*

Now that the model and view are created, add a GET controller action to the *ManageController.cs* file to show the Update Biography view.

```csharp
//
// Get: /Manage/UpdateBiography
public async Task<ActionResult> UpdateBiography()
{
    var userId = User.Identity.GetUserId();
    var updateBiographyViewModel = new UpdateBiographyViewModel()
    {
        Biography = await UserManager.GetBiographyAsync(userId) 
    };

    return View(updateBiographyViewModel);
}
``` 

You may not immediately recognize all of the code you just added, so let's break it down. First, we grab the current user's id by calling into ASP.NET Identity with `User.Identity.GetUserId()`. With the user's id, we call the funciton we created earlier in this chapter (`GetBiographyAsync()`) to load the user's biography. 

The retrieved biography is then used to construct a model passed back to the Update Biography view.

#### **Step 6:** Add a "Your biography was updated" message to *ManageController.cs*

Now that we have the Update Biography view showing a user's biography, let's start planning what happens when a user's biogrpahy is updated. 

When the biography is updated, the POST controller action will be called and the user will be redirected back to the Manage\Index view. We'll get to this next. 

When you return to the Manage\Index view, a message reading, "Your biography was updated" is also displayed. This is done by passing a specially-formatted query string value back to the view via the `ManageMessageId` enum. 

Update the enum's values to include a value for `UpdateBiographySuccess`. 

> **NOTE** You may have trouble finding the enum declaration because it's hidden behind a collapsed region labeled *Helpers*. Scroll down to the bottom of the *ManageController.cs* class to find the region. Expand it and you'll be able to locate the enum.

```csharp
public enum ManageMessageId
{
    AddPhoneSuccess,
    ChangePasswordSuccess,
    SetTwoFactorSuccess,
    SetPasswordSuccess,
    RemoveLoginSuccess,
    RemovePhoneSuccess,
    Error,
    UpdateBiographySuccess
}
```

#### **Step 7:** Add a POST controller action for the Update Biography view to *ManageController.cs*

Add a POST controller action to the Manage controller, using the `UpdateBiographySuccess` enum value just created.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> UpdateBiography(UpdateBiographyViewModel model)
{
    if (!ModelState.IsValid)
    {
        return View(model);
    }
    var user = await UserManager.FindByIdAsync(User.Identity.GetUserId());
    if (user != null)
    {
        user.Biography = model.Biography;
        await UserManager.UpdateAsync(user);
    }
    return RedirectToAction("Index", new { Message = ManageMessageId.UpdateBiographySuccess });
}
``` 

Much of the code is stright-forward, but we want to draw your attention to a few lines, starting with `var user = await UserManager.FindByIdAsync(User.Identity.GetUserId());`. You've already seen the `GetUserId()` function, but you haven't directly worked with the `UserManager` class. 

> **NOTE** The `UserManager` class, well, manages users. That sounds redundant, but we like ot think of it as a user repository. If you're not familiar with the repository pattern, Martin Fowler has an excellent article on [repositories] online. Check it out. 

Back to the code. Because `UserManager` acts as a repository, we use it to retrieve a user object (more specifically, the `ApplicationUser` object which inherits from `IdentityUser`). Once we have the user, we update the biography property and send the user back through the repository to be persisted: `await UserManager.UpdateAsync(user);`.

After the user is saved, you're redirected back to the Manage\Index view, passing the `UpdateBiographySuccess` enum value as a query string parameter.

#### **Step 8:** Update the GET controller action for the Index view in *ManageContoller.cs* 

The final step is to update the GET controller action of the Manage\Index view. Below is the entire function, but note the added line setting the `ViewBag.StatusMessage` to "Your biography was updated." when the `ManageMessageId` enum has a value of `UpdateBiographySuccess`. 

You should also note that the index view model's biography property is set by calling the method you created eariler: `UserManager.GetBiographyAsynx(userId)`.

```csharp
//
// GET: /Manage/Index
public async Task<ActionResult> Index(ManageMessageId? message)
{
    ViewBag.StatusMessage =
        message == ManageMessageId.ChangePasswordSuccess ? "Your password has been changed."
        : message == ManageMessageId.SetPasswordSuccess ? "Your password has been set."
        : message == ManageMessageId.SetTwoFactorSuccess ? "Your two-factor authentication provider has been set."
        : message == ManageMessageId.Error ? "An error has occurred."
        : message == ManageMessageId.AddPhoneSuccess ? "Your phone number was added."
        : message == ManageMessageId.RemovePhoneSuccess ? "Your phone number was removed."
        : message == ManageMessageId.UpdateBiographySuccess ? "Your biography was updated."
        : "";

    var userId = User.Identity.GetUserId();
    var model = new IndexViewModel
    {
        HasPassword = HasPassword(),
        PhoneNumber = await UserManager.GetPhoneNumberAsync(userId),
        TwoFactor = await UserManager.GetTwoFactorEnabledAsync(userId),
        Logins = await UserManager.GetLoginsAsync(userId),
        BrowserRemembered = await AuthenticationManager.TwoFactorBrowserRememberedAsync(userId),
        Biography = await UserManager.GetBiographyAsync(userId),
    };
    return View(model);
}
```

<div class="exercise-end"></div>

Phew! That was a huge update to the code base. Compile and cross your fingers ;-).

When you launch the app to test, login and navigate to the profile management page. You should see something similar to the image below. If you don't, that's ok. You can always grab the code from the `chapter6` branch of the Github repository.

<img src="images/chapter5/manage-biography.gif" class="img-large" />

#### Summary

In this chapter, you learned:
* It's easy to extend ASP.NET identity by adding a public property to the `ApplicationUser` class
* The `ApplicationUserManager` class acts as a repository for application users

In the next chapter, we'll continue to extend ASP.NET Identity by adding a profile picture to our app.