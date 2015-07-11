---
layout: post
title: "Les quines"
date: 2015-07-11 19:39:53 +0200
comments: true
categories: ["kata", "php", "livre"]
---

Aujourd'hui commençons par un petit programme 

``` sh
  #!/bin/sh
  quine () {
  echo -e "#!/bin/sh\n$1"
  echo "quine '$1'"
  }
  
  quine 'quine () {
  echo -e "#!/bin/sh\\n$1"
  echo "quine \047$1\047"
  }
  '
```

Pouvez vous devinez que fais ce programme ?

Une quine est un code donc la sortie est exactement son code-source. 

Cela ressemble à une exercice de programmation.

Mais toute en quelque sorte peuvent se résumer à l'idée suivante.

> Écrivez, puis écrivez entre guillemets et suivi d'un point, « Écrivez, puis écrivez entre guillemets et suivi d'un point, ».

Le code python suivant est une interprétation littérale.

``` py
a='a=%r;print(a%%a)';print(a%a)
```

En php
``` php
<?$a='<?$a=%c%s%c;printf($a,39,$a,39);';printf($a,39,$a,39);
```

Seulement possible en php

``` php
<?php
echo file_get_contents(__FILE__);
```

## A quoi cela sert ?

On parle de code qui se réplique. Il existe une quine dans tout les langages de programmations complets(c'est à dire turing-complet). Une des applications les plus curieuse est l'injection de code malicieux dans les compilateurs. C'est un des papier les plus célèbre de Ken Thompson (Inventeur de B, Unix, UTF8, Go bref un monstre..). C'est un publication académique très facilement lisible et qui ne fait que trois pages. Il décrit une méthode d'injection dans le compilateur lui-même pour créer un compilateur infecté. Le plus bizarre dans cette histoire est que cette attaque est théorique. Mais personne n'y croyait. Il existe pourtant réellement un virus qui utilise cette faille.

* [Reflections on Trusting Trust](https://www.ece.cmu.edu/~ganger/712.fall02/papers/p761-thompson.pdf)
* [la faille de sécurité](https://lists.owasp.org/pipermail/owasp-cincinnati/2009-August/000187.html)


## Des liens et bibliographie.

Il y a un chapitre complet dans le [Gödel Escher Bach](https://fr.wikipedia.org/wiki/G%C3%B6del,_Escher,_Bach_:_Les_Brins_d%27une_Guirlande_%C3%89ternelle)
quelques phrases issues de ce livre passionnant
> "est un fragment de phrase" est un fragment de phrase

> "se compose de cinq mots" se compose de cinq mots

> "Donne une fausseté si précédée d'elle-même entre guillemets" Donne une fausseté si précédée d'elle-même entre guillemets. 

Cette dernière est de [W. V. Quine](https://en.wikipedia.org/wiki/Willard_Van_Orman_Quine) qui est la première personne à écrire dessus et qui donne le noms de quine.

la plupart des exemples viens de l'article [quine](https://fr.wikipedia.org/wiki/Quine_%28informatique%29) sur wikipedia. Voir aussi [quine](http://c2.com/cgi/wiki?QuineProgram) sur c2.com

