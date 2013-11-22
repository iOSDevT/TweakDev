# Your first tweak - SliderChanger

.. will be a tweak that changes the text of the lockscreen slider. Yes this has been done before but it is rather easy and a good get-started project.

Let's get started.

## The project

Start by going to your terminal and create a new theos project with the `/opt/theos/bin/nic.pl` command. Choose the tweak option and follow the wizard. 

![asd](img/3.png "asd")

> TIP: To avoid having to rewrite the "com.yourcompany" part each time you make a new tweak you can open the actual "nic.pl" file, search for "com.yourcompany" and replace it with your preffered name. In my case I changed it to "com.jontelang".

Name the tweak "SliderChanger". In my case it became "com.jontelang.sliderchanger".

After you have finished you should end up with a directoy like this.

![asd](img/2.png "asd")

Run `make package install` and make sure the tweak is listed under S in the cydia installed packages section.

I like to run `make` often just to make sure everything is ok at all times.

## The research

I'd love for you to just be able to get into the coding and do the tweak instantly. But do you even know where to begin? Probably not. The next step is to dig into your headers, get used to it because you will spend an increbible amount of time reading them. It gets easier after a while.

With this project though, I will give you some hints. We are looking for the lockscreen-slider-bar-text right. So firstly we look for the lockscreen. I happen to know that the lockscreen is called `SBAwayView`. So open this up and look/search for something that could be a slider. There seems to be an ivar called `SBAwayLockBar *_lockBar` that could be interesting. So we open the file `SBAwayLockBar` and look through it. There is a method `- (void)setLabel:(id)arg1` that looks interesting. At this point we are fairly sure that this is the actual code we are looking for. 

In fact, it IS the code we are looking for so I will just continue with the tutorial instead and you will understand how to "console-log-research" other classes in the future. This next line may not make 100% practical sense now, but it will later. To "console-log-research" you will later just `%hook` a bunch of correctly-sounding classes, hijack their methods and log them to find the things you want.

### Console output

Console output is what you will be looking at when developing tweaks. The might be a way to use a debugger but that's nothing I've used so I will stick to `NSLog`.

Plug your device into your computer with USB. Open `Xcode`, click `Windows` in the menu and choose the `Organizer`. Your device should show up on the left side and you should also see an option named "console". Click this and you should see a giant wall of text. This is your devices "syslog" and this is where lots of things get logged, including anything that is "`NSLog`ged".

![asd](img/9.png "asd")

Keep this in mind, as this is where you will be looking at some point or another.

## The code

Time to write some actual code.

Open `Tweak.xm` and remove all pre-written text. We will be working the class from the "The research" chapter called `SBAwayLockBar` and its method `setLabel`. 

In your `Tweak.xm` write the following.

	%hook SBAwayLockBar
	-(void)setLabel:(id)arg1
	{
		arg1 = @"SliderChanger";
		%orig(arg1);
	}
	%end 

This is pretty straight forward. You are "hooking" into the class `SBAwayLockBar` and hijacking the method `setLabel`. The argument `arg1` may say `id` but in reality it is an `NSString`. So we reset the argument to `@"SliderChanger"` and then we call the original method with the updated argument. We do this by calling `%orig(arg1)`.

We then close the `%hook` with `%end`.

Now go to the terminal again and write `make package install` to install the new tweak to your device. By now it should have changed the "slide to unlock" to "SliderChanger".

![test](img/1.png "1")

Congratulations, you are now an iOS tweak developer.

## The settings

It is sometimes good to have settings for your tweaks. In the case of this tweak the user probably wants to be able to set the text to something else.

### Setup with theos

`cd` into your tweak folder and start the theos-templates-menu with `/opt/theos/bin/nic.pl`. Choose the `preferencebundle` option and follow the wizard. I prefer to use my tweaks name here with the additional "prefs" at the end. In my case it becomes "com.jontelang.sliderchangerprefs".

![asd](img/5.png "asd")

Once done your tweak folder should now look like this, with an extra folder inside it. 

![asd](img/6.png "asd")

There have been some changes inside the `makefile` also which you can look at, although it is not very important at the moment.

By now you should be able to just `make package install` to verify that there is an additional entry with your tweaks name. 

It should look something like this.

