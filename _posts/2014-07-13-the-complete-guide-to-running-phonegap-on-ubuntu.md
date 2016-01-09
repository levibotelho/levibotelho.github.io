---
layout: post
title: The complete guide to running PhoneGap on Ubuntu
category: Development
tags: [android, phonegap, cordova, ubuntu]
description: A step by step guide that walks through the process of installing and configuring PhoneGap for development on a Ubuntu system.
comments: true
share: true
---

PhoneGap is a great technology that's a lot of fun to use, but getting started with it can be daunting. Here I outline the entire process, beginning to end, of getting a basic PhoneGap setup running on Ubuntu, complete with an Android emulator.

## Installation

### 1. Install Git

{% highlight bash %}
sudo apt-get install git
{% endhighlight %}

### 2. Install NodeJS

{% highlight bash %}
sudo apt-get install node
{% endhighlight %}

While that's all you need to do to install node, there is a small detail that you need to take care of. When ubuntu installs the package, it names the NodeJS executable `nodejs`. The problem is that many applications, including PhoneGap, expect the executable to be named `node`. To fix this inconsistency, simply create a symlink named `node` that points to `nodejs` as follows.

{% highlight bash %}
sudo ln -s /usr/bin/nodejs /usr/bin/node 
{% endhighlight %}

### 3. Install PhoneGap

{% highlight bash %}
npm install -g phonegap
{% endhighlight %}

### 4. Install Ant

{% highlight bash %}
sudo apt-get install ant
{% endhighlight %}

### 5. Get the Android SDK

You can find it [here](http://developer.android.com/sdk/index.html). Extract the package to `/usr/local/android-sdk-linux`.

### 6. Get Java (JRE & JDK)

{% highlight bash %}
sudo apt-get install openjdk-7-jre
sudo apt-get install openjdk-7-jdk
{% endhighlight %}

### 7. Update your PATH

Open `.profile` for editing using the following command.

{% highlight bash %}
gedit ~/.profile
{% endhighlight %}

Append the following lines to the end of the file. *Do not put these inside any of the IF blocks.*

{% highlight bash %}
export ANDROID_HOME="/usr/local/android-sdk-linux/tools"
export ANDROID_PLATFORM_TOOLS="/usr/local/android-sdk-linux/platform-tools"
export PATH="$PATH:$ANDROID_HOME:$ANDROID_PLATFORM_TOOLS"
{% endhighlight %}

Save the file, log out, and log back in to apply the changes.

### 8. Install the necessary Android packages

Execute `android` from the shell. This is a good first test to make sure you've done everything right up to here. If you get an error, rerun through the above steps and make sure that you've installed all the requirements and added your environment variables correctly.

If you haven't made any mistakes, you'll see that executing `android` opens up a kind of package manager that you can use to install different Android components. By default, a certain number of these will be pre-selected for installation. Leave them as-is, and in addition tick the box next to the entry named "API 4.4.2 (API 19)". Once you've done that, download and install everything.

### 9. Update C/C++

Install the following packages

{% highlight bash %}
sudo apt-get install lib32stdc++6
sudo apt-get install lib32z1
{% endhighlight %}

Apparently this is only needed on 64-bit systems, but I can't confirm that because I haven't tried this on 32-bit Ubuntu.

## Executing a sample project 

If you've gotten this far, good job! You now should have everything you need in place to emulate an Android device with Cordova/PhoneGap. If you want to test your installation, try creating and emulating a demo project. Here's how you can do that.

### 1. Create the project

Go to the directory where you do all your development work and execute the following command

{% highlight bash %}
cordova create
{% endhighlight %}

This will create the equivalent a "hello world" project in your dev directory.

### 2. Create an emulation device

Execute the following.

{% highlight bash %}
android avd
{% endhighlight %}

This will open the Android Virtual Device Manager, which allows you to manage the virtual devices on which your apps will be emulated. By default, no virtual devices are created. To create one, click "Add" and fill in a name and the specifications for the device.

A couple tips if this is your first time configuring a virtual device. If you're running on Intel, you'll get better performance if you select the "Intel Atom (x86)" architecture for your virtual device. Another thing that you'll want to consider is ticking the "Snapshot" checkbox down at the bottom of the dialog. The emulator takes ages to start up and runs extremely slowly--enabling snapshots will save the emulator's state when you're done with it so that you don't have to start it up from scratch every time you use it.

### 3. Run the test app.

Navigate to your test app directory and run the following.

{% highlight bash %}
cordova emulate android
{% endhighlight %}

If you've followed all the steps up until now correctly, with a little luck the emulator will launch and after 5 minutes or so of startup time you'll see the cordova sample app onscreen. Have fun developing!