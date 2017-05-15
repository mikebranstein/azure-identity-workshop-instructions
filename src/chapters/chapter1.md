## Getting project from Github

All the code you'll need for working through the workshop are stored on Github at [https://github.com/mikebranstein/azure-identity-workshop](https://github.com/mikebranstein/azure-identity-workshop).

### Organization of the Repository

The repository at [https://github.com/mikebranstein/azure-identity-workshop](https://github.com/mikebranstein/azure-identity-workshop) is organized into several [branches](https://github.com/mikebranstein/azure-identity-workshop/branches/all):

* start
* chapter2
* chapter5
* chapter6
* chapter7
* chapter9

Each branch corresponds with the chapters in this workshop guide. The guide starts with the code from the `start` branch, and progresses with each chapter. 

> **NOTE** You don't need to copy the code from each branch, only the `start` branch. If you're following along with the guide, you can start with the `start` branch. If you get stuck, or if you can't follow-along, you can grab a fresh set of code from a branch name matching the chapter you're on. For example, if you get stuck and can't get your code working at the end of chapter 5, you can jump to chapter 6 and grab the `chapter6` branch.

### Pre-requisites

Before we go any further, be sure you have all the pre-requisites downloaded and installed. You'll need the following:

* Microsoft Windows PC
* [Visual Studio](https://www.visualstudio.com) 2015 or later (2017 preferred)
* [Azure Subscription](https://azure.microsoft.com) (trial is ok, and we'll sign you up in our first session)
* [Azure SDK for .NET](https://azure.microsoft.com/en-us/downloads/) installed (be sure to get the right one for your version of Visual Studio)
* [Storage Explorer](http://storageexplorer.com/) installed
* [Storage Emulator](https://go.microsoft.com/fwlink/?LinkId=717179&clcid=0x409) installed
* The [starter project](https://github.com/mikebranstein/azure-identity-workshop/tree/start) on Github

### Clone project from start branch

Let's get started by getting the `start` branch.

<h4 class="exercise-start">
    <b>Exercise</b>: Getting the code
</h4>

Clone or download the `start` branch from [https://github.com/mikebranstein/azure-identity-workshop/tree/start](https://github.com/mikebranstein/azure-identity-workshop/tree/start)).

Use this [link](https://github.com/mikebranstein/azure-identity-workshop/archive/start.zip) to download a zip file of the `start` branch.

![image](images/chapter1/downloaded-zip.png)

> Don't open the zip file yet. You need to unblock it first!

Right-click the zip file and go to the properties option. Check the *Unblock* option, press *Apply*, press *Ok*.

![image](images/chapter1/unblock.gif)

Now it's safe to unzip the file. 

<div class="exercise-end"></div>

### Verify the site works

<h4 class="exercise-start">
    <b>Exercise</b>: Compiling the solution
</h4>

Open the solution in Visual Studio by double-clicking the `Web.sln` file in to root of the extracted files:

![image](images/chapter1/solution-file.png)

The opened solution should look like this:

![image](images/chapter1/opened-solution.png)

Build and debug the solution. You should see the MVC 5 template spin up in your browser.

![image](images/chapter1/site.png)

<div class="exercise-end"></div>

That's it! You're up and running and ready to move on!