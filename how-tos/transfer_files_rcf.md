Getting Files into and out of BNL RCF servers
================================================
Ref: [RCF tutorial](https://www.racf.bnl.gov/docs/services/ftp)

This tutorial will walk you through how to copy files that you have stored on RCF onto your local desktop or laptop.  If you'll notice there is no *move* command since a local machine should not be able to modify files on a remote machine and vice versa.

There are two relevant commands *sftp* and *scp*  I will go through both of these.


Linux, Mac OS X, and Windows Subsystem for Linux
-----------------------------------------------------------

1. Make sure the correct private key is loaded using methods described [here](ssh_agent.md)

<a name="SftpLinux"></a>
### SFTP
Stands for SSH (or Secure) File Transfer Protocol.  It is works much like ssh except for one major difference, you can browse both local and remote files simultaneously.  For this reason, sftp has its own set of commands to do this.  Although most of these commands are similar to the usual Linux commands for browsing files I will go over the important ones here.

When connecting via sftp no setting file is loaded like when you ssh and tab completion may be limited or nonexistent.  I recommend using this method when you don't know where or what files you want to copy and need real time interaction with the files on the local and remote machine.
2. Run the sftp command to connect to RCF FTP machine `sftp \<username\>@rftpexp.rhic.bnl.gov`
	- Even with key loaded may need to give *sftp* key location. This can be done with option *-i* e.g. `sftp -i \<privatekey\> \<username\>@rftpexp.rhic.bnl.gov`
	- If connection is successful you should see the following output on terminal window
	> sftp\>
3. You can browse and transfer files using commands below
	- `lls`  -> List local directory contents
	- `lcd`  -> Change local directory
	- `lpwd` -> Show current local directory

	- `ls`   -> List remote directory contents
	- `cd`   -> Change remote directory
	- `pwd`  -> Show current remote directory

	- `get \<file\>` -> Copy *file* from current remote directory to current local directory
	- `put \<file\>` -> Copy *file* from current local directory to current remote directory
	- `exit` -> Terminate sftp session
4. Finished: The next time you ssh you should see the transfered files on RCF or your local machine.

<a name="ScpLinux"></a>
### SCP
Stands for Secure Copy.  It works exactly like `cp` command in Linux, and even uses most of the same command line options, except the source or destination can be a remote machine.  It can use standard Linux modifiers like *\** and *?* but no tab completion on remote files.  I would recommend this method as it is quicker when you know where and what files you need to copy.
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

2. Open a terminal window (Powershell recommended) and navigate to directory with PuTTY tools.

3. Here you can call the relevant executables from the command line
	- __sftp__: Use command `psftp.exe` and use just like *sftp* as described [here](#SftpLinux)
	- __scp__: Use command `pscp.exe` and use just like *scp* as described [here](#ScpLinux)

