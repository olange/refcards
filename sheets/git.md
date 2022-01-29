# Git

## Revision references

(source: [Git-SCM › Book › Git Tools › Git revision selection](http://git-scm.com/book/en/Git-Tools-Revision-Selection))

|Revision ref|Description|
|---|---|
|`a99d9d5`|short SHA-1 hash|
|`HEAD~1`|shorthand for the commit before HEAD|
|`branch`|commit pointed to by the branch reference|
|`HEAD@{5}` and `master@{yesterday}`|RefLog shortnames|
|`HEAD^`|ancestry ref: parent of commit HEAD|
|`HEAD~`|ancestry ref: first parent of HEAD (= `HEAD^`)|
|`d921970^2`|ancestry ref: the second parent of commit d921970|
|`d921970~2`|ancestry ref: the first parent of the first parent |(the grandparent)

## Undoing

|Command|Description|
|---|---|
|`git checkout ‹pathspec›`|restores the named paths in the working tree from the index|
|`git reset --soft HEAD~1`|deletes the commit but leaves all your changed files as `git status` would put it (if you have committed, but not pushed) [more](http://nakkaya.com/2009/09/24/git-delete-last-commit/)|
|`git revert HEAD`|creates a new commit that reverses everything introduced by the accidental commit (if already pushed and someone pulled)|

## Status

|Command|Description|
|---|---|
|`git reflog`|to see where your HEAD and branch references have been|
|`git show master@{yesterday}`|to see where your master branch was yesterday|

### See also

* [Cards › Git](../cards/README.md)