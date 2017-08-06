---
layout: post
title:  "Setting up a local IDE using Bash on Windows"
date:   2017-08-05 20:16:07 -0400
---


When I was looking for a solution for a local IDE, there are several guides out there for using Linux or OSX, but I was not able to find a single comprehensive guide for setting up an IDE for windows that closely mirrored the Learn IDE setup. I don't own a mac, so setting up a mac environment was not useful, which left the only option as either setting up a dual-boot linux environment, or a virtual machine in windows running linux. 

I did try both of those options, which ended up being unsatisfactory for a few reasons.

Problems with dual boot:
* I needed my desktop to remain in windows environment for other non-technical household members use, and I didn't want to be switching back and forth constantly
* On my laptop, having a linux environment broke some of the laptop's features that I was not interested in hunting down solutions for, such as: fingerprint id sensor for login, display mirroring when an external monitor is plugged in, etc.

Problems with virtual machine:
* My desktop is a middle-of-the-road, decent perfromance rig. Even giving the virtual machine 2 processor cores and 5 gigs of ram, there was still a perceptible input lag that over time became unacceptable to me.

So, deciding to dig deeper into Windows environment setup, I discovered the Ubuntu-based Bash shell that can run Linux software on Windows 10. It is not a perfect solution, and there are still problems that you can run into, but it gets the job done as far as using it for Learn.co's curriculum. It is still under development, and as of writing this, it is out of beta in the next Windows 10 update.

Bash setup in Windows 10:

Caveat: this is only supported in the 64-bit version of Windows 10. But who still uses a 32-bit version anyway?

Open the Settings app (gear in the Start Menu). Click on Update & security, then navigate to the For Developers tab. Activate the "Developer mode" option.

Now open the Control Panel, Programs > Turn Windows features on or off. Find the "Windows Subsytem for Linux" option, select it and click OK.

Windows will need to reboot to install the new feature. After the restart, hit the Windows key, type "bash" and hit enter.

Accept the terms of service and "Bash on Ubuntu on Windows" application will download. Follow the prompts to create a user account and password for the Bash environment. You now have command-line bash shell, which you can open by searching for "bash" in the Start menu and then pinning to your taskbar.

Ok, so now we have bash. But it's running in a basic windows command terminal... ugh. Tabs, we need tabs. For that, in comes [Hyper](https://hyper.is): which both has tabs and is configurable by JSON.

Hyper uses Windows system prompt by default. You can swap by typing bash at the prompt, or change the default shell in the .hyper.js config file to 
```
shell: 'C:\\Windows\\System32\\bash.exe'
```
From this point on, it is essentially setting up using the directions from the Learn.co Ubuntu Environment Setup, original doc [here](https://github.com/learn-co-curriculum/linux-env-setup), with a few tweaks. Note: the link https://learn.co/manual_setup will bring you to the default setup for your environment, which if you are in Windows, learn.co thinks is OS X, hence the github page link.

Starting from the top, you should have `sudo` access since you just got finished setting that up. Hyper in windows bash should be a login shell, but if you want to test it, you can copy the following command into Hyper to see for yourself:
```
shopt -q login_shell && echo 'Login shell' || echo 'Not login shell'
```

Under Installing Dev tools > The essentials the following line
> curl -sL https://deb.nodesource.com/setup_5.x | sudo -E bash -
is out of date. Replace the 5.x with 6.x


You will have to run `sudo apt install git` before you do the Setting up your computer with Github section.

Download the Chrome browser if you don't have it already. There are multiple code editors to choose from. Atom is the one the Learn IDE is based on, but I personally prefer VS Code. The rest of the programs listed in this section are not needed, because you have the Windows versions of the program available to you.

Final notes:

Dave Rupert, whose [blog](http://daverupert.com) is the source of a lot of my Bash on Windows information, has a tool on github that keeps track of tools that are available on WSL: [Can I Subsystem It?](https://github.com/davatron5000/can-i-subsystem-it).

You cannot change Linux files with Windows apps. This can lead to data corruption, and it is something that is being worked on. This includes using your code editor to create files. If you need to add files to your code, use `touch` in your hyper terminal.

Your ~/code/labs folder will be buried way down in your Windows file structure. The simplest way to deal with this is to find your bash home directory in windows explorer (mine was in C:/users/username/appdata/local/lxss/home/bash_username/code) and pin the labs folder to your quick access list. Not super elegant, but it works just fine.

****NOKOGIRI
Such a pain that it needs it's own header...

Nokogiri can be the bane of your existence if you don't know the workarounds for it. There is a bug in version 1.6.8 that causes a failure to install, and the bundle install will just hang while it tries to keep writing. This also happens to be the version I ran into the most doing labs when I first was trying to setup the Windows IDE. There are two ways around it:

1. install some XML libs:
```
apt install zlibc zlib1g-dev libxml2 libxml2-dev libxslt1.1 libxslt1-dev
```
and force nokogiri install using system ilbraries:
```
gem install nokogiri -- --use-system-libraries
```
--or--
2. remove the Gemfile.lock from the cloned lab repo:
```
rm Gemfile.lock
```
then
```
bundle install
```
Removing the Gemfile.lock lets `bundle install` load the latest version of a gem, unless a specific version is specified in the Gemfile itself, which is rare for nokogiri. You can also use this to update the version of rails specified in the Gemfile to a newer version, though if you do, be prepared for deprecation warnings. If something breaks, or there is a conflict, bundle can usually tell you what you need to do to resolve it.

And that is essentially it. If you decide it isn't working for you, Windows is being persnickety or whatnot, there is a great blog to check out if you want to try the virtual machine route: [Setting up a local IDE in Windows](http://http://michaelries.info/2017/04/27/setting_up_a_local_ide_in_windows/) by fellow Learner Michael Ries. 

If you try this method and it works out for you (or it doesn't work out), feedback is appreciated. You can hit me on slack @dschlaud, or email me: dschlaud@gmail.com








