Notes on using Alma 9 nodes at STAR
--------------------------------------

Ref: @[July 14, 2025] > [STAR Software Mattermost chat with link to tutorial page](https://chat.sdcc.bnl.gov/star/pl/dqp94zse97r39jh8aetbi3jq7e)

Ref: @[July 14, 2025] > [Jerome Tutorial on running Alma 9](https://drupal.star.bnl.gov/STAR/blog/jeromel/Running-a9-nodes)

Ref: @[July 14, 2025] > [STAR Software Mattermost chat link to issue with STAR_ROOT not found](https://chat.sdcc.bnl.gov/star/pl/9tog9swdafyo5jgxmyqop6ndoc)

Ref: @[July 14, 2025] > [STAR Software Mattermost chat link to fix issue with STAR_ROOT not found](https://chat.sdcc.bnl.gov/star/pl/3tw7krm3a7by8cakfw9jo7inca)

Ref: @[March 24, 2026] > [Singularity Paper](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0177459)

Ref: @[March 24, 2026] > [Singularity project becomes Apptainer as it moves into the Linux Foundation]([https://apptainer.org/news/community-announcement-20211130/]), [Also here on linuxfoundation.com](https://www.linuxfoundation.org/press/press-release/new-linux-foundation-project-accelerates-collaboration-on-container-systems-between-enterprise-and-high-performance-computing-environments)

Ref: @[March 24, 2026] > [Apptainer Github Page](https://github.com/apptainer/apptainer)

Ref: @[March 24, 2026] > [Apptainer Documentation](https://apptainer.org/user-docs/master/introduction.html)

Ref: @[March 24, 2026] > [Apptainer vs. Singularity](https://www.reddit.com/r/HPC/comments/1fkfzxl/apptainer_vs_singularity/)  
											 > Which linked to [this](https://www.reddit.com/r/HPC/comments/18fglf7/comment/kdiw5x8/) where the singularity author says to use Apptainer


**The information contained herein is valid as of the time of the last update, April 10, 2026 and may change as the STAR software develops**

## Summary

- Use Alma 9 nodes to have access to a larger pool of condor nodes
	- Alma 9 nodes are *starsub0X* where "X" is a number between 1 and 7 (1 and 2 having older versions of condor)
	- For example to login to the gateway and Alma 9 node in one go do `ssh -t username@ssh.sdcc.bnl.gov ssh starsub04`
		- This assumes you have your ssh private key loaded into the agent [see](ssh_agent.md).
- Alma 9 can only use NFS formatted disks
	- To setup NFS disks do the following (note that the first three of these thing have to actually be done in your .login or .cshrc scripts. you cannot do this later. You can set up root later, I believe):
		1. In your *.cshrc* login script replace `setenv GROUP_DIR /afs/rhic.bnl.gov/star/group` to `setenv GROUP_DIR /star/nfs4/AFS/star/group`
		2. One line above where you just did the replacement in the *.cshrc* file add `setenv USE_NFS4 1`
		3. You must create/modify a file in your home directory named *.login* (the dot is an important part of the file name) and make sure it contains the following lines
			```
			setenv GROUP_DIR /star/nfs4/AFS/star/group
			if ( -r  $GROUP_DIR/star_login.csh ) then
				source $GROUP_DIR/star_login.csh
			endif
			```
			- If there was previously a "setenv" line replace it with the one above
			- For the curious: It seems like you are repeating this command once in your *.cshrc* file and again in the *.login* file. I don't have an answer why it has to be done this way except that it works. If you know or find out I would be happy to know as well.
  		
  		- **NB:** If you want to use ROOT6 you must then add the following lines to your login script `setup 64b` followed by `setup ROOT 6.20.08`. I believe this number may change as the versions of root6 change.  
- STAR software doesn't run or compile on Alma 9 and must use the container `singularity`
	- To use `singularity` first setup NFS environment following instructions above
	- To run or compile STAR code interactively on an Alma 9 node do `singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif csh`
		- This will start the `singularity` environment for STAR
			- You can now compile STAR software with `cons`
			- You can now run your STAR scripts normally
- Condor jobs must also be handled differently to use the container
	- **Condor jobs cannot be submitted or queried while in the singularity container!**
	- **NB:** If you want to run jobs using condor in the singularity container, compile all code in the singularity container. This will use all the libraries available in the container. It also uses ROOT5.
	- For STAR scheduler
		1. add to your xml  `<shell>singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif</shell>`
		2. Submit using `star-submit-beta` or `star-submit-template-beta`
	- For condor jobs
		1. The executable should be `/bin/env`
		2. The arguments should be `singularity exec -e -B /direct -B /star -B /afs -B /gpfs -B /sdcc/lustre02 /cvmfs/star.sdcc.bnl.gov/containers/rhic_sl7.sif /path/to/script.csh rest of arguments to csh script`
			- Note that if your script was copied to the condor node the path need only be "./", i.e. the current directory
			
## Helpful information when working in STAR nodes

__Disclaimer__: The following guidelines are based on my experience working in STAR and what I could find online about NFS disks and is not an "official" set of rules

1. Copies are fast, reads are slow, when in doubt copy to scratch space (`$SCRATCH`) then read
	- An acronym to help you remember is CARP (Copy Agile, Read Poor)
	- `$SCRATCH` spaces are specific to a node and a file copied to `$SCRATCH` on one node will not be available on another
		- The same is true for `$SCRATCH` on condor nodes!
2. The `ls` command on NFS disks will read the metadata of all files in a directory before displaying the files. This can slow down the disk and take longer to display the files in a directory with many files
	- Keep number of files in a directory small and create subdirectories as needed to reduce individual file numbers in a given directory
		- Hundreds: No issues seen
		- Thousands: Noticeably slower performance
		- Tens of thousands: Avoid or be prepared to wait a very very long time!
			- If you find yourself in this situation and need to see the files use `echo` command as this does not read metadata and only displays filenames
				- For instance to see all ROOT files you can `echo "/path/to/*.root"`
			- The output is not pretty but at least this way you can see the filenames and use `mv` to create subdirectories for faster file display
3. Don't forget you are on a shared file system and if something is working slow for you it is probably working slow for others.
	- If something is running slow ask yourself:  
		i. Am I reading over a network disk or my "local" disk?  
			- local disks are your home directory, and the scratch space  
			- network disks are most likely everything else  
			- When in doubt assume it is a network disk  
		ii. Am I doing any excessive reads or writes over a network?  
			- Commands like `hadd` open and read multiple files simultaneously and put a strain on network disks  
				- If `hadd`ing many files on disk consider copying to scratch first before `hadd`ing; CARP!  
			- This is also true for programs that you write. If your ROOT macro is opening a file or many files over a network disk this will impact all users   performance  
5. If possible always send a zipped or compressed archive of your files for condor jobs since a single file is faster than sending multiple files
	- Submitting condor jobs requires reading metadata of files, the more files needed by a job the longer it will take. Consider compressing your files and folders and sending the compressed archive
		- Zip before you submit!
		- The STAR scheduler is already set to do this so don't write a \<command\> that copies files over to the condor node, or a program that will zip files on some network disk and then copy them over
			- If you need a file or folder for the job specify that in the appropriate xml tag when writing your STAR scheduler xml description
	- If condor jobs return disk quota exceeded as the hold reason try restarting the job after checking your disk quota. This error can happen when you process thousands of jobs and disk is busy so output of transfer files fails
		- Checking pwg disk quota: `mmlsquota --block-size auto -e gpfs01:star-pwg`
		- Restarting held condor jobs: `condor_release -u username` or for a specific job `condor_release jobid`
	- Condor will automatically transfer files to the *Initialdir* specified in the **job** file. This transfer is effectively a copy and so you don't need to call a separate move or copy in your commands or script.
7. Don't use Python unless you really have to
	- Python is slow compared to compiled C++ code
	- You are on a shared system and don't have administrator privileges to install python packages
	- Consider installing [MiniConda](https://www.anaconda.com/docs/getting-started/miniconda/main) locally if you really want to use Python
		- Miniconda is a much more condensed version of [AnaConda](www.anaconda.com/docs/main) and doesn't have the nice Jupyter notebooks but it is small and can be used to install any needed python packages (to your local space) and it will run.
		- I don't know how Miniconda will perform on condor jobs but because Python is so slow you shouldn't be submitting python scripts for jobs

## Background

The end of life for Scientific Linux 7 (SL7) has made it necessary and prudent for SDCC to switch to a different operating system (OS). This is the Alma 9 OS. However, The STAR software does not work out of the box on the Alma 9 OS. This could be due to the switch to a 64 bit OS. Also, on Alma 9, the AFS formatted disks are not accessible and only NFS formatted disks are. To run STAR software on Alma 9 one must use a "virtual box" to run code that uses the STAR libraries. This "virtual box" is called `singularity`. `singularity` is a container that will allow you to use the STAR software on STAR nodes running Alma 9.

The Alma 9 nodes can be reached with the `term -i` command. You can also ssh to them directly from the gateway. The nodes are *starsub0X* where "X" is a number from 1 to 7, e.g. `ssh starsub04`. The nodes numbered 3 and above have the latest version of condor and are thus recommended.

There is a "singularity" project but they decided to move into the Linux Foundation and thus got a new name "apptainer". I think SDCC is running "apptainer" but the executable name is still `singularity`. I believe this can be checked by running `singularity --version` and seeing it return *apptainer* instead of *singularity*.

## Q&A
* Fatal in <TVirtualStreamerInfo::Factory>: Cannot find the plugin handler for TVirtualStreamerInfo! However $ROOTSYS/etc/plugins/TVirtualStreamerInfo is accessible, Check the content of this directory!
	* This happens for older version of star libraries, use newer version (>= SL22c) to avoid this error. 


To check if in a `singularity` container check if environment variable *$SINGULARITY_ENVIRONMENT* exists
To exit container use command `exit`

