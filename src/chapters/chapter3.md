## Using Azure Storage Emulator to Develop Locally

Even though this bootcamp is all about the cloud and using Azure, it doesn't mean that we need to be connected to the cloud to develop and test our work.

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

You may have problems starting the storage emulator, and will receive a non-descript error like so:

![image](images/chapter3/emulator-error.png)

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

![image](images/chapter3/emulator-start.png)

<div class="exercise-end"></div>




- start storage explorer
- run the project
- when the site loads, you should see in your (Local and Attached) the tables AspNetIndex, AspNetRoles, AspNetUsers

### Test it out
- register a new user
- Look in the AspNetUsers table to the added user
