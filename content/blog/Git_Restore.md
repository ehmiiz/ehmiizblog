--- 
title: "How to Restore a File from Git" 
date: 2023-11-22T08:23:11+01:00 
draft: false

---

{{< figure
  src="/pics/restore_git.png"
>}}

Git is a powerful and popular version control system, sometimes a bit too powerful.

Depending on how your day went, you may want to restore a file from git to a previous state, either because you made an oopsie, want to undo some changes, or need to compare different versions.

Let's go through four common scenarios on how to do just that!

## Scenario 1: Saved Locally on the Local Git Repository

The simplest scenario is when you have saved your file locally on your local git repository, but have not staged or committed it yet.

In this case, you can use the git restore command to discard the changes in your working directory and restore the file to the last committed state.

For example, if you want to restore a file named index.html, you can run the following command:

```bash
git restore index.html
```

This will overwrite the index.html file in your working directory with the version from the HEAD commit, which is the latest commit on your current branch.

 You can also use a dot (.) instead of the file name to restore all the files in your working directory.

```bash
git restore .
```

## Scenario 2: Saved Locally and Staged Locally

The next scenario is when you have saved your file locally and staged it locally, but have not committed it yet.

In this case, you can use the git restore --staged command to unstage the file and remove it from the staging area.

For example, if you want to unstage a file named index.html, you can run the following command:

```bash
git restore --staged index.html
```

This will remove the index.html file from the staging area and leave it in your working directory with the changes intact. You can then use the git restore command as in the previous scenario to discard the changes in your working directory and restore the file to the last committed state. Alternatively, you can use this command:
```bash
git restore --source=HEAD
```
To unstage and restore the file in one step.

For example, if you want to unstage and restore a file named index.html, you can run the following command:

```bash
git restore --source=HEAD index.html
```

This will remove the index.html file from the staging area and overwrite it in your working directory with the version from the HEAD commit. You can also use a dot (.) instead of the file name to unstage and restore all the files in your staging area and working directory.

## Scenario 3: Saved Locally, Staged Locally and Committed

The third scenario is when you have saved your file locally, staged it locally and committed it, but have not pushed it to the remote repository yet. In this case, you can use the git reset --hard command to reset your local branch to the previous commit and discard all the changes in your staging area and working directory. For example, if you want to reset your local branch to the previous commit, you can run the following command:

```bash
git reset --hard HEAD~1
```

This will reset your local branch to the commit before the HEAD commit, which is the latest commit on your current branch.

This will also discard all the changes in your staging area and working directory, including the file you want to restore.

You can then use the git checkout command to check out the file from the previous commit and restore it to your working directory.

For example, if you want to check out and restore a file named index.html from the previous commit, you can run the following command:

```bash
git checkout HEAD~1 index.html
```

This will check out the index.html file from the commit before the HEAD commit and overwrite it in your working directory with the version from that commit.

You can also use a dot (.) here as well, to check out and restore all the files from the previous commit.

## Scenario 4: Saved Locally, Staged Locally, Committed and Pushed to Remote Repository

The fourth and final scenario is when you have saved your file locally, staged it locally, committed it and pushed it to the remote repository.

In this case, you can use the git revert command to create a new commit that reverses the changes in the previous commit and restores the file to the state before that commit.

For example, if you want to revert the previous commit and restore a file named index.html to the state before that commit, you can run the following command:

```bash
git revert HEAD
```

This will create a new commit that reverses the changes in the HEAD commit, which is the latest commit on your current branch.

This will also restore the index.html file in your working directory and staging area to the version from the commit before the HEAD commit.

You can then push the new commit to the remote repository to update it with the reverted changes.

You can also use the --no-commit option to revert the changes without creating a new commit, and then use the git restore or git checkout commands as in the previous scenarios to restore the file to the desired state.

## To sum it up

We've demonstrated how to restore a file from git in four different scenarios, depending on how far you have progressed in the git workflow.

We have used the git restore, git reset, git checkout and git revert commands to discard, unstage, check out and revert changes in your files and restore them to the previous states.

I hope this post has been helpful and maybe even saved some headache!

If you have any questions or feedback, please feel free to DM me on Twitter or LinkedIn.

### Happy coding