![asd](img/7.png "asd")

### Changing the settings-entries

Now you'd go into the newly created folder (in your tweaks folder) and you should see another folder called "resources". Open this and find a `.plist` file with the contents. 

The file will contain the following (or similar, at least):

	<plist version="1.0">
	<dict>
		<key>items</key>
		<array>
			<dict>
				<key>cell</key>
				<string>PSGroupCell</string>
				<key>label</key>
				<string>test First Page</string>
			</dict>
			<dict>
				<key>cell</key>
				<string>PSSwitchCell</string>
				<key>default</key>
				<true/>
				<key>defaults</key>
				<string>test</string>
				<key>key</key>
				<string>AwesomeSwitch1</string>
				<key>label</key>
				<string>Awesome Switch 1</string>
			</dict>
		</array>
		<key>title</key>
		<string>test</string>
	</dict>
	</plist>

You can change it so that it looks like this (using your own name instead of my "jontelang").

	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
	<plist version="1.0">
	<dict>
		<key>items</key>
		<array>
			<dict>
				<key>cell</key>             <string>PSGroupCell</string>
			</dict>
			<dict>
				<key>cell</key>             <string>PSSwitchCell</string>
				<key>default</key>          <true/>
				<key>defaults</key>         <string>com.jontelang.sliderchangerprefs</string>
				<key>key</key>              <string>SCisEnabled</string>
				<key>label</key>            <string>Enabled</string>
				<key>alternateColors</key>  <true/>
				<key>PostNotification</key> <string>com.jontelang.sliderchangerprefs/settingschanged</string>
			</dict>
	
			<dict>
				<key>cell</key>             <string>PSEditTextCell</string>
				<key>defaults</key>         <string>com.jontelang.sliderchangerprefs</string>
				<key>key</key>              <string>SCtext</string>
				<key>label</key>            <string>Text</string>
				<key>PostNotification</key> <string>com.jontelang.sliderchangerprefs/settingschanged</string>
			</dict>
			<dict>
				<key>cell</key>             <string>PSButtonCell</string>
				<key>label</key>            <string>Save</string>
				<key>action</key>           <string>save</string>
			</dict>
		</array>
		<key>title</key>
		<string>SliderChanger</string>
	</dict>
	</plist>

This is the layout of the settings panel. If you read through them slowly they all make sense. The "defaults" key is basically the id-key-thing that the tweak will save to. All files will be located in `/var/mobile/Library/Preferences/com.yourtweak.yourtweakprefs.plist`. You'll be spending time in that folder (iFile) so make a shortcut to it.

One most notable thing here is the key `PostNotification` which is a way to avoid having to respring for your changes to take effect. This specifies a string which will be sent to the system, and your tweak will - at launch - register itself for this string. You'll see this code soon.

The button in the last panel have a `action` key which specifies a method to be run when it is clicked. The method you will add in your `SliderChangerPrefs.mm` looks like this (see below). What it does is that it de-focuses the keyboard for the `PSEditTextCell` and thus the content are saved (and the `PostNotification` is sent).

	-(void)save
	{
		[self.view endEditing:YES];
	}

At this point, your settings panel should look something like this.

![asd](img/8.png "ads")

### Accessing the settings

Your settings area is now completed, it will save the settings to a file in the directory (see above) and it is now time for your tweak to read it. And with the `PostNotification` it will also be able to reload it instantly without the need for a respring.

Open up your `Tweak.xm` and change it to this.

	static BOOL SCisEnabled = YES; // Default value
	static NSString* SCtext = nil;

	%hook SBAwayLockBar
	- (void)setLabel:(id)arg1
	{
		if(SCtext && SCisEnabled)
		{
			arg1 = SCtext;
		}
		%orig(arg1);
	}
	%end
	
	static void loadPrefs()
	{
	    NSMutableDictionary *prefs = [[NSMutableDictionary alloc] initWithContentsOfFile:@"/var/mobile/Library/Preferences/com.jontelang.sliderchangerprefs.plist"];
	    if(prefs)
	    {
	    	SCisEnabled = ( [prefs objectForKey:@"SCisEnabled"] ? [[prefs objectForKey:@"SCisEnabled"] boolValue] : SCisEnabled );
	    	SCtext = ( [prefs objectForKey:@"SCtext"] ? [prefs objectForKey:@"SCtext"] : SCtext );
	    	[SCtext retain];
	    }
	    [prefs release];
	}
	
	%ctor 
	{
	    CFNotificationCenterAddObserver(CFNotificationCenterGetDarwinNotifyCenter(), NULL, (CFNotificationCallback)loadPrefs, CFSTR("com.jontelang.sliderchangerprefs/settingschanged"), NULL, CFNotificationSuspensionBehaviorCoalesce);
	    loadPrefs();
	}
	
