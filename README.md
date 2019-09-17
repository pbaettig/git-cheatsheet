# git-cheatsheet
## Diff
### Commits
`git log master..feature` lists all commits in `feature` that are not yet in `master`. If you've already checked out `feature` you can also use `git log master..`. 3 dots between branches shows the symetric difference between the branches, commits that are in either but not both. [StackOverflow](https://stackoverflow.com/questions/462974/what-are-the-differences-between-double-dot-and-triple-dot-in-git-com)

### Content
`git diff master..feature` shows the changes in `feature` in respect to `master`, or what would happen to `master` if `feature` was merged into it. With `--stat` only the count of changes per file will be shown, not the whole content.

## Squashing commits
Assume we're on a branch called `feature`, there we have 5 commits:
```
9479527 change #3
5d68bfe change #2
262ed77 fix change #1
2d05bc4 fix change #1
b70ed7b change #1
```

Before merging we want to clean up the history a bit and squash all the commits related to change #1.
Start a visual rebase of the last 5 commits: `git rebase -i HEAD~5`
```
pick b70ed7b change #1
fixup 2d05bc4 fix change #1
fixup 262ed77 fix change #1
pick 5d68bfe change #2
pick 9479527 change #3
```
Squashing (with `fixup` in this case) works from the bottom up, so a commit will be combined with the one above it.

After finishing the rebase the commit history looks like this:
```
db17d5d change #3
037be91 change #2
58b11dc change #1
```
Note that the commit IDs have changed, that's what can make rebasing potentially dangerous. Git uses commit IDs to determine changes when merging for example.


## Merging
### fast-forward merge
consider two branches
```
bugfix        /----O----O---\
             /               \
master  ----O----------------------
```
The `bugfix` branch contains 2 new commits, no new commits have been added to master during the "lifetime" of the branch. When merging `bugfix` back to `master` git can perform a fast-forward merge, which means that the commits from `bugfix` are incorporated into `master` but no merge commit will be generated. This is only possible because there are now concurrent changes that need merging. If you need a merge commit use the `--no-ff` switch.

To revert the merge you can simply reset to the commit when the branch was created (the very first `O` in the diagram above)

### traditional merge
consider this example:
```
feature        /----O----O----O--\
              /                   \
master   ----O----O-----O----O-----O---
```
There are concurrent changes between `feature` and `master`, so when merging the branches git has to actually look at the commits to make sure there are no conflicts. A fast-forward merge is not possible and a merge commit will be generated.

### preview merge
1. run `git fetch` to update commits from origin
2. to preview the changes that are coming in from `origin/master` run `git diff master..origin/master`<br>
to look at the incoming commits run `git log master..origin/master` or `git log orgin/master...` if you are already on `master`

## Rebase
A helpful way to look at what a rebase does is:
A rebase moves your changes on top of the branch you are rebasing against. Like moving the commit off which the branch was created (or the start point).

This has the advantage that no merge conflicts are created and keeps the history of the branch clean. The major disadvantage is that all commit IDs of your work change.

Example:

`feature` was branched off `master` at commit `X`. The developer has been working on `feature` and has created a number of commits. In the meantime new commits have been added to master (bugfixes, etc). To incoroporate these changes into `feature` without polluting the history with merge commits a rebase is the tool of choice. 

Before
```
feature        /----F----F----F--
              /
master   ----X----O-----O----O-----O
```

After
```
feature                              /----F'---F'---F'-
                                    /
master   ----X----O-----O----O-----O

```
Note that all the commits in `feature` have changed (`F` to `F'`)


## Reset
### Reset local changes
Suppose you have commits on your local branch that have not yet been pushed to the upstream branch, as well as some changes that have not yet been committed but should be preserved.
```
On branch master
Your branch is ahead of 'origin/master' by 10 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   pascal/stuff.md
```

To reset the local branch back to the state of the published upstream branch, run `git reset --merge origin/<branch>`. This will discard all the committed changes from the local branch while preserving the uncommited changes.

Use `git reset --hard origin/<branch>` to reset the branch and discard all changes in the working directory.

`git reset origin/<branch>` resets the branch (remove all commits that are not in the upstream branch) but preserve all the changes in the working directory.
