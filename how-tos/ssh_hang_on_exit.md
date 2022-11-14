Issue where ssh/rterm hangs on exit
==================

Description
----------

@[November 14, 2022]

I encountered this issue when I would use NoMachine to work at BNL computer systems (SDCC). The issue is that you log into NoMachine normally and then start a terminal session and ssh/rterm to a rcas node. On that node if you use emacs in graphical mode and then close emacs then try to exit the terminal it hangs until you kill the terminal with ctrl-c.

### Problem

The problem is that emacs checks if a dbus daemon is already running by checking the envirnoment variable *$DBUS_SESSION_BUS_ADDRESS* if it is not defined then it calls `dbus-launch --auto-launch`. This starts the dbus daemon which is a program used to handle some system messages. NoMachine when it starts up has a running dbus daemon but after logging into the rcas nodes, rcas is not aware of the NoMachine dbus daemon and so emacs starts a new one.

You can check for any dbus daemon processes using `ps -elu username | grep "dbus"`.

__WARNING:If you kill the NoMachine dbus daemon it will cause you NoMachine session to terminate__

### Solution

The solutions in order of preference are outlined below. Some of them are hacks and may not work in the future.

1. __TESTED__ Disable dbus daemon.  This can be done by adding the following lines to your login file. This is the preferred method since a dbus daemon is not needed for graphical displays.
```shell
if( ! $?DBUS_SESSION_BUS_ADDRESS ) then
    setenv DBUS_SESSION_BUS_ADDRESS "disabled:"
endif
```

2. __UNTESTED__ Send NoMachine dbus daemon process to rcas

3. __UNTESTED__ Start a dbus daemon manually `dbus-launch --exit-with-session`

4. __UNTESTED__ Kill the dbus daemon by creating/adding to the bash logout script. This code gets executed at logout which can cause problems with NoMachine.
```bash
pkill -u $USER -t `tty | cut -d '/' -f 3,4` dbus-launch
```

Sources:
- @[November 14, 2022]>[How to disable dbus from autolaunching](https://codereview.chromium.org/2861163002)
- @[November 14, 2022]>[Prevent dbus from hanging ssh exit](https://serverfault.com/questions/405518/how-to-configure-d-bus-and-ssh-x-forwarding-to-prevent-ssh-from-hanging-on-exit)

