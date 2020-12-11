Remote Desktop for RCF Using NX/NoMachine
------------------------------------------------

Ref: [see here](https://www.racf.bnl.gov/docs/services/nx)

Remote desktop access to RCF is supported by the latest NX technology compared to classic VNC session. RCF has partnered with `NoMachine` to provide the service.

As of December 2017, all the RHIC production NX servers - `nx01.rcf.bnl.gov, nx02.rcf.bnl.gov, nx06.rcf.bnl.gov and nx07.rcf.bnl.gov` are running NoMachine 5 server software - version 5.3.9.


**Steps for Linux / Instructions for NoMachine Client (NoMachine 6 version)**

**Part-1: Establish the tunneling**

```
ssh -L 7025:nx01.rcf.bnl.gov:22 -l [username] ssh.sdcc.bnl.gov
```
- replace nx01 with nx02 if logging into nx02 and so on.
- 7025 should be replaced with a random high numbered open port (between 4096 and 65535) on your desktop.
- There are four NX servers: nx01.rcf.bnl.gov, nx02.rcf.bnl.gov, nx06.rcf.bnl.gov and nx07.rcf.bnl.gov. Either one can be used.
- `username` is your RACF `username`


**Part-2: Use `NoMachine` client for Desktop access**

Download the latest NoMachine Client Software from the www.nomachine.com page
```
Downloads -> Enterprise Products Evaluation -> NoMachine Enterprise Client
```
`DO NOT` download and install the whole package (just do client), otherwise it will silently start `nx` server on your computer.

1. Make sure the ssh-agent is running described ( with the key added as described [here](ssh_agent.md)) and set up the SSH tunnel as mentioned in the instructions above.

2. Start the NoMachine Client Application.

3. Select New Connection

4. Select the 'SSH protocol' option

5. Enter Host - `localhost` and Port - `the port number chosen when setting up the tunnel`. In part-1 example, the port number is set to 7025.

6. Authenticate using the 'Password' option

7. HTTP Proxy - Do not use proxy option

8. Specify any general name for the connection and click on the Done button.

9. Connect and login with the RACF kerberos credentials. Click on the New desktop, select KDE virtual desktop and proceed.
