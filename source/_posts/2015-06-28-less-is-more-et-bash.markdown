---
layout: post
title: "Less is more et BASH"
date: 2015-06-28 19:19:59 +0200
comments: true
categories: ["vim", "emacs", "bash", "linux"] 
---

## less comme tail -f
On peux utiliser `less` pour suivre et parser les logs avec la commande

```
less +F nom_du_fichier
```

Ou tout simplement appuyer sur `F` quand le fichier est ouvert avec `less`.

Less est compatible avec les raccourcis VI donc les commandes suivantes marchent 

```
gg # debut du fichier
G # fin du fichier
/ #recherche
& #affiche seulement les lignes qui contiennent le mot 
h,j,k,l les directions
```
les touche suivantes marche aussi avec `man`

Je vous conseille ce post sur les [mouvement vi](blog/2015/03/08/comprendre-les-raccourcis-claviers-de-vi-slash-vim/)

## Éditer une ligne de commande trop complexe

Si on souhaite récupérer la commande actuelle sous BASH. C'est `Ctrl x + Ctrl e`. Cela ouvre la commande actuelle dans `vi` ou votre éditeur par défaut `$EDITOR` enregistrer et quitter. 

## Copier/Coller dans bash

 * Coupe toute la ligne : `Ctrl u`
 * Coupe à droite du curseur: `Ctrl k`
 * Coller `Ctrl y`

###Cas d'utilisation
```
vim /etc/hosts (oups j'ai oublié le sudo..)
<Ctrl u> sudo <Ctrl k>
```
# Annuler dans Bash

C'est `Ctrl _` 

# En conclusion
Les raccourcis claviers `Ctrl _` et `Ctrl y` sont des raccourcis claviers de emacs. 

Bien qu'utilisateur Vim, j'aime beaucoup Emacs. Notamment le `org-mode`. Emacs est supérieur à Vim. (plus de mode, plus de personnalisation, une véritable interaction en REPL pour les langage type lisp). Il y a une tendance à utiliser Emacs avec le Evil-mode (ce qui en gros rajoute les raccourcis claviers de vim dans Emacs). Mais je pas encore passé le cap.

Il est possible de passer son BASH en Vi-mode avec la commande suivante. Personnellement j'aime pas.

```
set -o vi
```

L'article [suivant](http://www.catonmat.net/blog/bash-vi-editing-mode-cheat-sheet/) aide un peu. Mais ce n'est pas évident de dé-apprendre les raccourcis claviers.

# Quelques liens

 * [the art of the command line](https://github.com/jlevy/the-art-of-command-line)
 * [commandlinefu](http://www.commandlinefu.com)
 * [org-mode](http://orgmode.org/index.html)
 * [evil-mode](http://www.emacswiki.org/emacs/Evil)
