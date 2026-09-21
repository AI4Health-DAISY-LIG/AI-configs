	
  
  # Git & GitHub — Notes

A quick reference covering Git fundamentals, essential commands, branches, remotes, and best practices for working with Git and GitHub.
## Overview

- Git is a local open source VCS software : version control system designed to handle everything from small to very large projects with speed and efficiency: enables to save snapshots of the projects over time
- Github a web based platform that incorporates Git version features / social coding .. (git hosting service)
- Github is a Microsoft platform (acquired by Microsoft in 2018)
- With Git : Gitlab another platform : store code and use Git : same as Github but no need for pull request (and other differences..)
- **Bitbucket**: another Git hosting platform, often used with Atlassian tools (Jira, Trello)
- Fork/pull/merge :
  - **Fork**: copy an existing repository to your own account so you can modify it without touching the original
  - **Pull request (Github) / Merge request (Gitlab)**: a request sent to the owner of the original repository to integrate changes made on your fork/branch
  - **Merge**: combine the changes from one branch into another
- Repo : file location — the folder containing all the project's files as well as the entire version history (the hidden `.git` folder)
- Commit: command used to save new changes
- Stage : preparation step before commit (index / staging area)
- Branch: the part of the project I'm changing — lets you develop a feature in isolation without affecting the main branch (often `main` or `master`)

### Git's working areas

- **Working directory**: the files as they currently are on disk
- **Staging area (index)**: the changes selected to be included in the next commit
- **Repository (local)**: the history of already-recorded commits

## Version control

- locally (server) with a database and metadata (for modification info)
- Centralized (e.g. CVS, SVN..): with a single server repository
- Distributed (e.g. Git, Mercurial, Bazaar..): a server repository, full retrieval/integration of the history: the history is the set of all commits made — each user has a complete copy of the repository and its history, which allows working offline

## Basic system commands

- `pwd`: path of the folder I'm currently in
- `mkdir folder_name`: create a folder
- `ls`: list existing files: `ls folder_name/`
- `cd folder_name/` : move into the folder
- `cd ..` : move one folder back
- `cd full_path/`
- `rm file_name` : delete a file ; `rm -r folder_name` : delete a folder
- `clear` : clear the terminal

## Git configuration

- `git config --global user.name ".."`
- `git config --global user.email ".."` : global configuration (name + email address set once for good)
- `git config --global --list`: to list Git's configuration
- `git` is equivalent to `git --help`
- `git init` : initialize a new Git repository in the current folder (creates the hidden `.git` folder)
- `git clone repo_url` : retrieve a full copy of a remote repository (with its entire history) onto your machine

## Workflow (status / add / commit)

- `git status` : the current state of the workspace: detects added files not yet committed, or other issues..
- `git add file_name` : staging
- `git add .` : stage everything (all files)
- `git reset file_name` : unstage the file in question (the file stays modified but leaves the stage)
- `git commit -m "message"`: a commit attached to a message
- `git diff`: shows all the changes present in the workspace compared to the last version saved in the repository
- `git diff --staged` : shows changes already staged compared to the last commit
- Changes in Git are always treated as line additions/deletions (+ or -)
- `git log` : shows the commit history (author, date, message, hash)
- `git log --oneline --graph` : a condensed, visual version of the history, useful for seeing branches

PS: Changes in Git are always treated as additions/deletions of lines (+ or -).

 (add figure1)

### Undoing changes

- `git checkout -- file_name` : discards unstaged changes to a file (reverts to the last committed version)
- `git revert <commit_hash>` : creates a new commit that undoes the changes of a previous commit (without rewriting history)
- `git reset --hard <commit_hash>` : goes back to a previous commit, removing the commits that came after (careful, destructive)

## Branches

- `git switch -c branch_name` : create a branch (and switch to it)
- `git branch` : list local branches
- `git switch branch_name` (or the older syntax `git checkout branch_name`) : switch to an existing branch
- `git branch -d branch_name` : delete a branch that has already been merged
- `git merge branch_name` : merge the given branch into the current branch







## Remote repositories

- Accessing the Github repo over SSH : to be able to clone via SSH (requires generating an SSH key with `ssh-keygen` and adding it in the Github account settings)
- Direct link between the local repository and the open-source owner's public repository:
  `git remote add owner_name owner_url`
- `git remote -vvv`: check which remotes exist (with their destination: owner or origin, along with their operations fetch, push..)
- To update the local repo relative to the owner's repo:
  `git pull remote_name branch_name`
  Then: `git push remote_name(origin) branch_name(master)` to update my personal repo
- `git fetch` : retrieves the latest info from the remote repository without automatically merging (unlike `pull`, which does `fetch` + `merge`)

## Contributing to an open source project

1. Fork the repository on Github/Gitlab
2. Clone your fork locally (`git clone`)
3. Create a branch dedicated to the change (`git switch -c`)
4. Make the changes, `add` then `commit`
5. `push` the branch to your fork
6. Open a Pull Request / Merge Request to the original repository
7. Wait for review and any requested changes before merging

   (add figure2)

## Repository files and best practices

- **README.md** : the project's presentation file (installation, usage..), automatically displayed on the repository's page
- **.gitignore** : list of files/folders that Git should not track (e.g. `node_modules/`, local config files, secrets..)
- **Issues** : bug tracking and feature requests on Github/Gitlab
- **Tags/Releases** : mark a specific version of the project (e.g. `v1.0.0`)
- **git stash** : temporarily set aside uncommitted changes to switch context, then retrieve them with `git stash pop`

## Tools

- Git Bash / Git GUI
- A tutorial showing some basic Git commands is provided in the doc folder: http://codeur-pro.fr/cadeau-formation-git/
