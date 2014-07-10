The complete guide to running Cordova/PhoneGap on Ubuntu

I've recently been exploring Cordova/PhoneGap for mobile app development, and found that the development experience to be considerably smoother in Linux than in Windows. However getting the environment set up wasn't easy. Luckily, I documented every pitfall I encountered, so whether you're starting with a brand new system, or one where you've already got some of the necessary components installed, this guide is for you.

## Installation

### Starting out

For this guide, we'll start out right from the beginning. I do all of my Linux work in 64-bit Ubuntu 14.04 LTS. I used to use a dual-boot setup, but recently changed to using a virtual machine and have been very happy since. I use VMWare Player (which is free) to host the virutal machine, and find it to perform better and to have a simpler setup than other free alternatives.

Let's get going.

### 1. Install Git

	`sudo apt-get install git`.

### 2. Install NodeJS

	`sudo apt-get install node`
	
That's not all, however. The node package for Ubuntu names the NodeJS executable as follows.


However, Cordova is going to look for Node here.

To fix this, we need to create a symlink which will allow Cordova to call NodeJS at the second path and get a hold of the executable. Do this with the following command.

### 3. Install PhoneGap

{% sourcecode bash %}
npm install -g phonegap

### 4. Install Ant

sudo apt-get install ant

### 5. Get the Android SDK

You can find it [here](http://developer.android.com/sdk/index.html). Extract the package to `/usr/local/android-sdk-linux`.

### 6. Get Java (JRE & JDK)

sudo apt-get install openjdk-7-jre
sudo apt-get install openjdk-7-jdk

### 7. Update your PATH

Open `.profile` for editing using the following command.

gedit ~/.profile

Append the following lines to the end of the file. *Do not put these inside any of the IF blocks.*

export ANDROID_HOME="/usr/local/android-sdk-linux/tools"
export ANDROID_PLATFORM_TOOLS="/usr/local/android-sdk-linux/platform-tools"
export PATH="$PATH:$ANDROID_HOME:$ANDROID_PLATFORM_TOOLS"

Save the file, log out, and log back in to apply the changes.

### 8. Install the necessary Android packages

Open up the package manager by executing `android` from the shell. This is a good first test to make sure you've done everything right up to here. If you cannot execute `android`, you've done something wrong, it is most likely your environment variables are incorrect. Make sure that the paths are valid, that the file was saved, and that if you run `echo $ANDROID_HOME`, `echo $ANDROID_PLATFORM_TOOLS`, and `echo $PATH`, that you get the values you expect. Also be sure to verify that the paths that you assigned to the environment variables are correct.

Executing `android` will open up a kind of package manager that you can use to install different Android components. By default, a certain number of these will be checked. Leave those checked, and in addition tick the API 4.4.2 (API 19) box. This will add another ten or so items to the list. Download and install the components.

### 9. Update C/C++

Install the following packages

sudo apt-get install lib32stdc++6
sudo apt-get install lib32z1

Apparently this is only needed on 64-bit systems. I wouldn't know because I haven't tried this on 32-bit Ubuntu.

## Executing a sample project 

If you've gotten this far, bravo. You now should have everything you need in place to emulate an Android device with Cordova/PhoneGap. If you want to test your installation, try creating and emulating a demo project. Here's how you can do that.

### 1. Create the project

Go to your development directory (mine is in `~/Dev`) and execute the following command

`cordova create`

This will create the equivalent of a "hello world" project in your directory.

### 2. Create an emulation device

Execute the following.

`android avd`

This will open the Android Virtual Device Manager, which allows you to manage virtual devices on which your apps will be emulated. By default, no virtual devices are created. To create one, click Add and fill in a name and the specifications for the device. Apparently you'll get better performance if you select an Intel x86 architecture rather than the AMD option.

The Android emulator takes ages to start up. If you want to save the state of the emulator

### 3. Run the test app.

Navigate to your test app directory and run the following.

cordova emulate android

If you've followed all the steps up until now correctly, with a little luck the emulator will launch and after 5 minutes or so of startup time you'll see the cordova sample app onscreen. Congratulations!