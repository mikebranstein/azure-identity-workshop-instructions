## Connecting the app to Azure

In this chapter, you'll be learning how to create an Azure storage account and connect the web app we created in chapters 2 and 3 to the storage account.

### Creating a Resource Group

Our first stop will be to create a Resource Group in Azure. 

> **DEFINITION** Formally, resource groups provide a way to monitor, control access, provision and manage billing for collections of assets that are required to run an application, or used by a client or company department. Informally, think ofresource groups like a file system folder, but instead of holding files and other folders, resource groups hold azure objects like storage accounts, web apps, functions, etc.

<h4 class="exercise-start">
    <b>Exercise</b>: Create a Dashboard and Resource Group
</h4>

#### Creating a Dashboard

We'll start by creating a dashboard. 

Login to the Azure portal, click *+ New Dashboard*, give the dashboard name, and click *Done customizing*.

<img src="images/chapter4/create-dashboard.gif" class="img-medium" />

That was easy! Dashboards are a quick way of organizing your Azure services. We like to create one for the bootcamp because it helps keep everything organized. You'll have a single place to go to find everything you build today.

#### Creating a Resource Group

Next, we'll create a resource group to hold the various services we'll be creating today. 

Click the *+ New* button on the left.

![image](images/chapter4/new.png)

Search for resource group by using the search box, selecting *Resource Group* when it appears.

![image](images/chapter4/new-resource.png)

Select *Resource Group* from the search results window:

![image](images/chapter4/resource-group-results.png)

Click *Create* at the bottom:

![image](images/chapter4/create-resource-group.png)

Give the Resource group a name, select your Azure subscription, and a location. Also CHECK the *Pin to Dashboard* option. Press *Create* when you're finished.

![image](images/chapter4/create-resource-group-2.png)

After it's created, it will be open in Azure automatically:

![image](images/chapter4/resource-group-created.png)

Close the resource group by clicking the *X* in the upper-right corner. Note that the resource group has been added to your dashboard.

![image](images/chapter4/resource-group-dashboard.png)

Form the dashboard, you can click on the resource group to re-open it at any time.

<div class="exercise-end"></div>

