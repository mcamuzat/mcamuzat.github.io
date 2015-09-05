---
layout: post
title: "Les promises et Php via React"
date: 2015-09-05 19:10:10 +0200
comments: true
categories: ["PHP", "Javascript", "Guzzle"] 
---

Les promises sont une alternative plus puissante au Callback. Relativement peu connues au début. Elle sont maintenant en standard dans beaucoup de langage javascript ES6, python 3.2, java. Où via des librairies comme [react/promise](https://github.com/reactphp/promise) pour php

Une promise représente une valeur donc le résultat n'est pas encore connu.
Quand le résultat est connu,  la promise a 3 états possibles

 * **Pending** ou **Unfulfillled**  en Attente..
 * **Resolved** ou **fulfillled**  Succès
 * **Rejected** ou **Failed**  Erreur.
 
Un *Deffered* représente un travail qui n'est pas encore fini. Donc un *Deferred* possède une promise (une valeur pas encore connues).

Quand la valeur est connue, la promesse utilise un *handler* (en général `->then()`) qui lui-même renvoie une promise. L'intérêt est que les promises sont chainables.

Mais essayons quelques exemples qui seront un peu plus parlant.

Pour ce faire nous allons d'abord installer [react/promises](https://github.com/reactphp/promise)

``` php
<?php
require __DIR__.'/vendor/autoload.php';

// On crée un travail
$deferred = new React\Promise\Deferred();

// On veux la promise
$promise  = $deferred->promise()->then(
    function () { echo "tout va bien \n"; },
    function () { echo "tout va mal \n"; },
    function () { echo "j'attends\n "; }
);


// Le travail est un succes, on résoud la promise
$deferred->resolve(); 
```

Voici ce que j'obtiens si je lance le programme.

``` php
tout va bien
```

Si je remplace `$deferred->resolve()` par `$deferred->reject()`. Le travail n'as pas marché. J'obtiens 

``` php
Tout va mal
``` 
La syntaxe de `then` est 

``` php
then(callable $onFulfilled = null, callable $onRejected = null, callable $onProgress = null)
```

 * Si l'action est un succès, On résous la promise en appelant la fonction `$onFulfilled`.
 * L'action n'est pas bonne, On rejette la promise avec `$onRejected`.
 * Si l'action est en cours , On appelle `$onProgress`.

A noter qu'une promise une fois qu'elle est résolue ou rejetée ne peut plus être réutilisée sauf dans le cas du `pending`

``` php
$promise->notify();
$promise->notify();
$promise->notify();
$promise->resolve();
```

Le résultat

``` 
j'attends
j'attends
j'attends
tout va bien
```

Pour l'instant rien de bien compliqué. On peut chainer les promises

``` php 
$promise  = $deferred->promise()->then(
    function () { echo "action 1 ok\n"; }
)->then(
    function () { echo "action 2 ok\n";}
)->then(
   function () { echo "action 3 ok\n";},
   function () { echo "une des actions n'est pas ok..\n";}
);

$deferred->resolve();
```

Le résultat
```
action 1 ok
action 2 ok
action 3 ok
```

Essayons avec `deffered->reject()`

Le résultat 

```
une des actions n'est pas ok..
```

Ajoutons un exception à la deuxième étape.
``` php 
$promise  = $deferred->promise()->then(
    function () { echo "action 1 ok\n"; }
)->then(
    function () { throw new Exception();}
)->then(
   function () { echo "action 3 ok\n";},
   function () { echo "une des actions n'est pas ok..\n";}
);

```

Le résultat
```
action 1 ok
une des actions n'est pas ok..
```

Ajoutons une exception dans le premier, et ajoutons un cas pour gérer l'erreur
``` php
$promise  = $deferred->promise()->then(
    function () { throw new \Exception(); }
)->then(
    function () { echo "action 2 ok\n";},
    function () { echo "l'action 1 pas ok mais on continue..\n";}
)->then(
   function () { echo "action 3 ok\n";},
   function () { echo "une des actions n'est pas ok..\n";}
);

$deferred->resolve();
```

Le résultat
```
l'action 1 pas ok mais on continue..
action 3 ok
```

Si on veux que l'erreur de l'action 1 se propage deux possibilités..

 * supprimer le callback d'erreur de l'action 2
 * Ou relancer l'exception

```
$promise  = $deferred->promise()->then(
    function () { throw new \Exception(); }
)->then(
    function () { echo "action 2 ok\n";},
    function () { echo "l'action 1 pas ok et on stop le processus"; throw new \Exception();}
)->then(
   function () { echo "action 3 ok\n";},
   function () { echo "une des actions n'est pas ok..\n";}
);
```


Ce qui est intéressant dans les promises c'est que le code équivalent en procédural est pas génial.
```
 $result = doAction1();
try {
    $result = doAction1();
    if ($result) {
        $result2 = doAction2($result);
        if ($result2) {
            $result3 = doAction3($result3);
            return $result3;
        } else {
            throw new Exception();
        }
    } else {
        throw New Exception();
    }
} catch (..){
    echo "une des actions n'est pas ok"
}
```

Devient
```
    $result = $doAction1()
        ->then(function($result1){doAction2($result1);})
        ->then(
            function($result2){doAction3($result2);},
            function() {echo "une des actions n'est pas ok"}
        );
``` 

C'est mieux non ?

Dans un prochain article nous nous utiliserons ce que nous avons appris avec [Guzzle](http://guzzle.readthedocs.org/en/latest/).
