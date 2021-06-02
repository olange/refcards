# Renommer une branche distante

## Rename branch locally    
$ git branch -m ‹old_branch› ‹new_branch›

## Delete the old branch    
$ git push origin :‹old_branch›

## Push the new branch, set local branch to track the new remote
$ git push --set-upstream origin ‹new_branch›

(initially published on my [Makandra Cards › Git](https://makandracards.com/git/34935-renommer-une-branche-distante))