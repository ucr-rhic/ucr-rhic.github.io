How to use an authentication agent (SSH agent) to store your private key for SSH transactions
===============================================================================================
Ref: [SDCC tutorial](https://www.sdcc.bnl.gov/information/ssh/ssh-agent)

In order to keep from having to tell ssh which key to use during log in you can use an ssh authentication agent or more commonly called an ssh agent.  The ssh agent will store the key for you and when you try to ssh with any connection it will check the loaded keys and use the appropriate one.

Once a key is loaded into the ssh-agent you will no longer be prompted for a password to use that key.  The only time you have to re-load the key is when you restart your computer or log off.


**Important: On most machines if the ssh-agent is running and you ssh with some private key then it will automatically add that key.  These instructions can help to debug some of those issues**

<a name="LinuxSshAgent"></a>Linux and Windows 10 machines that have OpenSSH installed
-----------------------------------------------------------
On most Linux OS the ssh-agent starts by default when you turn on the computer.
On Windows this is not the case [see here]() to check how to start ssh-agent on Windows 10 that has OpenSSH installed
- To explicitly check or run the agent use `ssh-agent [command [args ...]]`
	+ Where command is an optional application to run that will have access to the agent.
	+ This will print out information on the started *ssh-agent*
		+ Note: On Windows this output may be different or nonexistent (This may have to do with Windows still not having 100% support for OpenSSH programs. perhaps this may be fixed with time (Last date this was checked September 28, 2020)

	__**WARNING: Running `ssh-agent` may create multiple instances it is better to use the method below**__
- Alternatively to check if agent is running, or if any keys are loaded, type `ssh-add -l` (lower case L) into the terminal.
	+ If the output is, "Could not open a connection to your authentication agent.", then the ssh-agent is not running
	+ If the output is, "The agent has no identities.", then the ssh-agent is running but no keys have been loaded
	+ Any other output will display the hash of the loaded keys
- You add keys with the same command `ssh-add [file ...]`
	+ Where *\[file ...\]* is any private key file you want to load into the agent and then type your password into the prompt
	+ If no file name is provided, then an attempt is made to load the default files, otherwise the named private key file will be loaded. You will be prompted for the pass phrase for the private key file.


Windows 10 running subsystem for Linux (WSL) version 1
-------------------------------------------------------
Ref:[Source](https://unix.stackexchange.com/questions/132065/how-do-i-get-ssh-agent-to-work-in-all-terminals)

For WSL the exact same commands can be used as [here](#LinuxSshAgent).  Rather than repeat I will devote this section to setting up the ssh-agent to prevent multiple instances and quitting when WSL is exited.  This only works for WSL version 1 because WSL version 2 uses a virtual network connection which makes managing ssh keys more difficult and I haven't found a good safe way to do this at this time (Last Date checked September 28, 2020)
1. Navigate to home directory `cd ~`
2. Open for editing the file *.profile*
	+ This file automatically gets executed when WSL starts like *.bashrc* 
3. At the end of this file add the following lines
	> export SSH_AUTH_SOCK="$HOME/.ssh/ssh-agent.$HOSTNAME.sock"  
	> ssh-add -l 2>/dev/null >/dev/null  
	> if [ $? -ge 2 ]; then  
	> ssh-agent -a "$SSH_AUTH_SOCK" >/dev/null  
	> fi  
	+ These lines of code effectively check if ssh-agent is already running and if not then start it
4. Now you can add keys as usual outlined [above](#LinuxSshAgent)
5. Finished: Any new WSL terminal will have the keys already loaded as before and only one instance of ssh-agent will be running
	+ To check all running processes on Linux use `ps -elf`

Most Windows versions using PuTTY
----------------------------------
**This assumes you have downloaded all PuTTY tools from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/)**

Disclaimer: PuTTY is 3rd party telnet/ssh tool for Windows, however due to Windows 10 adding Linux capabilities I no longer advise this route but leave it here for legacy and backward compatibility reasons.

1. Start the ssh-agent for PuTTY `pageant.exe`
	+ At first it might seem like nothing happened but check your taskbar icons.  There should be an icon that looks like a computer wearing a hat.
	+ This simply indicates that the ssh-agent exclusively to be used with PuTTY has started.
2. To actually open the program *right click* the icon you found in *step 1*
	+ Several options pop up I will only discuss the most relevant ones.
		+ *View Keys* will open the main *pageant* interface where you can see, add, and remove keys
		+ *Add Key* will open a file explorer so you can select your private key
3. Once you add a private key you will be prompted for the password and once you enter it the key will be loaded
4. Finished: You must log in with `putty.exe` in order for the loaded key to work properly





