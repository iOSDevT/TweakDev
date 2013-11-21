# Environment setup

This chapter will take you through the setup and installation of everything you need to get started with iOS tweak development. Note that this is not the only way (by far) to do it, you may need some extra/fewer steps to get it working on you personal setup.

## What you will need

Macbook  
Xcode  
Theos  
DPKG  
LDID (iOS 5)  
Jailbroken iOS device with these tweaks/tools  
1. OpenSSH  
2. APT Strict 0.7  
3. iOS Toolchain  
4. BigBoss recommended tools  

Patience  
Some objective-c knowledge  

> NOTE: May vary somewhat depending on your current setup.

### Xcode

You can install Xcode from the AppStore for free.

#### Command Line tools

After the installation of Xcode you should start it, go to the preferences, downloads, and find "_Command line tools_" and click install.

![asd](img/4.png "asd")

Without this the `make` command will not work (for example).

### DPKG

This tool requires you to first install [macports](http://www.macports.com). After installation you write this command in the terminal `sudo port install dpkg`. 

### LDID (iOS 5)

If you intend to develop for iOS 5 you can just download [this file](http://cl.ly/3w1N0T1l0B3D) and put it into your `/opt/theos/bin/` folder. 

[Mirror](https://www.dropbox.com/s/c2yhmzhi0n6s5pg/ldid)

### Theos

Download _theos_ from DHowetts [github repo](https://github.com/DHowett/theos) and unzip it to `/opt/theos/` (You may have to create this folder).

> NOTE: The location is not actually important but it makes it easier for me to write this.

To verify that it works open your `terminal` and write `/opt/theos/bin/nic.pl`, this should show you a menu like this.

![ex](img/0.png "ex")

You can exit out of this with `CTRL + Z` (OSX Terminal).

### Headers

You can get some headers from [github](http://www.github.com). For iOS 6 I've had great times with [these](https://github.com/nst/iOS-Runtime-Headers). I just keep these in some random directory and use them as a manual "dictionary" where I search trhough them.

You should put your headers in the directory `/opt/theos/include/`. I do not remember where I got mine (this was over a year ago) but through some trail and error I managed to get a compiling setup. I am using [these headers](https://dl.dropboxusercontent.com/u/6084360/include.zip).

### Verify that everything works

First, open the terminal and `cd` to your `~/Desktop/`. Write the command `/opt/theos/bin/nic.pl/` and choose the "tweak" option, finish the wizard and `cd` into the directory and write `make`. This will show you whether the compilation (etc) worked or not. Hopefully it worked fine.

Write `export THEOS_DEVICE_IP=XX` where `XX` is the actual IP of your iOS device.

Now run the command `make package install` and enter your devices password (default is `alpine`). Your device should respring and this should mean that the tweak has successfully been transferred to your device. You can verify by looking in Cydia's installed packages.  
