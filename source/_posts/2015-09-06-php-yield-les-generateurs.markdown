---
layout: post
title: "PHP yield les générateurs"
date: 2015-09-06 19:06:48 +0200
comments: true
categories: ["PHP", "ruby", "python","générateur"] 
---



Nous allons voir une nouveauté de PHP 5.5 l'instruction **yield**

Cela permet de mettre en place ce qu'on appelle les générateurs.

## Un premier exemple

Regardons un exemple ensemble

``` php
<?php
function generateAnimal() {
    echo "Je suis dans le générateur\n";
    yield "Panda";
    echo "Je suis retourné dans le générateur\n";
    yield "Lama";
    echo "je suis de retour\n";
    yield "Alpaga";
    echo "plus de d'animaux\n";
}

$generator = generateAnimal();
foreach ($generator as $value) {
    echo "j'ai reçu $value \n";
}
```

Voici le résultat

```
j'ai reçu Panda 
Je suis retourné dans le générateur
j'ai reçu Lama 
je suis de retour
j'ai reçu Alpaga 
plus de d'animaux
```

D'abord un générateur se comporte comme un iterator. C'est grâce à cela que je peux faire un `foreach`.

Je vais refaire pas à pas avec des commentaires.

### premier passage

``` php
<?php
function generateAnimal() {
    echo "Je suis dans le générateur\n";
    yield "Panda"; // Je retourne ici 
        echo "Je suis retourné dans le générateur\n";
    yield "Lama";
        echo "je suis de retour\n";
    yield "Alpaga";
    echo "plus de d'animaux\n";
}


$generator = generateAnimal();

echo $generator->current();
// "je suis dans le générateur
// $value = "Panda"
```

### Itération suivante

En fait le générateur reste en suspens, `yield` est un pseudo `return` (enfin c'est comme cela que je l'ai compris)

``` php
<?php
function generateAnimal() {
    echo "Je suis dans le générateur\n";
    yield "Panda"; // Je suis reste ici .. je continue 
    echo "Je suis retourné dans le générateur\n";
    yield "Lama"; // je m'arrete à nouveau 
    echo "je suis de retour\n";
    yield "Alpaga";
    echo "plus d'animaux\n";
}


$generator->next() // On récupère la valeur suivante
echo $generator->current();
// "je suis retourné dans le générateur
// "Lama"
```


### Troisième itération

``` php
<?php
function generateAnimal() {
    echo "Je suis dans le générateur\n";
    yield "Panda";
    echo "Je suis retourné dans le générateur\n";
    yield "Lama"; // je me suis arrété ici 
    echo "je suis de retour\n";
    yield "Alpaga"; // je retourne .. 
    echo "plus d'animaux\n";
}


$generator->next() // On récupère la valeur suivante
echo $generator->current();
// Je suis de retour
// "Alpaga"
```

### Dernière Itération

Nous y sommes presque..

``` php
<?php
function generateAnimal() {
    echo "Je suis dans le générateur\n";
    yield "Panda";
    echo "Je suis retourné dans le générateur\n";
    yield "Lama";  
    echo "je suis de retour\n";
    yield "Alpaga"; // je me suis arréte ici
    echo "plus d'animaux\n"; //pas de yield je renvoie null..
}

$generator->next() // On récupère la valeur suivante
echo $generator->current();
// Plus d'animaux 
// il n'y a rien car echo null;

```

Une fois qu'un générateur a fini, on ne peux le réutiliser

``` php
foreach ($generator as $value) {
    echo "j'ai reçu $value \n";
}

foreach ($generator as $value) {
    echo "j'ai reçu $value \n";
}
```

J'obtiens 

``` 
PHP Fatal error:  Uncaught exception 'Exception' with message 'Cannot traverse an already closed generator' in /home/marc/yield.php:16
```

## Quel est l'intérêt ?

Admettons que je veux faire un `foreach` sur un tableau d'un millions de lignes. 

Pour faire un Array de 1 Million de valeurs ce n'est pas très compliqué. Une instruction suffit.

``` php
range(1000000) = [1,2,3,4,...,1000000];
```

Mais cela prend un peu de mémoire. Utilisons notre générateur de manière sympathique

