# Developing on Windows

Developers love power of command line, nice package management. Beacause of these and other reasons, most developers nowadays prefer Mac and some (like me sometimes) even Linux. However there are tons of reasons of buying a PC instead, one very important being the pricing. When spending your own money you don't really want to pay the Apple tax. Also, if you happen to be a gamer, the odds are that you own a powerful PC. Why buy a Mac just for development? Also, when you happen to have the PC, why install Linux on that just for development. From personal experience I can tell that a dual boot environment is cumbersome, you don't really want to maintain two completely separate environments and especially painful is boot to the other environment just to make a small code change.

This document is about exploring the world of Windows as a development environment for somebody preferring nice things from Linux/Mac such as command line.

The good news is that Windows has evolved a lot lately to become a developer friendly (friendlier) OS. 

Let's go and see what's the status nowadays.

## WSL

Let's have a look at [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/). A lot has happened since Microsoft CEO [called Linux cancer](https://www.zdnet.com/article/ballmer-i-may-have-called-linux-a-cancer-but-now-i-love-it/). Now Windows natively supports Linux inside it. And doesn't call it cancer, but a feature. First, what is WSL (2)?

> The Windows Subsystem for Linux lets developers run a GNU/Linux environment -- including most command-line tools, utilities, and applications -- directly on Windows, unmodified, without the overhead of a traditional virtual machine or dual-boot setup.

OK, sounds good but how does it work in practice? There are a few steps to get WSL up and running, but nothing funny there. Just follow the instructions and you'll be fine. Good news is that once you have it up and running I can tell that WSL really works and it's snappy, lightweight and fun. The experience is much smoother than anything I've ever seen using a VM on e.g. VirtualBox.

From a developers point of view main things you get from WSL are:

* A proper shell. No longer `cmd`. Finally! No more PowerShell (I've never used that...).
* Editing code on Windows (VS code) and running it on Linux, see [Get started using Visual Studio Code with Windows Subsystem for Linu](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-vscode)

What you don't get is that your Windows doesn't become Linux. The key issues in my opinion:

* The shell you run on WSL, e.g. `bash` is really running on a Linux kernel. This means that you can't run Windows command line commands there, e.g. no running of `.bat`-scripts.
* There's no support for Linux GUI apps. You can't run your Linux-based favourite editor (if it has a GUI).
* WSL is virtualized kernel, not a host running on metal. This means that you can't e.g. run VirtualBox on the WSL. From this point of view WSL is like an instance on any cloud provider. You can naturally run VirtualBox on Windows (that runs on metal), but in that case WSL and VMs inside VirtualBox are kind of siblings.

### Shell

Starting shell is as easy as starting any Windows app:

![starting wsl](images/wsl.png)

And without delay you get a Bash prompt that is running on a Linux instance. Magic!

![bash on wsl](images/wsl-bash.png)

The shell feels native, you couldn't tell there's another kernel running it. There are no strange behaviours e.g. when copy pasting text.

A cool feature is that the Windows file system is mounted, typically in `/mnt/c` allowing things like editing files on your Windows home directory using `vi` or `emacs`.  

### Windows Terminal

Although running a shell is simple, I'd recommend using [Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/) instead. WSL is integrated with Windows Terminal. WT is a fully fledged terminal app with tabs and split panes and you can have as many as you want Linux shells in it. This like running Terminal app on Ubuntu or iTerm2 on Mac. It's pretty cool that in WT you can mix and match WSL shells with Windows command prompt and PowerShell.

A small thing you might want to do is to use your WSL shell as the default in WT. From the box it uses PowerShell. Open WT and do `CTRL+,`. You'll see something like:

```
"defaultProfile": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
```

In `profiles` -> `list` you should see your Linux profile. Copy-paste the guid of that one to the `defaultProfile` and restart WT. 

Like in any terminal app there are tons of knobs to turn to make it feel your own, have a look at the conf guide.

Here's what it looks like to run `emacs` and `vi` side by side. No need to end the editor war, you can run both :) And both on Linux on Windows. Piece :) Next we'll somehow mix macOS to the soup... 

![Vi and Emacs](images/wsl-vi-emacs.png)

### Developing using VS code

What is great in native apps is they are native. They will always be snappier, better integrated with the OS etc than anything running remotely. Because of this MS has developed a mechanism to run VS code on Windows, but to execute the code on WSL. Can this really work?

The good news is that that simple things really work. 

The sad truth is that you can't do everything you could do with a full VM with this approach.

TODO: example




## Some notes on VirtualBox

The old-school / traditional approach of running Linux on Windows is to use a virtual machine inside e.g. VirtualBox.

To compare the user experience of using a VM to the WSL experience, I installed the same Ubuntu version in a VirtualBox VM and tried how it feels to run the VM with UI. The sad truth is that the experience is not very good. Everything is slower than when using a native app. Just having the bootup delay is something that feels annoying after playing around with WSL.

### Virtualbox and WSL

It seems that VirtualBox (at least 6.1.X) and WSL 2 don't work simultaneously. If you try that [strange problems](https://askubuntu.com/questions/1239382/error-when-installing-ubuntu-20-04-in-virtualbox) arise. This is a very sad thing, since it prevents one from having a nice shell in Windows and running a full fledged VM at the same time. You have to select one of these and can't even change the selection withtout reboot :(  Hopefully this gets fixed.

This a very good example of problems that arise when using virtualization. The problems are impossible to prevent and next to impossible to debug. And this category of problems come and go, it's perfectly possible something works today but not in a year. As a developer I want to spend 0% of my time dealing with issues like this.


