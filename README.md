# git-cheatsheet
## commit difference
`git log master..feature` lists all commits in `feature` that are not yet in `master`. If you've already checked out `feature` you can also use `git log master..`. 3 dots between branches shows the symetric difference between the branches, commits that are in either but not both. [StackOverflow](https://stackoverflow.com/questions/462974/what-are-the-differences-between-double-dot-and-triple-dot-in-git-com)

## content difference
`git diff master..feature` shows the changes in `feature` in respect to `master`, or what would happen to `master` if `feature` was merged into it. With `--stat` only the count of changes per file will be shown, not the whole content.
