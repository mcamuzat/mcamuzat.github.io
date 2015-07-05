---
layout: post
title: "Vim et Debbuger"
date: 2015-07-05 17:12:57 +0200
comments: true
categories: ["Vim", "PHP", "Xdebug", "plugin"] 
---

## Introduction

Je vais montrer aujourd'hui comment interfacer Vim et xdebug pour debugger du php.

* Installer xdebug
* Paramétrer xdebug
* Présentation de deux plugins **Vdebug** et **DBGPavim**
* Comment installer les deux plugin avec 

## Installer xdebug

Je connais deux méthodes

Utiliser PECL

```
sudo pecl install xdebug
```
il faut alors ajouter dans votre `php.ini` la ligne suivante

```
zend_extension=/usr/lib/php5/20090626/xdebug.so
``` 

Ou plus propre rajouter dans un nouveau fichier  `/etc/php5/apache2/conf.d/xdebug.ini` et pareil pour la ligne de commande `/etc/php5/cli/conf.d/xdebug.ini`

utiliser apt-get sous Ubuntu
```
sudo apt-get install php5-xdebug
```
sur mon Ubuntu, les fichier conf.d était déjà crée

## Paramétrer xdebug pour le debugger

Il faut rajouter les lignes suivantes le `xdebug.ini`

```
xdebug.remote_enable=on
xdebug.remote_handler=dbgp
xdebug.remote_host=localhost
xdebug.remote_port=9000
```

Puis aller sur l'url en ajoutant
```
http://monsite.com/?XDEBUG_SESSION_START=1
```
Il y a des plugins Firefox et Chrome qui s'occupe de cela.

Pour que le debugger soit lancer par défaut. Vous pouvez rajouter la ligne suivante

``` ini
xdebug.remote_autostart=1
```

Pour tester en ligne de commande.

``` sh
php -dxdebug.remote_autostart=1 test.php
```

un simple script bash fait l'affaire. `php-debug`

``` sh
#!/bin/bash
/usr/bin/php -dxdebug.remote_autostart=1 "$@"

```

## Les plugins VIM 

Je vais pas trop insister sur comment installer un plugin sous Vim. Ce n'est pas très compliqué. Il faut passer par un gestionnaire de plugin (la gestion des plugins par défaut dans Vim n'est pas pratique)

## Vdebug
### Utilisation

 * appuyer sur `<F5>`
 * vous avez 20 seconde pour lancer le script ou aller sur le serveur apache.
 * Une fois le signal capturé

### Liste des raccourcis claviers

{% img center /images/VDebug.png 600 375 'Vdebug' 'Screenshot de VDebug' %}

 * `<F5>`: start/run (to next breakpoint/end of script)
 * `<F2>`: step over
 * `<F3>`: step into
 * `<F4>`: step out
 * `<F6>`: stop debugging
 * `<F7>`: detach script from debugger
 * `<F9>`: run to cursor
 * `<F10>`: toggle line breakpoint
 * `<F11>`: show context variables (e.g. after "eval")
 * `<F12>`: evaluate variable under cursor
 * `:VdebugEval <code>`: evaluate some code and display the result
 * `<Leader>e`: evaluate the expression under visual highlight and display the result

La touche `<Leader>` est par défault `\` sur un clavier anglais. `<Leader>e` correspond à `\e`. Pas simple à taper sur un clavier azerty. la touche `<Leader>` est réglable grâce à cette configuration du `.vimrc`

```
let mapleader = ","
```

Sur mon poste, je tape `,e`

### Un avis

Marche plutôt bien, mais par défaut Le debugger commence au début du script et pas au premier breakpoint.

Il faut rajouter cette ligne dans votre `.vimrc`.

```
g:vdebug_options["break_on_open"]=0
```

L'histoire des 20 secondes pour se connecter est un peu frustrante.

Par contre le code python est super propre.


## DBGPavim 

{% img center /images/DBGPAVIM.png 600 375 'DBGPavim' 'Screenshot de DBGPavim' %}

Ce plugin résout les deux problèmes de Vdebug (aucune limitation de temps, démarre au premier point d'arrêt). Je le trouve moins intuitif. Mais il est visiblement plus puissant, il gère plusieurs sessions.

Sur les screenshots la différence est plutôt minime. 

Les touches sont un peu près les mêmes.


## Installations sous vim

Suivant le plugin que vous avez choisi

Par [Pathogen](https://github.com/tpope/vim-pathogen):
```
git clone git://github.com/joonty/vdebug.git bundle/vdebug
ou 
git://github.com/joonty/vdebug.git bundle/vdebug
```

Ou avec les submodules de Git

```
git submodule add git://github.com/joonty/vdebug.git bundle/vdebug
# ou 
git submodule add git://github.com/brookhong/DBGPavim.git bundle/DBGPavim

```

Ajouter cette ligne à votre `.vimrc`

par [Vundle](https://github.com/gmarik/Vundle.vim)
```
Bundle "joonty/vdebug" 
# ou
Bundle 'brookhong/DBGPavim'
```

par [NeoBundle](https://github.com/Shougo/neobundle.vim) : 
```
NeoBundle "joonty/vdebug" 
# ou
NeoBundle 'brookhong/DBGPavim'
```


## En conclusion
Le debugger c'est chouette et cela rends un peu obsolète ce bon vieux `var_dump(); die();` 

D'ailleurs sur le `var_dump`. Il est plus simple de taper cette commande directement.

```
die(var_dump($foo));
```

Enfin sur les versions récentes de Symfony la commande `dump()` est pratique.

Je vais reparler de xdebug. J'ai plein d'astuce à partager.

## Des liens

* [Vdebug](https://github.com/joonty/vdebug)
* [DBGPAVIM](https://github.com/brookhong/DBGPavim)
