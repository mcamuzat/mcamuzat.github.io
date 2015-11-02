---
layout: post
title: "Listes Chainées Iterator"
date: 2015-11-01 19:46:40 +0100
comments: true
categories: ["SPL", "Doctrine", "PHP"] 
---

Troisième partie sur la SPL et les listes chainées.

 * partie 1 [SPL et surcharge](blog/2015/10/03/spl-surcharge-magique)
 * partie 2 [Implémentation](blog/2015/10/10/liste-chainees-implementation)

Nous allons implémenter l'interface `ArrayAccess`. Donc notre liste chainée va se comporter comme un array.

Je vais rajouter deux méthodes. Attention les `Array` commencent traditionnellement à 0 d'où le `$this->count -1`

 * Supprimer le chainon N
``` php
    public function removeAtPosition($position)
    {
        if (!$this->validateInterval($position)) {
                throw new \Exception("L'index doit être valide");
        }

        if ($position == 0) {
            return $this->removeFirstValue();
        }

        if ($position  ==  $this->count -1 ) {
            return $this->removeLastValue();

        }
        $current = $this->first;
        $last = $current;
        for ($i = 0; $i < $position; $i++) {
            $last = $current;
            $current = $current->getNext();
        }

        $last->setNext($current->getNext());

        return $this;
    }
```

 * Récupérer le chainon N
``` php
    public function getAtPosition($position)
    {
        if (!$this->validateInterval($position)) {
            throw new \Exception("L'index doit être numerique");
        }
        if ($position == 0) {
            return $this->first->getData();
        }

        if ($position == $this->count - 1) {
            return $this->last->getData();
        }

        $current = $this->first;
        for ($i = 0; $i < $position; $i++) {
            $current = $current->getNext();
        }

        return $current->getData();
    }

```

Pour avoir le 9999 chainon,  il faut parcourir les 9998 chainons précédents.

Avec les deux méthodes précédentes. Il suffit d'implémenter les méthodes suivantes


``` php

    public function offsetSet($offset, $value) {
        if ($offset == null) {
            $this->insertAtEnd($value);
        } else {
            if (!$this->validateInterval($offset)) {
                throw new \Exception("L'index doit être valide");
            }
            $this->insertAtPosition($offset, $value);
        }
    }

    public function offsetExists($offset) {
        return $this->validInterval($offset);
    }

    public function offsetUnset($offset)
    {
         return $this->removeAtPosition($offset);
    }

    public function offsetGet($offset)
    {
        return $this->getAtPosition($offset);
    }

```

Pour vérifier que les valeurs en entrée sont correctes j'utilise la fonction suivante 

``` php 
    private function validateInterval($offset) {
        return (false !== filter_var(
            $offset,
            FILTER_VALIDATE_INT,
            array(
                'options' => array(
                    'min_range' => 0,
                    'max_range' => $this->count-1
                )
            )
        ));
    }

```

Bon cela semble un peu abstrait, voici quelques exemples d'utilisations.

``` php
$list = new LinkedList();
$list[] = "first";
$list[] = "second";
$list[] = "third";
//
var_dump(isset($list[1]));// => true
var_dump($list[1]); // => "second"
unset($list[1]);
var_dump($list[1]); // => third
```
Nous avons une liste qui se comporte comme un array. c'est pratique, mais on ne peux pas faire de `foreach` dessus.. Enfin pas encore.

## Ajout de l'itérator

Pour faire un itérator il faut implémenter l'interface suivante
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

Dans le cas de notre liste chainée cela n'est pas très compliqué.

``` php
 class LinkedList implements Countable, ArrayAccess, *Iterator* {
    .....	
    private $current;
    private $position = 0;
    ....

    public function current () {
        return $this->current->getData();
    }
    public function key () {
        return $this->position;

    }
    public function next () {
        $this->position++;
        $this->current = $this->current->getNext();

    }
    public function rewind () {
        $this->position = 0;
        $this->current = $this->first;

    }
    public function valid () {
        return $this->current !== null;
    }

```

Un petit code d'exemple 

``` php
$list = new LinkedList();
//
$list[] = "first";
$list[] = "second";
$list[] = "third";
foreach($list as $key => $value) {
    var_dump("$key => $value");
}

// string(10) "0 => first"
// string(11) "1 => second"
// string(10) "2 => third"
```

Je peux a tout moment le retransformer en `array` grâce à la méthode `iterator_to_array($list)`

``` php

array(3) {
  [0] =>
  string(5) "first"
  [1] =>
  string(6) "second"
  [2] =>
  string(5) "third"
}
```

Pour faire dans l'autre sens nous pouvons implémenter le constructor
``` php
    public function __construct($input = null) 
    {
        if ($input) {
            if (! (is_array($input) || $input instanceof Traversable)) {
                throw new \Exception("Un array ou Un iterator..");
            }
            foreach($input as $value) {
                $this->insertAtEnd($value);
            }
        }
    }
```

Mon constructor prend un array ou un Objet qui implémente `Traversable` (en gros un Itérateur);

Quelques exemples
``` php
$list = new LinkedList(array("one", "two", "three"));
foreach($list as $key => $value) {
    var_dump($value);
}
//string(3) "one"
//string(3) "two"
//string(5) "three"

$spl = New SplQueue();
$spl[] = "travail1";
$spl[] = "travail2";
$spl[] = "travail3";
$list = new LinkedList($spl));
foreach($list as $key => $value) {
    var_dump($value);
}

// string(8) "travail1"
// string(8) "travail2"
// string(8) "travail3"

$linked = New LinkedList();
$linked[] = "valeur 1";
$linked[] = "valeur 2";
$linked[] = "valeur 3";

$list = new LinkedList($linked);
foreach($list as $key => $value) {
    var_dump($value);
}
// string(9) "valeur 1"
// string(8) "valeur 2"
// string(8) "valeur 3"

```

## En conclusion.
Nous avons implémenter Les listes chainées avec toutes les méthodes. Mon exemple est un peu théorique. Mais je vous conseille de re-regarder les doctrines collections.

Merci de m'avoir lu.

