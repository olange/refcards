# Supprimer des commits fautifs avec Git

Pour supprimer des commits fautifs qui ont été placés dans un dépôt Git distant (par un `git push`), dont *on sait* qu'ils n'ont pas encore été référencés par d'autres utilisateurs -- parce que l'on est omniscient, mais pas pour autant clairvoyant...

## Revenir à l'état désiré

    $ git reset --hard <commit hash>

## Forcer cet état comme le dernier état du dépôt

    $ git push --force origin master

Ce faisant, on diverge de la ligne des commits maladroits et leur historique est délibérément perdu. Ces commits ne sont alors plus référencés -- dès lors qu'il n'y avait effectivement aucun tag ou autre référence -- et ils seront supprimés au prochain _garbage collect_ exécuté par l'administrateur du dépôt distant.

## Attention

Ces commandes sont susceptibles d'engendrer le chaos lorsque plusieurs utilisateurs collaborent sur le même dépôt. S'il existait déjà des références aux commits que l'on souhaite annuler, tels qu'un tag ou un commit réalisé par un autre utilisateur (pas omniscient celui-là), alors elles résulteront en un micmac prodigieux: d'une part, les commits ne seront pas supprimés par le garbage collect; d'autre part, l'autre utilisateur possède une base de code orpheline et sera bien en peine de la raccrocher à la branche principale.

## Références

* [Undoing in Git -- reset, checkout and revert](http://book.git-scm.com/4_undoing_in_git_-_reset,_checkout_and_revert.html)

(initially published on my [Makandra Cards › Git](https://makandracards.com/git/7347-supprimer-des-commits-fautifs-avec-git))