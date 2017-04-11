## Updating Windows Identity

### Exploring Windows Identity

### Replacing it with Azure Table Storage

### What is Azure Table Storage?

### Remove Entity Framework

- Microsoft.AspNet.Identity.EntityFramework
- EntityFramework

### Add ElCamino.AspNet.Identity.AzureTable 

ElCamino is a ASP.NET Identity package that allows Identity to use table storage instead of a SQL database.

- ElCamino.AspNet.Identity.AzureTable

It'll install a bunch of other packages, but just let it. 

### Update the additional NuGet packages that were just installed with ElCamino

You may need to do that multiple times. 

### IdentityModel.cs

- replace usings to strip out EF and support the ElCamino versions
- instead of public class ApplicationDbContext : IdentityDbContext<>, you need to inherit ApplicationDbContext: IdentityCloudContext
- change the whole class reference of ApplicationDbContext

### IdentityConfig.cs

- replace usings
- add the StartupAsync() function to the ApplicationUserManager class to tell identity to auto-create the azure tables

### Globalasax.cs

- call the azure table create process 

### web.config

- remove EF references
- add elcamino configuration to configSections

### build and compile your app
