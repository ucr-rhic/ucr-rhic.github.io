Using an ssh config file to bypass passing options to ssh
============================================================

An ssh config file is a special file that can be used to tell what options to pass to the ssh command.  This tutorial will show you how to write an ssh config file that automatically passes the correct options to log into RCF either using ssh or for nx connections.

The main reason for doing this is to bypass having to type in all the ssh options when giving the command on the terminal and reduce it to a much shorter and easier to remember form.  The ssh config will set up a kind of profile that can be then be used as alias to the ssh command.  e.g. if I have profile with name "MyConnection" I need only execute `ssh MyConnection` to establish the connection rather than something like `ssh -AX user@ipaddress`.

An ssh config file is used by any \*Nix system.  This means Ubuntu, Mac OS, Windows with OpenSSH installed, Windows subsystem for Linux (WSL), etc.


Basic Structure
----------------

An ssh config file is just a text file.  The file itself should have no extension and be named __config__.  It must be placed in the home directory folder __.ssh__.

1. Open or create a file named __config__ and put into home directory folder named __.ssh__ `cat ~/.ssh/config`
	- *cat* command will echo the file if it exists if not then you will have to create one using `touch ~/.ssh/config`
2. Add the connection details per example below
	> Host \<ConnectionName\>  
	> HostName \<Address\>  
	> User \<UserName\>  
	> \#Hashtags denote comments and will be ignored
	- The text following the keyword *Host* is the alias you will use when you want to call ssh on this connection
	- The text following keyword *HostName* is the IP address either as numbers or letters that you would like to connect to
	- The text following *User* is the user name for that connection
	- The connection details above are equivalent to `ssh UserName@Address`
3. More than one connection can be specified by an empty line between connection names and the last option
	> Host Connection1  
	> HostName ip1.ip1.ip1  
	> User user1  
	>
	> Host Connection2  
	> HostName ip2.ip2.ip2  
	> User user2  
4. Call ssh on the desired connection name `ssh ConnectionName`

Other Useful Options
---------------------

Here I list the other options that would be useful for RCF connections

- __IdentityFile \<FileName\>__ -> The ssh private key file to use for the connection.  *FileName* must be full path and name of private key (equivalent option '-i')
- __ForwardAgent__ \<yes/no\>-> Forward the ssh agent so that any subsequent ssh connections will act as if the private key is loaded into the connection.  Must be followed by either *yes* (equivalent option '-A') or *no*.
- __ForwardX11 \<yes/no\>__ -> Forward X11 connections.  Must be followed by either *yes* or *no*.
- __ForwardX11Trusted \<yes/no\>__ -> Specify whether to use X11 trusted option or not [see](setup_xwindow.md).  Must be followed by *yes* (equivalent option '-Y') or *no* (equivalent option '-X')
- __LocalForward \<ForwardPort IpLocation:Port\>__ -> Specify port forwarding
- __RequestTTY \<yes/no\>__ -> Ask for a pseudo-terminal to execute commands on remote connection even before login (equivalent option -t)

RCF Example
-----------
Below is an example showing how to write the config file for connecting to RCF ssh gateway *ssh.sdcc.bnl.gov*.  To find the most recent available ssh gateways check [here](https://www.racf.bnl.gov/docs/services/Ssh/gateways)
> Host MyRcfConnection  
> HostName ssh.sdcc.bnl.gov  
> User username  
> IdentityFile /path/to/key  
> ForwardAgent yes  
> ForwardX11 yes  
> ForwardX11Trusted yes  
> RequestTTY yes
- *MyRcfConnection* should be replaced with any name you want to give it
- *username* should be replaced by your user name
- */path/to/key* should be replaced by the location and name of your private key file.
- By including the pseudo terminal you can quickly bypass gateway and go straight into analysis node with command `ssh MyRcfConnection rterm -i`
	+ *rterm -i* can be replaced with *ssh rcasXXXX* where XXXX is the node to connect to.

NoMachine Example
------------------
Below is an example showing how to write the config file for connecting to RCF gateway and allowing port forwarding for nomachine (nx).
> Host MyNxConnection  
> HostName ssh.sdcc.bnl.gov  
> User username  
> IdentityFile /path/to/key  
> ForwardAgent yes  
> LocalForward 7025:nx01.rcf.bnl.gov:22  
- *MyNxConnection* should be replaced with any name you want to give it
- *username* should be replaced by your user name
- */path/to/key* should be replaced by the location and name of your private key file.
- For option __LocalForward__ *7025* should be replaced with a random high numbered port and *nx01* can be replaced as well all which is explained [here](rcf_remote_login.md)
- You can now set up the tunnel using `ssh MyNxConnection`

