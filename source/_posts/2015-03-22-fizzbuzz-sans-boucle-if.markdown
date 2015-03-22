---
layout: post
title: "FizzBuzz sans boucle If"
date: 2015-03-22 23:19:46 +0100
comments: true
categories: [PHP, FizzBuzz, Question, Kata]
---


FizzBuzz sans boucle if.

Bon, j'ai passé deux-trois entretiens ou on m'a demandé d'implementer "fizzbuz"

les règles sont simples.

* Ecrire un programme qui écrit les nombres de 1 à 100.
* Si le chiffre est divisible par 3 afficher seulement "fizz" 
* Si le chiffre est divisible par 5 afficher seulement "buzz"
* Si divisible par 3 et 5 afficher "fizzbuzz"
* sinon afficher le chiffre tout seul

Il y a plein de solutions possibles

##la plus littérale
``` php
for ($i = 1; $i <= 100; $i++) {
    if ($i % 15 == 0) {
        echo 'fizzbuzz'. "\n";
    } elseif ($i % 3 == 0) {
        echo 'buzz'. "\n";
    } elseif ($i % 5 == 0) {
        echo 'fizz'. "\n";
    } else {
    echo $i ."\n";
    }
}
```

##la version de wikipedia

``` php
for ($i = 1; $i <= 100; $i++) {
    $output = '';
    if ( $i%3 == 0) {
        $output .= 'fizz';
    }
    if ( $i%5 == 0) {
        $output .= 'buzz';
    }

    if ($output == '') {
        $output .= $i;
    }
    echo $output. "\n";
}
```

##Ma version que j'avais programmé

Le `continue` n'est pas souvent utilisé. Mais je trouve qu'il remplit son rôle ici. 

``` php
for ($i = 1; $i <= 100; $i++) {
    if ($i % 15 == 0) {
        echo 'fizzbuzz'. "\n";
        continue;
    }
    if ($i % 3 == 0) {
        echo 'buzz'. "\n";
        continue;
    }
    if ($i % 5 == 0) {
        echo 'fizz'. "\n";
        continue;
    }
    echo $i ."\n";

}
```
##Sans boucle if

Il existe une version qui n'utilise aucune boucle if.
```php
$resp = array(
    'fizzbuzz', 
    false,
    false,
    'fizz',
    false,
    'buzz', 
    'fizz',
    false, 
    false, 
    'fizz', 
    false, 
    false, 
    'fizz',
    false,
    false
);

for ($i = 1; $i <= 100; $i++) {
    ($output = $resp[$i%15]) || ($output = $i);
    echo $output. "\n";
}

```

En php on ne peux pas faire la commande suivante qui correcte en javascript;
``` js
var a = value || defautvalue ;
```

```
$output = $resp[i%15] || $i// => $output = true
```


C'est pour cela que l'on utilise cette ligne un peu bancale.
```
($output = $resp[$i%15]) || ($output = $i);
```
Il n'y a pas de boucle if. Si un jour on vous demande d'implémenter FizzBuzzben essayer cette version.  


