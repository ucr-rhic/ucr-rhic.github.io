Getting Files into and out of BNL RCF servers
================================================
Ref: [SDCC tutorial](https://www.sdcc.bnl.gov/information/transfer-files-using-sftp-gateways)

This tutorial will walk you through how to copy files that you have stored on RCF onto your local desktop or laptop.  If you'll notice there is no *move* command since a local machine should not be able to modify files on a remote machine and vice versa.

There are two relevant commands *sftp* and *scp*  I will go through both of these.  These two commands __should__ be executed on your laptop terminal. Exceptions exist depending on what you are transferring from where but in general this how-to will assume you are transferring from RCF servers to your computer.


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

SFTP can also be used to transfer individual files all in a single command from a terminal. It is done much the same way as outlined below for [scp](#ScpLinux). The problem is that wildcard expansions, like "\*" don't work in the same way as for [scp](#ScpLinux). Wildcards must be used inside double quotes as demonstrated in the examples below.
- To copy a file from local machine to RCF  
	- `sftp <Source File> <username>@sftp.sdcc.bnl.gov:<Full Path>/[Destination File Name]`  
	- If *Source File* contains wild cards then you should use enclose it in double quotes, i.e. `"<Source File>"`  
		- Example `sftp "*.pdf" <username>@sftp.sdcc.bnl.gov:/remote/path/to/transfer/to` will copy all pdfs in the current directory of your laptop to the RCF servers
- To copy files from RCF to local machine
	- `sftp <username>@sftp.sdcc.bnl.gov:<Full Path>/<Source File> [Destination File Name]`
	- If you want to use wildcards in *Source File* then then should enclose the whole location in double quotes i.e. `"<username>@sftp.sdcc.bnl.gov:<Full Path>/<Source File>"`
		- Example: `sftp "user@sftp.sdcc.bnl.gov:/remote/path/to/file/*.pdf" .` will transfer all pdf files to current direcotry on your laptop

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

