---
layout: post
title: "Dockers et CI"
date: 2015-04-18 18:13:48 +0200
comments: true
categories: ["Docker", "Php", "Intégration continue"]
---

##Introduction
je continue sur ma découverte de Docker. Je vais parler de deux containers

* [phaudit](https://github.com/jolicode/docker-images/tree/master/languages/php/phaudit)  pour faire de la qualité de code
* [JoliCi](https://github.com/jolicode/JoliCi) pour l'intégration continu

##Faire de la qualité avec Docker

Il y a un container fourni par [phaudit](https://github.com/jolicode/docker-images/tree/master/languages/php/phaudit) qui contient déjà des outils pour auditer le code. 

Voici la ligne de commande pour l'installer
```
docker pull jolicode/phaudit
```

Et je me place dans mon répertoire projet

###Listes des programmes


* [PHPLoc](http://github.com/sebastianbergmann/phploc) `phploc` donne le nombre de ligne, le nombre de classes etc ..

```
docker run -t -i -v `pwd`:/project jolicode/phaudit phploc src
Directories                                         10
Files                                               91

Size
  Lines of Code (LOC)                             7295
  Comment Lines of Code (CLOC)                    3228 (44.25%)
  Non-Comment Lines of Code (NCLOC)               4067 (55.75%)
  Logical Lines of Code (LLOC)                     774 (10.61%)
    Classes                                        711 (91.86%)
      Average Class Length                           8
        Minimum Class Length                         0
        Maximum Class Length                        67
      Average Method Length                          1
        Minimum Method Length                        0

```

* [PHP Mess Detector](http://phpmd.org/) `phpmd` donne un retour sur la qualité du code (nommage des variables etc..)

```
phaudit phpmd . text naming
/project/src/Allmy/Protocol/LineReceiver.php:38	Avoid variables with short names like $b. Configured minimum length is 3.
/project/src/Allmy/Reactor/StreamSelectReactor.php:32	Avoid variables with short names like $id. Configured minimum length is 3.
/project/src/Allmy/Reactor/StreamSelectReactor.php:43	Avoid variables with short names like $id. Configured minimum length is 3.
/project/src/Allmy/Reactor/StreamSelectReactor.php:53	Avoid variables with short names like $id. Configured minimum length is 3.
...
...
```

* [PHP_CodeSniffer](http://pear.php.net/PHP_CodeSniffer)  `phpcs` erreur de convention de code (Psr-..) et  `phpcbf`pour les fixer automatiquement

```
docker run -t -i -v `pwd`:/project jolicode/phaudit phpcs src/Allmy/Reactor/StreamSelectReactor.php
FILE: /project/src/Allmy/Reactor/StreamSelectReactor.php
----------------------------------------------------------------------
FOUND 85 ERRORS AND 3 WARNINGS AFFECTING 63 LINES
----------------------------------------------------------------------
   2 | ERROR   | [ ] Missing file doc comment
  11 | ERROR   | [ ] Missing class doc comment
  15 | ERROR   | [ ] Private member variable "timers" must be
.....
.....
.....
 275 | ERROR   | [ ] Parameter tags must be grouped together in a doc

```

* [PHP Copy/Paste Detector](http://github.com/sebastianbergmann/phpcpd) `phpcpd` détecte les copier/coller 

``` 

docker run -t -i -v `pwd`:/project jolicode/phaudit phpcpd src
Found 2 exact clones with 101 duplicated lines in 4 files:

  -	/project/src/Allmy/Stream/Factory.php:9-53
 	/project/src/Allmy/Internet/Factory.php:9-53
 
  -	/project/src/Allmy/Transport/TcpServer.php:9-66
 	/project/src/Allmy/Socket/Server.php:9-66
 
1.38% duplicated lines out of 7295 total lines of code.

```

d'autre commandes que je connais un peu moins

* [PHP_Depend](http://pdepend.org/) `pdepend` donnes des analyses, dépendences, complexités etc..

* [PHP Dead Code Detector](http://github.com/sebastianbergmann/phpdcd) `phpdcd` détecte le code qui semble ne pas servir. 

* [PhpMetrics](http://www.phpmetrics.org/) `phpmetrics` donnes des métriques (Je ne connais pas)
```
docker run -t -i -v `pwd`:/project jolicode/phaudit phpmetrics --report-cli .
```
* [PHP Coding Standards Fixer](http://cs.sensiolabs.org/) as `php-cs-fixer` une autre commandes pour fixer le code par [Sensio](http://cs.sensiolabs.org/) 


L'astuce est de se créer l'alias suivant
```
alias phaudit="docker run --rm -ti \
    -v \`pwd\`:/project \
    jolicode/phaudit"
```

alors les lignes de commandes précédentes deviennent plus simple
```
phaudit phpmd . text naming
```

## Tester sur toutes les versions de php

Fait par la même équipe. 

il est possible de faire une intégration continue en local. Il va lancer les builds en testant toutes versions de php spécifié dans un fichier `yml`.

L'installation est très simple il suffit de télécharger le `.phar` à l'url [suivante](https://github.com/jolicode/JoliCi/releases) 
```
wget url du fichier phar
```

il faut créer un fichier `.travis.yml` voici les lignes à ajouter pour un projet en php
``` yml
language: php
php:
  - "5.5"
  - "5.4"
  - "5.3"
```
puis la commande suivante: 

```
php jolici.phar run
Creating builds...
3 builds created

Running job php = 5.5

PHPUnit 3.7.38 by Sebastian Bergmann.

Configuration read from /home/project/phpunit.xml.dist

......

Time: 106 ms, Memory: 3.50Mb

OK (6 tests, 34 assertions)

Running job php = 5.4

PHPUnit 3.7.38 by Sebastian Bergmann.

Configuration read from /home/project/phpunit.xml.dist

......

Time: 131 ms, Memory: 3.50Mb

OK (6 tests, 34 assertions)

Running job php = 5.3

PHPUnit 3.7.38 by Sebastian Bergmann.

Configuration read from /home/project/phpunit.xml.dist

......

Time: 7 ms, Memory: 6.25Mb

OK (6 tests, 34 assertions)
```

En ajoutant un fichier `.yml` et sans installer aucune version de php, je peux tester sur trois plateformes mon code. C'est vraiment impressionnant.

## Conclusion
Nous avons vus ensemble deux applications très simples qui permettent d'intégrer Docker dans notre workflow. 

Merci à l'équipe [JoliCode](http://jolicode.com/)  pour ces deux outils.

Je me suis inspiré de la présentation suivante.
http://slides.com/jeremyderusse/docker-dev#/5/2
