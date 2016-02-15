---
layout: post
title: "Halt compiler : twig et php dans le même fichier"
date: 2015-08-23 17:20:41 +0200
comments: true
categories: ["php", "twig", "composer", "phar"] 
---

## Introduction

je vais parler de l'instruction `__halt_compiler()`. 

Cette instruction arrête l'interprétation du PHP. Instruction inutile ? Pas vraiment.. 

Pour ce faire je vais montrer un exemple ou je vais mixer un fichier php et un fichier [twig](http://twig.sensiolabs.org/) ensemble.


## L'instruction `__halt_compiler()`

D'après php.net

> Stoppe l'exécution du compilateur. Ceci peut être très utile pour embarquer des données dans des scripts PHP, comme des fichiers d'installation.
> L'octet de la position du début des données peut être déterminé par la constante `__COMPILER_HALT_OFFSET__` qui n'est définie que s'il y a une fonction `__halt_compiler()` présente dans le fichier. 

<!--more-->

## Une première expérimentation

Soit le fichier php suivant :

```php
<?php
echo 'salut la compagnie!!';
__halt_compiler();
echo "j'apparais pas";
```

Comme prévu la deuxième ligne ne s'affiche pas.

## Une seconde expérimentation. 

```php
<?php
$txt = file_get_contents(__FILE__, false, null, __COMPILER_HALT_OFFSET__);
echo strtoupper($txt);
__halt_compiler();
Bonjour tout le monde!
```

La constante `__FILE__` représente le fichier actuelle, la constante `__COMPILER_HALT_OFFSET__` représente la position de l'instruction `__halt_compiler()`. On récupère le texte qui ne s'affiche pas.

```sh
BONJOUR TOUT LE MONDE!
```

## Mettre le fichier php et le fichier de template twig dans le même fichier

Un exemple un peu théorique car pas très optimisé.

J'ai besoin de twig, voici le `composer.json`

```json
{
   "require": {
        "twig/twig": "^1.20"
    }
}
```

Voici mon programme.

```php
<?php
require_once __DIR__ . '/vendor/autoload.php'; // Autoload files using Composer autoload

function get_halt_data() {
    return file_get_contents(__FILE__, false, null, __COMPILER_HALT_OFFSET__);
}

$loader = new Twig_Loader_Array(array(
    'index' => get_halt_data()));
$twig = new Twig_Environment($loader);

echo $twig->render('index', array('name' => 'Fabien'));

__halt_compiler();

hello {\{name}\}

```

```sh
hello fabien
```

Désolé pour les `{\{` sinon cela n'apparait pas sur le code.

C'est relativement inefficace. Il doit avoir moyen de faire un peu plus propre en profitant du cache.

Comme annoncé dans le titre , j'ai dans le même fichier le php et le template twig.

### Un exemple plus concret `composer.phar`

Ouvrons pour voir le fichier `composer.phar`

Voici ce que je vois. Nous retrouvons notre instruction du jour.

```php
...
Phar::mapPhar('composer.phar');
define('COMPOSER_DEV_WARNING_TIME', 1445255994);
require 'phar://composer.phar/bin/composer';

__HALT_COMPILER(); ?>
.. 
```

Les fichiers `.phar` en pratique n'utilise que cette astuce. Je connais assez peu.

## Conclusion

 * Sur l'utilisation en template. Il y a le projet [mustache.php](https://github.com/bobthecow/mustache.php) vous ne devriez pas être perdu avec le loader [suivant](https://github.com/bobthecow/mustache.php/wiki/Template-Loading#inline-loader)
 * Sur l'utilisation de string dans un template twig, la lecture de cet [article](https://techpunch.co.uk/development/render-string-twig-template-symfony2) est très bien.
 * Sur les `.phar` la [documentation officielle](http://php.net/manual/en/phar.mapphar.php) semble un bon début.

Je vais essayer de continuer à parler d'autres instructions et structures peu connues (par exemple `__invoke` ou les `SplHeap`).

Merci de m'avoir lu.

