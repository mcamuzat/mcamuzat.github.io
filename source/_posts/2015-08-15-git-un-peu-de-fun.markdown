---
layout: post
title: "Git un peu de fun"
date: 2015-08-15 21:35:25 +0200
comments: true
categories: ["Git", "Bash", "Github"]
---


Il est possible de faire des commits qui clignotent avec la commande suivante.

``` sh
git commit --all-empty -m "^[[5m Bonjour ^[[0"

```
**Attention** le caractère `^[` est la touche `Escape` (on parle de caractère d'échappement). Il n'est pas très simple à taper. Il faut appuyer sur `Ctrl+v` puis `<ESC>` 

Bienvenue dans le monde du terminal et du ANSI. Il existe de véritable oeuvre d'art juste en mode texte. Et dans le temps les fichiers pirates contenaient souvent des fichiers textes avec Logo et présentation de la team. 


On peut rajouter des trucs plus rigolos

``` sh
git commit --allow-empty -F <(curl https://raw.githubusercontent.com/thiderman/doge/master/doge/static/doge.txt)
```

## Tout les terminaux ne sont pas égaux. 

Voici un gif animé de mes commits

{% img center /images/outgnome.gif 554 410 'So meme' 'So meme' %}

Cela ne clignote pas beaucoup sur `gnometerminal` essayons avec  Xterm

{% img center /images/outxterm.gif 585 397 'Gif animé' 'On a un menu interactif' %}

C'est un peu mieux.

## Mais peux-ton commiter si on a rien à commiter

Avec la commande `--allow-empty` c'est parfaitement possible. 

Mais a quoi cela sert ?

 * A mettre des annotations
 * A distinguer différentes parties


## On se connait et paranoia ?

Dans une ligne de commande (aucun danger).
```
ssh whoami.filippo.io
``` 

Le résultat est surprenant. Le logiciel me reconnait immédiatement (nom et prénom).

{% img center /images/clesssh.png 600 392 'Je ne donne aucun login, pourtant le logiciel me reconnait' 'On se connait' %}

Tout cela vient du fait que lorsque on se connecte en ssh, on envoie toujours sa clé publique.. Et la clé publique de mon ordinateur est connue, car je l'utilise pour me connecter sur github, pour éviter de renseigner à chaque fois mon mot de passe des que je commite. D'ailleurs votre clé publique est ici en `https://github.com/<mon.compte>.keys`

## Conclusion

 * Le contenu vient d'un post sur [hacker-news](https://news.ycombinator.com/item?id=10058967). 
 * pour le ANSI il y a des exemples magnifique sur [blocktronics](http://blocktronics.org/) et aussi [sixteencolors](http://sixteencolors.net/)

Voici quelques utilisations de git, cela ne sert pas à grand chose on est d'accord.. 
