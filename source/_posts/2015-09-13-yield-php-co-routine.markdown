---
layout: post
title: "Yield PHP Co-routine"
date: 2015-09-13 20:31:37 +0200
comments: true
categories: ["PHP", "Javascript", "Async", "ReactPHP", "Générateur"] 
---


Nous allons continuer sur le *yield* [partie1](/blog/2015/09/06/php-yield-les-generateurs/)

Nous avons vu la fonction xrange qui permet de générer un million de valeurs pour un coup très faible en mémoire. 

Mais il y a mieux ! On peux envoyer des valeurs dans le générateur
``` php
<?php
function generateAnimal() {
    $input = (yield 'Panda');
    var_dump("j'ai reçu $input");
    $input = (yield 'Lama');
    var_dump("j'ai reçu $input");
}

$gen = generateAnimal();
var_dump($gen->current());// string(5) "Panda"
var_dump($gen->send('Canard'));//string(16) "j'ai recu Canard"
                               //string(4) "Lama"
var_dump($gen->send('Poney')); // j'ai recus Poney.
```

Si j'avais fais deux fois `->next()`  au lieux de `->send()`

``` php
$gen = generateAnimal();
var_dump($gen->current());// string(5) "Panda"
var_dump($gen->next());//string(16) "j'ai recu NULL"
                               //string(4) "Lama"
var_dump($gen->next()); // j'ai recus NULL.

```

## Les Co-routines
Une co-routine est une fonction qui peut se suspendre en reprendre quand on le souhaite.

Nous allons faire une classe `Task`  pour mieux comprendre.

``` php
class Task
{
    protected $generator;
 
    protected $firstCall = true;
 
    public function __construct(Generator $generator)
    {
        $this->generator = $generator;
    }
 
    public function run()
    {
        if ($this->firstCall) {
            $this->generator->current();
        } else {
            $this->generator->next();
        }
 
        $this->firstCall = false;
    }
 
    public function finished()
    {
        return !$this->generator->valid();
    }
}

```

J'ai besoin d'un Runner

``` php

class Runner
{
    public function __construct(Task $task)
    {
        $this->task = $task;
    }

    public function run()
    {
        while (!$this->task->finished()) {
            $this->task->run();
        }
    }
}
```

Un petit code d'exemple

```
function task1() {
    for ($i = 1; $i <= 10; ++$i) {
        echo "This is task 1 iteration $i.\n";
        yield;
    }
}

$task = new Task(task1());
$runner = new Runner($task);
$runner->run();
```

Cela donne 

```
This is task 1 iteration 1.
This is task 1 iteration 2.
This is task 1 iteration 3.
This is task 1 iteration 4.
This is task 1 iteration 5.
This is task 1 iteration 6.
This is task 1 iteration 7.
This is task 1 iteration 8.
This is task 1 iteration 9.
This is task 1 iteration 10.
```

J'ai un objet Task qui appelle une fonction et qui rend la main à chaque itération. Cela semble compliqué pour une seule tache. Mais modifions le code pour avoir plusieurs taches.

```
class Scheduler
{
    protected $queue;
 
    public function __construct()
    {
        $this->queue = new SplQueue();
    }
 
    public function enqueue(Task $task)
    {
        $this->queue->enqueue($task);
    }
 
    public function run()
    {
        while (!$this->queue->isEmpty()) {
            $task = $this->queue->dequeue();
            $task->run();
 
            if (!$task->finished()) {
                $this->enqueue($task);
            }
        }
    }
}
```
Bon toute la magie est faite grâce à la `SplQueue` qui est une file d'attente. J'ajoute dans la file d'attente toutes les taches.

Je prend une tache de la file d'attente. Je l'exécute avec `->run()`, si la tache n'est pas finie, je la remets dans la file d'attente.

Reprenons un code d'exemple

``` php
function task1() {
    for ($i = 1; $i <= 10; ++$i) {
        echo "This is task 1 iteration $i.\n";
        yield;
    }
}

function task2() {
    for ($i = 1; $i <= 5; ++$i) {
        echo "This is task 2 iteration $i.\n";
        yield;
    }
}

$task1 =  new Task(task1());
$task2 = new Task(task2());
$scheduler = new Scheduler();
$scheduler->enqueue($task1);
$scheduler->enqueue($task2);
$scheduler->run();
```

Le résultat.

```
This is task 1 iteration 1.
This is task 2 iteration 1.
This is task 1 iteration 2.
This is task 2 iteration 2.
This is task 1 iteration 3.
This is task 2 iteration 3.
This is task 1 iteration 4.
This is task 2 iteration 4.
This is task 1 iteration 5.
This is task 2 iteration 5.
This is task 1 iteration 6.
This is task 1 iteration 7.
This is task 1 iteration 8.
This is task 1 iteration 9.
This is task 1 iteration 10.
```

On voit que j'exécute en parallèle toutes mes deux taches.

## En conclusion


Il existe deux librairies qui utilise ce concept

 * [Icicle](https://github.com/icicleio/icicle)
 * [recoil](https://github.com/recoilphp/recoil)

Cette façon d'implémenter est assez curieuse. Car le code ne ressemble pas au code classique asynchrone avec des callbacks et autre événements. Si on regarde bien cela ressemble beaucoup a du code synchrone. Elle est inspirée du `C#` `async/wait`. On a l'impression que cela ressemble a du code synchrone où on ajoute des `yield` un peu partout. (en simplifiant beaucoup..)

Il y a peu de documentation et d'exemple sur le sujet.

 * la référence est ce post de [Nikic](http://nikic.github.io): [Cooperative multitasking using coroutines (in PHP!)](https://nikic.github.io/2012/12/22/Cooperative-multitasking-using-coroutines-in-PHP.html)
 * Une version légèrement simplifié dont je me suis inspiré pour le code [Co-operative PHP Multitasking](https://medium.com/@assertchris/co-operative-php-multitasking-ce4ef52858a0)


Je vais essayer de continuer avec le yield et repartir sur les promises.

Merci de m'avoir lu.
