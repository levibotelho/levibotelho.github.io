---
layout: post
title: Dead simple polyfilling 2
category: Development
tags: [javascript]
description: Polyfill missing browser features with an 8-line JavaScript function.
comments: true
share: true
---

Azure Websites are designed to provide a turnkey solution for getting a web application up and running on the net. Build it, and then either push it to a connected service such as GitHub or Bitbucket, or to a specialised deployment Git repository and you’re set.

However this simplicity comes at a cost of reduced flexibility. If you’re building a continuous integration server you probably won’t want to have to maintain a separate Git repository just to deploy your app, and deploying straight from GitHub or another similar service isn’t really an option if you want to have a controlled build chain that you’re managing yourself.

However it is entirely possible to integrate Azure Websites with a CI system by using MSBuild and MSDeploy. Here’s how:

# 1. Get your files

This requires a simple call to your source control repository. For Git, we do the following:

{% highlight batch %}
cd /d %USERPROFILE%\CI\[ProjectName]
git fetch --all
git checkout --force origin/master
{% endhighlight %}

I put all of my CI-related files into a folder called `CI` in the server user’s folder. You can of course put them wherever you want, so long as the user has the appropriate rights to perform all of the necessary actions in the location where the files are stored.

Quick note here before we continue. Notice that we’re checking out `origin/master` and not pulling. This updates our code to the latest commit on the master branch of the remote server. Pulling takes the changes on the remote server and merges them into our local version of the branch. Normally on a CI server these two actions would give us the same result, but there is no guarantee of such. Checking out origin/master is semantically exactly what we want to do.

# 2. Build your project

This step is a bit tricky because MSBuild has so many different options and therefore there are many ways that you can get this step wrong. What we actually need here is not to build the project, but to publish it. Building the project won’t transform web.config files, for example. Publishing it to the filesystem will give us exactly what we need to send to Azure.

To do this, run MSBuild with the following options.

{% highlight batch %}
MSBuild "%UserProfile%\CI\ceelk\Ceelk.Api\Ceelk.Api.csproj" /p:Configuration=Release /p:Platform=AnyCPU /t:WebPublish /p:WebPublishMethod=FileSystem /p:DeleteExistingFiles=True /p:publishUrl=%UserProfile%\CI\[ProjectBuildFolder]
{% endhighlight %}

This will publish the project to the build folder of your choosing. At this point you could run IIS on this directory and your app would be up and running.

# 3. Deploy your project

As mentioned at the top of the article, MSDeploy is definitely the easiest way to deploy an application to an Azure Website from a continuous integration server. Unfortunately however it’s not well documented, as Microsoft appears to want to show off their more advanced deployment options like Git deployment or integration with third party project hosting platforms.

Like MSBuild, MSDeploy is tricky to use because of all the options and configuration that it takes. And because its use with Azure Websites is barely documented it can be very difficult to get this working without an example. Here is the MSDeploy command that you need to run.

{% highlight batch %}
MSDeploy -verb:sync -source:contentPath="%UserProfile%\CI\[ProjectBuildFolder]" -dest:contentPath='[AzureWebsiteName]',ComputerName="[PublishUrl]?site=[AzureWebsiteName]",UserName='$[AzureWebsiteName]',Password='[DeployPassword]',AuthType='Basic'
{% endhighlight %}

You’ll notice that there are four variables that need to be filled in here to make the command work. [ProjectBuildFolder] is the folder to which the project was built in the previous step. [AzureWebsiteName] is just the name of your Azure Website as created in the Azure Management Portal.

To retrieve the values for `[PublishUrl]` and `[DeployPassword]` you’ll need to sign into the Azure Management Portal, open up your site, and click on “Get Publish Profile”. This will download a file that you can then open up in notepad.

In the first `<publishProfile>` element there is an attribute called `publishUrl` that contains a url pointing to port 443. This is the URL you need. If the URL doesn’t start with https://, be sure to add it. This same element also contains an attribute called `userPWD`, which is the `[Password]` that you need to inject into the above command.

Once you’ve reconstituted the MSDeploy call, run it and your site will be pushed into the cloud. Combine these three commands and you’ve got yourself a working build chain that you can hook in to the CI runner of your choice.
