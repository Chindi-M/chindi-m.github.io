---
layout: post
title: "DevOps Git & GitHub Workflow Guide"
date: 2025-01-02
categories: [devops, version-control]
tags: [git, github, version-control, workflow, git-commands, collaboration, tutorial, beginner-friendly, hands-on, rebasing, pull-requests]
---



This guide walks you through the typical workflow used by engineers. You will create repositories, initialise existing folders, write and commit changes, update your work with the latest changes, push code, open pull requests, handle secrets, work with GitHub Actions, and manage common Git operations.

It is designed so that you can follow along on your own machine. Every command is included so that you can copy and practise as you go.

---

## 1. Creating a New Repository in the GitHub Web Interface

1. Log in to GitHub
2. Select **New Repository**
3. Provide a name, choose public or private, and decide if you want a README
4. Select **Create Repository**

Your remote repository is now created.

---

## 2. Creating a New Repository Locally with Code

You can also create a repository using Git.

```bash
mkdir my-project
cd my-project
git init
git add .
git commit -m "Initial commit"
```

Then connect it to GitHub:

```bash
git remote add origin https://github.com/<your-username>/my-project.git
git push -u origin main
```

---

## 3. Initialising Git in an Existing Folder

If you already have a project on your machine, you can initialise Git inside it.

```bash
cd existing-folder
git init
git add .
git commit -m "Initial project commit"
```

If you want to connect this folder to an existing GitHub repo:

```bash
git remote add origin https://github.com/<your-username>/<repo>.git
git push -u origin main
```

---

## 4. Writing Code and Committing Your Work

Add files normally, then run:

```bash
git add .
git commit -m "Describe the work you completed"
```

If you have made several commits and they all relate to the same piece of work, you can squash them into a single commit:

```bash
git rebase -i HEAD~5
```

Change the lines marked for each commit to `squash` or `s`, then write a single commit message.

---

## 5. Working in an Active Repository and Rebasing Often

When several people work in the same repository, you should regularly update your branch with the latest changes.

**Step 1: Save your current work**

If you have uncommitted changes, either commit them or stash them:

```bash
git add .
git commit -m "Work in progress"
```

Or stash them temporarily:

```bash
git stash
```

**Step 2: Update your local main branch**

Switch to main and pull the latest changes:

```bash
git checkout main
git pull origin main
```

**Step 3: Rebase your feature branch**

Switch back to your branch and rebase it onto the updated main:

```bash
git checkout <branch-name>
git rebase main
```

**Step 4: Handle any conflicts**

If conflicts appear, Git will pause and show which files need attention. Edit the conflicting files, then:

```bash
git add <file>
git rebase --continue
```

Repeat until the rebase completes.

**Step 5: Push your rebased branch**

After rebasing, you'll need to force push:

```bash
git push origin <branch-name> --force-with-lease
```

**Step 6: Restore stashed changes (if applicable)**

If you stashed changes earlier:

```bash
git stash pop
```

**Quick tip:** `git commit --amend` allows you to modify your most recent commit instead of creating a new one. This is useful for fixing typos or adding forgotten files:

```bash
git add <forgotten-file>
git commit --amend --no-edit
```

---

## 6. After Rebasing and Committing, Push Your Code Correctly

Always confirm that you are pushing from the correct local branch to the correct remote branch.

Check your branches:

```bash
git branch -vv
```

Push your work:

```bash
git push origin <branch-name>
```

If you rebased and Git requests a force push:

```bash
git push origin <branch-name> --force-with-lease
```

---

## 7. Opening a Pull Request

Once your branch is pushed to GitHub, open the Pull Request in the web interface.

Provide a clear description in the PR comments. Include what you changed, why you changed it, and anything reviewers need to know. This supports clear and helpful reviews.

---

## 8. Working with Secrets in GitHub

Some repositories require credentials to run code or pipelines. You can store them safely in GitHub by going to:

**Settings → Secrets and variables → Actions → New repository secret**

Use environment variables such as `API_KEY` or `DB_PASSWORD` in your workflows without placing them in your code.

---

## 9. Basic GitHub Actions Structure

GitHub Actions uses a standard folder structure:

```
repo/
  .github/
    workflows/
      workflow-file.yml
```

Any YAML file placed inside `.github/workflows` will run automatically when its triggers match.

You will learn more about Actions in a later guide.

---

## 10. Pulling Down Changes from a Repository

To update your local copy:

```bash
git pull
```

To pull with rebase:

```bash
git pull --rebase
```

To clone a complete repository for the first time:

```bash
git clone https://github.com/<user>/<repo>.git
```

---

## 11. Forking a Repository

Forking creates your own copy of another repository so that you can improve the original project. You can then submit a Pull Request to suggest your updates.

After creating your fork in the GitHub UI, clone it:

```bash
git clone https://github.com/<your-username>/<repo>.git
```

Add the original as an upstream remote:

```bash
git remote add upstream https://github.com/<original-owner>/<repo>.git
```

To keep your fork up to date:

```bash
git fetch upstream
git rebase upstream/main
git push origin main
```

---

## 12. Common Git Configuration Tasks

Set your preferred editor for commit messages.

**Vim:**

```bash
git config --global core.editor "vim"
```

**Nano:**

```bash
git config --global core.editor "nano"
```

**Visual Studio Code:**

```bash
git config --global core.editor "code --wait"
```

**Amend the last commit message:**

```bash
git commit --amend
```

**View status:**

```bash
git status
```

**View commit history:**

```bash
git log
```

**View remote repositories:**

```bash
git remote -v
```

**Set a remote repository:**

```bash
git remote add origin https://github.com/<user>/<repo>.git
```

---

## 13. Installing Git Bash

On Windows, you can install Git Bash from the official Git website. After installation, open Git Bash to run all the commands in this guide.

Popular commands include:

- `git clone`
- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`
- `git fetch`
- `git branch`
- `git merge`
- `git rebase`

---

## 14. Handling Merge Conflicts

If a conflict appears, Git will tell you which files need attention.

Open the file and look for markers like:

```
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> origin/main
```

Edit the file to keep the correct lines. Then:

```bash
git add <file>
git rebase --continue
```

Or after a merge:

```bash
git commit
```

---

## Final Notes

This guide gives you hands-on experience with the tasks DevOps engineers perform every day. Keep practising these commands until they become comfortable. The more you use Git and GitHub, the more confident you will become with collaborative workflows, automation, and version control.

---

