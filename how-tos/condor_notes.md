Notes on using HTCondor
------------------------------------------------

Ref: @[December 14, 2022] > [Condor Manual](https://htcondor.readthedocs.io/en/latest/index.html)  
Ref: @[December 14, 2022] > [Condor SDCC](https://www.sdcc.bnl.gov/information/services/htcondor-sdcc)  
Ref: @[December 14, 2022] > [Condor Quick Start SDCC](https://www.sdcc.bnl.gov/information/htcondor-quick-start-guide)  
Ref: @[February 10, 2025] > [Useful how to for some basic condor commands](https://chtc.cs.wisc.edu/uw-research-computing/condor_q)
Ref: @[February 11, 2025] > [Managing condor jobs](https://htcondor.readthedocs.io/en/latest/users-manual/managing-a-job.html)
Ref: @[February 11, 2025] > [All condor commands](https://htcondor.readthedocs.io/en/latest/man-pages/index.html)

High-Throughtput Computing (HTC) is an important part of data analysis. The idea is that computer nodes (servers) are reserved to run commands/executables. This allows to process the same command/executable multiple times to generate some output. For instance if you want to simulate 10000 particles a single computer/node executing that many particles will take a long time. If instead you were to simulate 100 particles on 1000 machines you will still get 10000 particles but in the time it takes to process only 100 particles so you save time. All you need to do now is combine the results which is often much less time consuming than simulating 10000 particles on a single computer. However, the node needs to know what executable you are running, what input files are needed and where to send the output. This is where HTCondor comes in.

HTCondor or just condor is a program with it's own language and commands that is used for HTC. The STAR scheduler utlizes condor when submitting jobs. HTCondor allows you to specify what program you want to run, where the inputs are, how to run it, and where to send the output. It also has built-in commands to check the status of your running program, or what condor calls "jobs". To use condor you create a "job" file which tells the node that will run your program everything it needs to run it. You also can specify the requirements of the computer you want your job to run on e.g. memory, cpu speed, etc.

The software group at BNL maintains condor and the nodes used to do analysis. The only thing you need to know is how to write a job file, [sample](#SampleJob), and the [commands](#CondorCommands) to check your job status.

<a name="CondorCommands"></a>
## Useful condor commands

 - `condor_q` query the condor system for any running jobs
   + `condor_q -long` long output of a job or jobs
   + `condor_q -run` show only running jobs
   + `condor_q -better-analyze` show more job details like why it is idle or on hold
   + `condor_q -hold` show hold reasons for a job (when given a jobid as argument) or all jobs
     + `condor_q -hold -af HoldReason` if hold reason is cut-off
 - `condor_submit jobfile` process *jobfile* to send to condor nodes
 - `condor_tail jobid` look at stdout of a running *jobid*
 - `condor_ssh_to_job jobid` ssh to condor node running *jobid*
 - `condor_rm jobid/username` remove job *jobid*
 - `condor_hold jobid/username` to hold a condor job
 - `condor_release jobid/username` to release a held job.
 - `condor_history username [-limit]` to check previous submitted jobs really slow since every user's history is checked
 
## Condor Job files

### Hints and Tips

1. All condor jobs will generate a log file of the events as they happen

2. Any submitted condor job can only be checked on the node it was submitted on, see [here](#LinuxComputing)
  - After submitting a condor job make sure to write down which node it was submitted from otherwise you will not be able to check the status of you jobs.
  - This can be done in the job file itself, a new file with the name of the node, or in the log file name itself

3. Use *GetEnv = True* to import the current environment to condor, see [environment variables](#EnvVar)

4. Files are transfered to \_CONDOR\_SCRATCH\_DIR and will be deleted when the job stops running, it is also the directory condor jobs run on.

5. Each job should have a unique file for *stdout* and *stderr* as well the log file generated by condor, see [Linux Ouput](#LinuxIO)
  - *stdout* files should end in ".out" for consistency
  - *stderr* files should end in ".err" for consistency
  - log files should end in ".log" for consistency
  - Use condor built-in variables $(Cluster) and $(Process) when naming files for the condor log, *stdout*, and *stderr*
     - $(Cluster) is the condor cluster of the job
     - $(Process) is the cluster process of that job

5. Environment Variables can be accessed $ENV(EnvironmentVariableName), see [environment variables](#EnvVar) for some common ones

6. Holding a job sends a hard kill signal (SIGKILL), see [kill signals](#LinuxJobs)

7. Releasing a held job restarts it from the beginning

<a name="SampleJob"></a>
### Sample Job File
The example below is taken from @[December 14, 2022][Condor Quick Start SDCC](https://www.sdcc.bnl.gov/information/htcondor-quick-start-guide)

> \# All local jobs are part of the vanilla universe.  
> Universe        = vanilla  
> 
> \# The executable we want to run.  
> Executable      = /bin/echo  
> 
> \# The argument to pass to the executable.  
> Arguments       = "test job"  
> 
> \# The requirement line specifies which machines we want to  
> \# run this job on.  Any arbitrary classad expression can  
> \# be used.  
> Requirements    = (CPU_Speed >= 1)  
> 
> \# Rank is an expression that states how to rank machines which  
> \# have already met the requirements expression.  Essentially,  
> \# rank expresses preference.  A higher numeric value equals better  
> \# rank.  Condor will give the job the machine with the highest rank.  
> Rank		= CPU_Speed  
> 
> \# Jobs by default get 1.4Gb of RAM allocated, ask for more if needed  
> \# but if a job needs more than 2Gb it will not be able to run on the  
> \# older nodes  
> request_memory = 1800M  
> 
> \# If you need multiple cores you can ask for them, but the scheduling  
> \# may take longer the "larger" a job you ask for  
> request_cpus = 1  
> 
> \# This flag is used to order only one's own submitted jobs  
> \# The jobs with the highest numbers get considered for  
> \# scheduling first.  
> Priority        = 4  
> 
> \# Copy all of the user's current shell environment variables  
> \# at the time of job submission.  
> GetEnv          = True  
> 
> \# Used to give jobs a directory with respect to file input  
> \# and output.  
> Initialdir      = /experiment/u/user/jobdir/  
> 
> \# Input file given to the job.  
> Input           = /dev/null  
> 
> \# The job's stdout is sent to this file.  
> Output          = /experiment/u/user/myjob.out  
> 
> \# The job's stderr is sent to this file.  
> Error           = /experiment/u/user/myjob.err  
> 
> \# The htcondor log file for this job, useful when debugging.  
> Log             = /experiment/u/user/myjob.log.$(Cluster)  
> 
> 
> \# This should be the last command and tells condor to queue the  
> \# job.  If a number is placed after the command (i.e. Queue 15)  
> \# then the job will be submitted N times.  Use the $(Process)  
> \# macro to make your input/output and log files unique.  
> Queue  


<a name="LinuxComputing"></a>
## Notes on running a program (Linux)

Any time you execute a program, command, or shell script on the computer from a controlling terminal that program will allocate resources until the job finishes. In fact, Linux creates a subshell that will run that program. When that program finishes (with any output/input sent to the appropriate place) it returns you back to the controlling shell session. Every running program also gets a Process ID (PID). This PID can be used to see how long a program is running as well as its status. The command `ps -u username` can be used to check the status of all running programs for a given *username* on the current node. Different nodes can have different programs running. This is especially important since submitted condor jobs can only be viewed on the node that they were submitted on. This has to do with a condor shadow process on that node and I won't go into any more details here.

Normally when you run a program from the terminal shell that terminal shell is the parent of that process (PPID). Any time you exit the shell session all programs with a PPID of the shell must be completed/terminated first, with some exceptions. In order to run a program that isn't associated with any controlling shell process you can use the command `nohup`. This is useful because you can log out of the session and not kill your running program because the PPID will no longer be your shell session. However, calling `nohup` does not automatically send the program to the background and will still look like it is running in your current shell. In the section about [managing jobs](#LinuxJobs) you can read about how to start (or send) programs to the background to prevent your shell session from idling.

<a name="LinuxIO"></a>
### *stdout*, *stderr*, and *stdin*

There are two places the program can send any text that it needs to output. First is called the *stdout* and is normally where commands like `echo`, `cout`, and `printf` send their output. The other is called *stderr* and this is where error text relating to the program will go; e.g. if you try to `ls` to a nonexistent directory that will get written to *stderr*. All text from *stdout* and *stderr* will be displayed on the running terminal unless the redirection operator `>` for *stdout* is used. To redirect both *stdout* and *stderr* use `>&`. I will not go into details about all the redirection operations as this can be found in many places online and only list the two most common ones. Both *stdout* and *stderr* utilize a special file called */dev/null*. I won't go into details about this special file but it can be used as a place to dump text you don't want printed out to the terminal.

Try (You can delete the generated files)
1. `ls DirThatDoesntExist >& /dev/null` no output to terminal
2. `ls DirThatDoesntExist > dump.out` output from *stderr* is output to terminal, file *dump.out* empty
3. `ls DirThatDoesntExist >& dump.all` no output to terminal, file *dump.out* contains error message

Lastly, *stdin* is used to grab text input from the user. It also has a redirection operator `<`. In this case rather than outputing text to the terminal screen the computer grabs the keyboard input. The key to not is that often times the *Enter* is pressed when finished inputting text. Depending on the terminal, OS, or machine that *Enter* key can mean one of three things: a newline character "\n" (ASCII *0xA*), a carriage return "\r" (ASCII *0xD*), or both "\n\r". It is important to get rid of any extra "characters" that may show up as part of the input before processing *stdin* in your program. (I believe on Linux only "\n" is used). */dev/null* is also utilized by *stdin*

<a name="LinuxJobs"></a>
### Managing a running Linux program

Whenever a program is executed the running shell session waits until that program finishes before you can do any other commands. This is not desirable in many instances such as programs that start a graphical window or will be known to take a long time to complete. In order to keep the controlling shell session from going to idle while a program is running you can use `&` to send jobs to the background. This means you can continue to use the same shell session to issue more commands without waiting for the other program to finish. The drawback is that any *stdout* and *stderr* may be printed to your working terminal while the job is running in the background. This can be circumvented with the redirection operators discussed [above](#LinuxIO).

However suppose you have started a program without the `&` and would like to now send it to the background. You can do this by pressing *CTRL-z*. This sends a SIGTSTP signal to the running program, which effectively suspends it. The command `jobs` can be used to see this suspended process and any other running jobs in this terminal session. The command `bg` can be used to send jobs to the background and the command `fg` can be used to bring jobs to the foreground i.e. current shell session. The command `kill` can be used to stop a running job.

There are several kinds of `kill` signals that one can send. The SIGTSTP is one that was discussed before and is linked to *CTRL-z*. The other one is linked to *CTRL-c* and sends SIGINT. This will terminate the running foreground process. Below are some noteworthy signals. All kill signals can be listed using `kill -l`.  
- SIGCONT continue, run a stopped process
- SIGTSTP pause a running process (*CTRL-z*)
- SIGINT interrupt, terminates program (*CTRL-c*)
- SIGQUIT quit, terminates with core dump
- SIGKILL kill, force termination

Example:  
1. `sleep 1000` will sleep for 1000 seconds and shell session will be idling the whole time  
2. press *CTRL-z*  
3. `jobs`  
> [1]  + suspended  sleep 1000  
4. [job number] (+ means default for fg, - for other jobs) status program arguments  
5. `bg %1` will run the sleep command in the background, Here the *%* is needed since not using PID, if using PID exclude *%*  
6. `jobs`  
> [1]  + running    sleep 1000  
7. `ps -lu username`  
>F S   UID   PID  PPID ... TIME     CMD  
>0 S 00000 00002 00001 ... 00:00:00 sleep  
8. `kill 00002` terminates the sleep command, here using PID, be sure to replace it with the one from your `ps` command  
9. `sleep 1000 &` will sleep for 1000 seconds but in the background  
10. `jobs`  
> [1]  + running    sleep 1000  
11. `fg %1` will now run sleep command in foreground while keeping current shell idle  
12. press *CTRL-c* terminate running sleep command  

<a name="EnvVar"></a>
### Environment Variables

Environment Variables are variables built into the computer that helps it find the programs it needs to run as well as store information about the computer itself that can then be utilized in a progam. On most linux systems variables start with *$* and environment variables are no exception. Usually environment variables are in all capital letters. Just like normal variables environment variables can be changed to customize the look and feel of your terminal and working "environment". However, you should excercise caution when changing these variables as they may break your computer so be sure you know what you are changing and why. Normally environment variables should be set once in the login script file *.cshrc* for c-shell and *.bash_profile* for bash shell.

Noteworthy Environment Variables
- **PATH** directories to search when you type a command in a *:* seperated list, e.g. `ls`, `echo`, `cd`
- **HOME** user's home directory
- **USER** user name
- **prompt** on c-shell, **PS1** on bash; define how your command prompt looks like and what it displays
- **SHELL** shell you are using
- **HOST** name of host machine
- **LD_LIBRARY_PATH** directories to search for dynamic libraries in a *:* seperated list

Noteworthy Environment Variables for STAR
- **STAR_VERSION** STAR code version you are using
- **STAR** directory of the version of STAR code you are using
- **STAR_HOST_SYS** name of directory where `cons` will compile your code
- **STAR_LIB** directory where STAR offical libraries are compiled

