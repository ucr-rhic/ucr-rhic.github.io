How to login in using Windows 10 Powershell with OpenSSH
=========================================================

Windows 10 introduced many \*NIX style functionality.  This means you no longer need cygwin, virtual OS, or dual boot linux on Windows 10 to get many of \*NIX style commands.  Powershell on Windows has aliases to the most well known linux commands which makes the transition from linux to Powershell fairly simple with the exception of a few commands that are unique to Windows.

This tutorial will install the necessary components to get \*NIX style ssh to work on Windows 10 using Powershell.  First you need to install and start the OpenSSH service.

Install and Enable OpenSSH Service
----------------------------------
[source](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)
[source](https://www.howtogeek.com/336775/how-to-enable-and-use-windows-10s-built-in-ssh-commands/)
[source]https://stackoverflow.com/questions/52113738/starting-ssh-agent-on-windows-10-fails-unable-to-start-ssh-agent-service-erro
1. Open *Settings* __NOT__ *Control Panel* on Windows
2. Click on *Apps*
3. Under the heading "Apps & features" click *Optional features* and wait for it to load
4. Two features are needed *OpenSSH Client* and *OpenSSH Server*
	1. To install click *Add a feature* at the top of the list page
	2. Search for the missing features and install them
5. Once the features are installed both need to be started
	- The service name is *OpenSSH SSH Server* and *OpenSSH Authentication Agent*.  The steps for either is the same so I will use *OpenSSH SSH Server* as the example
	1. Open up the Windows *Services* App
	2. Find the service *OpenSSH SSH Server* and right click on it then select *Properties*
	3. Look for a drop-down menu called *Startup Type*
	4. Select from the drop-down list *Manual* or any other __except__ *Disabled*
	__NOTE:__ If you select *Manual* you will have to manually start this service every time you restart Windows
6. Start the two services *OpenSSH Client* and *OpenSSH Server*
	- This can be done through Powershell using `Start-Service` (*Note: May need to start as admin*)
		1. `Start-Service sshd`
		2. `Start-Service ssh-agent`
	- It can also be done using *Services* App mentioned above or through Windows Task Manager in the Services Tab.
		1. Right click on service then click *Start*
7. Finished you can run, on Powershell, the commands `ssh`, `ssh-agent`, or `ssh-add`	

Configure Windows
------------------
[source](https://github.com/PowerShell/Win32-OpenSSH/issues/1088)
Due to the switch of the RCF gateways from rssh.rhic.bnl.gov to ssh.sdcc.bnl.gov you need to manually create a file on Windows to be able to ssh from Powershell to the ssh.sdcc.bnl.gov gateway.  This file should be left blank and only requires that it exist because ssh on linux checks for this file and so does ssh using *OpenSSH* on Windows 10.

1. First create the *dev* directory, using Powershell `mkdir $Env:HOMEDRIVE\dev`
2. Create a file called *tty* in *dev*, using Powershell `cd $Env:HOMEDRIVE\dev` then `cat "" > tty`.  You can ignore any error messages that come up as long as the file is created.

Configure Powershell (Optional)
--------------------------------
On \*NIX systems you can modify a login script to set up your environment and aliases.  Powershell has a similar concept but any login script you write will not be executed due to a "security" feature on Windows 10.  This "security" feature prevents any Powershell scripts from running unless it has a valid "signed certificate".  This tutorial will show you how to bypass this security feature as well as create your own "signed certificate" to authenticate your Powershell scripts.

The execution level by default is *Undefined*, which is the most restrictive type.  At the end of this tutorial you should switch to *AllSigned*, which is in my opinion, the the safest.  There is a *Bypass* mode which doesn't require "signed certificates" and is good for debugging your Powershell script but you should be cautious using this as a permanent solution.  Also, there are different *Scope*s, these scopes merely denote the user level that is executing the scripts.  All of these details are described [here](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7)

#### Change execution level
[source](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7)
- Check Powershell script execution level `Get-ExecutionPolicy -List`
	- The important one here is *Current User*.
- Set *Current User* level to *Bypass* using `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Bypass`
This is good for testing
	- Changing the arguments to *-Scope* will change which scope gets set
	- Changing the arguments to *-ExecutionPolicy* will change which execution policy gets set

#### Create your own "signed certificate"
The purpose of doing this is so that you can leave your Powershell script execution policy to *AllSigned*.  The certificate you create can be used to verify your script as a valid script that Windows can execute.  It can be used to verify your Powershell login script or any other script you write.
The certificate will last one year from date of creation.
Resources:
	- [Certificates](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/working-with-certificates)
	- [Make and Install Certificate](https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development)
	- [Make New Certificate](https://docs.microsoft.com/en-us/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps)
	- [View Certificate](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/viewing-test-certificates)
3rd Party Guides:
	- [Make Certificate](https://blogs.u2u.be/u2u/post/creating-a-self-signed-code-signing-certificate-from-powershell)
	- [Powershell Make and Export Certificate](https://4sysops.com/archives/create-a-self-signed-certificate-with-powershell/)
	- [Generic Make and Export Certificate](https://medium.com/the-new-control-plane/generating-self-signed-certificates-on-windows-7812a600c2d8)
	
1. Make a new code signing certificate using the command `New-SelfSignedCertificate -Type CodeSigningCert -Subject "MyCertificate" -CertStoreLocation Cert:\CurrentUser\My
	- Replace the 'MyCertificate' with whatever name you want
	- Powershell will output details of the created certificate showing the unique *Thumbprint* for the certificate
	- To view this certificate use command `ls Cert:\CurrentUser\My` and you should see any certificates created in *Cert:\CurrentUser*.
		- To see just your certificate use the Thumbprint of the certificate `ls Cert:\CurrentUser\My\[Thumbprint]`
		- The location *Cert:\CurrentUser\My* can be thought of as a directory location like *C:\Users\\* except that it can't be directly accessed.  This is why you need use the `ls` command; the *Thumbprint* acts like the filename
2. Open the certificate manager for *CurrentUser* using the command `certmgr.msc`
3. Navigate to where the created certificate will be in the folder titled *Personal* then under *Certificates*
4. Right click on the created certificate and navigate to *All Tasks* then click *Export*
5. Click *Next* to continue
6. Click *Yes, export the private key* and then click *Next*
7. Check the options on this screen and select as needed, the default ones will do but feel free to select any others; click *Next*.
8. Check the box marked *Password* and type in a suitable password into the password fields.  Default selected *Encryption* will do but you can change this; click *Next*.
9. Click *Browse* and enter a name for the export key and choose where you want to save it; click *Next*.
10. If everything looks good on the summary screen click *Finish*, if not, click *Cancel* and start over.

If you clicked *Finish* then you have created a password protected certificate.  In order to use this script to sign your Powershell scripts you need to "authenticate" this certificate as a valid signing certificate.  The next steps will show you how to do this using the created certificate from above.

1. Open the certificate manager if unopened by running command `certmgr.msc`
2. Click on folder marked *Trusted Root Certification Authorities*
3. Right click on sub-folder *Certificates* and go to *All Tasks* then click *Import...*
4. Click *Next*
5. Click *Browse*, then find and click the exported certificate from the steps above, then click *Open*
6. Type the password you used to create the certificate and select any other options you would like; click *Next*
7. Click *Next*
8. Click *Finish*.  You now have a certificate you can use to sign your Powershell scripts.

#### Sign your Powershell script with your "signed certificate"
[source]()
This assumes you have followed the steps above to create and export your own certificate.

1. Load the certificate from above using its *Thumbprint* with the command `$cert = ls Cert:\CurrentUser\Root\<Thumbprint>`.
	- This creates a local shell variable called "cert" whose value is the certificate.
2. Sign the file using the certificate `Set-AuthenticodeSignature MyPSscript.ps1 $cert`
	- *MyScript.ps1* is the Powershell script you want to sign.
3. Finished you can now set the execution policy to *AllSigned* and the script will still run.

#### Customize Powershell
- [Guide](https://www.howtogeek.com/50236/customizing-your-powershell-profile/)
- [Installing PowerShell7](https://www.howtogeek.com/663684/how-to-install-powershell-7-on-windows-10/)
Here I will briefly describe where your Powershell profile needs to go.  In Linux, this is done through a file in the home folder called either *.bashrc*, *.bash_profile* for bash shells or *.cshrc* for c-shell.  In Powershell the profile folder is in *%USERPROFILE%\WindowsPowershell* for Powershell 5 and *%USERPROFILE%\PowerShell* for Powershell 7.  The script name is *Microsoft.PowerShell_profile.ps1*.  You need to sign this Powershell script as described above if you want to keep the execution policy to *AllSigned*.
I will not go into details on how to write Powershell scripts since examples of Powershell codes can be found in many places.  I will only mention a few important things.  The first is how to customize the prompt.  This has to be done through a special function called *prompt*, rather than an environment variable like in \*NIX systems (*$PS1*).  The other thing is aliases.  Aliases in Powershell must match exactly one executable and cannot have extra options as part of the alias.  To make an "alias" that has extra options you must write a function.

##### Examples

1. An example of a prompt function that displays the history number, the date, and the current directory with some coloring options

```powershell
#The prompt function determines how to display the command prompt
#"PS $($executionContext.SessionState.Path.CurrentLocation)$('>' * ($nestedPromptLevel + 1)) ";
# .Link
# https://go.microsoft.com/fwlink/?LinkID=225750
# .ExternalHelp System.Management.Automation.dll-help.xml
function prompt
{
  $identity = [Security.Principal.WindowsIdentity]::GetCurrent()
  $principal = [Security.Principal.WindowsPrincipal] $identity
  $adminRole = [Security.Principal.WindowsBuiltInRole]::Administrator
  $printdate = Get-Date -Format "ddd MMM dd HH:mm"     #Date to print for format see:https://docs.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings?view=netframework-4.8
  #For color prompts see: https://superuser.com/questions/1259900/how-to-colorize-the-powershell-prompt
  #Basically works just like bash where you need escape character below plus color code found here: https://docs.microsoft.com/en-us/windows/console/console-virtual-terminal-sequences#span-idtextformattingspanspan-idtextformattingspanspan-idtextformattingspantext-formatting
  $ESC = [char]27
  $colordate = "$ESC[01;35m["+ $printdate +"]$ESC[00m"
  
   $historylevel = @(Get-History).Count
   $historylevel = $historylevel + 1  #This is so the history 'Id' match

  #Having a custom prompt command will prevent the conda environment from being shown on the "prompt" the solution for this is given here where you use the '$ENV:CONDA_PROMPT_MODIFIER' variable to recover it [ref Aug. 15, 2021](https://gitmemory.com/issue/conda/conda/10860/899136290)
  #Also in the same link is a command for how to output only current directory
  $(if (Test-Path variable:/PSDebugContext) { '[DBG]: ' }
    elseif($principal.IsInRole($adminRole)) { "[ADMIN]: " }
    else { '' }
    ) +
	"$ENV:CONDA_PROMPT_MODIFIER" +
	"$ESC[0;32mPS"+$PSVersionTable.PSVersion.Major + 
	"(" + $historylevel + ")$ESC[00m " +
	$colordate + " " + 
	#"$ESC[01;32m$Env:USERNAME@$Env:COMPUTERNAME$ESC[00m" + ':' + 
	"$ESC[0;34m"+$(Split-Path -leaf -path (Get-Location)) + "$ESC[00m" +
    $(if ($NestedPromptLevel -ge 1) { '>>' }) + '> '
}
```

2. Bash like tab completion [Src](https://stackoverflow.com/questions/8264655/how-to-make-powershell-tab-completion-work-like-bash): `Set-PSReadlineKeyHandler -Key Tab -Function Complete`


3. Alias for explorer.exe for opening files and directories (Use Shell.Application COM in future?), [Src](https://dev.to/ofhouse/add-a-bash-like-autocomplete-to-your-powershell-4257): `New-Alias -Name open -Value 'Invoke-Item'`

4. Function to directly log into rcas node. No arguments means least occupied node, single argument to indicate node number; e.g. `star_login 6010`.

```powershell
function star_login
{
	[CmdletBinding(SupportsShouldProcess=$true)]
	param
	(
		[parameter(Mandatory=$false)]
		[String]$ServerNode
	)
	process
	{
		#Source for checking valid parameter:https://stackoverflow.com/questions/48643250/how-to-check-if-a-powershell-optional-argument-was-set-by-caller
		if( !($PSBoundParameters.ContainsKey('ServerNode')) ){ $ServerNode = "rterm -i" }
		#STAR nodes are on 60XX where X is some digit (Help on Regex:https://powershell.org/forums/topic/regex-on-if-statement/)
		elseif( $ServerNode -match "60\d{2}" ){ $ServerNode = "ssh rcas${ServerNode}" }
		else{ echo "Invalid input"; return; }
		ssh user@ssh.sdcc.bnl.gov -At $ServerNode; #Here user would be your username and assumes you have the proper key loaded
	}
}
```

