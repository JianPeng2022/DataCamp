## 01 Version Control
Shell/Terminal: executing commands

*pwd* print current working directory.

<span style="background-color: black; color: white; padding: 4px;">/home/DATACAMP/Git</span>

*ls* what is in our current directory.

<span style="background-color: black; color: white; padding: 4px;">archive  data.csv  ai.py</span>

***cd** archive* open the *achive* folder

## 02 git --version
Git repository = directory contining files and sub-directories + Git storage *.git* (history version)

Benefits: 
- Systematically track versions
- Revert to previous versions
- Compare versions at different points in time 
- Collaborate with collegues 

*git init data-ai-project*
*git status*
*git init* convert an existing directory into a Git repo.

<span style="color: red;">Do not create a Git repo inside another Git repo- **Nested Repos**</span> Because when committing, git cannot distinguish.

Git workflow:
1. Edit  and save files on our computer
2. Add the files to the Git **Staging area**, tracks what has been modified
3. Commit the files, Git takes a snapshot of the files at the point in time, allow us to compare and revert files.



