Getting Files into and out of BNL RCF servers
================================================
Ref: [SDCC tutorial](https://www.sdcc.bnl.gov/information/transfer-files-using-sftp-gateways)

This tutorial will walk you through how to copy files that you have stored on RCF onto your local desktop or laptop.  If you'll notice there is no *move* command since a local machine should not be able to modify files on a remote machine and vice versa.

There are two relevant commands *sftp* and *scp*  I will go through both of these.  These two commands __should__ be executed on your laptop terminal. Exceptions exist depending on what you are transferring from where but in general this how-to will assume you are transferring from RCF servers to your computer.


Summary Linux Flavors
-----------------------
__*IMPORTANT:Either make sure ssh-agent has key loaded or use option '-i' to give private key explicitly*__
- Download: `sftp "username@sftp.sdcc.bnl.gov:/rcf/path/to/file /local/path/to/copy/to/`
- Upload: `echo "put <Source File>" | sftp <username>@sftp.sdcc.bnl.gov:<Full Path>/`
- *Interactive session*: `sftp username@sftp.sdcc.bnl.gov`


Linux, Mac OS X, and Windows Subsystem for Linux
-----------------------------------------------------------

1. Make sure the correct private key is loaded using methods described [here](ssh_agent.md)

<a name="SftpLinux"></a>
### SFTP Interactive mode
Stands for SSH (or Secure) File Transfer Protocol.  It is works much like ssh except for one major difference, you can browse both local and remote files simultaneously.  For this reason, sftp has its own set of commands to do this.  Although most of these commands are similar to the usual Linux commands for browsing files I will go over the important ones here.

When connecting via sftp no setting file is loaded like when you ssh and tab completion may be limited or nonexistent.  I recommend using this method when you don't know where or what files you want to copy and need real time interaction with the files on the local and remote machine.

1. Run the sftp command to connect to SDCC SFTP machine `sftp \<username\>@sftp.sdcc.bnl.gov`
	- Even with key loaded may need to give *sftp* key location. This can be done with option *-i* e.g. `sftp -i \<privatekey\> \<username\>@sftp.sdcc.bnl.gov`
	- If connection is successful you should see the following output on terminal window
	> sftp\>
2. You can browse and transfer files using commands below
	- `lls`  -> List local directory contents
	- `lcd`  -> Change local directory
	- `lpwd` -> Show current local directory

	- `ls`   -> List remote directory contents
	- `cd`   -> Change remote directory
	- `pwd`  -> Show current remote directory

	- `get \<file\>` -> Copy *file* from current remote directory to current local directory
	- `put \<file\>` -> Copy *file* from current local directory to current remote directory
	- `exit` -> Terminate sftp session
3. Finished: The next time you ssh you should see the transfered files on RCF or your local machine.


### SFTP command line mode
[Ref sftp command line](https://stackoverflow.com/questions/16721891/single-line-sftp-from-terminal), [Ref wildcards](https://groups.google.com/g/comp.unix.shell/c/c3NdGKz86WQ), [Ref wildcards](https://stackoverflow.com/questions/58344727/downloading-files-from-sftp-server-with-wildcard-and-in-a-loop)

SFTP can also be used to transfer individual files all in a single command from a terminal; __WARNING:There is no way to check file overwrite with sftp__. Uploading a file requires feeding the `put` command from *stdout* to the *stdin* when executing sftp. This can be done using the pipe operator "|" in c-shell (see example belwo) or here strings "<<<" in bash (not tested but used in [ref](https://stackoverflow.com/questions/16721891/single-line-sftp-from-terminal) ). Downloading a file is done in much the same way as outlined below for [scp](#ScpLinux). The biggest difference is a problem with wildcard expansions, like "\*"; they don't work in the same way as for [scp](#ScpLinux). Wildcards must be used inside double quotes as demonstrated in the example below. 
- To copy a file from local machine to RCF  
	- `echo "put <Source File>" | sftp <username>@sftp.sdcc.bnl.gov:<Full Path>/`  
	- Where *Source File* is the file you want to copy to RCF
		+ *Source File* can contain wildcad characters
	- The *Full Path* needs to be the full path as it appears using a `pwd` command on RCF.  You cannot do local with respect to home directory and even *~* shortcut will not work.
	- Example: Copy all pdfs in the current directory of your laptop to the RCF servers
	> `echo "put *.pdf" | <username>@sftp.sdcc.bnl.gov:/remote/path/to/upload`
- To copy files from RCF to local machine
	- `sftp <username>@sftp.sdcc.bnl.gov:<Full Path>/<Source File> [Destination File Name]`
	- If you want to use wildcards in *Source File* then then should enclose the whole location in double quotes i.e. `"<username>@sftp.sdcc.bnl.gov:<Full Path>/<Source File>"`
	- Example: download all pdf files in a given rcas directory to your laptop
	> `sftp "user@sftp.sdcc.bnl.gov:/remote/path/to/file/*.pdf" .`

<a name="ScpLinux"></a>
### SCP
Stands for Secure Copy.  It works exactly like `cp` command in Linux, and even uses most of the same command line options, except the source or destination can be a remote machine.  It can use standard Linux modifiers like *\** and *?* but no tab completion on remote files. ~~I would recommend this method as it is quicker when you know where and what files you need to copy.~~ __EDIT:(February 28, 2022): RCF [discontinued](https://www.sdcc.bnl.gov/news-events/sdcc-announcements/legacy-rftpexp-gateways-be-retired) the rftpexp servers and SCP no longer works so SFTP is the only way to transfer files. This section will be kept as reference for sftp command line mode.__
- To copy files from local machine to RCF
	- `scp <Source File> <username>@rftpexp.rhic.bnl.gov:<Full Path>/[Destination File Name]`
	- Where *Source File* is the file you want to copy to RCF
	- The *Full Path* needs to be the full path as it appears using a `pwd` command on RCF.  You cannot do local with respect to home directory and even *~* shortcut will not work.
	- Optionally you can rename the file by typing in a new name for *Destination file*
- To copy files from RCF to local machine
	- `scp <username>@rftpexp.rhic.bnl.gov:<Full Path>/<Source File> [Destination File Name]`
	- The *Full Path* needs to be the full path as it appears using a `pwd` command on RCF.  You cannot do local directory with respect to home directory and even *~* shortcut will not work.
	- *Source File* is the file you want to copy to your local machine
	- *Destination File Name* is the destination on your local machine to copy to.  Here you can use tab completion and even *~* shortcut.  The default will preserve the file name but you can optionally give the copied file a new name.
	
	
### Mount the remote file system (Windows)
How to mount the SDCC file system in Windows [Useful Ref](https://adamtheautomator.com/sshfs-mount/#Using_SSHFS_Mount_on_Windows)


All commands should be executed in powershell terminal window

1. Download and install [WinFsp](https://github.com/winfsp/winfsp/) using `winget install WinFsp.Fsp`
2. Download and install [SSHFS-Win](https://github.com/winfsp/sshfs-win) using `winget install SSHFS-Win.SSHFS-Win`
3. Open a Powershell (or command prompt if you prefer) and go to the *bin* folder in the install folder for SSHFS-Win; this is because you will be using the installed command line tool `sshfs-win.exe`
4. Ensure that your private key exists in this particular location and has this specific name *~/.ssh/id_rsa*
5. `.\sshfs-win.exe svc \sshfs.kr\username@sftp.sdcc.bnl.gov X:`
	- *username* is your SDCC user name
	- *X:* is the drive letter you want to mount the remote file system to
	- This will mount the root directory i.e. The whole SDCC file system.
		+ This is preferred since it will allow you to access files on other disks like the pwg disk and your symlink will also work
		+ To mount just your home directory do `.\sshfs-win.exe svc \sshfs.k\username@sftp.sdcc.bnl.gov X:`
6. Enter your password for the private key

If everything worked you should see a message "The service sshfs has been started". This means the sshfs service has started and you should see the remote file system in Windows explorer. To disconnect just *CTRL-c* to stop the service or close the controlling terminal.

If you want to start a separate controlling terminal using powershell you can use the command `Start-Process` instead. This command will look like `Start-Process "C:\Path\To\SSHFS-Win\bin\sshfs-win.exe` -ArgumentList "svc","\sshfs.kr\sftp.sdcc.bnl.gov","X:"`.

You can also add all the options for the sftp connection like hostname and identity file to your [ssh config](ssh-config.md) file to bypass having to type in all the information on the command line. For example if the "Host" in you ssh config file is "sdccsftp" then you can do the following command to connect `.\sshfs-win.exe svc \sshfs.kr\sdccsftp X:`

### SSHFS on a Mac OS
[Useful Ref](https://www.petergirnus.com/blog/how-to-use-sshfs-on-macos)

1. Download and install [OSXFuse](https://osxfuse.github.io/) a file system integration for MAC needed to create a mountable drive
2. Download and install [SSHFS](https://osxfuse.github.io/) the actual executable needed for the sshfs command
3. Go to home directory `cd ~`
4. Create a temporary directory `mkdir tmp`
5. Ensure that your private key is loaded into the [ssh-agent](ssh_agent.md)
6. `sshfs username@sftp.sdcc.bnl.gov:/path/to/dir ~/tmp`
    - To bind the root directory "/" of the entire SDCC file system you can just omit the "/path/to/dir" and just leave a "/".
7. Unmount with `diskutil umount ~/tmp`

Note that symlinks or soft links don't work if the symlink starts with a "/" since your computer will think the "/" refers to your root directory and not the mounted file systems root directory. You will have to explicitly give it the correct path. Again this will only work if you mount the root directory.



Most Windows versions using PuTTY
----------------------------------
**This assumes you have downloaded all PuTTY tools from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/)**

Disclaimer: PuTTY is 3rd party telnet/ssh tool for Windows, however due to Windows 10 adding Linux capabilities I no longer advise this route but leave it here for legacy and backward compatibility reasons.

Using PuTTY tools to transfer files works the same way except the name of the executable is different.
1. Load private key into *pageant* as described [here](ssh_agent.md)

2. Open a terminal window on your laptop (Powershell recommended) and navigate to directory with PuTTY tools.

3. Here you can call the relevant executables from the command line
	- __sftp__: Use command `psftp.exe` and use just like *sftp* as described [here](#SftpLinux)
	- ~~__scp__: Use command `pscp.exe` and use just like *scp* as described [here](#ScpLinux)~~

## Usage of SSHFS with Visual Studio on Windows
--------------------
If you are looking to use Visual Studio as file editor and display if you want to avoid using XMing or one of it's alternatives, this is a viable option. In theory, this tutorial should work with Windows, Mac, and Linux with some minor changes, but I will focus on Windows specifically. Please follow these steps:

1. Follow steps 1-2 [here](https://github.com/privdonut123/ucr-rhic.github.io/edit/master/how-tos/transfer_files_rcf.md#mount-the-remote-file-system-windows).
2. Download and install one of the [Visual Studio Distributions](https://visualstudio.microsoft.com/). I would recommend that you get Visual Studio Code since is available for Windows, Mac, and Linux. Pick whichever you prefer that suits your operating system.
3. Open Visual Studio and get any extensions you might want. These are useful, especially if you plan on running code locally (**Disclaimer**; These are not all compilers or debuggers, and in many cases, you need to install a separate compiler. If you want to use many of these extensions properly, follow all of their directions). My recommendations are the [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack), [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter), [Powershell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell) (if you have it), and [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
4. Follow steps 3-6 [here](https://github.com/privdonut123/ucr-rhic.github.io/edit/master/how-tos/transfer_files_rcf.md#mount-the-remote-file-system-windows).
5. Once the drive is mounted, you can either navigate it and open files using:
	- Your operating system's file explorer.
	- The built-in file explorer in Visual Studio (you can get to this by using `Ctrl+Shift+E` (most convenient).
	- In powershell you can navigate to the file you would like to edit and type `code file_name`. This will open the desired file in Visual Studio.

Make sure to save any edited files, doing so will save those changes on SDCC as well. Also keep in mind, if you want to run your code, you will still need to `ssh` into SDCC since the STAR libraries are not local. Use your preferred method of login from [here](https://ucr-rhic.github.io/how-tos/rcf_generic_login.html).
