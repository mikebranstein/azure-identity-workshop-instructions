## Using Azure Storage Emulator to Develop Locally

Even though this workshop is all about the cloud and using Azure, it doesn't mean that we need to be connected to the cloud to develop and test our work.

In fact, an important aspect of a technology stack is being able to quickly and easily create an isolated (and local) environment for development and testing. 

In this section, you'll learn how to use the Azure Storage Emulator to host your own Azure-like table storage environment. You'll also learn how to use Storage Explorer, a GUI application (similar to SQL Server Management Studio), that allows you to navigate and browse through your Azure storage accounts.  

### Pre-requisite Check

Before we jump in, be sure to have installed these tools:

* [Azure Storage Emulator](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)
* [Storage Explorer](http://storageexplorer.com/)

### Running Azure Storage Emulator

The Azure Storage Emulator is a command line tool. Let's start it up.

<h4 class="exercise-start">
    <b>Exercise</b>: Starting up the Azure Storage Emulator
</h4>

Locate the `Microsoft Azure Storage Emulator - vX.X` in your start menu. 

> **NOTE** I find it easiest to find by opening the start menu and typing `storage`.

![image](images/chapter3/run-storage-emulator.gif)

When you run the storage emulator, a command prompt will open, initialize the emulator by installing a SQL database into your (localdb)\MSSQLLocalDB instance, and start the emulator.

After the emulator is started, a tray icon will appear noting the emulator is running:

![image](images/chapter3/emulator-running.png)
 
#### Troubleshooting the Storage Emulator

You may have problems starting the storage emulator, and will receive an ambiguous error:

<img src="images/chapter3/emulator-error.png" class="img-medium" />

There's a number of reasons why you may receive this error, including:

* Another program is listening on ports 10000, 10001, and 10002. Try shutting off Bit Torrent clients or other file sharing programs.
* You're not running the emulator as an Administrator.

Most of the time, I've found that the command prompt session trying to run the storage emulator wasn't running as an Administrator. Here's how to fix that problem:

Open an Administrator command prompt by opening the start menu, typing `cmd`, right-clicking on the Command Prompt app, and selecting *Run as administrator*.

![image](images/chapter3/admin-cmd.gif)

Change into the `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` folder:

```
cd "C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator"
```

Run the `AzureStorageEmulator.exe start` command to start the storage emulator:

```
AzureStorageEmulator.exe start
```

The storage emulator should start.

<img src="images/chapter3/emulator-start.png" class="img-medium" />

<div class="exercise-end"></div>

### Using Storage Explorer

After the the Azure Storage Emulator is running, let's turn our attention to [Storage Explorer](http://storageexplorer.com). Storage Explorer is a standalone app from Microsoft that allows you to easily work with Azure Storage Accounts on Windows, macOS and Linux.

We'll be using Storage Explorer throughout the workshop to peek into the local storage account (created by the Storage Emulator) and Azure-hosted storage account. 

Let's get started!

<h4 class="exercise-start">
    <b>Exercise</b>: Starting up the Azure Storage Emulator
</h4>

You'll find Storage Explorer in your start menu. Open the start menu, type in storage, and look for *Microsoft Azure Storage Explorer* under the *Apps* section:

![image](images/chapter3/storage-explorer-launch.gif)

When Storage Explorer launches for the first time, you'll be prompted to connect to Azure Storage:

![image](images/chapter3/azure-prompt.png)

Click *Sign In* and enter your Azure account credentials. 

![image](images/chapter3/sign-in.png)

After entering your credentials, you'll see a list of Azure subscriptions associated with your account. NOTE: You can have multiple subscriptions linked to your account (like mine), or just one.

Check the box next to the subscription you want to use. Press *Apply*.

![image](images/chapter3/azure-subs.png)

On the left-hand side, you'll see a list of azure subscriptions and local storage accounts. Locate the local storage account named *(Local and Attached)*. Drill down to *Storage Accounts* -> *(Development)* -> *Tables*.

Take note there are no tables in your local storage account.

![image](images/chapter3/local-storage.gif)

Congratulations! That's how easy it is to use Storage Explorer. We'll come back to Storage Explorer in a few minutes.

Head back to Visual Studio and run your web app. 

<img src="images/chapter3/launch-web-app.png" class="img-small" />

If you've made all the changes to your app successfully, the app should start up, and you'll be greeted with the default ASP.NET MVC template page again. 

But, something will be different this time. You'll recall that we've swapped out Entity Framework and SQL Server in lieu of Azure Table Storage. You'll also recall that we configured the app to create the necessary tables needed for ASP.NET Identity on app startup. 

Let's check back in Storage Emulator to see the tables created:

![image](images/chapter3/local-storage-with-tables.gif)

You should now see the *AspNetIndex*, *AspNetRoles*, and *AspNetUsers* tables. 

<div class="exercise-end"></div>

### Testing it out

Now that everything is running, let's test it out by registering a new user.

<h4 class="exercise-start">
    <b>Exercise</b>: Register a user
</h4>

Click the *Register* link in the upper-right corner of the app. Enter in an email and password. Press the *Register* button to create the user.

![image](images/chapter3/register.png)

After registering, you're automatically logged in as that user. Take a minute to explore your user profile by clicking on your name in the upper-right. 

![image](images/chapter3/view-profile.png)

We'll be coming back to this profile page throughout the workshop, modifying it to add a profile picture and a biography.

<img src="images/chapter3/manage-profile.png" class="img-medium" />

After registering the user, let's look back at Storage Explorer. Select the *AspNetUsers* table and view the contents of the table in the right-side panel. 

> **NOTE** You may need to press the *Refresh* button to load the data into the table.

<img src="images/chapter3/table-data.gif" class="img-large" />

<div class="exercise-end"></div>

That's it for chapter 3. In the next chapter, you'll learn how to connect the web app to an Azure-hosted Table Storage account.