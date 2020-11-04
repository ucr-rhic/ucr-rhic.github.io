Setup X window system for graphical display of remote files
============================================================

The X window system (X11) is a way for displaying graphical windows over an ssh connection.

Disclaimer: Most of the X11 programs I list here are 3rd party that I have found to be reliable over the years.  ***You*** **are ultimately responsible for any software you download and install.**

Installation
-------------
Installing and setting up X11 is highly machine dependent.  I will go over some of the common machines here.

### Ubuntu
**Note: This includes Windows Subsystem for Linux: Ubuntu, as well but need a working X11 installed on Windows first. Follow instructions to install for Windows first then follow [setup instructions](#WindowsX11Start)**
1. Open a terminal
2. Type the command `xeyes`
	- If X11 tools __are__ already installed then a window will pop up with a pair of eyes that follow your mouse.
	- If X11 tools are __not__ installed you will be prompted to download and install any of its dependencies.  Follow the terminal prompts to do this
		- Once completed type `xeyes` again to confirm it is working.
3. Finished: Proceed to [setup](#X11Setup)

### Mac OS X
In order to use X11 on a Mac you need to download and install an X11 program.  I use __XQuartz__ because it is free (at least as of October 7, 2020) and very lightweight.  It can be downloaded from [here](https://www.xquartz.org/).  There may be others out there and if there is another you prefer just use that one.  After you install an X11 program on Mac proceed to [setup](#X11Setup).

### Most Windows OS
In order to use X11 on Windows you need to download and install an X11 program.  There are two third party software I know of that can be used.  The first is [Xming](http://www.straightrunning.com/XmingNotes/), it is easy to use and very lightweight but I do not recommend it since the free version comes with limited tools and the developers require a "donation" in order to get the full package.  The other I do recommend is [VcXsrv](https://sourceforge.net/projects/vcxsrv/) because it comes with all X11 tools and is as easy to use as __Xming__.  Once you install the X11 program proceed to [setup](#X11setup)


<a name="X11Setup"></a>
Setup
-----------------------------
This describes only how to set up X11 windows for ssh connections.  X11 windows can be used for other things but here I limit it to ssh connections to RCF.  The examples shown use the ssh gateway *rssh.rhic.bnl.gov* but check [here](https://www.racf.bnl.gov/docs/services/Ssh/gateways) for most recent ssh gateway information.

<a name="UbuntuSetupX11"></a>
### Ubuntu (Not Tested)
**Note: This does include Windows Subsystem for Linux: Ubuntu**

Using X11 on Ubuntu is fairly simple.

1. Set the __DISPLAY__ environment variable to *localhost:0*[^1] using the command `export DISPLAY=localhost:0`
2. Enable X11 forwarding with your SSH command using option '-X' `ssh -X user@rssh.rhic.bnl.gov`
	- You can use option '-Y' instead which tells ssh it is a trusted client but this may lead to security issues [see here](https://askubuntu.com/questions/35512/what-is-the-difference-between-ssh-y-trusted-x11-forwarding-and-ssh-x-u) and [here](https://stackoverflow.com/questions/13425820/difference-between-ssh-x-and-ssh-y), only use it if '-X'	is not working.
3. Finished: Any graphical interface will now be opened on the X-window display, you can also [display image files](#X11DisplayImage)
	- It is a good idea to check that (not modify) __DISPLAY__ environment variable is set to *localhost*[^1] on remote machine and `xeyes` runs.  If it is not set or `xeyes` doesn't run, then go back to beginning and try again.

### Mac OS X

Using X11 on Mac OS X is similar to  Ubuntu.  The only difference is __DISPLAY__ environment variable need not be set.  Simply ssh using option '-X' or '-Y' as needed [see](#UbuntuSetupX11) `ssh -X user@rssh.rhic.bnl.gov`.  It is a good idea to check that (not modify) __DISPLAY__ environment variable is set to *localhost*[^1] on remote machine and `xeyes` runs.  If it is not set or `xeyes` doesn't run, then go back to beginning and try again.

### Windows

Using X11 on windows is highly dependent on the terminal application you want to use or if you want to use Windows subsystem for Linux.  The one common element between all these different methods is that you need to start the X11 program first before you do anything else.

<a name="WindowsX11Start"></a>
##### Start X11 program
- Start Xming or VcXsrv
	1. Find and start the program `XLaunch.exe`
		- Any installation of *Xming* or *VcXsrv* will install this executable
	2. Select settings as desired and click next until X11 starts
		- Default options are fine but if you want to here is the place to customize.[^1]
		- The only indication that the program has started is an icon that looks like an __X__ will appear on the taskbar.  This icon can be right clicked to access more options as well as quitting.
	3. Leave the X11 program running while you connect with one of the methods below.
		- Once you are done you can terminate the X11 program.
		
##### Windows 10 with OpenSSH installed using Powershell

This is my recommended way to use X11 on Windows 10.  Using X11 on Powershell with OpenSSH installed is the same as for Mac or Ubuntu but due to variations in commands I will repeat it here.

1. Ensure the __DISPLAY__ environment variable is set to *localhost:0*[^1] using the command `echo $env:DISPLAY`
	- If unset or incorrect set it using command `$Env:DISPLAY = "localhost:0"`
2. Enable X11 forwarding with your SSH command using option '-Y' `ssh -Y user@rssh.rhic.bnl.gov`
	- __**Note(October 9, 2020):**__ The option '-X' does not work possibly due to incompatibilities between certain Linux libraries running on Windows.  I may not sure if they this may cause security issues [see](#UbuntuSetupX11)
3. Finished: Any graphical interface will now be opened on the X-window display, you can also [display image files](#X11DisplayImage)
	- It is a good idea to check that (not modify) __DISPLAY__ environment variable is set to *localhost*[^1] on remote machine and `xeyes` runs.  If it is not set or `xeyes` doesn't run, then go back to beginning and try again.

##### Windows Subsystem for Linux (WSL) Version 1 Ubuntu

**Due to the way WSL Version 2 works I have not been able to find a good secure solution for X11 forwarding.  There are solutions that may not be "security sound" so I wouldn't recommend it, instead use Powershell with OpenSSH installed**

These instructions are exactly the same as for [Ubuntu](#UbuntuSetupX11)

##### Most Windows versions using PuTTY
**This assumes you have downloaded all PuTTY tools from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/)**

Disclaimer: PuTTY is 3rd party telnet/ssh tool for Windows, however due to Windows 10 adding Linux capabilities I no longer advise this route but leave it here for legacy and backward compatibility reasons.

1. Load your saved putty configuration for RCF connection
2. On PuTTY Configuration side tab navigate to Connection->SSH->X11
3. Click on icon labeled `Enable X11 forwarding`[^1]
	- *Optional* Save the configuration file
4. Click `Open` to start the connection.
5. Finished: Any graphical interface will now be opened on the X-window display, you can also [display image files](#X11DisplayImage)
	- It is a good idea to check that (not modify) __DISPLAY__ environment variable is set to *localhost*[^1] on remote machine and `xeyes` runs.  If it is not set or `xeyes` doesn't run, then go back to beginning and try again.

[^1]:The name __localhost__ can be replaced with it's explicit form __127.0.0.1__.  The __:0__ simply tells it which display number to use of which *0* is the default.  If you have manually set it to something else replace *0* with chosen display number.

<a name="X11DisplayImage"></a>
How to Use the X11 Forward to Display Graphical Windows
--------------------------

This will happen automatically for applications that require a graphical display.  To display image files simply use the command `display` on any image file (*png*,*jpg*,etc), e.g. `display image.png` or any other image file.  To display *pdf* files need to install a separate *pdf* reader.  The *pdf* reader will then utilize the X11 connection to display *pdf* files.

Potential Issues
-------------------

#### Mac OS X
If you get a message that says:
> Warning: untrusted X11 forwarding setup failed: xauth key data not generated.  

To solve this issue; in your ssh config file for the RCF connection ([tuturial](ssh_config.md)) add the following line.  I don't know what ssh option does the same thing [reference](https://stackoverflow.com/questions/27384725/ssh-x-warning-untrusted-x11-forwarding-setup-failed-xauth-key-data-not-gener).
> XAuthLocation /opt/X11/bin/xauth  


#### Windows with OpenSSH installed using Powershell
If you ssh with option '-Y' you may get a message that says:
> Warning: No xauth data; using fake authentication data for X11 forwarding.  
This is most likely due to incompatibilities between Linux commands and Windows. At least that was my understanding from reading [this](https://unix.stackexchange.com/questions/57138/why-does-my-x11-forwarding-attempt-fail-with-connect-tmp-x11-unix-x0-no-such).  I don't think there is a solution yet (October 9, 2020) and perhaps as Linux on Windows matures this may go away with later versions.

