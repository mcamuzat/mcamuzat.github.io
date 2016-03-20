---
layout: post
title: "Les plugins sous VIM : Tabular"
date: 2016-03-20 21:49:10 +0100
comments: true
categories: ["plugin", "vim"]
---


Nous allons voir ensemble un Plugin sous vim [Tabular](https://github.com/godlygeek/tabular).

Il permet d'aligner les lignes de codes
par exemple :

```
|un tableau | une autre colonne|
| ha | Ho|
| bonjour | hello|

```
<!--more-->

Si je sélectionne le texte et que j'appuie sur `:` et que je tape `:Tabularize /|`

Mon tableau devient 

```
| un tableau | une autre colonne |
| un         | deux              |
| bonjour    | hello             |
```


Cela marche avec n'importe quelle clés
```
var var1 = "hello";
var une_variable_tres_longue =  "atchoum";
```

J'appuie `:Tabularize /=`

```
var var1                     = "hello";
var une_variable_tres_longue = "atchoum";
```

la syntaxe est `:Tabularize /<le ou les caractères que vous voulez indenter>`
Pour les tableaux en php
```php
[
 "a" => "b",
 "salut" => "bonjour",
]
```

Résultats : `:Tabularize /=>`:
```php
[
 "a"     => "b",
 "salut" => "bonjour",
]
```

Je m'en sers surtout pour indenter les tableaux dans les features dans [Behat](http://docs.behat.org/en/v3.0/).

## installation

Si vous avez installé [vim-plug](https://github.com/junegunn/vim-plug) il suffit de rajouter la ligne suivante.

```
Plug 'godlygeek/tabular'
```

## Des liens
Une [video]( http://vimcasts.org/episodes/aligning-text-with-tabular-vim/) qui explique tout (en anglais).

##La série sur les plugin VIM
 
 * [Installation des plugins](/blog/2016/03/13/vim-plug-gestion-des-plugins/)
 * [Tabularize](/blog/2016/03/20/les-plugins-sous-vim-tabular/)

