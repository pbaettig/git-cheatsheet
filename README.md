# git-cheatsheet
## commit difference
`git log master..feature` lists all commits in `feature` that are not yet in `master`. If you've already checked out `feature` you can also use `git log master..`. 3 dots between branches shows the symetric difference between the branches, commits that are in either but not both. [StackOverflow](https://stackoverflow.com/questions/462974/what-are-the-differences-between-double-dot-and-triple-dot-in-git-com)

## content difference
`git diff master..feature` shows the changes in `feature` in respect to `master`, or what would happen to `master` if `feature` was merged into it. With `--stat` only the count of changes per file will be shown, not the whole content.

## squashing commits
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

