# [HPSS archiving tools](https://www.sdcc.bnl.gov/services/storage-services/hpss/hpss-archiving-tools)

## Commands
1. `hsi` Starts an interactive session use sparingly and mostly for navigating HPSS
2. `htar` Like `tar` command but with some extra features use this to archive

### [HSI](https://www.racf.bnl.gov/Facility/HPSS/Documentation/HSI/)

Only use for exploring the file system. Puts you into an interactive session to explore your "HPSS" space. Most Linux commands will work as usual such as `pwd`, `ls`, `cd`, etc. albeit with some different options. There is also `save` and `get` commands to archive and grab files from HPSS respectively.

Simply type `hsi` in SDCC rcas nodes to enter into interactive session

#### Single line Examples:
*These commands should be executed from SDCC rcas nodes only and not during interactive session*

1. `hsi "pwd"` will print your home directory on HPSS  
2. `hsi "ls"` will show files in your home directory on HPSS
3. `hsi "get path/to/filename"` will restore *filename*
4. `hsi "save path/to/filename"` will archive *filename*

### [HTAR](https://www.sdcc.bnl.gov/sites/default/files/2021-09/htar.txt)
[See also](https://drupal.star.bnl.gov/STAR/comp/sofi/hpss/htar)

Like Linux `tar` command but with some important differences. `htar` will automatically create a compressed file and archive that. It creates an index file that will allow you to see the files without grabbing them off HPSS. This means if you use `hsi` to browse your files you will see two actual files: one for the index, and the other is the data. You must always supply an "Action" flag. These can be found in the links above. The examples below highlight the most useful ones.

#### Examples:
1. `htar -c -f hpssfilename /path/to/files/*.root` compresses and archives all root files in `/path/to/files`  
    - __NB:__ Overwrite is automatic and doesn't prompt
2. `htar -x -f path/to/hpss/filename` extracts the files in *filename*  
    - __NB:__ If file being extracted exists overwrites without prompting  
3. `htar -t -f path/to/hpss/filename` shows the files in *filename*
