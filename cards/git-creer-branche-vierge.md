# Créer une branche vierge avec Git

Pour créer une branche vierge `mybranch`, sans référence à un état préalable du dépôt -- par exemple, pour versionner documentation générée par RDoc, ou encore travailler sur les Ruby Koans:

    $ git symbolic-ref HEAD refs/heads/mybranch
    $ rm .git/index 
    $ git clean -fdx 
    $ <travailler sur les sources> 
    $ git add <nouveaux fichiers>
    $ git commit -m 'Commit initial'
    
## Référence

* [Git Community Book -- Creating new empty branches](http://book.git-scm.com/5_creating_new_empty_branches.html)

(initially published on my [Makandra Cards › Git](https://makandracards.com/git/7349-creer-une-branche-vierge-avec-git))