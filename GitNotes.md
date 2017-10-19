# Git Tips and Tricks
## Useful Commands ##
**Creating & tracking branches**

To create a new local branch:
```
git checkout -b feature/winners-page parent-branch
```
To add this local branch to the centralised repository (origin):
```
git push -u origin feature/winners-page
``` 
This pushes the branch to the remote repository (origin) and the `-u` flag sets it as a remote tracking branch.

*After this initial push, `git push` can be used without any parameters.*


If you wish to create a new local branch and for it to track an exisiting branch on the repository, use:
```
git checkout -b <my_new_branch> <remote>/<branch_name>
```
ie `git checkout -b develop origin/develop` will create a local branch called develop, and will track the upstream branch develop on the origin.

If things get muddled and you're not sure if the right branches are being tracked, use the following command to display the list of what is being tracked for push and pull requests:
```
git remote show origin
```
**Deleting branches**

You can delete a remote branch with:
```
git push -d <remote_name> <branch_name>
```
and to delete a local branch use:
```
git branch -d <branch_name>
```

## Git workflow

You should fetch and pull before you push any changes. In an instance where you try to push,
but get notified that you need to pull first you should use the `--rebase` flag which moves your changes to the head of the branch you're pushing too.

`git pull --rebase origin master`

This reduces the need for git to merge your changes which is often unncessary. 

This method also allows you to deal with any merge conflicts easier due to the fact that it moves each commit one by one to the head of the branch, 
so it will stop when it reaches a conflict on a commit by commit basis rather than one big merge commit.

If any conflicts are found, this message:
```
CONFLICT (content): Merge conflict in <some-file>
``` 
will be returned. By using `git status` you can find where the problem is.

Once all changes have been made to fix the conflict, stage the updated files and run the command:

```
git rebase --continue
```
and it will continue where it left off, and the process repeats until every commit has been moved.

If you need to stop the rebase, the command `git rebase --abort` will revert you back to where you started.


### Setting up feature branches
Feature branches should have descriptive names, like `feature/winners-page` or `issue-#1011`.





## Gitflow Workflow
Gitflow Workflow defines a strict branch model of how different branches should interact with each other. 
It states that `master` should never merge with feature branches. Instead, a `develop` branch should be used to dictate a *in progress* version of the product, and is the parent branch of all feature branches.

By doing this, it keeps the `master` branch in a production ready environment.

When the `develop`branch has enough features to warrant a new version, it gets forked. 
This creates the next release cycle, and no new features should be added after this point - only bug fixes, documentation and release orientated changes.

This allows for the team to be split up and work completely independantly - those who are working on the next release, 
and those who are creating new features.

**Maintenance Branches**

Maintenance or *hotfix* branches are used to quickly patch bugs in the production version, and is the only type of branches that are allowed to fork off of `master`
