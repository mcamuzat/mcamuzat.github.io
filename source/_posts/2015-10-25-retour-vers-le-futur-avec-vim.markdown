---
layout: post
title: "Retour vers le futur avec Vim"
date: 2015-10-25 18:52:49 +0100
comments: true
categories: ["vim", "git", "mercurial"] 
---

## Parlons de retour vers le futur.

En effet le héros dans le film 2 arrive le 21 octobre 2015. Bon on n'a pas les voitures volantes. Et c'est toujours le même éditeur de texte (vi date de 1976 !).

## Annuler dans VIM

Pour annuler de VIM c'est plutôt simple `u` pour **u**ndo. Pour refaire c'est moins simple `<CTRL + r>`. Mais en pratique c'est plus puissant que cela.

En fait on peut voyager dans le temps avec VIM.

## Retour dans le passé

Grâce à la commande  `:earlier` 

 * `:earlier 5m` reviens en arrière de 5 minutes
 * `:earlier 10` annule 10 modifications
 * `:earlier 5h` annule 5 heures.
 * `:earlier 1f` ramène le fichier avant le dernier enregistrement 
 * `:earlier 2f` ramène le fichier à l'avant-dernier enregistrement 

Bien sur on peut faire un *retour vers futur* avec la commande suivante.

 * `:later 5m` retourne 5 minute plus tard.
 * `:later 10` refait les 10 derniers modifications

Encore plus fort se déplacer dans l'historique de VIM

## Se déplacer dans les différents passés

Celui-ci ce comporte comme un arbre.

Par exemple je rentre le texte `premier`, puis le texte `second`. Puis je change `second` en `troisième` mon historique ressemble à cela.
```
   premier 
     |
   modif 1
     |
   premier second
     |
   modif 2
     |
   premier troisième
```

Si j'annule une étape et que je change le texte mon historique ressemble à cela

```
                  premier 
                     |
                   modif 1
                     |
                   premier second
                ^    |       \
     annulation |  modif 2    modif 3
                |    |               \
                   premier troisième premier quatrième.

```

Impossible de revenir à la modif 2 avec `u` et `Ctrl-r`.

Mais les touches `g+` et `g-` permette de faire cela.

Par exemple `g-` va nous ramener à la modif 2, un seconde fois `g-` nous ramène à la modif 1 etc ..

Pour voir toute les modifications il existe une liste de tous les changements avec `:undolist`

Cela n'est pas très *user-friendly* comme vue. C'est pour cela qu'il existe un plugin vim [gundo](https://github.com/sjl/gundo.vim/) qui rend cela beaucoup plus simple

```
  Undo graph                          File
+-----------------------------------+---------------------------+
| " Gundo for something.txt [1]     |one                        |
| " j/k  - move between undo states |two                        |
| " <cr> - revert to that state     |three                      |
|                                   |five                       |
| @  [5] 3 hours ago                |                           |
| |                                 |                           |
| | o  [4] 4 hours ago              |                           |
| | |                               |                           |
| o |  [3] 4 hours ago              |                           |
| | |                               |                           |
| o |  [2] 4 hours ago              |                           |
| |/                                |                           |
| o  [1] 4 hours ago                |                           |
| |                                 |                           |
| o  [0] Original                   |                           |
+-----------------------------------+                           |
| --- 3 2010-10-12 06:27:35 PM      |                           |
| +++ 5 2010-10-12 07:38:37 PM      |                           |
| @@ -1,3 +1,4                      |                           |
|  one                              |                           |
|  two                              |                           |
|  three                            |                           |
| +five                             |                           |
+-----------------------------------+---------------------------+
```

## Sauvegarder les annulations. 

Parfois on fait des bêtises et que le fichier n'est pas encore versionné et/ou commité (et cela vous est déjà arrivé non ?). Quand on a quitté vim. On perd tout l'historique. Ce n'est plus le cas en précisant un `undofile`

## Conclusion

Nous avons appris à nous déplacer comme des pros dans l'historique vim.

Résumé des touches
 
 * `u` annule, `ctrl+r` refait
 * `:earlier` reviens en arrière
 * `g-` et `g+` reviens/retourne à l'état précédent

## Références

 * la documentation de vim `help undo`
 * [Gundo](https://github.com/sjl/gundo.vim/)