The `%ctor` thing here is a method which will run when the tweak is loaded, it will only run once (per respring) and is the place we use to register the tweak for listening to our `PostNotification`. The register method specifies which string to listen to and which method to call when it gets the correct string. I've created a method called "loadPrefs" that is run each time. This method simply loads the settings-saved file and reads the values from it. 

> TIP: Remember to (as in my case) make sure the value is actually present in the dictionary/file. Otherwise you might end up with mismatched default values and so on.

The rest of the tweak is pretty straight forward. I just create some static variables and add the extra check in `setLabel` to make sure nothing is modified if the tweak is, for example, disabled.

> NOTE: Make sure to prefix your variables (and methods (ignore my non-prefixed method for now)) as to avoid conflicts with other tweaks. 
	
### Meta-data

The meta-data of the settings-entry, that is.

#### Changing the icon

As you've noticed there is no icon for the entry on the settings panel. This is easy to change. The file `entry.plist` (inside the `/sliderchangerprefs/` directory) contains an entry called `icon = SliderChangerPrefs.png`.

Now go to the folder `/sliderchangerprefs/resources/` and put a `58x58` file here, and name it `SliderChangerPrefs@2x.png`. For non-retina devices you should also put a `29x29` file here with the same name, removing the `@2x` part.

## The final steps

These are the final steps before submitting the tweak to a repo. Some things to check/think about.

### Resetting the version

You might have noticed that your tweaks version increases its build number each time you do `make package`. When you release it you might not want to have the version as 0.0.1-2526. Maybe you want to have it as 1.0 or similar. 

To change this you simply open the file `control` and change the version to whatever you want.

Note that this will still not get rid of the build number, so if you set it to 1.0 the actual version will be 1.0-1. For your next version you may want to have it as 1.0-2, but the actualy version might be 1.0-65 at the point of release. To reset the counter to 0 you will go to an invisible folder called `.theos` which lies in the tweaks project root folder. Inside this there will be another folder called `packages`. Open this folder and delete the files inside it. Next time you build you will have the version at 1.0-1 again.

> NOTE: These versions might not even matter (at least when submitting to the BigBoss repo) as the repos de-assemble and re-assemble the packages when they get them. Also, when submitting to the BigBoss repo you can enter the version number. For more info though, ask the repo managers.

### Submitting to a repo

The repos have pretty straight forward submission links/processes.

[MyRepoSpace](https://www.myrepospace.com/) if you want a private repo where you can manage everything by yourself, good for beta testing.  
[BigBoss](http://www.thebigboss.org) is a default repo.  
[ModMyi](http://www.modmyi.com) is also a default repo, although I don't have much experience with hosting here it seems as good as BigBoss.  

Each website will have details on how to submit to it. If you ae unsure about something you will always be able to email the owners.  

#### The identifiers

Your tweak has an identifier, in this case it is `com.jontelang.sliderchanger`. If you do not own the domain (again, this case) "jontelang.com" the repo will change the tweak identifier to e.g. `org.bigboss.sliderchanger`. This can be important if you are doing something in regards to piracy-checks which relies on the identifiers (more on this later). 

If you do own the domain in question, tell this to the repo managers so that they can avoid changing it.

#### Paid tweaks versus free

> NOTE: All times as super approximate and can change with many many variables

**Free tweaks** takes maybe 1-2 days from the submission process to appear in Cydia.  

**Paid tweaks** can take up to a week, usually 5 days. To submit a paid tweak you will need (BigBoss experience now) to sign some form (digitally is fine) which takes some day(s) to process. You will also need a [PayPal](http://www.paypal.com) account. After submitting a paid tweak you will get links to a basic management website where you can do some things.
