Generate A New Key Pair
===========================
Ref: [RCF tutorial](https://www.racf.bnl.gov/docs/authentication/ssh/keygen)

SSH key authentication is needed to log into the RHIC computing facility (RCF) gateway *rssh.rhic.bnl.gov*

SSH keys are a convenient way to authenticate users logging into remote machines.  The idea is that there is a private and public key.  The public key is kept on the server/remote machine you want to log into.  The private key you keep and typically requires a pass-phrase to use.  Although you can generate a private key without a password this will defeat the purpose of using key pairs to authenticate.

The public and private keys are paired in that they are unique "hashes".  I will not go into the algorithms responsible for this but you can have different levels of security based on different algorithms.  The generating algorithm can be modified with the key generating command that I will use.  These instruction create a hash using the default which is good enough for our purposes.

The authentication happens when the server you are logging into checks the hash of the public key against your private key.  Since no one but you knows the password to your private key this ensures that the one logging in is in fact the person who created the public key.
**So don't share your private key password or private key it is the only thing that tells the remote machine you are you and not someone else**

Linux and Windows 10 machines that have OpenSSH installed
-----------------------------------------------------------
1. Open a terminal window (Powershell recommended on Windows) on the desktop machine or laptop that you will be using to login to the RCF/ACF.

2. At the prompt, type: `ssh-keygen -t rsa`
	- You will see output similar to the following: "Generating public/private rsa key pair."

3. Enter location and file in which to save the key
	- To accept the default file name and location (*~/.ssh/id_rsa*) press *Return/Enter*.
	- This command will generate an RSA key of the default length for use with ssh protocol version 2

5. At the Enter pass-phrase prompt, type in a pass phrase, which will not be echoed as you type, and then press *Return/Enter*.
	- This pass phrase will be used to unlock your private key file (failing to enter a pass phrase for your key will, of course, defeat all security related to the key pair).
	- You will be prompted to verify the pass phrase by entering it again. Retype your pass phrase, and then press Return.

6. 	<a name="KeyFingerprint"></a>The key pair will be generated, and you will see output similar to the following:
	> Your identification has been saved in *~/.ssh/id_rsa*.  
	> Your public key has been saved in *~/.ssh/id_rsa.pub*.  
	> The key fingerprint is: 99:99:99:99:99:99:99:99:99:99:99:99:99:99:99:99 \<user\>@\<machine.name\>	
	- The actual fingerprint for your key will be displayed (not the one shown in the example above), and where the terms in brackets (<>) are replaced by the values appropriate for your machine.
	- Two files will be created:
		1. The first file is the private key, with the default name (or the name you entered above).
		2. The second file is the public key, with .pub appended to the file name.

	**Important Note:**
	- The key may not look like that and instead look something like below.
		> SHA256:AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA \<user\>@\<machine.name\>  
		> The key's random art image is:  
		>
	- This is normal since your laptop is displaying using the 'SHA256' standard instead of the *md5* standard.  To see the *md5* standard run the command `ssh-keygen -lf key.pub -E md5`
		- [Command Ref](https://superuser.com/questions/929566/sha256-ssh-fingerprint-given-by-the-client-but-only-md5-fingerprint-known-for-se)	

7. Copy the key fingerprint value from the output above.  To upload the key, browse to [RCF key upload](https://web.racf.bnl.gov/Facility/SshKeys/UploadSshKey.php)
	+ In order to view the form, you will be prompted for your Kerberos user name and password.

8. Click the Browse button, and in the dialog box, navigate to your .ssh directory (or the directory in which your public key file is stored).  If your browser does not display hidden directories (ones that begin with a period), then you will have to type in or cut and paste the name of the public key file into the dialog box. Enter the full name of the public key file (as displayed in output earlier), including the path and the .pub file extension (if you copy and paste the name or path, take care to leave off the period at the end of the line with the public key file name).

9. Copy and paste the fingerprint of your public key (as displayed in output earlier) into the second box in the form, or type it into the dialog box. The *md5* key is comprised of 16 2-digit hexadecimal numbers separated by colons (:).

10. To upload your key file, click the Send File button.

11. You can now login to one of the gateway machines using SSH keys using command below. You will be prompted for the pass-phrase for your private key during the login process. The pass-phrase will not leave your local machine.
	+ `ssh -i ~/.ssh/id_rsa/ username@rssh.rhic.bnl.gov`
	
12. In order to prevent having to type the password every time you can add the key to the *ssh-agent*  [see here](ssh_agent.md)

Most Windows versions using PuTTY
----------------------------------
**This assumes you have downloaded all PuTTY tools from [here](https://www.chiark.greenend.org.uk/~sgtatham/putty/)**

Disclaimer: PuTTY is 3rd party telnet/ssh tool for Windows, however due to Windows 10 adding Linux capabilities I no longer advise this route but leave it here for legacy and backward compatibility reasons.

1. Run the executable `puttygen.exe`
	- Default options when starting are fine
2. Press *Generate*
	- Follow instructions from program to generate key
3. Once key is generated you will see the key fingerprint as [above](#KeyFingerprint)
4. Enter a pass-phrase for the key in the box labeled as such
5. <a name="SavePuttyKey"></a>**Important** Click *Save public key* and give name and location (keep .pub extension [see](#KeyFingerprint)) Click *Save private key* and give name and location same as public key without *.pub* extension.
6. Finished: you can use this key to login into RCF using `putty.exe` go [here](placeholder) to see how to do so.

### Convert keys from Linux to Windows PuTTY keys
I leave this here in case you had a public private key pair generated with Linux and now want to use it on Windows with PuTTY or vice versa.

**Important: This is only for private keys.  The public key doesn't need to be converted and should be left unchanged since it is already sitting at the remote machine.  To use a different public key generate a new key pair and upload that.**
#### Linux to Windows
1. Run the executable `puttygen.exe`
2. Click *Load* follow prompts to select **Linux private key** and enter pass-phrase
2. On top bar click *Conversions* then *Import key*
3. Select Linux key you want to convert and follow instructions from prompts to enter pass-phrase
	- This will load the key into the program
4. You will see output similar to [above](#KeyFingerprint)
5. Follow these [instructions](#SavePuttyKey)
#### Windows to Linux
1. Run the executable `puttygen.exe`
2. Click *Load* follow prompts to select **PuTTY private key (.ppk)** key and enter pass-phrase
3. On top bar click *Conversions* then *Export OpenSSH key*
	- There might be several "Export" options just select the first one and it it doesn't work then go back and try the others.
4. Give the key a name with no extensions
5. Move the converted private key to your Linux machine to use.
