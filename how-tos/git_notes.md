Notes on using git and Github
------------------------------------------------

These notes are from my (David Kapukchyan) experiences using git and Github, things taken from many references around the web, and conversations with other git and Github users. In particular the [Github documentation](https://docs.github.com/en/get-started) is great for looking up commands and their basic usage. The [git website](https://git-scm.com/doc) is also a great resource.

Some other resources I found and used:
 - [A git tutorial](https://www.atlassian.com/git/tutorials)  
 - [git Cheat Sheet](https://training.github.com/downloads/github-git-cheat-sheet.pdf)  
 - [git workflow visualized](https://ndpsoftware.com/git-cheatsheet.html#loc=index;)  
 - [Good Explanation of how git works](https://stackoverflow.com/questions/72477056/git-fork-always-commits-ahead-i-dont-want/72480346)  
 - [FCS How to with Github](https://www.star.bnl.gov/protected/spin/akio/fcs/howto_MC_github.html)

Versioning software is an important tool in tracking changes to your code and analysis. `git` is one such software that is used to track changes in files. `git` is a standalone tool and can be downloaded and installed on almost any computer. I won't go into this and refer you to the online documentation for how to do that. `git` does a graphical interface version but I recommend to use the command line on a terminal.

`git` keeps tracks of your history in what are called "commits". These "commits" are immutable hashes that tag your history as you change and add files. You can think of a "commit" has a pointer to some point in the history of your code. The idea is to keep a clean history of what you have done so that you can trace back your work. `git` uses a hidden folder in your working directory called *.git* that is used to store history and files in a special format. This is a format that can only be read by `git` so browsing your history by going through *.git* folder is not necessary. There are commands to do this and we will discuss these later or you can check the [summary](#SummaryCommands) section. There may be some files you would like to modify in that folder but again this is special case. Normally you should not be modifying the contents of *.git*.

`git` also has the capability to store your history and files in a remote (i.e. online) server. This is typically the website [github.com](www.github.com) but can be any server as long as you configure `git` to give it a proper server address. Github typically has some nice add-on features to `git` that make it great for projects with many contributors such as forking. A code project is called a repository on Github and is just a folder with all the files related to your project. You can check their website for how to create a repository and I will not discuss that here. I will assume that there is some repository on Github that you would like to use or that you have created a new repository and want to upload your code to that repository.

<a name="SummaryCommands"></a>
## Summary of Some Useful Commands
 - `git init` initialize current directory to start tracking changes  
 - `git status` print status of files such as whether they are or not in index (staging area) , untracked files, etc.  
 - `git add [file]` add [file] changes to index (staging area)  
 	+ [file] can also be a folder name and all files in that folder will get added  
 - `git commit` commit changes in index (staging area) to history will start default editor to add a "commit" message  
 	+ To type the message on the command line and bypass the editor use option `-m` e.g. `git commit -m "commit message"`  
 - `git commit --amend` overwrite last commit with a new one; generates a new hash and deletes old one. Be careful not to amend on pushed commits otherwise will mess up your history  
 - `git push [remotename] [branchname]` push commit history and files to remote repository and branch  
 - `git log` list commit history  
 	+ Use option `--oneline` for a short summary  
 	+ Use option `-[N]` where *N* is some number to list history up to *N* commits  
 - `git reflog` log of all git actions that can be used to undo stuff you did with git  
 - `git fetch [remotename]` get changes and history from remote repository and copy to local machine  
 - `git merge` merge changes from one branch into another  
 	+ Merge will fast forward to the latest commit and if no conflicts only update history to point to latest commit (fast forward)  
 	+ During merging you may have conflicts and these need to be resolved in a new commit  
 - `git pull` does a `git fetch` and then a `git merge`  
 - `git branch` list branches  
 	+ `git branch [branchname]` create a new branch called *branchname*  
 	+ `git checkout [branchname]` to switch branches
 - `git restore --staged [file]` remove a file or folder from the index (staging area)  
 - `git reset [commithash]`  reset the directory to the state of a commit ID 
 	+ Use option `--hard` when you need to, will overwrite and delete files if not careful  
 - `git remote -v` list remote repository locations/addresses  
 - `git clone [url]` clone an existing github repository into current directory, it will create a folder with the repository name and put all the files and folders there including *.git*


## Workflow

There are several levels of tracking that `git` uses and is important to understand for the command `git status`. A file can be either untracked, or modified. Regardless of the files state you must move it to the index (staging area) before you can commit it to the history of the project. After staging the files you can then commit then to the history. The history and files can then be upload to the remote repository or server. I will now talk about these various stages.

### Untracked, and modified files/folders
 - Untracked files are files that will not be included with the project's history. These are files that `git` will ignore and won't track changes for and are labeled as such when `git status` is called. You can add untracked files using the command `git add`. Once you add a file it goes to the index (staging area).  
 - Modified files are files that git is tracking for changes and have changed since the last commit. These files must also be added to the index (staging area) before you can save those modifications.  

### Index or Staging area
The index or staging area is a feature of `git` that tells it what files need to be committed. You add files to the staging area using `git add`. These can be either modified files or new files. You can remove a file from the staging area using `git restore --staged [file]`. Any file not in the staging area will not get committed.

### Commit changes
After staging all the files you want to commit you call `git commit` to create a history point for that directory that is now saved for those files. You can get back to that saved/committed state using `git reset`

### Push changes
After committing you may want to upload, share, or save the history and files on a remote server. You can do this using `git push`. Once you push the changes now your local copy is saved on the remote server. This means any changes to the history on your local copy won't show up on the remote until you push again.
 

## Setting up `git` and Github

#### Setting up `git` on folder you want to track changes
To set up git to track changes in a directory type the command `git init` this will create a folder in that directory called *.git* that `git` will use to keep track of changes. If you type `git status` in that directory and files are present it will show untracked files. Calling `git init` only needs to happen once. To stop the tracking and delete all history on your local copy you can remove the *.git* folder; however, this should not be done to resolve problems and issues with `git`.

#### Setting up Github for a directory you are tracking with git
1. Create a new repository with your Github account.
2. Get the url from Github for your repository
3. `git remote add origin [url]`
   - Note that *origin* can also be *upstream* or any other name you want to associate with the remote repository 
4. `git remote -v` to confirm the url. There will be two one for fetch and push, and normally they should match

<a name = "CloneRepo"></a>
#### Cloning your own Github repository
1. go the directory where you want the repository saved
2. Find out the url for your repository
3. `git clone [url]` This will copy the whole history and all the files so for a large repository you may want to do a sparse checkout which I will discuss [here](#SparseCheckout)

#### Cloning someone else' Github repository
1. Ask for permission from the author
2. Follow same steps [here](#CloneRepo)

### Working with Github features

### Forking a repository
A fork is a copy of someone else's repository under your own Github account. This is specific to GitHub and has nothing to do with `git`. A fork allows you to copy someone else's repository without requesting permission and making changes to it. The changes are saved on your GitHub account. This means if you want to merge your changes with the original you must submit a "Pull Request".

#### Forking and cloning a repository
1. Navigate to repository you want to fork
2. Click on option to create a new fork
3. Navigate to forked repository and get [url]
4. Follow same steps [here](#CloneRepo)

#### [Syncing a forked repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork)
You can use the Github page for your forked repository to sync with the original repository but this is not recommended. It is better through the command line.

In order to sync a fork you need to have set both the forked repository's remote url and the original repository's url using git remote. The fork is usually called *origin* and the original is *upstream*. You can use the command `git remote add/set-url upstream [url_original]` to add the original repository's url. Once you have *upstream* url set then you can do the commands below.

1. `git remote -v` to check that both *origin* and *upstream* url is set correctly
2. `git fetch upstream` to update the local copy with the original repository's changes
3. `git merge upstream/main` to fast forward to latest commit
   - If have made commits to the forked repository you will make an extra "merge" commit to your fork's repository. This may be troublesome if later you want to do a pull request because those merge commits will now be integrated into the original repository's history. Please this [section](#WorkFlowFork) to get a better idea how to work with forks
4. `git push origin [branch]` to push the local updates back to the fork on GitHub server.

#### Working with branches
It is recommended on a forked repository to always work on a separate branch from the main (master) one because the main branch can be reserved to always grab updates from the original repository. The branch you are working on can always be merged back with the main once you are ready for a pull request. In that instance a merge commit in the history will show up but that is fine because hopefully you are not creating lots of merge commits.

<a name="WorkFlowFork"></a>
#### Suggested Work-flow for Forks

Starting with a cloned fork with both *origin* and *upstream* set to correct locations

1. `git branch` list current branches  
2. `git branch [branchname]` creates a branch named *branchname*  
3. `git checkout [branchname]` switch to branch named *branchname*  
4. Make updates and commits as needed on this branch  
 	+ The advantage of this you can switch back to the main branch and update your fork without having to worry about merge conflicts right away  

When finished making updates and want to create a pull request

1. `git checkout main` to switch back to main branch  
2. `git merge [branchname]` merges updates from *branchname* into *main* and creates a new merge commit if needed  
3. `git push origin main` push changes to Github server  
4. Open pull request on Github merging the main branches  
5. Do not sync your main branch with the upstream until your pull request is approved  
   + You can keep adding commits and pushing to your fork to the main branch as you receive comments. These changes once pushed will update the pull request files automatically  
6. After pull request sync your local copy with upstream  
	1. Push changes to your Github fork
 	 + If your github fork shows some number of commits ahead of the original this is not because you have more commits but because the pointer for the history is in the wrong place to fix it you can do the following in your local copy [Ref1](https://github.com/orgs/community/discussions/22440), [Ref2](https://stackoverflow.com/questions/72477056/git-fork-always-commits-ahead-i-dont-want/72480346).
 	  1. `git checkout main` to switch to main branch
 	  2. Ensure directory has no files that need to be committed (i.e. working tree is clean)
 	  3. `git fetch upstream` to make sure you have latest updates
     (To keep commits)
     4. git merge upstream/main
     (To discard commits)
 	  4. `git reset --hard upstream/main` to force git to move to latest commit from upstream/main. In this instance option *hard* is ok since you want git to force the commit pointer to move to the latest commit on the upstream/main branch and not your fork's branch. The approval of the pull request will have integrated your changes into the history of the upstream already.
 	   - *Note that you don't want to do a `git merge` like above because you are not fast forwarding*
 	  5. `git push origin main` to push changes to forked repository. Option *hard* may need to be used

<a name = "GitIgnore"></a>
### Ignoring files in local directory
You may not want to commit and push all the files on your local machine to the remote repository. There is a special file in `git` called that you can use to ignore certain files; this file is called ".gitignore", *Note: that the "." is only to make it hidden on most operating systems*. These can be executables, shared libraries, picture files, etc. Ignoring files means that they won't show up in `git status` under untracked files. They will also be ignored if you try to add them. You can use `git add -f` to add an ignored file.

If a file shows up on `git status` after adding it to the ".gitignore" it could be because  
1. Improper *.gitignore* syntax  
2. That file or folder has been committed previously and needs to removed from the index  
   - `git rm --cached [file]` will remove a tracked *file* from the index.  
   - **NOTE: Without the "--cached" the file will be deleted permanently**  

#### Example *.gitignore*
#You can use "#" for comments in a gitignore file
> myfile.txt                 #ignore all files named "myfile.txt"
> \*.so                      #ignore files ending in ".so" (i.e. shared libraries)
> \*.exe                     #ignore files ending in ".exe" (i.e. executables)
> ignorethis/                #ignore all files and folders in the directory "ignorethis"
> !ignorethis/important.txt  #don't ignore file "important.txt" in directory "ignorethis/"
> \*\*/ignoreall/            #ignore all directories matching name "ignoreall"

<a name = "SparseCheckout"></a>
### Sparse Checkout
Sparse checkout can be used to exclude files to download from a repository.

2. `git clone --no-checkout [url]`  
2. `git config core.sparseCheckout true`  
3. `cd .git` go to hidden git folder in the working tree  
4. `cd info` go the *info* folder  
5. Create or modify file named *sparse-checkout* with files to download  
   - Supports linux wildcard expansions
   - Use `!` preceding a file name to exclude it
   
Example: Want to check out only StSpinDbMaker and all files that start with "StFcs" in StRoot
> /\*  
> !/\*/  
> /StRoot/  
> !/StRoot/\*/  
> StRoot/StSpinPool/  
> !/StRoot/StSpinPool/\*/  
> StRoot/StSpinPool/StSpinDbMaker/  
> StRoot/StFcs\*  

Note that you first need to tell it grab everything in a folder first before you exclude things in that folder

## Setting up ssh for Github
The steps for setting up ssh on Github are similar to setting up ssh for BNL SDCC rcas machines. You need to generate a ssh public and private key pair. Then upload the public key hash to Github. The main difference is that there is nowhere to log in. You just need to ensure the private key is loaded into your ssh agent when you call git fetch/pull.

1. [Generate ssh key pair](generate_keys.md). You can reuse ssh key pairs but it is not recommended)  
2. Copy public key hash as discussed [here](generate_keys.md)  
3. Go to Github website and navigate to ssh settings  
4. Copy public key hash into the appropriate box and give a name for the key  
5. [Add key to agent](ssh_agent.md)  
6. Add the ssh url of the repository to your local directory  
   - If no remote has been set as shown by `git remote -v` then you add it by doing `git remote add origin [ssh_url]`  
   - If a remote has been set and you want to change it use `git remote set-url origin [ssh_url]`  


@[February 22, 2023]>https://stackoverflow.com/questions/7244321/how-do-i-update-or-sync-a-forked-repository-on-github


https://www.earthdatascience.org/courses/intro-to-earth-data-science/git-github/github-collaboration/update-github-repositories-with-changes-by-others/

https://ljvmiranda921.github.io/notebook/2021/11/12/sync-your-fork/

