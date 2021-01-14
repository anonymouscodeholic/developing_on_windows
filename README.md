# Developing on Windows

Developers love the power of command line, nice package management. Basically, a Unix-like environment. Most developers nowadays prefer Mac and some (like me sometimes) Linux. However there are tons of reasons of buying a PC instead of a Mac, one very important being the pricing. When spending your own money you don't really want to pay the Apple tax. Also, if you happen to be a gamer, the odds are that you own a powerful Windows PC. Why buy a Mac just for development? Also, when you happen to have the PC, why install Linux on that just to occasionally develop something? If you do install Linux, you either need a dedicated SSD or you need to allocate precious disk space for it. And with another OS comes with all the overhead such as keeping up to date. Also, you end having some files on Windows and some on Linux. It's just a mess, I can say from my experience.

This document is about exploring the world of Windows as a development environment for somebody preferring nice things from Linux/Mac such as command line. The experiment was done on late 2020.

The good news is that Windows has evolved a lot lately to become a developer friendly (friendlier) OS.

Let's go and see what's the status nowadays.

## WSL

Let's have a look at [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/). A lot has happened since Microsoft CEO [called Linux cancer](https://www.zdnet.com/article/ballmer-i-may-have-called-linux-a-cancer-but-now-i-love-it/). Now Windows natively supports Linux inside it. And doesn't call it cancer, but a feature. First, what is WSL (2)?

> The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dual-boot setup.

OK, sounds good but how does it work in practice? There are a few steps to get WSL up and running, but nothing funny there. Just follow the instructions and you'll be fine. Good news is that once you have it up and running I can tell that WSL really works and it's snappy, lightweight and fun. The experience is much smoother than anything I've ever seen using a VM on e.g. VirtualBox.

From a developers point of view the main things you get from WSL are:

