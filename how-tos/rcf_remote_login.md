Remote Desktop for RCF Using NX/NoMachine
------------------------------------------------

__EDIT (March 9, 2021): RCF transitioned to SDCC so now new login methods are requried. Old instructions have been strikethroughed but kept for archival reasons.__

Ref: [see here](https://www.sdcc.bnl.gov/information/services/how-use-nx-sdcc)

Remote desktop access to RCF is supported by the latest NX technology compared to classic VNC session. RCF has partnered with `NoMachine` to provide the service.

As of March 9, 2021, the SDCC NX servers can be accessed via web browser or the NoMachine client.

To use either option, one needs to set up multifactor authentication (MFA) with SDCC for which instructions can be found [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication). __NOTE__:This MFA is different than the one for STAR drupal login.

**Steps for Web Browser**

1. Set up multifactor authentication (MFA) with SDCC [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication)
2. Go to [https://nx.sdcc.bnl.gov/nxwebplayer](https://nx.sdcc.bnl.gov/nxwebplayer)  
3. Follow instructions below starting with *[step 9](#NxLoginStep)*  

**Steps for Linux / Instructions for NoMachine Client (NoMachine 6 version)**

Download the latest NoMachine Client Software from the www.nomachine.com page
```
Downloads -> Enterprise Products Evaluation -> NoMachine Enterprise Client
```
`DO NOT` download and install the whole package (just do client), otherwise it will silently start `nx` server on your computer.

1. Set up multifactor authentication (MFA) with SDCC [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication)  
2. Start the NoMachine Client Application.  
3. Select New Connection  
4. Select the 'NX protocol' option  
5. Enter Host - `nx.sdcc.bnl.gov` and Port - `4000`  
6. Authenticate using the 'Password' option  
7. HTTP Proxy - Do not use proxy option  
8. Specify any general name for the connection and click on the Done button.  
<a name="NxLoginStep"></a>
9. Connect and login first entering your RACF kerberos credentials.  
10. After entering those credentials it will then ask you for another password. This will be the password from the MFA you setup. Enter it  
    - To setup MFA (not the same as STAR drupal) follow instructions [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication)  
11. Pick *Create a new KDE desktop* or another desktop of your liking  
12. Select *Automatically select a node* or pick your favorite node :)

## How to Make Use of Tunnels in Visual Code Studio
-----------------------------------------------------
 Visual Studio Code is great for ease of access and convenience in your workspace. In the tutorial listed [here](https://github.com/ucr-rhic/ucr-rhic.github.io/blob/master/how-tos/transfer_files_rcf.md#mount-the-remote-file-system-windows), we can mount remote directories onto a drive on our local machine using `sftp`. This is great as it makes tranferring files very easy, with just a drag and drop as opposed to using the commands in sftp. Additionally, because the files are mounted on your drive, you can make any edits you make while connected will transfer over to your remote drive as long as you remember to save. However if you want to run commands a STAR analysis that requires STAR libraries, you need to run them on the remote machine. As such, you would need to `ssh` onto SDCC and run your analysis there. This works fine, but it can get a bit inconvenient to have to consistently swap terminals between your local and remote machines for editing, running, general directory navigation, etc. This is exacerbated if locally you are running a particular operating system, since the terminal formats and commands can be different from your remote and local machines.

One way around this is to use [NoMachine](rcf_remote_login.md). NoMachine works fine, but feedback can be a bit poor due to latency issues. In addition, everytime there is a disconnect, you are required to log in again and re-enter your MFA code.

Usage of tunnels in Visual Studio Code (VS Code) allows you the convenience of a remote login like NoMachine, but with the added benefit of being able to use any machine you like. You can even make edits even through your browser if you don't have VS Code locally. Also, you don't need to repeatedly login everytime you disconnect unless you shutdown the tunnel. Lastly, with VS Code, you can make use of all of the extensions that it has to offer, which can help writing code quite a bit.

These instructions will go over how to setup the tunnel with VS Code on your remote machine. [Source](https://code.visualstudio.com/docs/remote/tunnels)

1. Log into SDCC using your desired method [here](https://github.com/ucr-rhic/ucr-rhic.github.io/blob/master/how-tos/rcf_remote_login.md)
   - I would **HIGHLY** recommend that you login via NoMachine. The reason for this is that if you just `ssh` from your local terminal, you will at some point need to authenticate the tunnel using your GitHub account. When you are done with your session and exit, or if there is a network disconnect, the tunnel will be killed. As such, you will need to restablish the tunnel again on the remote side by reauthenticating the tunnel via GitHub. This adds tedium, especially when running VS Code on your local machine. Instead, load up a terminal on NoMachine and follow all of the steps below. When you are done with all the steps, click the exit button at the top right of the NoMachine window. Click `Disconnect`. This will perpetually leave the tunnel open on the remote end, meaning you don't have to restablish and verify the tunnel from the remote side on GitHub again.
2. Download and unpack the VS Code Client. I'm going to do this step in the `~/Downloads/` folder to reduce clutter, but feel free to do this wherever you want.
   1. From the terminal in the chosen directory, run:

          curl -Lk 'https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64' --output vscode_cli.tar.gz
          tar -xf vscode_cli.tar.gz
      
3. Run `code tunnel`. This will ask if accept the terms and conditions. Read through them if you like and when ready, type `y`.
   1. You can run `code tunnel --accept-server-license-terms` instead if you don't care about the license terms.
4. This will prompt you to login into GitHub and enter a code for verification. Enter in the code given in the terminal.
5. On the next page, click the option `Authorize Visual-Studio-Code`. Once this is done, go back to the terminal. Name your device whatever you like and hit `enter`.
6. This will output a `vscode.dev` URL. You can open this link anywhere on your local machine. This will prompt you to sign into GitHub on your local machine if you haven't already. This will authenticate your tunneling connection from the local end. 
   
From here, you can use VS Code from your remote server through your web browser. However, if you would like to connect the tunnel from the remote to your local instance of vscode, do the following:

1. Download and install [VS Code](https://visualstudio.microsoft.com/) on your local machine if you don't have it already.
2. Go to the extensions icon on the left of the screen and click it. In the extensions marketplace, search "Remote-Tunnels" and install it.
3. Click on the new remote explorer icon on the left of the screen. If you are not already signed into GitHub, an click on the arrow of the github option next to "Sign in to see the registered tunnels".
4. In the new window that pops up, Click "allow" and sign into you github account. This should take you back to vscode with a new window that says "Allow an extension to open this URI?" Click "open".
5. Now, in the remote explorer menu, click on the arrow next to the tunnel you named earlier. This will open a connection to your remote in your current window.

For most cases, this installation works fine. However, when you try to run `code "filename"` you might get this error:

    No installation of Visual Studio Code stable was found. 
    Install it from your system's package manager or https://code.visualstudio.com, restart your shell, and try again.
    If you already installed Visual Studio Code and we didn't detect it, run `code version use stable --install-dir /path/to/installation` 

To fix this error, what works is to run this command:

    code version use stable --install-dir ~/.vscode/cli/servers/Stable*Whatever the number is here*/server/bin/remote-cli/code

If for whatever reason this still doesn't work and you get an error saying that code can't be found in the path, then add the following to your .cshrc file:

    setenv PATH "$HOME/.vscode/cli/servers/Stable*Whatever the number is here*/server/bin/remote-cli:$PATH"

Otherwise, try a fresh install of VSCode.

You have now succefully tunneled into the remote machine with VS Code on your local machine. Here is some of what you can do with this:
* If you want to access the remote machine, all you need to do is open VS Code and select your tunnel in the remote explorer menu. As long as you setup everything properly, you don't need a password.
* Opening up a terminal will open a terminal on the remote machine.
* Clicking on the explorer icon on the left of the screen will let you open up any directory on you remote machine. You can easily scroll through and see all your files.
* Clicking on the source control icon on the left of the screen will let keep track of any git repositories in your workspace. You can easily look at all of your files with changes, what is in the staging area, and what has been committed. You can easily add and commit files by selecting the appropriate options as well.
* Extensions are very convenient. They provide a lot of great features for you, like suggestions, tab-to-complete, file viewers, etc. 
* The command `code [filename]` will open up any file you have in VS Code, as long as you have the appropriate extension installed in VS Code. As such, you don't need to have an X11 forwarding service installed on your machine like XMing if you are trying to view a pdf, png ROOT file, etc. VS Code will handle everything for you. 

