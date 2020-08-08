Adding Entry to The UCR Github Page
=====================================

- The UCR Github web page address is [`ucr-rhic.github.io`](https://ucr-rhic.github.io). And the corresponding Github repository is [`https://github.com/ucr-rhic/ucr-rhic.github.io`](https://github.com/ucr-rhic/ucr-rhic.github.io). You always update or modify the Github repository and the changes will be automatically applied to the Github web page within a couple of minutes.

- Any text file written in Markdown format will be translated to web pages. Markdown is a very easy/intuitive text-based format. Markdown files are generally given `.md` as extension.  Knowing the very few basic syntax will be sufficient in our case. For example, compare the main README.md file in raw format [here](https://raw.githubusercontent.com/ucr-rhic/ucr-rhic.github.io/master/README.md) and it's translated format [here](https://github.com/ucr-rhic/ucr-rhic.github.io/blob/master/README.md).


There are two ways you can add an entry or modify an existing entry to the UCR Github page.
The first approach does not require any prior knowledge of `git`, I will call it `non-git user's` approach. The other approach assumes you are a git user who is familiar with the `git` commands. In both cases, you need to have a Github account and the account should be added to this repository as a collaborator.


Non-git User's Approach
-------------------------

- Go to the UCR Github repository [here](https://github.com/ucr-rhic/ucr-rhic.github.io) and login to your Github account.

**Modifying The Main Page**
- The `README.md` file in the top directory is translated as the main page. If you want to modify the main page, you need to modify this `README.md` file. To modify:

  - Click on the README.md file  
  - Click on `edit this file` icon on the top right (represented with a pen symbol) 
  - Now make you changes 
  - Commit changes when done by pressing the green button.


**Adding New File**
- New files are added to the corresponding directory (say `meetings, how-tos` etc).
- Go to the appropriate directory.
- Add file > Create New file
- Give the file an appropriate name with `.md` extension
- Enter the content of the file using Markdown syntax.
- Commit changes.
- Link your newly created file to the main `README.md` file in the top directory following the instruction in the previous paragraph.


Git User's Approach
---------------------

You are a git user if you use git for versioning of your analysis code, tex files, etc. In this case, you just clone the repository `https://github.com/ucr-rhic/ucr-rhic.github.io.git` like any other git repository. Now add files/directory or modify any content. Finally, push your changes to the `master` branch. Before you make changes, do `git pull` to get new updates incorporated. 
