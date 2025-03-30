
[Certification](./Git/01Introduction_to_Git_Certificate.pdf)


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

Adding to the staging area:
- Adding a sigle file *git add README.md*
- Adding all modified files *git add .* (. = all files in the current directory and sub-directories)

Making a commit:
- *git commit -m "Adding a README"*
- *-m* allows a log message without opening a text editor
- without *-m*, *git commit* would open a text editor, which we need to add a message to, save the file, then exit to return to the terminal.

## 03 Version History
Git commits have 3 parts:
1. **Commit**: contains the metadata- autor, log message, commit time
2. **Tree** tracks the names and locations of files and directories in the repo, like a dictionary - mapping keys to files/directories
3. **Blob** Binary Large OBject, contain data of any kind, a compressed snapshot of a file's contents.

Git hash: a unique identifier, if 2 files are the same, then the hash are the same.

*git log* shows commits from newest to oldest, we can press *space* to show more recent commits, press *q* to quit the log.

## 04 Version history tips and tricks
Projects grow, more and more commits:
- restricting the number(3...) of commits *git log -3*
- restricting the file, to only look the commit history of one file *git log report.md*
- *git log -3 report.md*
- restricting by date *git log --since='Apr 2 2024'*, *git log --since='Apr 2 2024' --until='Apr 11 2024'*
- finding a particular commit with hash 
  - *git log*
  - *git show c27df23hebt*

## 05 Comparing version
*git diff report.md* compare last commited version with latest version not in the staging area.

*git diff --staged report.md* compare last committed version of *report.md* with the version **in the staging area**

*dit diff 37f423hb 1839djhi* compare 2 commits(hashes), what changed **from** first hash **to** second hash.

*HEAD* state latest commit. *git diff HEAD~1 HEAD* compare second most recent with the most recent commit

## 06 Restoring and reverting files
*git revert* reinstates previous version and makes a commit, and restores **all files updated in the given commit**:
- *a78hditt*,*d35bu45b*...
- *HEAD*...
- By default, this opens a text editor where we are propted to add a commit message
- Avoid opening the text editor *git revert --no-edit HEAD*
- Revert without committing(bring files into the staging area) *git revert -n HEAD*

*git checkout* reverts a sigle file
- *git checkout HEAD -- report.md*

unstaging a single file
- *git restore --staged report.md*
- Move the file back to the working directory, allowing us to edit it before restaging and committing, *git add report.md* + *git commit -m "Editing and adding"*

*git restore --staged* remove all files from the staging area

