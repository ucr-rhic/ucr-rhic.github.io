Notes on using Alma 9 nodes at STAR
--------------------------------------

Ref: @[July 14, 2025] > [STAR Software Mattermost chat with link to tutorial page](https://chat.sdcc.bnl.gov/star/pl/dqp94zse97r39jh8aetbi3jq7e)

Ref: @[July 14, 2025] > [Jerome Tutorial on running Alma 9](https://drupal.star.bnl.gov/STAR/blog/jeromel/Running-a9-nodes)

Ref: @[July 14, 2025] > [STAR Software Mattermost chat link to issue with STAR_ROOT not found](https://chat.sdcc.bnl.gov/star/pl/9tog9swdafyo5jgxmyqop6ndoc)

Ref: @[July 14, 2025] > [STAR Software Mattermost chat link to fix issue with STAR_ROOT not found](https://chat.sdcc.bnl.gov/star/pl/3tw7krm3a7by8cakfw9jo7inca)

**The information contained herein is valid as of the time of writing January 30, 2026 and may change as the STAR software develops**

## Summary

- Use Alma 9 nodes to have access to a larger pool of condor nodes
	- Alma 9 nodes are *starsub0X* where "X" is a number between 1 and 7 (1 and 2 having older versions of condor)
	- For example to login to the gateway and Alma 9 node in one go do `ssh -t username@ssh.sdcc.bnl.gov ssh starsub04`
		- This assumes you have your ssh private key loaded into the agent [see](ssh_agent.md).
- Alma 9 can only use NFS formatted disks
	- To setup NFS disks do the following (note that the first three of these thing have to actually be done in your .login or .cshrc scripts. you cannot do this later. You can set up root later, I believe):
		1. In your *.cshrc* login script replace `setenv GROUP_DIR /afs/rhic.bnl.gov/star/group` to `setenv GROUP_DIR /star/nfs4/AFS/star/group`
		2. One line above where you just did the replacement in the *.cshrc* file add `setenv USE_NFS4 1`
		3. If you have a *.login* script replace `setenv GROUP_DIR /afs/rhic.bnl.gov/star/group` to `setenv GROUP_DIR /star/nfs4/AFS/star/group`
  		- **NB:** If you want to use ROOT6 you must then add the following lines to your login script `setup 64b` followed by `setup ROOT 6.20.08`. I believe this number may change as the versions of root6 change.
- STAR software doesn't run or compile on Alma 9 and must use the container `singularity`
	- To use `singularity` first setup NFS environment following instructions above
	- To run or compile STAR code interactively on an Alma 9 node do `singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif csh`
		- This will start the `singularity` environment for STAR
			- You can now compile STAR software with `cons`
			- You can now run your STAR scripts normally
- Condor jobs must also be handled differently to use the container
	- **NB:** If you want to run jobs using condor in the singularity container, compile all code in the singularity container. This will use all the libraries available in the container. It also uses ROOT5.
	- For STAR scheduler
		1. add to your xml  `<shell>singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif</shell>`
		2. Submit using `star-submit-beta` or `star-submit-template-beta`
	- For condor jobs
		1. The executable should be `/bin/env`
		2. The arguments should be `singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif /path/to/script.csh rest of arguments to csh script`
			- Note that if your script was copied to the condor node the path need only be "./", i.e. the current directory

## Background

The end of life for Scientific Linux 7 (SL7) has made it necessary and prudent for SDCC to switch to a different operating system (OS). This is the Alma 9 OS. However, The STAR software does not work out of the box on the Alma 9 OS. This could be due to the switch to a 64 bit OS. Also, on Alma 9, the AFS formatted disks are not accessible and only NFS formatted disks are. To run STAR software on Alma 9 one must use a "virtual box" to run code that uses the STAR libraries. This "virtual box" is called `singularity`. `singularity` is a container that will allow you to use the STAR software on STAR nodes running Alma 9.

The Alma 9 nodes cannot be reached with the usual `rterm -i` command. You need to ssh to them directly from the gateway. The nodes are *starsub0X* where "X" is a number from 1 to 7, e.g. `ssh starsub04`. The nodes numbered 3 and above have the latest version of condor and are thus recommended.

## Q&A
* Fatal in <TVirtualStreamerInfo::Factory>: Cannot find the plugin handler for TVirtualStreamerInfo! However $ROOTSYS/etc/plugins/TVirtualStreamerInfo is accessible, Check the content of this directory!
	* This happens for older version of star libraries, use newer version (>= SL22c) to avoid this error. 


To check if in a `singularity` container check if environment variable *$SINGULARITY_ENVIRONMENT* exists
To exit container use command `exit`