* A proper shell. No longer `cmd`. Finally! No more PowerShell (I've never used that...).
* Editing code on Windows (VS code) and running it on Linux, see [Get started using Visual Studio Code with Windows Subsystem for Linu](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode)

What you don't get is that your Windows doesn't become Linux. The key issues in my opinion:

* The shell you run on WSL, e.g. `bash` is really running on a Linux kernel. This means that you can't run Windows command line commands there, e.g. no running of `.bat`-scripts.
* There's no support for Linux GUI apps. You can't run your Linux-based favourite editor (if it has a GUI). Basically no running of VS Code or IntelliJ directly on Linux.
* WSL is a virtualized kernel, not a host running on metal. This means that you can't e.g. run VirtualBox on the WSL. From this point of view WSL is like an instance on any cloud provider. You can naturally run VirtualBox on Windows (that runs on metal), but in that case WSL and VMs inside VirtualBox are kind of siblings.

There are major and minor issues in the details when using WSL, let's see those next as we check out the main features.

### Shell

Starting a Linux shell is as easy as starting any Windows app:

![starting wsl](images/wsl.png)

And without delay you get a Bash prompt that is running on a Linux instance. Magic! OK, to tell the truth, there's gonna be a small delay the first time you fire up WSL, but after that it's going to be instantenious.

![bash on wsl](images/wsl-bash.png)

The shell feels native, you couldn't tell there's another kernel running it. There are no strange behaviours and annoyances, e.g. copy-pasting just works. 

A cool feature is that the Windows file system is mounted, typically in `/mnt/c` allowing things like editing files on your Windows home directory using `vi` or `emacs`.  

### Windows Terminal

Although running a shell is simple, I'd recommend using [Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/) instead. It gives you more. WSL is integrated with Windows Terminal. WT is a fully fledged terminal app with tabs and split panes and you can have as many as you want Linux shells in it. This feels like running Terminal app on Ubuntu or iTerm2 on Mac. It's pretty cool that in WT you can mix and match WSL shells with Windows command prompt and PowerShell.

One minor complaint I have is that e.g. in Ubuntu terminal you can double-click a word and it gets selected. There's no such functionality, at least by defeault.

A small thing you might want to do is to use your WSL shell as the default in WT. Out of the box the default is PowerShell. Open WT and do `CTRL+,`. You'll see something like:

```
"defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
```

In `profiles` -> `list` you should see your Linux profile. Copy-paste the guid of that one to the `defaultProfile` and restart WT. 

Like in any terminal app there are tons of knobs to turn to make it feel your own, have a look at the conf guide.

Here's what it looks like to run `emacs` and `vi` side by side. No need to end the editor war, you can run both :) And both on Linux on Windows. Peace :) Next we'll somehow mix macOS to the soup... 

![Vi and Emacs](images/wsl-vi-emacs.png)

### Developing using VS code

What is great in native apps is they are native. They will always be snappier, better integrated with the OS etc than anything running remotely. Remember, you can't run GUI apps on WSL. Because of this MS has developed a mechanism to run VS code on Windows (to make the IDE UI great), but to execute the code on WSL. Can this really work?

The good news is that that simple things really work. You can e.g. develop on Python and run on Linux:

![VS Code and remote Python](images/wsl-vscode.png)

This is pretty cool. VS Code will actually install the required plug-ins on WSL side and run them there.

### Issues

Some of the most typical issues with virtualization have always been performance related. In WSL case there are potentially blocker issues related to disk IO performance that are caused by the WSL architecture. The WSL instance (Ubuntu) has a WSL-local ext4 partition mounted to `/`. The Windows drive, typically C is mounted to `/mnt/c` as type [9p](https://en.wikipedia.org/wiki/9P_(protocol)). From Windows you can access the WSL disk e.g. by opening `\\wsl$` in Windows Explorer. Note that cross-using disk is over network, for both directions (from Windows to WSL and from WSL to Windows). Is this fast? Depends on the speed you need. When doing basic stuff like browsing file system (on shell or in Windows Explorer), you probably won't notice anything. But when doing something heavier the performance _can be_ intolerable. I tried having a largish Java project (Apache Flink) on the Windows drive and opening it up on VS Code (Windows native, using WSL remote). It took eternity to open it, at least half an hour. I'd assume this is at least 10 times slower than if everything (IDE and files) were local. I verified the same performance characteristics when compiling Maven. Very slow.

I didn't try more complex networking setup with e.g. bunch of Docker containers running on WSL and exposing TCP ports from those Windows. I'd assume there's going to be pain to get things nice and smooth.

Since the issue is architectural, there's can be no fix unless the architecture is rewamped. This is kind of sad.

### Conclusions

I didn't end up using WSL for anything more than experimenting. For my use cases, it didn't work well enough. I think it has potential though.

## VirtualBox

The old-school / traditional / heavy approach of running Linux on Windows is to fire up a virtual machine inside e.g. VirtualBox. This time I tried Ubuntu 20.04.1.

### Installation and setup notes

Here come some notes of what I learned while installing and setting up the VM.

It's probably a good idea to make the root drive big enough for the future. Resizing is surprisingly painful. The disk file size on Windows will automatically grow to fit the VM disk, however it will never shrink.

For developing things you'll want to have more processors allocated for the VM than the default one. 

To run the UI with a proper resolution you need to increase "Video Memory" in Display -> Screen. Max it out to 128MB.

To use some features (e.g. clipboard sharing) of VirtualBox you need to install the VirtualBox Guest Additions. If the Additions are not _correctly_ installed, the features  won't work and there's no error message.

I personally like to have just one clipboard, by default the VM and Windows don't share their clipboards. By having a bidirectional way I can e.g. browse in Windows and copy-paste text from the browser to the VM. The "Shared Clipboard" can be configured in General -> Advanced in the VM config.

### UI performance

To compare the user experience of using a VM to the WSL experience, I installed the same Ubuntu version as in WSL in a VirtualBox VM and tried how it feels to run the VM with UI (Gnome). I tried installing IntelliJ on such a VM and testing how it feels to use it.

The sad truth is that the UI experience is not as good as with native Windows applications. Everything is _just a little bit_ slower.

Also, just having the bootup delay is something that feels annoying after playing around with WSL. It'd be a much much better user experience if one could retire VB and use WSL instead.

But, I have to say the experience is least to say usable. I don't think I'd feel bad using this setup for hours / days.

### VirtualBox and WSL

It seems that VirtualBox (at least 6.1.X) and WSL 2 don't work simultaneously. If you try that, you'll have [strange problems](https://askubuntu.com/questions/1239382/error-when-installing-ubuntu-20-04-in-virtualbox), probably a [VirtualBox issue](https://www.virtualbox.org/ticket/19766) arise. This is a very sad thing, since it prevents one from having a nice shell in Windows (WSL) and running a full fledged VM at the same time (VirtualBox). You have to select one of these and can't even change the selection withtout reboot :(  Hopefully this gets fixed.

This a very good example of problems that arise when using virtualization. The problems are impossible to anticipate and prevent and next to impossible to debug. You'll end up wasting a lot of time. And this category of problems come and go, it's perfectly possible something works today but not in a year. As a developer I want to spend 0% of my time dealing with issues like this.

### Conclusions

I ended up using VirtualBox to develop a hobby project. It worked. The overall experience is not at all that nice as using a Mac or a Ubuntu directly running on the hardware.

I don't really what to say. In the end I started feeling that I should have installed Ubuntu on its own partition. Using VirtualBox will always be clumsier than not using it. And when working on something for hours and days you really want everything to be as smooth as possible. 

## Dual boot

For decades it has been possible to install Linux and Windows on the same physical storage medium side by side and on boot time select which one to use. The benefits of this approach are everything you get from running the OS on metal, e.g. performance. The main drawback is that this is awkward if you want to switch between OSes. And why wouldn't you?

## Conclusions

WSL 2 is a very nice and welcome feature to Windows. It seems it has traction in Microsoft and will be improved in the future. It seems that even as it is now, it does provide a lot of value, e.g. by being able to run `bash` and work on command line. However, for many use cases, it just is not good enough e.g. because of the disk IO and the fact that you're limited to use only command line programs.  

VirtualBox.... What can I say. If you really need to run Linux desktop on Windows, you can use it. I guess whether to use VirtualBox or a dual boot depends on the use case. For occasional development, VirtualBox probably makes most sense, but if you work mostly on Linux-side, maybe go for dual boot.

For me personally, it right now looks like I'm going to use Ubuntu on VirtualBox.

EDIT: see [my comments](virtualbox.md) on using VirtualBox.

