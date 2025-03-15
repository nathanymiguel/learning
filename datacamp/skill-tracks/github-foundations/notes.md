# Notes
Here's the most important commands. [^1]

How to create update this file locally:
1. the right repo `cd learning`
2. create branch `git checkout -b update-notes-YYYY-MM-DD`
3. stage the file `git add datacamp/skill-tracks/github-foundations/notes.md`
4. commit the changes `git commit -m "Atualiza as notas do curso GitHub Foundations"`
5. push the changes `git push origin update-notes-YYYY-MM-DD`
6. create the PR in GitHub, remember to mention Fix #000 (issue number)


## **1. Introduction to Git (Course)**
### **Fundamentals**

| Command | Description |
| ----------- | ----------- |
|`pwd` | know my directory |
|`cd` | join/change a directory |
|`ls` | show files of a directory |
|`git --version` | check version |

### **Creating Repo**
| Command | Description |
| ----------- | ----------- |
|`git init` | convert an existing directory to a repo |
|`git init (name)` | create a repo |
|`cd` | go to created repo |
|`git status` | what is being tracked |

### **Staging and Commiting**
| Command | Description |
| ----------- | ----------- |
|`git add report.md`| stage one single file |
|`git add .` | stage all files | 
|`git commit -m "Comment"`| comment commit |

### Structure

| Command | Description |
| ----------- | ----------- |
|Commit | metadata, author, log author, log message | 
|Tree | name and location of files and directories in the repo |
|Blob | Binary Large Object, snapshot of file's contents |

### Git Logs
| Command | Description |
| ----------- | ----------- |
|`git log` |commits from newest to oldest|
|`space` |for more commits|
|`q` |for quit |
|`git log -3` |most recent 3 commits|
|`git log report.md`| limit log of one file |
|`git log -3 report.md`| limit 3 commits log of one file |
| `git show c27fa856` | 8-10 characters of the hash of the commit |

### Git Diff
| Command | Description |
| ----------- | ----------- |
|`git diff report.md` | Shows changes between an unstaged file and the latest commit.|
|`git diff --staged report.md` | Shows changes between a staged file and the latest commit.|
|`git diff 35f4b4d 186398f` | Shows changes between two commits using commit hashes.|
|`git diff HEAD~1 HEAD~2` | Shows changes between two commits using HEAD syntax.|

### Git Revert
| Command | Description |
| ----------- | ----------- |
|`git revert HEAD` | Revert all files from a given commit.|
|`git revert HEAD --no-edit` | Revert without opening a text editor.|
|`git revert HEAD -n` | Revert without making a new commit.|
|`git checkout HEAD~1 -- report.md` | Revert a single file from the previous commit.|
|`git restore --staged report.md` | Remove a single file from the staging area.|
|`git restore --staged` | Remove all files from the staging area.|

## **2. Intermediate Git (Course)**

Branch as a version of a repo, goals: new feature or fix bugs.

### Branches

| Command | Description |
| ----------- | ----------- |
|`git diff main chatbot`	|Compare the state of the main and chatbot branches|
|`git branch`	|List all branches|
|`git branch -m old_name new_name`	|Rename branch called old_name to new_name|
|`git branch -d chatbot`	|Delete chatbot branch, which has been merged|
|`git branch -D chatbot`	|Delete chatbot branch, which has not been merged|
|`git merge ai-assistant`|From main, to merge ai-assistant into main|
|`git merge ai-assistant main` | From another branch: git merge source destination

### Clones


| Command | Description |
| ----------- | ----------- |
 |`git clone path-to-project-repo`	 |Clones a repository from the specified path. |
 |`git clone /home/george/repo`	 |Clones a local repository from the given path. |
 |`git clone /home/george/repo new_repo`	 |Clones and renames a local repository. |
 |`git clone URL`	|Clones a remote repository from the specified URL. |
 |`git clone https://github.com/datacamp/project`	 |Clones a specific project from GitHub. |
 |`git remote add name URL`	 |Adds a new remote with the specified name and URL. |
 |`git remote add george https://github.com/george_datacamp/repo`	 |Creates a remote named george with the specified URL.
|`git remote -v`	|Displays all configured remotes for the repository with their URLs.
|`git fetch origin`	|Fetches updates from the remote repository but does not merge.
|`git pull origin`	|Fetches updates from the remote and merges them into the current branch.
|`git push origin documentation`	|Pushes changes from the local 'documentation' branch to the remote.

## **3. Introducation to GitHubt Concepts (Course)**

"How to create a repository and a README file for your data projects."

"How to add, modify, and delete files, switch branches, create private repos, add collaborators, and generate Personal Access Tokens."

"How to copy a repository, how to communicate with your peers on GitHub, and all about pull requests." 

## **4. Introduction to GitHub Products: A Complete Guide (Resource)**

https://www.datacamp.com/blog/github-products

## **5. Intermediate Github Concepts (Course)**

Authentication Methods: You explored different methods to secure GitHub repositories, such as:

| Command | Description |
| ----------- | ----------- |
Two-Factor Authentication (2FA)|  Adds an extra security step by requiring a code from your phone, enhancing account protection.
Personal Access Tokens (PATs)|  Serve as a substitute for passwords, allowing tools and tasks to access GitHub with customizable permissions.
SAML Single Sign-On (SSO)|  Enables a single login for multiple services through a single identity provider, streamlining access management.
2FA in GitHub Organizations|  You examined the importance of enforcing 2FA within your organization. Key insights included:

Members who haven't enabled 2FA will be removed and notified if it's enforced.
Recovery codes are available to regain access if a 2FA device is lost.

## **6. Introducation to GitHub CodeSpaces (Tutorial)**

https://app.datacamp.com/learn/tutorials/github-codespaces

[^1]: The most important commands ==until now== :joy:.