That wraps up the basics of creating resource groups. We're not going to take a deep dive into Azure Resource Group. If you're interested in learning more, check out this [article](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

### Creating a Storage Account

Our next stop is at Storage Accounts. You'll recall from the last chapter that storage accounts are the topmost Azure service that contains other resources such as the blob storage, file storage, queue storage, and table storage services.

Before we can store data to an Azure table, we'll need to create a storage account.

<h4 class="exercise-start">
    <b>Exercise</b>: Create a Storage Account
</h4>

Return to your dashboard if you're not already there. Click *+ New*, select *Storage* from the list of service categories, and select *Storage account - blob, file, table, queue*.

<img src="images/chapter4/create-storage-account.gif" class="img-medium" />

Complete the following fields:
* **Name:** this must be a unique, URL-friendly name that will be prepended to the URL `{storage-account-name}.core.windows.net`. You'll use this full URL name to reference the storage account in code
* **Deployment model:** Resource manager. The *Classic* option is a legacy option that will be deprecated soon. Don't pick it. Chosing the *Resource manager* options also allows you to create automation scripts for provisioning Azure resources easily
* **Account kind:** General purpose. You can create an account that stores only blobs (binary large objects), but for our bootcamp we want a storage account that can store both tables and blobs
* **Performance:** Standard, because our needs are simple. If you were an enterprise, you may want to choose Premium for the enhanced SLA and scalability. To learn more about the difference, check out this [article](https://docs.microsoft.com/en-us/azure/storage/storage-introduction#introducing-the-azure-storage-services)
* **Replication:** Locally-redundant storage (LRS). The data in your Microsoft Azure storage account is always replicated to ensure durability and high availability. Replication copies your data, either within the same data center, or to a second data center, depending on which replication option you choose. Replication protects your data and preserves your application up-time in the event of transient hardware failures. If your data is replicated to a second data center, that also protects your data against a catastrophic failure in the primary location. To learn more read this [article](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy).
* **Storage service encryption:** Disabled. Azure Storage Service Encryption (SSE) for Data at Rest helps you protect and safeguard your data to meet your organizational security and compliance commitments. With this feature, Azure Storage automatically encrypts your data prior to persisting to storage and decrypts prior to retrieval. The encryption, decryption, and key management are totally transparent to users. To learn more, read this [article](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
* **Subscription:** Select your subscription.
* **Resource group:** Use existing and select the resource group you just created. By selecting the existing resource group, the storage account will be added to the resource group. This makes it easier to manage.
* **Location:** East US
* **PIN to dashboard:** Yes

![image](images/chapter4/storage-account-options.png)

After you create the Storage account, you'll be brought back to your dashboard and presented with a deployment placeholder:

<img src="images/chapter4/storage-account-placeholder.png" class="img-small" />

All Azure services deployed with the Resource Manager option are deployed in an asynchronous manner.

When the storage account is provisioned, it will open up in the portal. 

<img src="images/chapter4/storage-account-provisioned.png" class="img-large" />

From the storage account details pages, you can explore the contents of blobs, files, tables, and queues. The experience is similar to that of storage explorer.

<img src="images/chapter4/exploring-storage-account.gif" class="img-large" />

<div class="exercise-end"></div>

### Accessing a Storage Account in Storage Explorer

Now that you've added a storage account to your Azure Subscription, let's check it out in Storage Explorer.

<h4 class="exercise-start">
    <b>Exercise</b>: Viewing an Azure-hosted Storage Account in Storage Explorer 
</h4>

In a previous step, we added our Azure subscription to Storage Explorer. Before continuing, ensure your subscription has been added. 

Return to Storage Explorer and press the *Refresh All* link. You should see the storage account appear underneath your Azure subscription. You'll recall my storage account was named *globalazurelouisville*:

![image](images/chapter4/refresh.png)

Expand the storage account, then the *Tables* item to verify no tables have been created.

![image](images/chapter4/no-tables.png)

<div class="exercise-end"></div>

Great work! Now that we've provisioned a storage account and we can access it from Storage Explorer, it's time to start using it. In this next exercise, you'll obtain connection information from the storage account and add it to your web app.

<h4 class="exercise-start">
    <b>Exercise</b>: Getting a Storage Account Connection String 
</h4>

In an earlier chapter, you replaced Entity Framework with the El Camino package, so our web app could use Azure Table Storage to persist user information via ASP.NET Identity. As part of that process, several lines we added to the `web.config` file:

```xml
<configSections>
    <section name="elcaminoIdentityConfiguration" type="ElCamino.AspNet.Identity.AzureTable.Configuration.IdentityConfigurationSection,ElCamino.AspNet.Identity.AzureTable " />
</configSections>
<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="UseDevelopmentStorage=true" />
<!--<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="DefaultEndpointsProtocol=https;AccountName=STORAGE_ACCOUNT_NAME;AccountKey=STORAGE_ACCOUNT_KEY;" />-->
```

Take a closer look at the `<elcaminoIdentityConfiguration>` element:

```xml
<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="UseDevelopmentStorage=true" />
``` 

This element contains a storage account connection string. Similar to SQL Server databases, storage accounts have a connection string. And, similar to local SQL server installations (like localdb), locally-hosted storage emulators have a special connection string.

When you're devleoping locally, the connection string is `UserDevelopmentStorage=true`.

But, when we move to the cloud, the connection string has an enhanced structure containing 3 components: 
* **Default Endpoints Protocol:** the commnucation protocol used to talk to the remote table storage. By default it's https, and there's truthfully no reason for you to change it.
* **Account Name:** the name you gave the account.
* **Account Key:** a secret key that you shouldn't share with others. Treat it like a super user password.

You can see this structure in the commented-out section of the `web.config` from above:

```xml
<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="DefaultEndpointsProtocol=https;AccountName=STORAGE_ACCOUNT_NAME;AccountKey=STORAGE_ACCOUNT_KEY;" />
```

Let's update our web app to use the Azure-hosted storage account we just created. Comment-out the development configuration element and uncomment the element with the Azure-hosted connection string.

```xml
<configSections>
    <section name="elcaminoIdentityConfiguration" type="ElCamino.AspNet.Identity.AzureTable.Configuration.IdentityConfigurationSection,ElCamino.AspNet.Identity.AzureTable " />
</configSections>
<!--<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="UseDevelopmentStorage=true" />-->
<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="DefaultEndpointsProtocol=https;AccountName=STORAGE_ACCOUNT_NAME;AccountKey=STORAGE_ACCOUNT_KEY;" />
```

Replace `STORAGE_ACCOUNT_NAME` with the name of the storage account you created in the previous steps. Ours looks like:

```xml
<elcaminoIdentityConfiguration tablePrefix="" storageConnectionString="DefaultEndpointsProtocol=https;AccountName=globalazurelouisville;AccountKey=STORAGE_ACCOUNT_KEY;" />
```

The last step is to get our storage account key, replacing the `STORAGE_ACCOUNT_KEY` placeholder int he `web.config`. Luckily, Storage Explorer makes this simple.

Go back to Storage Explorer, right-click the storage account name, selecting *Copy Primary Key*.

![image](images/chapter4/copy-primary-key.gif)

This copies the primary storage account key to your clipboard. Paste the key into your `web.config`, replacing `STORAGE_ACCOUNT_KEY` with the value from your clipboard. 

<div class="exercise-end"></div>

### Testing with Azure Table Storage

Now that you've updated the `web.config` to point to an Azure storage account, let's re-launch the web app and test it out!

<h4 class="exercise-start">
    <b>Exercise</b>: Testing the updated web app
</h4>

When the web app launches, register a user. 

> **NOTE** You may recall that you previosuly registerd a user with the app, but we've just told the app to use a different storage account for ASP.NET Identity. This means you'll have to re-register the user.

Click the *Register* link in the upper-right corner of the app. Enter in an email and password. Press the *Register* button to create the user.

![image](images/chapter3/register.png)

As a final check, refresh the Azure storage account in Storage Explorer, verifying that the *AspNetIndex*, *AspNetRoles*, and *AspNetUsers* tables were created and your registered user appears in the *AspNetUsers* table.

<img src="images/chapter4/verify.gif" class="img-large" />

<div class="exercise-end"></div>

Congratulations!

You just built a highly-scalable, secure, centralized, single-sign on system. That's right. The storage account you just created and attached to ASP.NET Identity can be shared across applications: just reuse the connection string in your next app. And the best part - it took ~45 minutes. Bam!

Pretty impressive.

### Understanding App Service and Web Apps

In the last part of this chapter, you'll learn how to create an Azure Web App and deploy our solution to the cloud. In short, I like to think of Azure Web Apps like IIS in the cloud, but without the pomp and circumstance of setting up and configuring IIS.

Web Apps are also part of a larger Azure service called the App Service, which is focused on helping you to build highly-scalable cloud apps focused on the web (via Web Apps), mobile (via Mobile Apps), APIs (via API Apps), and automated business processes (via Logic Apps). 

We don't have time to fully explore all of the components of the Azure App Service, so if you're interested, you can read more [online](https://azure.microsoft.com/en-us/services/app-service/).

#### What is an Azure Web App?

As we've mentioned, Web Apps are like IIS in the cloud, but calling it that seems a bit unfair because there's quite a bit more to  Web Apps:

* **Websites and Web Apps:** Web Apps let developers rapidly build, deploy, and manage powerful websites and web apps. Build standards-based web apps and APIs using .NET, Node.js, PHP, Python, and Java. Deliver both web and mobile apps for employees or customers using a single back end. Securely deliver APIs that enable additional apps and devices.

* **Familiar and fast:** Use your existing skills to code in your favorite language and IDE to build APIs and apps faster than ever. Access a rich gallery of pre-built APIs that make connecting to cloud services like Office 365 and Salesforce.com easy. Use templates to automate common workflows and accelerate your development. Experience unparalleled developer productivity with continuous integration using Visual Studio Team Services, GitHub, and live-site debugging.

* **Enterprise grade:** App Service is designed for building and hosting secure mission-critical applications. Build Azure Active Directory-integrated business apps that connect securely to on-premises resources, and then host them on a secure cloud platform that's compliant with ISO information security standard, SOC2 accounting standards, and PCI security standards. Automatically back up and restore your apps, all while enjoying enterprise-level SLAs.

* **Build on Linux or bring your own Linux container image:** Azure App Service provides default containers for versions of Node.js and PHP that make it easy to quickly get up and running on the service. With our new container support, developers can create a customized container based on the defaults. For example, developers could create a container with specific builds of Node.js and PHP that differ from the default versions provided by the service. This enables developers to use new or experimental framework versions that are not available in the default containers.

* **Global scale:** App Service provides availability and automatic scale on a global datacenter infrastructure. Easily scale applications up or down on demand, and get high availability within and across different geographical regions. Replicating data and hosting services in multiple locations is quick and easy, making expansion into new regions and geographies as simple as a mouse click.

* **Optimized for DevOps:** Focus on rapidly improving your apps without ever worrying about infrastructure. Deploy app updates with built-in staging, roll-back, testing-in-production, and performance testing capabilities. Achieve high availability with geo-distributed deployments. Monitor all aspects of your apps in real-time and historically with detailed operational logs. Never worry about maintaining or patching your infrastructure again.

### Deploying to a Web App from Visual Studio

Now that you understand the basics of web apps, let's create one and deploy our app to the cloud! 

Earlier in this chapter, we created a storage account in Azure via the Azure portal. You can also create Web Apps via the Azure portal in the same manner. But, we're going to show you another way of creating a Web App: from Visual Studio.

<h4 class="exercise-start">
    <b>Exercise</b>: Deploying to a Web App from Visual Studio
</h4>

From Visual Studio, right-click the *Web* project and select *Publish*. In the web publish window, select *Microsoft Azure App Service*, *Create New*, and press *Publish*. This short clip walks you through the process:

<img src="images/chapter4/publish-web-app.gif" class="img-large" />

On the next page, give your Web App a name, select your Azure subscription, and select the Resource Group you created earlier (mine was named Global-Azure-Louisville-2017).

<img src="images/chapter4/web-app-settings.png" class="img-medium" />

Click *New...* to create a new Web App plan.

> **NOTE** Web App plans describe the performance needs of a web app. Plans range from free (where multiple web apps run on shared hardware) to not-so-free, where you have dedicated hardware, lots of processing power, RAM, and SSDs. To learn more about the various plans, check out this [article](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/).

Create a new free plan.

<img src="images/chapter4/new-plan.png" class="img-medium" />

After the plan is created, click *Create* to create the Web App in Azure.

<img src="images/chapter4/deploying.gif" class="img-large" />

When the Azure Web App is created in Azure, Visual Studio will publish the app to the Web App. after the publish has finished, you shoudl see something similar:

<img src="images/chapter4/published.png" class="img-large" />

Finally, Visual Studio will launch the site in your browser, showing you your deployed site.

<div class="exercise-end"></div>

Well done. You've reached the end of chapter 4. If you've been following along, you have learned:
* How to create dashboards and resource groups in the Azure portal
* How to create a storage account for blobs, files, tables, and queues
* How to view the contents of a storage account with Storage Explorer
* What a storage account connection string looks like
* How to create a Web App from Visual Studio and deploy to it