``` php
function xrange($min, $max) {
  for ($i = $min; $i < $max; $i++) yield $i;
}

foreach (xrange(1,1000000) as $value) {
   echo $value;
}
```
l'énorme avantage est que je n'ai pas besoin de générer un array de 1 Millions de lignes, je génère valeur par valeur. Si la fonction est appelle deux fois je ne génère que deux valeurs. L'occupation en mémoire est faible. Les valeurs sont instanciées *paresseusements*.

### Un exemple encore plus concret.

Pour lire un fichier:

``` php 
function getLinesFromFile($fileName) {
    $fileHandle = fopen($fileName, 'r');
    while (false !== $line = fgets($fileHandle)) {
        yield $line;
    }
    fclose($fileHandle);
}
$lines = getLinesFromFile($fileName);
foreach ($lines as $line) {
    // do something with $line
}
```

Ce code a plusieurs avantages.

 * On va chercher la ligne à la demande.
 * Il y a une couche d'abstraction entre la lecture et le programme principale.

### Un petit quizz

Pouvez vous deviner la fonction suivante ?

```
function mystere() {
    $last = 0;
    $current = 1;
    yield 1;
    while (true) {
        list($current, $last) = array($current + $last, $current);
        yield $current;
    }
}

$count = 0;
foreach (mystere() as $value) {
    $count++;
    echo $value . "\n";
    if ($count > 10) {
        break;
        // pas cool la boucle infinie
    }
}

```

## Une mise au point

Les générateurs se comportent comme des itérateurs, mais pour implémenter un [iterator](http://php.net/manual/fr/class.iterator.php) il faut implémenter l'interface suivante.

``` php 
 Iterator extends Traversable {
/* Méthodes */
abstract public mixed current ( void )
abstract public scalar key ( void )
abstract public void next ( void )
abstract public void rewind ( void )
abstract public boolean valid ( void )
}
```

Par exemple pour l'exemple du fichier (je recopie la doc de php)

``` php
class LineIterator implements Iterator {
    protected $fileHandle;
 
    protected $line;
    protected $i;
 
    public function __construct($fileName) {
        if (!$this->fileHandle = fopen($fileName, 'r')) {
            throw new RuntimeException('Impossible d\'ouvrir le fichier : "' . $fileName . '"');
        }
    }
 
    public function rewind() {
        fseek($this->fileHandle, 0);
        $this->line = fgets($this->fileHandle);
        $this->i = 0;
    }
 
    public function valid() {
        return false !== $this->line;
    }
 
    public function current() {
        return $this->line;
    }
 
    public function key() {
        return $this->i;
    }
 
    public function next() {
        if (false !== $this->line) {
            $this->line = fgets($this->fileHandle);
            $this->i++;
        }
    }
 
    public function __destruct() {
        fclose($this->fileHandle);
    }
}
```

L'implémentation en générateur.

``` php
function getLinesFromFile($fileName) {
    if (!$fileHandle = fopen($fileName, 'r')) {
        throw new RuntimeException('Impossible d\'ouvrir le fichier : "' . $fileName . '"');
    }
 
    while (false !== $line = fgets($fileHandle)) {
        yield $line;
    }
 
    fclose($fileHandle);
}

```

C'est quand même plus simple.

## En conclusion.

Cela existe aussi dans les autres langages

On trouve l'instruction `yield` surtout dans python

``` python
def countdown(n):
    while n > 0:
        yield n
        n -= 1

for x in countdown(10):
    print 'depart dans %s' % x

```

La référence est ce [site](http://www.dabeaz.com/generators/) , Il existe une [video](http://www.youtube.com/watch?v=5-qadlG7tWo) (**3 heures !!!**)

Cela existe aussi dans [ruby](http://www.tutorialspoint.com/ruby/ruby_blocks.htm), [C#](https://msdn.microsoft.com/fr-fr/library/9k7k7cf0.aspx), et dans le javascript ES6

C'est un peu plus qu'une nouvelle syntaxe. Cela permet de faire du code asynchrone. Car cela permet une structure de codage que l'on appelle: Les [Couroutines](https://fr.wikipedia.org/wiki/Coroutine). Mais plus d'info dans un prochain post . 


