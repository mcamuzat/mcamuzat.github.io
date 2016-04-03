---
layout: post
title: "Nano : Activer la coloration syntaxique"
date: 2016-03-27 21:41:45 +0200
comments: true
categories: ["vim", "linux", "nano"]
---

Sous linux, on utilise le plus souvent `vim` ou `nano`. Je suis un grand fan de de `vim` mais aujourd'hui je vais parler de `nano`. Par défaut il n'y a pas de coloration syntaxique et pas de couleurs tout cours. Nous allons activer celle-ci

Et voici le résultat sur un fichier php.

{% img center /images/nanocolors.png 600 404 'la coloration syntaxique sous nano' 'la coloration syntaxique' %}

<!--more-->

Il faut créer un fichier `.nanorc` dans votre home. Et rajouter la ligne suivante.


```
include /usr/share/nano/sh.nanorc
```

Tous les fichiers se situent `/usr/share/nano`

```
asm.nanorc     fortran.nanorc   man.nanorc     ocaml.nanorc   ruby.nanorc
awk.nanorc     gentoo.nanorc    mgp.nanorc     patch.nanorc   sh.nanorc
cmake.nanorc   groff.nanorc     mutt.nanorc    perl.nanorc    tcl.nanorc
c.nanorc       html.nanorc      nano-menu.xpm  php.nanorc     tex.nanorc
css.nanorc     java.nanorc      nanorc.nanorc  pov.nanorc     xml.nanorc
debian.nanorc  makefile.nanorc  objc.nanorc    python.nanorc
```

Il suffit d'ajouter la ligne avec le langage que voulez.

```
include /usr/share/nano/<le language>.nanorc
```

Mais il y a encore mieux !

Un [dépot](https://github.com/serialhex/nano-highlight) avec plein de fichier `.nanorc`. 

Pour installer

```
git clone git://github.com/serialhex/nano-highlight.git ~/.nano
```

Il suffit de rajouter dans son `.nanorc`

```
include "~/.nano/php.nanorc"
```

Il y a aussi ce [dépôt](https://github.com/scopatz/nanorc).

## En conclusion

Nano est un outil très chouette. C'est souvent l'éditeur par défault dans linux.

