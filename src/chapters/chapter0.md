## Introduction

Welcome to the Azure Identity Workshop! 

### About the Azure Identity Workshop

The Azure Identity Workshop is a free, developer-focused, one-day training event on Azure. 

Content is presented and facilitated by:

* [Mike Branstein](https://twitter.com/mikebranstein)
    * [KiZAN Technologies](http://kizan.com)
    * [Brosteins](https://brosteins.com)

### Getting Started

To get started you'll need the following pre-requisites. Please take a few moments to ensure everything is installed and configured.

* Microsoft Windows PC
* [Visual Studio](https://www.visualstudio.com) 2015 or later (2017 preferred)
* [Azure Subscription](https://azure.microsoft.com) (Trial is ok, or an Azure account linked to a Visual Studio subscription or MSDN account. See later sections of this chapter to create a free trial account or activate your Visual Studio subscription)
* [Azure SDK for .NET](https://azure.microsoft.com/en-us/downloads/) installed (be sure to get the right one for your version of Visual Studio)
* [Storage Explorer](http://storageexplorer.com/) installed
* [Storage Emulator](https://go.microsoft.com/fwlink/?LinkId=717179&clcid=0x409) installed
* The [starter project](https://github.com/mikebranstein/azure-identity-workshop/tree/start) on Github

### What You're Building

Azure is big. Really big. Too big to talk about all things Azure in a single day. 

We've assembled an exciting workshop to introduce you to several Azure services that cloud developers should know about:
* [Web app](https://azure.microsoft.com/en-us/services/app-service/web/)
* [BLOB storage](https://azure.microsoft.com/en-us/services/storage/blobs/)
* [Table storage](https://azure.microsoft.com/en-us/services/storage/tables/)
* [Functions](https://azure.microsoft.com/en-us/services/functions/)
* [Cognitive Services](https://www.microsoft.com/cognitive-services) API for [computer vision](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api)
* [SignalR](https://www.asp.net/signalr) (yeah it's not a Azure service, but it is pretty cool)

In this workshop you'll learn how to use these Azure services to build a cloud-hosted single sign-on app that can manage your user profile. When you're finished, you will have built an app that allows you to upload profile pictures that pass through an AI content filter to ensure they're work appropriate. 

#### Key concepts and takeaways

* Navigating the Azure portal
* Using Azure Resource Groups to manage multiple Azure services
* Deploying a web app to Azure web app service
* Using Windows Identity as a login provider
* Creating Azure storage accounts
* Azure Table storage 
* Storing images in Azure BLOB storage
* Using Azure functions to coordinate asynchronous processes
* Consuming the Microsoft Cognitive Services API to analyze images
* Using SignalR to asynchronously update web UIs

### Materials

You can find additional lab materials and presentation content at the locations below:

* Presentation: [https://github.com/mikebranstein/azure-identity-workshop/blob/master/AzureIdentityWorkshop.pptx](https://github.com/mikebranstein/azure-identity-workshop/blob/master/AzureIdentityWorkshop.pptx)
* Source code for the code used in this guide: [https://github.com/mikebranstein/azure-identity-workshop](https://github.com/mikebranstein/azure-identity-workshop)
* This guide: [https://github.com/mikebranstein/azure-identity-workshop-instructions](https://mikebranstein.github.io/azure-identity-workshop-instructions/)

### Creating a Trial Azure Subscription

> **NOTE:** If you have an Azure account already, you can skip this section. If you have a Visual Studio subscription (formerly known as an MSDN account), you get free Azure dollars every month. Check out the next section for activating these benefits.

There are several ways to get an Azure subscription, such as the free trial subscription, the pay-as-you-go subscription, which has no minimums or commitments and you can cancel any time; Enterprise agreement subscriptions, or you can buy one from a Microsoft retailer. In exercise, you'll create a free trial subscription.

<h4 class="exercise-start">
    <b>Exercise</b>: Create a Free Trial Subscription
</h4>

Browse to the following page http://azure.microsoft.com/en-us/pricing/free-trial/ to obtain a free trial account.

Click *Start free*.

Enter the credentials for the Microsoft account that you want to use. You will be redirected to the Sign up page.

> **NOTE:** Some of the following sections could be omitted in the Sign up process, if you recently verified your Microsoft account.

Enter your personal information in the About you section. If you have previously loaded this info in your Microsoft Account, it will be automatically populated.

<img src="images/chapter0/sign-up.png" class="img-medium" />

In the *Verify by phone* section, enter your mobile phone number, and click *Send text message*.

<img src="images/chapter0/send-text-message.png" class="img-medium" />

When you receive the verification code, enter it in the corresponding box, and click *Verify code*.

<img src="images/chapter0/verify-code.png" class="img-medium" />

After a few seconds, the *Verification by card* section will refresh. Fill in the Payment information form. 

> **NOTE:** Your credit card will not be billed, unless you remove the spending limits. If you run out of credit, your services will be shut down unless you choose to be billed.

<img src="images/chapter0/verify-by-card.png" class="img-medium" />

In the *Agreement* section, check the *I agree to the subscription Agreement*, *offer details*, and *privacy statement* option, and click *Sign up*.

Your free subscription will be set up, and after a while, you can start using it. Notice that you will be informed when the subscription expires.

<img src="images/chapter0/agreement.png" class="img-medium" />

Your free trial will expire in 29 days from it's creation.

<img src="images/chapter0/expiration.png" class="img-medium" />

<div class="exercise-end"></div>

### Activating Visual Studio Subscription Benefits

If you happen to be a Visual Studio subscriber (formerly known as MSDN) you can activate your Azure Visual Studio subscription benefits. It is no charge, you can use your MSDN software in the cloud, and most importantly you get up to $150 in Azure credits every month. You can also get 33% discount in Virtual Machines and much more.

<h4 class="exercise-start">
    <b>Exercise</b>: Activate Visual Studio Subscription Benefits
</h4>

To active the Visual Studio subscription benefits, browse to the following URL: [http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/](http://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/)

Scroll down to see the full list of benefits you will get for being a MSDN member. There is even a FAQ section you can read.

Click *Activate* to activate the benefits.

<img src="images/chapter0/activate.png" class="img-medium" />

You will need to enter your Microsoft account credentials to verify the subscription and complete the activation steps.

<div class="exercise-end"></div>
