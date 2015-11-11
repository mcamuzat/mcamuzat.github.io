---
layout: post
title: "Guzzle Asynchrone avec les promises"
date: 2015-09-21 21:39:06 +0200
comments: true
categories: ["php", "Guzzle", "Promise", "Async", "ReactPHP"] 
---

Nous continuons sur les promises et le yield.

  * [partie 1 les promises](/blog/2015/09/05/les-promises-et-php-via-react/)
  * [partie 2 le Yield](/blog/2015/09/06/php-yield-les-generateurs/)
  * [partie 3 les co-routines](/blog/2015/09/13/yield-php-co-routine/)

Je vais parler de [Guzzle](https://github.com/guzzle) qui est un client HTTP. Nous allons voir la version 6 qui utilise Php5.5 

## Promise et Guzzle.

*Guzzle* connait les promises et possède sa propre [implémentation](https://github.com/guzzle/promises). 


la signature de la fonction est un peu près la même que [react/promise](https://github.com/reactphp/promise). 

Attention *Guzzle* ne fait pas la différence entre le *Deferred* qui est un travail dont la réponse est encore inconnu et représenter par une *promise*. Dans *Guzzle* le travail et la réponse sont la même chose.

``` php
use GuzzleHttp\Promise\Promise;

$promise = new Promise();
$promise->then(
    // $onFulfilled
    function ($value) {
        echo 'Tout va bien.';
    },
    // $onRejected
    function ($reason) {
        echo 'On a un problème.';
    }
);

$promise->resolve(null); // 'Tout va bien.';
// Ou 
$promise->reject(null); // 'On a un problème.';
```

<!--more-->
*Guzzle* est un client Web essayons un cas concret.

``` php
$client = new GuzzleHttp\Client();

$promise = $client->requestAsync('GET', 'http://httpbin.org/get');
$promise->then(
    function ($res) {
        return $res->getStatusCode();
    }
)->then(function ($value) { echo "j'ai recu un code $value"} ;

// Notre requète n'est pas encore partie. Il faut lancer manuellement l'appel.
$client->wait();
```

L'avantage ici est que je décide quand je lance l'appel. Par exemple on peut lancer en parallèle les requêtes.

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

// Je crée toute mes requetes
$promises = [
    'image' => $client->getAsync('/image'),
    'png'   => $client->getAsync('/image/png'),
    'jpeg'  => $client->getAsync('/image/jpeg'),
    'webp'  => $client->getAsync('/image/webp')
];

// je resouds tout en même temps

$results = Promise\unwrap($promises);
```

On peux créer des pools. Si on souhaite limiter le nombre de requête en même temps.

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

$batch = [
    'image' => '/image',
    'png'   => '/image/png',
    'jpeg'  => '/image/jpeg',
    'webp'  => '/image/webp'
];

$requests = function ($batch) {
    foreach ($batch as $url) {
        yield new Request('GET', $url);
    }
};

$pool = new Pool($client, $requests($batch), [
    'fulfilled' => function ($response, $index) {
        var_dump($index);
    },
    'concurrency => 2,
]);
$promise = $pool->promise();
$promise->wait();
```
le résultat ici. 

``` php
int(3)
int(0)
int(1)
int(2)
```

On reconnait aussi notre nouvel ami le `yield`.

## Le premier arrivé

Nous allons utiliser l'instruction `any()` toutes les requêtes sont lancées en concurrences. C'est la première arrivée qui l'emporte.

```
$client = new Client(['base_uri' => 'http://httpbin.org/']);

// je crée toute mes requetes
$promises = [
    'image' => $client->getAsync('/image'),
    'png'   => $client->getAsync('/image/png'),
    'jpeg'  => $client->getAsync('/image/jpeg'),
    'webp'  => $client->getAsync('/image/webp')
];

$result = Promise\any($promises)->then(function($value){var_dump($value->getHeader('Content-Type'));});
$result->wait();
```

Je veux juste les deux premières réponses `some(2, $promise)`

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

// je crée toute mes requetes
$promises = [
    'image' => $client->getAsync('/image'),
    'png'   => $client->getAsync('/image/png'),
    'jpeg'  => $client->getAsync('/image/jpeg'),
    'webp'  => $client->getAsync('/image/webp')
];

$result = Promise\some(2, $promises)
    ->then(function($results)
    {foreach ($results as $value)
        var_dump($value->getHeader('Content-Type'));
    }
);
$result->wait();

``` 

## Yield + Promise == Coroutine promise

Bon Nous allons complexifier encore un peu.

Soit le code suivant

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

$promiseGenerator = function () use ($client) {
    yield $client->getAsync('/image');
    yield $client->getAsync('/image/png');
    yield $client->getAsync('/image/jpeg');
    yield $client->getAsync('/image/webp');
};

$result = array();
$promise = Promise\each_limit($promiseGenerator(), 2, function($value, $idx) use (&$result) {$result[$idx] = $value;});

$promise->wait();
```

Je mets à la suite toute les promises que je souhaite exécuter en ajoutant `yield` devant.

Je laisse Guzzle gérer avec un limitation de 2. des que le programme a une place de libre, il appelle le générateur pour avoir un nouvelle promise.


Mais il existe dans Guzzle des co-routines..

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

$myfunction = function ($url) use ($client) {
    return Promise\coroutine(
        function () use ($client, $url) {
            try {
                $value = (yield $client->getAsync($url));
            } catch (\Exception $e) {
                yield New RejectedPromise($e->getMessage());
            }
        }
    );
};

$images = ['foo', 'baz', 'bar'];
$promises = [];

// Build an array of promises.
foreach ($images as $image) {
    $promises[] = $myfunction($image);
}

$aggregate = Promise\all($promises)->then(
    function($values) {echo 'ok' ;}, function($values){echo 'nope';});

$aggregate->wait();
```

Le code est complètement asynchrone. 

Il est intéressant de voir le code synchrone et non parallèle.

``` php
$client = new Client(['base_uri' => 'http://httpbin.org/']);

$getImages = function ($url) use ($client) {
            try {
                return $value = $client->get($url);
            } catch (\Exception $e) {
                $value = $e->getMessage();
            }
        };

$images = ['foo', 'baz', 'bar'];
$promises = [];

// Build an array of promises.
foreach ($images as $image) {
    $result[] = $getImages($image);
}

```
En gros, j'ai retiré le `async` et les `yields` mais les deux codes se ressemblent non ?

## Conclusion

Les promises sont pratiques.

 * elles sont chainables
 * elles sont asynchrones, annulables, rejetables
 * On peut faire des foreach dessus.
 * On peut les combiner.

Ce n'est pas vraiment un hasard. Les promises sont des **Monades**. Il n'est pas simple d'expliquer les monades. Les monades viennent de la programmation fonctionnelle et c'est surtout [haskell](https://www.haskell.org/) qui a popularisé cette structure. J'espère que je reviendrai dessus. 

*Guzzle* est vraiment très sympathique à utiliser. Le coté asynchrone n'est pas simple, la fonction `co-routine` n'est pas dans la documentation. Il a été très difficile de trouver un code d'exemple. Je regrette que parfois le seul moyen de déclencher la résolution est d'appeler de manière synchrone `->wait()` ce qui est dommage.
