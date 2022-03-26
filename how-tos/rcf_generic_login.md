Logging into Brookhaven National Lab's (BNL) Computer System
==================

[Main help page for new users](https://www.sdcc.bnl.gov/information/getting-started)

Quick Start
----------

### Get BNL ID and Request Computer Account
1. Web search for BNL new user account
2. Fill out form for new user account
3. Wait until you get approved and have a id/registration number
4. Request a Computer account using  
	i. Generate a public and private ssh key by following instructions [here](generate_keys.md)  
	ii. Only upload __public__ key (.pub extension)  
5. Once account is approved you will get a username and temp password with instructions on how to change your password.
6. Log in to the computing nodes using one of the methods below to change your password

### Connect to computing nodes (RCAS)
Connecting to BNL can be done using SSH or NoMachine. The NoMahcine is the preferred way to go if you want a graphical interface. SSH can utilize graphics using X11 but it is __really slow__.

#### Flavors of Linux including MacOS and Windows Subsystem for Linux [WSL](windows_setup.md)
1. Load Private key into ssh-agent  
	i. `ssh-add -l` to check status of ssh-agent program and if any keys are loaded  
	ii. `ssh-add <path/to/private/key>` add private key to ssh-agent it will ask for password if not then you probably chose the pubic key (.pub)  
2. Run `ssh -tA <username>@ssh.sdcc.bnl.gov rterm -i`  
	- `ssh -tAX username@ssh.sdcc.bnl.gov rterm -i` to connect with X11 [see also](setup_xwindow.md)  
3. Enter your kerberos password
4. Enjoy :)

