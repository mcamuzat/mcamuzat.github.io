---
layout: post
title: "PHP Code Sniffer mise en place"
date: 2015-08-01 18:33:12 +0200
comments: true
categories: ["PHP", "Docker", "vim", "Symfony"] 
---

## Qualité de code et php
Dans ce post nous allons voir les logiciels qui permettent de respecter les standards de code en PHP. 

En pratique il n'y en a que 2.

 * [CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
 * [PHP Coding Standards Fixer](http://cs.sensiolabs.org/)

<!--more-->

## CodeSniffer

Il y a différentes normes de codage :  les plus connus sont la [psr-1](http://www.php-fig.org/psr/psr-1/) et la [psr-2](http://www.php-fig.org/psr/psr-2/). La psr-2 hérite de la psr-1. Mais certain Framework ont leur propres méthodes d'indentations. On peux personnaliser d'ailleurs les différentes règles. Les gouts et les couleurs de chacun sur le code est subjectif. L'intérêt de suivre les recommandations est que cela dépassionne le débat. Cela passe CodeSniffer ou cela ne passe pas. Pour participer au projet Open-source, il faut suivre la norme. 

La norme est pointilleuse sur les commentaires aussi. Si pas de commentaire pas de validation. On est quasiment forcer d'écrire la documentation. Cela a parfois un effet pervers ou le programmeur remplit sans trop réfléchir pour faire passer code sniffer.

Très souvent on ajoute un *hook* sur les commits de git. Lorsque on commite, git déclenche le logiciel, si un des fichiers n'est pas valide CodeSniffer on refuse de commiter. C'est assez peu contraignant quand la codebase est déjà indentée. (quoique parfois cela tombe vraiment au mauvais moment).En général on vérifie seulement sur les fichiers modifiés. Re-indenter tout le projet est souvent trop compliqué, trop long et un peu suicidaire si vous avez des rebases et des merges. 


## Installation de CodeSniffer

Deux possibilités

### Dans le projet
via une dépendance au projet en l'ajoutant dans le `composer.json`

```json
{
    "require-dev": {
        "squizlabs/php_codesniffer": "2.*"
    }
}
```

Alors dans le projet
```sh
./vendor/bin/phpcs -h
```

Personnellement je définie toujours un répertoire `bin` par défaut

```json
    "config": {
        "bin-dir": "bin"
    },
```

Ainsi la commande précédente devient

```sh
bin/phpcs 
```
### Installation globale

Soit l'installer de manière globale

```
composer global require "squizlabs/php_codesniffer=*"
```

Attention si vous utilisez Composer de manière globale ne pas oublier de rajouter dans votre `$PATH`
```
export PATH=~/.composer/vendor/bin:$PATH
```


### Ajoutez les reglès symfony
par défaut phpcs vient avec les règles suivantes

 * [psr-1](http://www.php-fig.org/psr/psr-1/)
 * [psr-2](http://www.php-fig.org/psr/psr-2/)
 * [Squiz](https://github.com/squizlabs/PHP_CodeSniffer/tree/master/CodeSniffer/Standards/Squiz)
 * [Zend](http://framework.zend.com/manual/1.12/fr/coding-standard.html)
 * [Pear](https://pear.php.net/manual/en/standards.php)
 * [Phpcs](https://github.com/squizlabs/PHP_CodeSniffer/tree/master/CodeSniffer/Standards/PHPCS)

Il manque Symfony.. 

Voici une méthode simple (mais pas la méthode officielle) si vous voulez l'installer de manière globale. Je rajoute dans le répertoire `Standards` un répertoire `Symfony2`

```
$ composer global require "squizlabs/php_codesniffer=*"
$ cd ~/.composer/vendor/squizlabs/php_codesniffer/CodeSniffer/Standards
$ git clone git@github.com:escapestudios/Symfony2-coding-standard.git Symfony2
```
On vérifie que les règles symfony2 sont bien installées

```
phpcs -i
```

Symfony2 en défaut. Ainsi pas besoin de préciser `--standard=Symfony2`

```
phpcs --config-set default_standard Symfony2
```

### Php-cbf

Code sniffer est capable de corriger certaines fautes tout seul. Encore une fois essayer de faire au fur et à mesure. Le commit d'indentation avec 500 fichiers modifiés est un calvaire à gérer si vous faite des revues de codes ou pire un rebase.

La syntaxe est la même que phpcs.
```
php-cbf src/ 
```
## Php-cs-fixer

Écris par [Sensio](http://sensiolabs.com/) les créateurs de [Symfony](http://symfony.com/). Ce logiciel fixe automatiquement l'indentation et différentes règles. L'avantage est qu'il est simple à installer pas besoin de cloner d'autre dépôt.

## Avoir tout les outils via docker.

J'ai déjà présenté les différentes méthodes avec [phaudit](https://github.com/jolicode/docker-images/tree/master/languages/php/phaudit) ans cette article [ici](/blog/2015/04/18/dockers-et-ci/)

## Méthode par rapport à git

Pour lancer une vérification avant chaque commit 
Il suffit de créer un fichier `pre-commit.sh` dans le répertoire `.git/hook`. Il y a plein d'exemples sur le net. Je n'ai pas d'exemple à partager. Le code ne m'appartient plus.. 

## Installation sous VIM.

C'est très simple Il faut installer le plugin [syntastic](https://github.com/scrooloose/syntastic). C'est un plugin qui gère un peu près tout les formats possibles.

Code-sniffer doit être installé de manière **globale**

Il suffit de rajouter cette ligne dans votre `.vimrc`

```
let g:syntastic_php_checkers=['php', 'phpcs']
```
A chaque fois que l'on enregistre le fichier, le plugin lance automatiquement d'abord `php` pour vérifier que le fichier est valide, puis `phpcs`. 

Le résultat est très intuitif on a une flèche `>` à chaque ligne qui pose problème. Il suffit de passer le curseur pour connaitre l'erreur. Enfin un screenshot sera plus clair.

{% img center /images/syntastic.png 600 212 'Screenshot de syntastic' 'syntastic' %}

## Conclusion

Respecter la norme psr-2 ou autre n'est pas très compliqué, avec l'habitude c'est même plutôt facile. Il est plus facile de d'intervenir sur un code propre. Sur la mise en place, on a vu qu'il y a deux possibilités soit des warning dans le code soit une correction automatique. Je ne suis pas *fan* pas la correction automatique. Je ne veux pas que le logiciel prennent des décisions pour moi.
