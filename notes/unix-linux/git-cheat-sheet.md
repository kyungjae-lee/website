[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Git Cheat Sheet

# Git Cheat Sheet



## Installation & GUIS

* With platform specific installers for Git, Github also provides the ease of staying up-to-date with the latest release of the command line tool while providing a graphical user interface for day-to-day interaction, review, and repository synchronization.
* For Linux and Solaris platforms, the latest release is available on the official Git web site.
* Git for All Platforms: <http://git-scm.com>




## Setup

Configuring user information used across all local repositories:

- Set a name that is identifiable for credit when review version history

  ```plain
  git config --global user.name "<firstname lastname>"
  ```

- Set an email address that will be associated with each history marker

  ```plain
  git config --global user.email "<valid_email>"
  ```

- Set automatic command line coloring for Git for easy reviewing

  ```plain
  git config --global color.ui auto
  ```

- Show system, global, and (if inside a repository) local configs

  ```plain
  git config --list
  ```

- Also shows the origin file of each config item

  ```plain
  git config --list --show-origin
  ```




## Initialization

* Initialize an existing directory as a Git repository

  ```plain
  git init
  ```

* Retrieve an entire repository from a hosted location via URL

  ```plain
  git clone <url>
  ```




## Stage & Snapshot

Working with snapshots and the Git staging area:

* Show modified files in working directory, staged for your next commit

  ```plain
  git status
  ```

* Add a file as it looks now to your next commit (stage)

  ```plain
  git add <file>
  ```

* Unstage a file while retaining the changes in working directory

  ```plain
  git reset <file>
  ```

* `diff` of what is changed but not stanged

  ```plain
  git diff
  ```

* `diff` of what is staged but not yet committed

  ```plain
  git diff --staged
  ```

* Commit your staged content as a new commit snapshot

  ```plain
  git commit -m "<descriptive_message>"
  ```

  


## Branch & Merge

Isolating work in branches, changing context, and integrating changes:

* List your branches, a `*` will appear next to the currently active branch

  ```plain
  git branch
  ```

* Create a new branch at the current commit

  ```plain
  git branch <branch_name>
  ```

* Switch to another branch and check it out into your working directory

  ```plain
  git checkout
  ```

* Merge the specified branch's history into the current one

  ```plain
  git merge <branch>
  ```

* Show all commits in the current branch's history

  ```plain
  git log
  ```

  


## Inspect & Compare

Examining logs, diffs and object information:

* Show the commit history for the currently active branch

  ```plain
  git log
  ```

* Show the commits on `branchA` that are not on `branchB`

  ```plain
  git log branchB..branchA
  ```

* Show the commits that changed file, even across renames

  ```plain
  git log --follow <file>
  ```

* Show the `diff` of what is in `branchA` that are not in `branchB`

  ```plain
  git diff branchB..branchA
  ```

* Show any object in Git in human-readable format

  ```plain
  git show <SHA>
  ```

  


## Tracking Path Changes

Versioning file removes and path changes:

* Delete the file from project and stage the removal for commit

  ```plain
  git rm <file>
  ```

* Change an existing file path and stage the move

  ```plain
  git mv <existing_path> <new_path>
  ```

* Show all commit logs with indication of any paths that moved

  ```plain
  git log --stat -M
  ```




## Ignoring Patterns

Preventing unintentional staging or committing of files:

* Add the name of the file or the path to the directory to ignore tracking in the `.gitignore` file




## Share & Update

Retrieving updates from another repository and updating local repos:

* Add a git URL as an alias

  ```plain
  git remote add <alias> <url>
  ```

* Fetch down all the branches from that Git remote

  ```plain
  git fetch <alias>
  ```

  > After 'git fetch', 'git log' and 'git status' will show you the difference between origin/master(remote repository) and HEAD->master(local reposiory)

* Merge a remote branch into your current branch to bring it up to date

  ```plain
  git merge <alias>/<branch>
  ```

* Transmit local branch commits to the remote repository branch

  ```plain
  git push <alias> <branch>
  ```

* Fetch and merge any commits from the tracking remote branch

  ```plain
  git pull
  ```




## Rewrite History

Rewriting branches, updating commits and clearing history:

* apply any commits of current branch ahead of specified one

  ```plain
  git rebase <branch>
  ```

* clear staging area, rewrite working tree from specified commit

  ```plain
  git reset --hard <commit>
  ```




## Temporary Commits

Temporarily store modified, tracked files in order to change branches:

* save modified and staged changes

  ```plain
  git stash
  ```

* list stack-order of stashed file changes

  ```plain
  git stash list
  ```

* write working from top of stash stack

  ```plain
  git stash pop
  ```

* discard the changes from top of stash stack

  ```plain
  git stash drop
  ```

