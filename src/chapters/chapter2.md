## Updating Windows Identity

In this chapter, you'll be learning aobut ASP.NET Identity and how to move it's backend data store from SQL Server to Azure Table Storage.

Before we begin, let's cover a few basics on what ASP.NET Identity is and how it works. 

### A brief history of ASP.NET authentication and authorization

If you've developed an ASP.NET application prior to 2014, you've probably heard of the ASP.NET membership system. 

The ASP.NET membership system was introduced with ASP.NET 2.0 back in 2005, and since then there have been many changes in the ways web applications typically handle authentication and authorization (credits: [docs.microsoft.com](https://docs.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity)).

> Credit for this section is attributed to [Microsoft](https://docs.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity).

#### ASP.NET Membership

[ASP.NET Membership](https://msdn.microsoft.com/en-us/library/yh26yfzy.aspx) was designed to solve site membership requirements that were common in 2005, which involved Forms Authentication, and a SQL Server database for user names, passwords, and profile data. Today there is a much broader array of data storage options for web applications, and most developers want to enable their sites to use social identity providers for authentication and authorization functionality. The limitations of ASP.NET Membership's design make this transition difficult:

* The database schema was designed for SQL Server and you can't change it. You can add profile information, but the additional data is packed into a different table, which makes it difficult to access by any means except through the Profile Provider API.
* The provider system enables you to change the backing data store, but the system is designed around assumptions appropriate for a relational database. You can write a provider to store membership information in a non-relational storage mechanism, such as Azure Storage Tables, but then you have to work around the relational design by writing a lot of code and a lot of `System.NotImplementedException` exceptions for methods that don't apply to NoSQL databases.
* Since the log-in/log-out functionality is based on Forms Authentication, the membership system can't use [OWIN](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/an-overview-of-project-katana). OWIN includes middleware components for authentication, including support for log-ins using external identity providers (like Microsoft Accounts, Facebook, Google, Twitter), and log-ins using organizational accounts from on-premises Active Directory or Azure Active Directory. OWIN also includes support for OAuth 2.0, JWT and CORS.

#### ASP.NET Simple Membership

[ASP.NET Simple Membership](https://docs.microsoft.com/en-us/aspnet/web-pages/overview/security/16-adding-security-and-membership) was developed as a membership system for ASP.NET Web Pages. It was released with WebMatrix and Visual Studio 2010 SP1. The goal of Simple Membership was to make it easy to add membership functionality to a Web Pages application.

Simple Membership did make it easier to customize user profile information, but it still shares the other problems with ASP.NET Membership, and it has some limitations:

* It was hard to persist membership system data in a non-relational store.
* You can't use it with OWIN.
* It doesn't work well with existing ASP.NET Membership providers, and it's not extensible.

#### ASP.NET Universal Providers

[ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) were developed to make it possible to persist membership information in Microsoft Azure SQL Database, and they also work with SQL Server Compact. The Universal Providers were built on Entity Framework Code First, which means that the Universal Providers can be used to persist data in any store supported by EF. With the Universal Providers, the database schema was cleaned up quite a lot as well.

The Universal Providers are built on the ASP.NET Membership infrastructure, so they still carry the same limitations as the SqlMembership Provider. That is, they were designed for relational databases and it's hard to customize profile and user information. These providers also still use Forms Authentication for log-in and log-out functionality.

### Exploring ASP.NET Identity

As the membership story in ASP.NET has evolved over the years, the ASP.NET team has learned a lot from feedback from customers.

The assumption that users will log in by entering a user name and password that they have registered in your own application is no longer valid. The web has become more social. Users are interacting with each other in real time through social channels such as Facebook, Twitter, and other social web sites. Developers want users to be able to log in with their social identities so that they can have a rich experience on their web sites. A modern membership system must enable redirection-based log-ins to authentication providers such as Facebook, Twitter, and others.

As web development evolved, so did the patterns of web development. Unit testing of application code became a core concern for application developers. In 2008 ASP.NET added a new framework based on the Model-View-Controller (MVC) pattern, in part to help developers build unit testable ASP.NET applications. Developers who wanted to unit test their application logic also wanted to be able to do that with the membership system.

Considering these changes in web application development, ASP.NET Identity was developed with the following goals:

* **One ASP.NET Identity system**
    * ASP.NET Identity can be used with all of the ASP.NET frameworks, such as ASP.NET MVC, Web Forms, Web Pages, Web API, and SignalR.
    * ASP.NET Identity can be used when you are building web, phone, store, or hybrid applications.

* **Ease of plugging in profile data about the user**
    * You have control over the schema of user and profile information. For example, you can easily enable the system to store birth dates entered by users when they register an account in your application.

* **Persistence control**
    * By default, the ASP.NET Identity system stores all the user information in a database. ASP.NET Identity uses Entity Framework Code First to implement all of its persistence mechanism.
    * Since you control the database schema, common tasks such as changing table names or changing the data type of primary keys is simple to do.
    * It's easy to plug in different storage mechanisms such as SharePoint, Azure Storage Table Service, NoSQL databases, etc., without having to throw `System.NotImplementedExceptions` exceptions.

* **Unit testability**
    * ASP.NET Identity makes the web application more unit testable. You can write unit tests for the parts of your application that use ASP.NET Identity.

* **Role provider**
    * There is a role provider which lets you restrict access to parts of your application by roles. You can easily create roles such as "Admin" and add users to roles.

* **Claims Based**
    * ASP.NET Identity supports claims-based authentication, where the user's identity is represented as a set of claims. Claims allow developers to be a lot more expressive in describing a user's identity than roles allow. Whereas role membership is just a boolean (member or non-member), a claim can include rich information about the user's identity and membership.

* **Social Login Providers**
    * You can easily add social log-ins such as Microsoft Account, Facebook, Twitter, Google, and others to your application, and store the user-specific data in your application.

* **Azure Active Directory**
    * You can also add log-in functionality using Azure Active Directory, and store the user-specific data in your application. For more information, see Organizational Accounts in Creating ASP.NET Web Projects in Visual Studio 2013

* **OWIN Integration**
    * ASP.NET authentication is now based on OWIN middleware that can be used on any OWIN-based host. ASP.NET Identity does not have any dependency on System.Web. It is a fully compliant OWIN framework and can be used in any OWIN hosted application.
    * ASP.NET Identity uses OWIN Authentication for log-in/log-out of users in the web site. This means that instead of using FormsAuthentication to generate the cookie, the application uses OWIN CookieAuthentication to do that.

* **NuGet package**
    * ASP.NET Identity is redistributed as a NuGet package which is installed in the ASP.NET MVC, Web Forms and Web API templates that ship with Visual Studio 2013. You can download this NuGet package from the NuGet gallery.
    * Releasing ASP.NET Identity as a NuGet package makes it easier for the ASP.NET team to iterate on new features and bug fixes, and deliver these to developers in an agile manner.

### Getting Started with ASP.NET Identity

ASP.NET Identity is used in the Visual Studio project templates for ASP.NET MVC, Web Forms, Web API and SPA. In this walkthrough, we'll illustrate how the project templates use ASP.NET Identity to add functionality to register, log in and log out a user.

ASP.NET Identity is implemented using the following procedure. The purpose of this section is to give you a high level overview of ASP.NET Identity.

> You do not need to follow along with these steps, but are welcome to. 

1. Create an ASP.NET MVC application with Individual Accounts. You can use ASP.NET Identity in ASP.NET MVC, Web Forms, Web API, SignalR etc. In this example we start with an ASP.NET MVC application.

    ![image](images/chapter2/mvc-create.png)

2. The created project contains the following three packages for ASP.NET Identity.

    * **Microsoft.AspNet.Identity.EntityFramework**
        This package has the Entity Framework implementation of ASP.NET Identity which will persist the ASP.NET Identity data and schema to SQL Server.

    * **Microsoft.AspNet.Identity.Core**
        This package has the core interfaces for ASP.NET Identity. This package can be used to write an implementation for ASP.NET Identity that targets different persistence stores such as Azure Table Storage, NoSQL databases etc.

    * **Microsoft.AspNet.Identity.OWIN**
        This package contains functionality that is used to plug in OWIN authentication with ASP.NET Identity in ASP.NET applications. This is used when you add log in functionality to your application and call into OWIN Cookie Authentication middleware to generate a cookie.

3. **Registering a user.** After the project is created, launch the web applicaiton. Click on the Register link to create a user. The following image shows the Register page which collects the user name and password.

    ![image](images/chapter2/register.gif)

    When the user clicks the Register button, the Register action of the Account controller creates the user by calling the ASP.NET Identity API. In the code snippet below, the `ApplicationUser` and `UserManager` classes are part of ASP.NET Identity. An `ApplicationUser` is created and passed to the `UserManager` class to create the user. 

    ```csharp
    [HttpPost]
    [AllowAnonymous]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Register(RegisterViewModel model)
    {
        if (ModelState.IsValid)
        {
            var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
            var result = await UserManager.CreateAsync(user, model.Password);
            if (result.Succeeded)
            {
                // code truncated purposefully
            }
            AddErrors(result);
        }

        // If we got this far, something failed, redisplay form
        return View(model);
    }
    ```

    > **DEFINITION** The `ApplicationUser` class is part of the ASP.NET MVC template, and inherits fromt he `IdentityUser` class. The class name doesn't matter, but inhireting from `IdentityUser` does. We're not going to cover the specifics of the `IdentityUser` class in this bootcamp, but it represents a user, with properties like *Name*, *Email*, *PhoneNumber*, etc. For more details on the `IdentityUser` class check out [this article](https://msdn.microsoft.com/en-us/magazine/dn818488.aspx).

    > **DEFINITION** The `UserManager` class is part of ASP.NET Identity and provides methods for managing users. Strange, right? ;-) 

    Luckily, the ASP.NET MVC template provides a solid foundation for us, so you don't need to know *everything* about ASP.NET Identity to start using it. As we continue, we'll make sure you know what you'll need to know as we go. 

4. **Logging in.** If user registration is successful, the user is automatically logged in. A call to the `SignInManager` class is made, passing the instance of the `ApplicationUser` previously created.  

    ```csharp
    [HttpPost]
    [AllowAnonymous]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Register(RegisterViewModel model)
    {
        if (ModelState.IsValid)
        {
            var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
            var result = await UserManager.CreateAsync(user, model.Password);
            if (result.Succeeded)
            {
                await SignInManager.SignInAsync(user, isPersistent:false, rememberBrowser:false);
                
                return RedirectToAction("Index", "Home");
            }
            AddErrors(result);
        }

        // If we got this far, something failed, redisplay form
        return View(model);
    }
    ```

    > **DEFINITION** The `SignInManager` class is also part of ASP.NET Identity and provides methods for managing the sign in processes.

    We're not diving into the details of what actually happens when a user is signed in (for example, creating a claim, cookie, etc.). If you're interested in the details, check out [this article](https://docs.microsoft.com/en-us/aspnet/identity/overview/getting-started/introduction-to-aspnet-identity).

There are various other classes in ASP.NET Identity and the ASP.NET MVC template of importance, but we're not going to investigate them here. But, don't worry. We'll teach you about it as needed.

### ASP.NET Identity Data Storage

In this section, you'll be learning how to replace the backend data storage platform of ASP.NET Identity. By default, ASP.NET Identity uses [Entity Framework](https://msdn.microsoft.com/en-us/library/aa937723.aspx) to manage data persistence to SQL Server. 

Using Entity Framework and SQL Server is a great choice. In fact, we *could* provision a SQL Server database in Azure, and use that for the backend data store for ASP.NET Identity. 

> But we're not.

Instead, you'll be replacing Entity Framework and SQL Server with a highly-scalable and light-weight NoSQL Azure service called [Table storage](https://azure.microsoft.com/en-us/services/storage/tables/). 

Before we jump in, let's learn a little bit about Azure Table Storage.

### What is Azure Table Storage?

Azure Table storage is a service that stores structured NoSQL data in the cloud, providing a key/attribute store with a schemaless design. Because Table storage is schemaless, it's easy to adapt your data as the needs of your application evolve. Access to Table storage data is fast and cost-effective for many types of applications, and is typically lower in cost than traditional SQL for similar volumes of data.

You can use Table storage to store flexible datasets like user data for web applications, address books, device information, or other types of metadata your service requires. You can store any number of entities in a table, and a storage account may contain any number of tables, up to the capacity limit of the storage account

#### The Azure Table Service

The Azure Table storage service stores large amounts of structured data. The service is a NoSQL datastore which accepts authenticated calls from inside and outside the Azure cloud. Azure tables are ideal for storing structured, non-relational data. Common uses of the Table service include:

* Storing TBs of structured data capable of serving web scale applications
* Storing datasets that don't require complex joins, foreign keys, or stored procedures and can be denormalized for fast access
* Quickly querying data using a clustered index
* Accessing data using the OData protocol and LINQ queries with WCF Data Service .NET Libraries

You can use the Table service to store and query huge sets of structured, non-relational data, and your tables will scale as demand increases.

#### Table Service Concepts

The Table service contains the following components:

![image](images/chapter2/table-service.png)

At the top level is a storage account. Storage accounts are named containers with a URL, which is used to access various services hosued within the account. You'll be creating a storage account later in the bootcamp.

There are various concepts important to know about Azure Table Storage:

* **URL format:** You can access tables and entities Code addresses tables in an account using this address format: http://`<storage account>`.table.core.windows.net/`<table>`
You can address Azure tables directly using this address with the OData protocol. For more information, see [OData.org](OData.org).

* **Storage Account:** All access to Azure Storage is done through a storage account. 

* **Table:** A table is a collection of entities. Tables don't enforce a schema on entities, which means a single table can contain entities that have different sets of properties. The number of tables that a storage account can contain is limited only by the storage account capacity limit.

* **Entity:** An entity is a set of properties, similar to a database row. An entity can be up to 1MB in size.

* **Properties:** A property is a name-value pair. Each entity can include up to 252 properties to store data. Each entity also has 3 system properties that specify a partition key, a row key, and a timestamp. Entities with the same partition key can be queried more quickly, and inserted/updated in atomic operations. An entity's row key is its unique identifier within a partition.

#### Comparing Table storage to SQL Server tables

If you're familiar with SQL server, you can compare it easily to the storage account, table service, tables, and entitites:

* SQL Server == Storage Account
* Database == Table Service
* Table == Table
* Record == Entity
* Column == Property

Now that you know about Table storage, let's get to work replacing Entity Framework and SQL Server as the backend store for ASP.NET Identity.

### Replacing the Backend Data Store of ASP.NET Identity

<h4 class="exercise-start">
    <b>Exercise</b>: Removing Entity Framework
</h4>

Start by right-clicking your solution in the Visual Studio solution explorer and selecting *Manage NuGet Packages for Solution...*.

![image](images/chapter2/manage-nuget.png)

In the NuGet window, click on *Installed*, type in *entity* in the *Search* bar:

![image](images/chapter2/search-nuget.gif)

Uninstall these NuGet packages in the following order:

* Microsoft.AspNet.Identity.EntityFramework
* EntityFramework

![image](images/chapter2/uninstall-ef.gif)

We're not done yet, so hold on. There are several references to Entity Framework in the `web.config` file. Remove the following:

In `<configSections>`, remove the section named *entityFramework*. Leave the `<configSections>` element in place, because we'll be adding to it later.

```xml
<configSections>
    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
</configSections>
```

Delete the connection string named `DefaultConnection`:

```xml
<connectionStrings>
    <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-Web-20170403111511.mdf;Initial Catalog=aspnet-Web-20170403111511;Integrated Security=True" providerName="System.Data.SqlClient" />
</connectionStrings>
```

Scroll to element named `<entityFramework>...</entityFramework>` near the end of the file. Remove the entire element:

```xml
<entityFramework>
    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
        <parameter value="mssqllocaldb" />
        </parameters>
    </defaultConnectionFactory>
    <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
    </providers>
</entityFramework>
``` 

<div class="exercise-end"></div>

Now that we've removed Entity Framework, we need to replace it. If you recall, Entity Framework was used by ASP.NET Identity as a middleware to map between ASP.NET Identity code objects (like the `IdentityUser` class) and the backend data store. 

We'll be using Azure Table storage as our data store, so we'll have to add another library to act as the middleware for persisting data to Azure Table storage. The package we'll be using is named *ElCamino.AspNet.Identity.AzureTable*.

<h4 class="exercise-start">
    <b>Exercise</b>: Adding the ElCamino.AspNet.Identity.AzureTable package
</h4>

Open the NuGet package manager for the solution, browse for and install the ElCamino.AspNet.Identity.AzureTable NuGet package:

![image](images/chapter2/install-elcamino.gif)

The installation of this package will install various additional packages. If prompted, allow them to be installed. Also accept any license agreements, if prompted.

After the *ElCamino.AspNet.Identity.AzureTable* package has been installed, there will be several NuGet packages that need updated. Click the *Updates* link and update all NuGet packages

![image](images/chapter2/update-nuget2.gif)

> **NOTE** You may need to update the NuGet packages several times, as various packages will install new dependencies during the upgrade process.

<div class="exercise-end"></div>

Now that the easy part is finished, it's time to start updating code to account for a new backend data store. ASP.NET Identity makes this reletively painless. Let's get started.

<h4 class="exercise-start">
    <b>Exercise</b>: Updating ASP.NET Identity code to replace Entity Framework
</h4>

#### IdentityModel.cs Changes

The first code change we'll make is to the `IdentityModel.cs` file. You can find this file in the `Models` folder of the web project.

![image](images/chapter2/identity-model.png)

Replace the using statements at the top, removing the Entity Framework references and adding the ElCamino references.

> **NOTE** Throughout the bootcamp, feel free to copy and paste code directly from the guide into Visual Studio. There's a handy *Copy* button above our code listings!

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNet.Identity;
using ElCamino.AspNet.Identity.AzureTable;
using ElCamino.AspNet.Identity.AzureTable.Model;
```

Find the class declaration for `ApplicationDbContext`. This class is an abstraction used to interface between the ASP.NET MVC template code for ASP.NET Identity and the middleware that interfaces with ASP.NET Identity. The class currently inherits from `IdentityDbContext<>`. 

Change the class declaration so it inherits from `IdentityCloudContext`. Also remove the parameterized base class constructor. The new `ApplicationDbContext` class should look like this:

```csharp 
public class ApplicationDbContext : IdentityCloudContext
{
    public ApplicationDbContext() : base() { }

    public static ApplicationDbContext Create()
    {
        return new ApplicationDbContext();
    }
}
```
 
#### IdentityConfig.cs Changes

The next file we'll change is the `IdentityConfig.cs` file, located in the `App_Start` folder:

![image](images/chapter2/identity-config.png)

This file contains a variety of class definitions used by the ASP.NET MVC template. The most important of the classes is the `ApplicationUserManager` class. This class is responsible for configuring policies and defaults for user accounts in the application (for example, the password validation policy, user email address uniqueness, lockout period if a password is typed in wrong X number of times, and multi-factor authentication via SMS and/or email).

The `ApplicationUserManager` class also contains a reference to the backend data store used to store user account information. We'll be modifying the class to auto-create the necessary tables if they don't exist.  

Start by replacing the using statements at the top, removing the Entity Framework references and adding the ElCamino references.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Threading.Tasks;
using System.Web;
using Microsoft.AspNet.Identity;
using Microsoft.AspNet.Identity.Owin;
using Microsoft.Owin;
using Microsoft.Owin.Security;
using Web.Models;
using ElCamino.AspNet.Identity.AzureTable;
using ElCamino.AspNet.Identity.AzureTable.Model;
```

Next, add a function named `StartupAsync()` that creates a new `UserStore` and creates the necessary Azure Tables. Place the below code beneath the `ApplicationUserManager` class constructor.

> **NOTE** You might be wondering how we came up with this code. ElCamino has documentation online with this well-documented code. If you're interested in the details and their implementation specifics, check out their [website](https://dlmelendez.github.io/identityazuretable/#/walkthrough).

```csharp
/// <summary>
/// ElCamino - Creates the Azure Table Storage Tables
/// </summary>
public static async void StartupAsync()
{
    var azureStore = new UserStore<ApplicationUser>(new ApplicationDbContext());
    await azureStore.CreateTablesIfNotExists();
}
```

#### Global.asax.cs Changes

Unfortunately, the `StartupAsync()` function you just added doesn't get called automatically. We'll need to update the `Global.asax.cs` file to invoke the `StartupAsync()` function when the application starts.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace Web
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            //ElCamino - Added to create azure tables
            ApplicationUserManager.StartupAsync();

            AreaRegistration.RegisterAllAreas();
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
    }
}
```

#### web.config Changes

The last step is to update the `web.config` file and add several ElCamino references to configure the middleware and specify a conneciton string to connect to Azure.

Replace the `<configSections>...</configSections>` element with the following code:

```xml
  <configSections>
    <section name="elcaminoIdentityConfiguration" type="ElCamino.AspNet.Identity.AzureTable.Configuration.IdentityConfigurationSection,ElCamino.AspNet.Identity.AzureTable " />
  </configSections>
  <elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="UseDevelopmentStorage=true" />
  <!--<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="DefaultEndpointsProtocol=https;AccountName=STORAGE_ACCOUNT_NAME;AccountKey=STORAGE_ACCOUNT_KEY;" />-->
```

By adding these XML settings, we're now able to specify an azure table storage account conneciton string via the web.config file. You may notice the connection string is `UseDevelopmentStorage=true`. This allows us to devleop locally without interfacing directly with Azure.  You'll learn the details of this soon, so hang in there. 

<div class="exercise-end"></div>

Nice work! We've finished replacing the backend data store of ASP.NET Identity to use Azure Table storage instead of Entity Framework and SQL Server. 

If you've been following along, you should be able to compile the solution. Go ahead and try.

In the next chapter, you'll learn how we can develop locally by using the Azure Storage emulator, and how to create an Azure storage account in the cloud.