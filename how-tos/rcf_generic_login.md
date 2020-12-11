Logging into RCF
==================

Logging into RCF consists of several steps.  Before starting one must fill out a request form to get a valid account with BNL Scientific Data and Computing Center (SDCC) otherwise or perhaps formerly called RHIC Computing Facility (RCF).  Once you get an email confirming your account you are ready to log in.

Log in is done using a program called ssh which according to wikipedia;
> \[SSH\] is a cryptographic network protocol for operating network services securely over an unsecured network ... The IANA has assigned TCP port 22, UDP port 22 and SCTP port 22 for this protocol  

On many systems this is pre-installed.  Windows before Windows 10 required a third party software called PuTTY.  If you are using a Windows computer without Windows 10 you can find instructions for using PuTTY [here](placeholder).

In order to log in you must pass through two layers to get to the servers where you will be working.  The first layer is simply a gateway node.  __**No actual work should be done on the gateway nodes**__.  Gateway nodes for STAR work are the main RCF one *ssh.sdcc.bnl.gov* and the NoMachine nodes *nx.rcf.bnl.gov*.  To find the most recent available ssh gateways check [here](https://www.racf.bnl.gov/docs/services/Ssh/gateways). There is also a special STAR gateway node *stargw.starp.bnl.gov* that can be used to log into the online machines at STAR which are to be used only when you are working with raw data from the detectors.  This tutorial will not discuss access to this gateway since it is only for very specialized use.

After you log in to the gateway you need to log into one of many other nodes that you can use for analysis or for setting up No Machine virtual desktop.

These instructions will guide you through most of these steps with the relevant links.

1. Create a public and private key and upload to SDCC/RCF database as described [here](generate_keys.md)
2. Load the key into an ssh-agent or give private key file directly to ssh command.  All described [here](ssh_agent.md)
	- Optionally you can create an ssh config file to make login in easier, [instructions](ssh_config.md)
3. SSH into the desired gateway.
	- For the *rssh* gateway the command is `ssh username@ssh.sdcc.bnl.gov`
		+ Where *username* should be replaced by the user name of your SDCC account.
		+ Upon success you will see a warning message that shows you have logged into a Federal computer system and see a terminal prompt that includes text like *username@rsshXX*, where 'XX' will be replaced by whatever gateway number you have logged into.
	- For No Machine gateway stop and follow instructions [here](rcf_remote_login.md) instead.  Once you get to the virtual desktop open a terminal and continue with step 4.
		+ All the following commands should be executed from the virtual desktop terminals.
		+ __**IT IS BEST NOT TO EXECUTE ANY COMMANDS ON THE TERMINAL YOU USED FOR PORT FORWARDING TO SET UP NO MACHINE EXCEPT TO TERMINATE THE CONNECTION**__
		+ Once you are done with No Machine you can terminate the port forwarding connection using `exit`.
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

Summary
----------
### Connect RCAS with X11
1. Load Private key into ssh-agent
2. Run `ssh -tAX username@ssh.sdcc.bnl.gov rterm -i`

### No Machine Connect
1. Load Private key into ssh-agent
2. Run `ssh -L <port>:nx.rcf.bnl.gov:22  username@nx.rcf.bnl.gov`
	- *port* should be an open port with a high value random number between 4096 and 65535 and is the port to use for port forwarding.  It should match the saved port number in your No Machine connect file or for first time pick one and stay with it.
3. Start No Machine
4. If you have a previously saved connection click on that and done. If not follow these instructions to create a new connection
	i. Select New Connection
	ii. Select the 'SSH protocol' option
	iii. Enter Host - `localhost` and Port - *port* number used above.
	iv. Authenticate using the 'Password' option
	v. HTTP Proxy - Do not use proxy option
	vi. Specify any general name for the connection and click on the Done button.
	vii. Connect and login with the RACF kerberos credentials. Click on the New desktop, select KDE virtual desktop and proceed.

### Transfer Files using scp to and from RCF ([tutorial](transfer_files_rcf.md))
__**IMPORTANT:Either make sure ssh-agent has key loaded or use option '-i' to give private key explicitly**__

These commands must be executed on your laptop terminal not the RCF nodes or gateways
- Download: `scp username@rftpexp.rhic.bnl.gov:/rcf/path/to/file /local/path/to/copy/to/`
- Upload: `scp /local/file/to/copy username@rftpexp.rhic.bnl.gov:/rcf/path/to/copy/to/`
- __BONUS__: `sftp username@ssh.sdcc.bnl.gov`

### Display image files with existing X11
1. `display myimage.png`
