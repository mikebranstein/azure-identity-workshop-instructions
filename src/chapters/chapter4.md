## Connecting the app to Azure

In this chapter, you'll be learning how to create an Azure storage account and connect the web app we created in chatpers 2 and 3 to the storage account.

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

That was easy! Dashboards are a quick way of organizing your Azure services. We like to create one for the bootcamp becaus eit helps keep everything organized. You'll have a single place to go to find everything you build today.

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
* **Account kind:** General purpose. You can create an account that stores only blobs (binary large objects), but for our bootcamp we want a storage account that can sotre both tables and blobs
* **Performance:** Standard, because our needs are simple. If you were an enterprise, you may want to choose Premium for the enhanced SLA and scalability. To learn more about the difference, check out this [article](https://docs.microsoft.com/en-us/azure/storage/storage-introduction#introducing-the-azure-storage-services)
* **Replication:** Locally-redundant storage (LRS). The data in your Microsoft Azure storage account is always replicated to ensure durability and high availability. Replication copies your data, either within the same data center, or to a second data center, depending on which replication option you choose. Replication protects your data and preserves your application up-time in the event of transient hardware failures. If your data is replicated to a second data center, that also protects your data against a catastrophic failure in the primary location. To learn more read this [article](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy).
* **Storage service encryption:** Disabled. Azure Storage Service Encryption (SSE) for Data at Rest helps you protect and safeguard your data to meet your organizational security and compliance commitments. With this feature, Azure Storage automatically encrypts your data prior to persisting to storage and decrypts prior to retrieval. The encryption, decryption, and key management are totally transparent to users. To learn more, read this [article](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption).
* **Subscription:** Select your subscription.
* **Resource group:** Use existing and select the resource group you just created. By selecting the existing resource group, the storage account will be added to the resource group. This makes it easier to manage.
* **Location:** East US
* **PIN to dashboard:** Yes

![image](images/chapter4/storage-account-options.png)

After you create the Storage account, you'll be brought back to your dashboard and presenting with a deployment placeholder:

<img src="images/chapter4/storage-account-placeholder.png" class="img-small" />

All Azure services deployed with the Resource Manager option are deployed in an asynchronous manner.

When the storage account is provisioned, it will open up in the portal. 

<img src="images/chapter4/storage-account-provisioned.png" class="img-large" />

From the storage account details pages, you can explore the contents of blobs, files, queues, tables, and queues. The experience is similar to that of storage explorer.

<img src="images/chapter4/exploring-storage-account.gif" class="img-large" />

<div class="exercise-end"></div>

### Opening the Storage Account in Storage Explorer

Now that we've added the stor

- add subscription azure storage explorer
- select the subscription
- find the sotrage account
- right-cick, copy Primary key
- Update connection string
- Account name: what you entered above
- Account key: what you copied from storage explorer

### Re-run and test!
- register a new account, test out the site

### now you have a cloud-based central authentication database
- highly-scalable
- can be encrypted
- can use it across applications

### web app
- create web app in resource group
- upload to azure from visual studio
- test it!

