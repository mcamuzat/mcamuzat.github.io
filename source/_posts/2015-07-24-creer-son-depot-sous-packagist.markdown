---
layout: post
title: "Créer son dépôt sous Packagist"
date: 2015-07-24 17:50:58 +0200
comments: true
categories: ["PHP", "Packagist", "PHPUnit", "composer", "tutoriel"]

---

## Introduction

J'avais montré dans le précédent [Post](/blog/2015/07/19/histogramme-et-ligne-de-commande/), une fonction qui permet de créer des histogrammes.

J'ai décidé de la publier sous [Packagist](https://packagist.org/). Je n'avais jamais fais. Je vous propose de suivre mon cheminement

 * Je mets au propre le fichier
 * J'ajoute le `composer.json`
 * j'ajoute les tests unitaires
 * Je crée le dépôt sur Github
 * J'enregistre le dépôt sur Packagist

<!--more-->

## Industrialisation

Nous sommes partis du code suivant.

``` php
function spark($array) {
    $bars = array('▁','▂','▃','▄','▅','▆','▇','█');
    $divide = max($array);
    if ($divide == 0) {
        $divide = 1;
    }
    $countBars = count($bars)-1;
    $out = '';
    foreach ($array as $tick)
        $out .= $bars[round(($tick / $divide) * $countBars)];
    echo $out;
}

// si je n'ai aucun argument ..
if (count($argv) == 0) {
    $str = '';
    // recupère le flux d'entrée
    while (!feof(STDIN)) {
        $str .= fgets(STDIN);
    }
    // explode laisse la derniere ligne vide.
    // d'ou le array_filter
    spark(array_filter(explode("\n", $str),'strlen'));
    return 0;
}

$iDontCare =array_shift($argv);
spark($argv);
return 0;

```

Je vais découper cela en plusieurs fichiers et répertoires.

``` 
.
├── bin
│   └── spark
├── composer.json
├── LICENSE
├── phpunit.xml.dist
├── src
│   └── Spark
│       └── Spark.php
└── tests
    ├── bootstrap.php
    └── Spark
        └── SparkTest.php
```

Voici `Spark.php`

``` php
<?php
namespace Spark;

class Spark
{

    private static $bars = array('▁','▂','▃','▄','▅','▆','▇','█');
    /**
     *
     */
    public static function spark($array)
    {
        $divide = max($array);

        if ($divide == 0) {
            $divide = 1;
        }
        $countTick = count(self::$bars)-1;
        $out = '';
        foreach ($array as $tick) {
            $out .= self::$bars[round(($tick / $divide) * $countTick)];
        }
        return $out;
    }
}
```

Et le fichier `spark` dans le répertoire `bin`

``` php
#!/usr/bin/env php
<?php

require_once __DIR__ . '/../vendor/autoload.php'; // Autoload files using Composer autoload

use Spark\Spark;

$iDontCare =array_shift($argv);
if (count($argv) == 0) {
    $str = '';
    while (!feof(STDIN)) {
        $str .= fgets(STDIN);
    }
    echo Spark::spark(array_filter(explode("\n", $str),'strlen'));
    return 0;
}
echo Spark::spark($argv);
return 0;
```

## Le Composer.json

**Pour publier sur Packagist il faut un `composer.json`**


C'est plutôt simple en fait. Il suffit de lancer la commande `composer init`. Tout se fait de manière interactive. 

à la fin j'obtiens.

``` javascript
{
    "name": "mcamuzat/spark",
    "description": "histogram in command line. Clone of holman/spark",
    "require-dev": {
        "phpunit/phpunit": "~4.0",
        "squizlabs/php_codesniffer": "2.*"
    },
    "license": "MIT",
    "authors": [
        {
            "name": "Marc Camuzat",
            "email": "marc.camuzat@gmail.com"
        }
    ],
    "minimum-stability": "dev",
    "require": {},
    "autoload": {
        "psr-0": {
            "Spark": "src/"
        }
    },
    "config": {
        "bin-dir": "bin"
    },
    "bin": ["bin/spark"]
}

```
Je crée le Namespace et j'ajoute que j'ai un fichier à marquer comme exécutable de le répertoire `bin/spark`. Je n'ai aucune dépendance.

## Ajoutons les tests.

Il faut un minimum de tests. 

Pour avoir des tests il faut 3 fichiers

* Un `phpunit.xml.dist`
* Un `bootstrap.php` pour qui se borne à appeler l'autoload de composer
* Enfin un fichier de test

Et installer PHPUnit (qui est déjà dans mon `composer.json`)

Le `phpunit.xml.dist` 

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<phpunit backupGlobals="false"
         backupStaticAttributes="false"
         colors="true"
         convertErrorsToExceptions="true"
         convertNoticesToExceptions="true"
         convertWarningsToExceptions="true"
         processIsolation="false"
         stopOnFailure="false"
         syntaxCheck="false"
         bootstrap="tests/bootstrap.php"
>
    <testsuites>
        <testsuite name="Spark Test Suite">
            <directory>./tests/</directory>
        </testsuite>
    </testsuites>

    <filter>
        <whitelist>
            <directory>./src/</directory>
        </whitelist>
    </filter>
</phpunit>
```

On appelle le `bootstrap.php` qui fait venir l'autoloader

``` php
<?php
$loader = @include __DIR__.'/../vendor/autoload.php';
if (!$loader) {
    $loader = require __DIR__.'/../../../../vendor/autoload.php';
}
```


Et enfin les tests

``` php
<?php
namespace Spark;
use Spark\Spark;
class SparkTest extends \PHPUnit_Framework_TestCase
{
    public function testItCreateAStringFromData()
    {
        $out = Spark::spark(array(1,5,22,13,5));
        $this->assertEquals('▁▃█▅▃', $out);
    }
    public function testItDontCareifInputIsString()
    {
        $out = Spark::spark(array("2","4","8"));
        $this->assertEquals('▃▅█', $out);
    }
    public function testItWorkWithFloatNumber()
    {
        $out = Spark::spark(array("0.2","0.8","0.4"));
        $this->assertEquals('▃█▅', $out);
    }
    public function testItNotDivideBy0()
    {
        $out = Spark::spark(array(0,0,0));
        $this->assertEquals('▁▁▁', $out);
    }
}
```

Lançons les tests

``` sh
$ bin/phpunit 
PHPUnit 4.8-ge1cc585 by Sebastian Bergmann and contributors.

....

Time: 722 ms, Memory: 4.50Mb

OK (4 tests, 4 assertions)
```

## Publication 

Je crée le dépôt sur [GitHub](https://github.com/mcamuzat/spark) que je clone

```
git clone mcamuzat/spark.git
git add .
git commit -m"initialize repo"
git push -u origin master
```

Je me suis inscris sur [Packagist](https://packagist.org/). 

Il suffit de cliquer sur **Submit**

{% img center /images/packagist_ajout.png 600 258 'Ajouter un dépôt sur Packagist' 'Ajouter un dépôt sur packagist' %}

Et de mettre l'url de Github. Et c'est tout !


## Des liens

 * Le projet sur [Github](https://github.com/mcamuzat/spark)
 * Le projet sur [Packagist](https://packagist.org/packages/mcamuzat/spark)

## En conclusion

Il manque encore le `README` sur Github, car pour l'instant l'intérêt du programme est encore un peu mystérieux pour une personne extérieure.

J'espère que si vous voulez créer votre propre librairie sur Packagist, ce tutoriel vous servira.