#### No Machine Connect
1. Set up multifactor authentication (MFA) with SDCC [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication)
2. Start NoMachine and click on your saved connection or use [website](https://nx.sdcc.bnl.gov/nxwebplayer)  
	- Setup NoMachine connection  
		i. Select New Connection  
		ii. Select the 'NX protocol' option  
		iii. Enter Host - *nx.sdcc.bnl.gov* and Port - *4000*  
		iv. Authenticate using the 'Password' option  
		v. HTTP Proxy - Do not use proxy option  
		vi. Specify any general name for the connection and click on the Done button.  
3. Enter your login credentials
	- Username is the same as the one for your computer account
	- Password is your kerberos password
4. Click Connect
5. It will ask for another password if above worked. This is the MFA password. If not then there may be an issue with your computer account
6. Select create a new desktop and/or pick your favorite vitual desktop *GNOME* or *KDE*
7. Click *Automatically select a node*
8. Start a terminal program and connect to computing nodes `rterm -i`
	- __IMPORTANT:Do not work on the nx gateway. You must ssh into the computing nodes even from NoMachine__
	- `display` can be used to view *png* files for *pdf* installing a pdf reader is recommended.


#### Transfer Files using sftp to and from RCF ([tutorial](transfer_files_rcf.md))
__*IMPORTANT:Either make sure ssh-agent has key loaded or use option '-i' to give private key explicitly*__

These commands should be executed on the machine you want to upload/download files to/from the RCF nodes
*Note:* wildcard expansions are not processed by sftp unless used inside double quotes [see examples here](transfer_files_rcf.md)

- Download: `sftp "username@sftp.sdcc.bnl.gov:/rcf/path/to/file /local/path/to/copy/to/`
- Upload: `echo "put <Source File>" | sftp <username>@sftp.sdcc.bnl.gov:<Full Path>/`
- *Interactive session*: `sftp username@sftp.sdcc.bnl.gov`

### Long Summary
Logging into BNL consists of several steps.  Before starting one must fill out a request form to get a valid account with BNL Scientific Data and Computing Center (SDCC) otherwise or perhaps formerly called RHIC Computing Facility (RCF). Requesting a new account can be done with the link above. Once you get an email confirming your account you are ready to log in.

Log in is done using a program called ssh which according to wikipedia;
> \[SSH\] is a cryptographic network protocol for operating network services securely over an unsecured network ... The IANA has assigned TCP port 22, UDP port 22 and SCTP port 22 for this protocol  

On many systems this is pre-installed.  Before continuing here please check out this [tuturial](windows_setup.md) to setup ssh on Windows 10 with Powershell.  Windows before Windows 10 required a third party software called PuTTY.  If you are using a Windows computer without Windows 10 you can find PuTTY [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/).  There are tutorials interleaved in the various guides for using PuTTY so I will not go into details here because Powershell with OpenSSH is the recommended and easiest method since it allows for \*NIX style commands.

In order to log in you must pass through two layers to get to the servers where you will be working.  The first layer is simply a gateway node.  __**No actual work should be done on the gateway nodes**__.  Gateway nodes for STAR work are the main RCF one *ssh.sdcc.bnl.gov* and the NoMachine nodes *nx.rcf.bnl.gov*.  To find the most recent available ssh gateways check [here](https://www.sdcc.bnl.gov/information/ssh/ssh-gateways). There is also a special STAR gateway node *stargw.starp.bnl.gov* that can be used to log into the online machines at STAR which are to be used only when you are working with raw data from the detectors.  This tutorial will not discuss access to this gateway since it is only for very specialized use.

__EDIT (March 9, 2021): When RCF switched to a unified organization "Scientific Data and Computing Center" (SDCC) not only did the gateways change they also got rid of the NX gateways in favor of new ways of using NoMachine briefly discussed here. For new instructions go [here](rcf_remote_login.md).__

These instructions will guide you through most of these steps with the relevant links.

1. Create a public and private key and upload to SDCC/RCF database as described [here](generate_keys.md)
2. Load the key into an ssh-agent or give private key file directly to ssh command.  All described [here](ssh_agent.md)
	- Optionally you can create an ssh config file to make login in easier, [instructions](ssh_config.md)
3. SSH into the desired gateway.
	- For the *ssh.sdcc* gateway the command is `ssh username@ssh.sdcc.bnl.gov`
		+ Where *username* should be replaced by the user name of your SDCC account.
		+ Upon success you will see a warning message that shows you have logged into a Federal computer system and see a terminal prompt that includes text like *username@rsshXX*, where 'XX' will be replaced by whatever gateway number you have logged into.
	- For NoMachine stop and follow instructions [here](rcf_remote_login.md) instead.  Once you get to the virtual desktop open a terminal and continue with step 4.
4. On the gateway prompt, SSH into one of the analysis nodes named *rcasXXXX* where 'XXXX' needs to be replaced with the number of the node you want to log into.  Here I demonstrate STAR node *rcas6005*, `ssh rcas6005`
	- If successful, you will be prompted for your password.  Type it in and you will be on the rcas nodes.
	- There are several nodes that can be used by STAR analyzers.  The least populated node can be accessed by using the command `rterm -i` on the gateway prompt.  The drawback is that any command history, work, running processes, etc. executed on one node __**cannot**__ be accessed from another.
5. Finished: You have logged into RCF nodes; Enjoy :)

Useful SSH options
----------------------
The ssh command has lots of useful options to help facilitate certain extra benefits.  My recommended and preferred way is to know what these options mean and which ones you will want or need, then write your own ssh config file so you don't have to type out all the options.  This is detailed [here](ssh_config.md)

### Identity File
If you have no agent running or it is not working for some reason then you can specify the private key file for ssh to use for the connection.  The option for this is '-i'; e.g. `ssh -i /path/to/private/key username@ssh.sdcc.bnl.gov`

### Port Forwarding
Port Forwarding allows you to specify a port for ssh to forward the connection to; effectively means that ssh will listen in on a different port.  This is needed for No Machine.  The option is '-L port:host:hostport'.  For RCF use hostport will always be the ssh port 22 and host will be *nx.rcf.rhic.bnl.gov*.  The port should be chosen to be a random high numbered open port (between 4096 and 65535) on your desktop.  e.g. for No Machine `ssh -L 7025:nx01.rcf.bnl.gov:22 username@ssh.sdcc.bnl.gov`.

### Pseudo-Terminal allocation to bypass gateway and go directly to RCAS node
A pseudo-terminal is basically a terminal that will run in the background as the ssh connection is established.  The purpose of this is to execute a command on the remote machine before a prompt shows up.  This means you can bypass the gateway and go right into rcas nodes with one ssh command.  The ssh option for pseudo-terminal is '-t', e.g. `ssh -t username@ssh.sdcc.bnl.gov ssh rcas6005`

### SSH Agent forwarding
SSH allows you to forward the loaded private keys from your running [ssh-agent](ssh_agent.md) so that if your key is needed beyond the gateway you can do this.  The SSH option for forwarding the keys from your running ssh-agent is '-A', e.g. `ssh -A username@ssh.sdcc.bnl.gov`

### X11 Forwarding
X11 is graphical window system you can use to display files on RCF.  Note that because of the dual tunnel, X11 runs slow and should be used for looking at things quickly.  The preferred graphical method for long term use is [no machine](rcf_remote_login.md).  Instructions for how to setup and use X11 can be found [here](setup_xwindow.md).  The other option is to just download the files from RCF to your local machine for which instructions can be found [here](transfer_files_rcf.md).  The X11 forward option is '-X' for untrusted servers and '-Y' for trusted servers, e.g. `ssh -X username@ssh.sdcc.bnl.gov`.

Any program that requires a graphical display will automatically start an "X" window for it's graphical display.  To manually open image files like *png*, *jpg*, etc. use the command `display`, e.g. `display myimage.png`.  Need to install a separate utility to open *pdf* files but that utility will utilize the X11 connection.

### Combining Options
SSH options can be combined.  You only need one *-* followed by option character.  You can also use multiple dashes separated by a space.  For instance pseudo-terminal allocation with ssh-agent forward and X11 forward can be done simultaneously with the following two ways.
- Combined dashes: `ssh -tAX username@ssh.sdcc.bnl.gov ssh rcas6005`
- Separate dashes `ssh -A -X username@ssh.sdcc.bnl.gov -t ssh rcas6005`

Both are acceptable.

