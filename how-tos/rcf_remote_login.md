Remote Desktop for RCF Using NX/NoMachine
------------------------------------------------

__EDIT (March 9, 2021): RCF transitioned to SDCC so now new login methods are requried. Old instructions have been strikethroughed but kept for archival reasons.__

~~Ref: [see here](https://www.racf.bnl.gov/docs/services/nx)~~  
Ref: [see here](https://www.sdcc.bnl.gov/information/services/how-use-nx-sdcc)

Remote desktop access to RCF is supported by the latest NX technology compared to classic VNC session. RCF has partnered with `NoMachine` to provide the service.

~~As of December 2017, all the RHIC production NX servers - `nx01.rcf.bnl.gov, nx02.rcf.bnl.gov, nx06.rcf.bnl.gov and nx07.rcf.bnl.gov` are running NoMachine 5 server software - version 5.3.9.~~

As of March 9, 2021, the SDCC NX servers can be accessed via web browser or the NoMachine client.

To use either option, one needs to set up multifactor authentication (MFA) with SDCC for which instructions can be found [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication). __NOTE__:This MFA is different than the one for STAR drupal login.

**Steps for Web Browser**

1. Go to [https://nx.sdcc.bnl.gov/nxwebplayer](https://nx.sdcc.bnl.gov/nxwebplayer)  
2. Follow instructions below starting with *[step 9](#NxLoginStep)*  

**Steps for Linux / Instructions for NoMachine Client (NoMachine 6 version)**

~~**Part-1: Establish the tunneling**~~

~~`ssh -L 7025:nx01.rcf.bnl.gov:22 -l [username] ssh.sdcc.bnl.gov`~~

~~- replace nx01 with nx02 if logging into nx02 and so on.~~
~~- 7025 should be replaced with a random high numbered open port (between 4096 and 65535) on your desktop.~~
~~- There are four NX servers: nx01.rcf.bnl.gov, nx02.rcf.bnl.gov, nx06.rcf.bnl.gov and nx07.rcf.bnl.gov. Either one can be used.~~
~~- `username` is your RACF `username`~~

**Part-2: Use `NoMachine` client for Desktop access**

Download the latest NoMachine Client Software from the www.nomachine.com page
```
Downloads -> Enterprise Products Evaluation -> NoMachine Enterprise Client
```
`DO NOT` download and install the whole package (just do client), otherwise it will silently start `nx` server on your computer.

~~1. Make sure the ssh-agent is running described ( with the key added as described [here](ssh_agent.md)) and set up the SSH tunnel as mentioned in the instructions above.~~

2. Start the NoMachine Client Application.

3. Select New Connection

~~4. Select the 'SSH protocol' option~~  
4. Select the 'NX protocol' option

~~5. Enter Host - `localhost` and Port - `the port number chosen when setting up the tunnel`. In part-1 example, the port number is set to 7025.~~  
5. Enter Host - `nx.sdcc.bnl.gov` and Port - `4000`

6. Authenticate using the 'Password' option

7. HTTP Proxy - Do not use proxy option

8. Specify any general name for the connection and click on the Done button.

~~9. Connect and login with the RACF kerberos credentials. Click on the New desktop, select KDE virtual desktop and proceed.~~  
<a name="NxLoginStep"></a>9. Connect and login first entering your RACF kerberos credentials.

10. After entering those credentials it will then ask you for another password. This will be the password from the MFA you setup. Enter it
    - To setup MFA (not the same as STAR drupal) follow instructions [here](https://www.sdcc.bnl.gov/information/unified-multi-factor-authentication)

11. Pick *Create a new KDE desktop* or another desktop of your liking

12. Select *Automatically select a node* or pick your favorite node :)

