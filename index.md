---
layout: default
---

<br/>
<iframe src="https://ghbtns.com/github-btn.html?user=jpoon&amp;repo=voting-app&amp;type=watch&amp;count=true&amp;size=large"
  allowtransparency="true" frameborder="0" scrolling="0" width="170" height="30"></iframe>
<br/>

The following guide shows a step-by-step process of creating a scalable web application and it's deployment to Microsoft Azure. It assumes you have no prior experience using Microsoft Azure.

<div style="background-color:rgba(247, 148, 30, .3);padding:25px">
<b>What will you learn:</b>
<ul>
  <li>How to build and connect end-to-end web applications and a datastore</li>
  <li>An introduction to Microsoft Azure Platform as a Service (PaaS) including Web Apps and Storage</li>
  <li>How to deploy a Python Flask and Node.JS Express application to Azure</li>
</ul>
</div>

## Voting App (aka. the thing we'll be building)

The voting app we'll create will allow users to vote between two options. It'll be comprised of two web applications (vote and result) and a worker:

![Voting App]({{ site.baseurl }}/assets/architecture.png)

1. **Vote Web Application**
  * Python + Flask application that allows users to vote between two options. The votes are put into an Azure Storage Queue to be processed. Here's what it looks like (feel free to vote and see the results change below):
    <center><iframe src="https://catsvsdogs.azurewebsites.net/" allowtransparency="true" frameborder="0" scrolling="0" width="600" height="400"></iframe></center>

2. **Worker Web Job**
  * As new messages appear in the Storage Queue, the C# worker will save each message and the total vote count to Azure Table Storage.

3. **Result Web Application**
  * Node.js + Socket.IO application retrieves the vote count from Azure Table Storage and outputs the overall results of the vote: 
    <center><iframe src="https://catsvsdogs-admin.azurewebsites.net/" allowtransparency="true" frameborder="0" scrolling="0" width="600" height="500"></iframe></center>

<br>
![]({{ site.baseurl }}/assets/divider.png)
<br>

## Prerequisities:

Before we start, you'll need a couple of things:

* A [GitHub account](htts://www.github.com)
* A [Microsoft account](https://www.microsoft.com/account)
* Sign up for [Azure](https://azure.microsoft.com/free/) with your Microsoft account. When you sign up, you'll automatically get a $200 credit. You'll also get a few more perks if you sign up via [DreamSpark](https://www.dreamspark.com/).

With this tutorial, you can get away without running any of the applications locally, but it is *highly* recommended that you download the tools such that you can debug them applications locally if necessary.

* Python 2.7+
* Node.JS, NPM
* [.NET Core](https://www.microsoft.com/net/core)
* IDE of choice: [Visual Studio Code](https://code.visualstudio.com/), [Visual Studio 2015](https://www.visualstudio.com/vs-2015-product-editions)

<br>
![]({{ site.baseurl }}/assets/divider.png)
<br>

## Azure Storage Account

A [Storage Account](https://azure.microsoft.com/documentation/articles/storage-introduction/) is one of the fundamental building blocks of Azure. In designing applicatiosn for scale, it is important to decouple applications such that they can scale independently. As such, we'll be using two aspects of Azure Storage:

* Queue Storage
* Table Storage

As users vote through the web application, each vote will be pushed to a queue whereupon it will later be processed by a worker and finally persisted into table storage. 


### Creating an Azure Storage Account
To create an Azure Storage account, head on over to the [Azure portal](https://portal.azure.com) and follow [these instructions](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).

> *Tip: A free tool that you can use to take a look at what's inside your Azure Storage account is [Azure Storage Explorer](http://storageexplorer.com/). It's free and works on Windows, MacOS, Linux.

Once created, take note of the Azure Storage Name and Azure Storage Key, we'll need these later.

## Voter Web Application

The voter web application issues a cookie to the end-user that serves as the `voter_id`. When a vote is registered, the `voter_id` and `vote` are pushed to the Azure Queue. 

1. Fork and clone [https://github.com/jpoon/voting-app-voter](https://github.com/jpoon/voting-app-voter)
2. `cd voting-app-voter`
3. Run the application locally (optional):
    1. Update the `storage_account` and `storage_account_key` with the Azure Storage Account that you had created. You can put the values [here](https://github.com/jpoon/voting-app-voter/blob/master/FlaskWebProject/views.py#L13).
    2. Setup [virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/)

       ~~~bash
       pip install virtualenv
       virtualenv env
       source env/bin/activate
       pip install -r requirements.txt
       ~~~

    3. Run the app
    
       ~~~
       python runserver.py
       ~~~

4. Create an Azure Web App
    1. Log into the Azure Portal and click the *NEW* button in the top-left corner
    2. Click *Web + Mobile*
    3. Select *Web App*
5. [Configure the App Settings](https://azure.microsoft.com/documentation/articles/web-sites-configure/) adding `AZURE_STORAGE_ACCOUNT` and `AZURE_STORAGE_ACCESS_KEY` with the Azure Storage Account that you had created earlier
6. Setup [automated code deployment](https://github.com/blog/2056-automating-code-deployment-with-github-and-azure) of the voting-app-voter repository in GitHub to the Web App. 
7. Done. 

<br>
![]({{ site.baseurl }}/assets/divider.png)
<br>

## Worker Web Job

One feature of an Azure Web App is that you can have it run jobs at no extra cost using [Web Jobs](https://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/). We'll upload a C# webjob that will run continuously to process the Azure Storage Queue.

Connection strings

<br>
![]({{ site.baseurl }}/assets/divider.png)
<br>

## Result Web Application

The result web application retrieves the vote count from the Azure Table and displays it on the page. The instructions for deploying the result web application are very similar to the voter web application.

1. Fork and clone [https://github.com/jpoon/voting-app-admin](https://github.com/jpoon/voting-app-admin)
2. `cd voting-app-admin`
3. Run the applicatino locally (optional):
    1. `npm install`
    2. `node server.js`
4. Create an Azure Web App
5. [Configure the App Settings](https://azure.microsoft.com/documentation/articles/web-sites-configure/) adding `AZURE_STORAGE_ACCOUNT` and `AZURE_STORAGE_ACCESS_KEY` with the Azure Storage Account that you had created earlier
6. Setup [automated code deployment](https://github.com/blog/2056-automating-code-deployment-with-github-and-azure) of the voting-app-voter repository in GitHub to the Web App. 
7. Done. 

<a href="https://github.com/jpoon/voting-app" class="github-corner"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#151513; color:#fff; position: absolute; top: 0; border: 0; right: 0;"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a><style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>
