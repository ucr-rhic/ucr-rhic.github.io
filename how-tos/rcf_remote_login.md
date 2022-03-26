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

