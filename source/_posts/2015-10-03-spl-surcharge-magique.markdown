---
layout: post
title: "SPL surcharge magique"
date: 2015-10-03 17:17:33 +0200
comments: true
categories: ["PHP", "SPL"] 
---

Nous allons repartir sur la [SPL](http://php.net/manual/fr/book.spl.php). 

Je vais parler des différentes méthodes amusantes à surcharger.

## Count 

Soit la classe suivante
 
```php
class BadCounter implements countable{
    public function count() {
        return 42;
    }
}

$counter = new BadCounter();

var_dump(count($counter));// int(42)
```

On peux surcharger la méthode `count`. C'est d'ailleurs le cas dans le cas du [Paginator](http://doctrine-orm.readthedocs.org/en/latest/tutorials/pagination.html) de doctrine.

``` php
  
$dql = "SELECT p, c FROM BlogPost p JOIN p.comments c";
$query = $entityManager->createQuery($dql)
                       ->setFirstResult(0)
                       ->setMaxResults(10);

$paginator = new Paginator($query);

count($paginator) // nombre de lignes dans la base
```

## Les ArrayObjects

On peut aussi changer toutes les méthodes pour un tableau.

 * `isset(counter['valeur'])`
 * `unset(counter['valeur'])`
 * `counter['valeur'] = 3`
 * `counter[] = 3`
 * `counter['valeur']`
 
``` php

class GeekCounter implements Countable, ArrayAccess {
    public function count() {
        return 42;
    }
    public function offsetSet($offset, $value) {
        if (is_null($offset)) {
            echo("on ajoute $value \n");

        } else {
            echo("on change la clé $offset par $value \n");
        }
    }

    public function offsetExists($offset) {
        echo("on teste la clé  $offset \n");
        return true;
    }

    public function offsetUnset($offset) {
        echo("on unset la clé $offset \n");
    }

    public function offsetGet($offset) {
        echo("on me demande la clé $offset \n");
        return 42;
    }
}

$counter = new GeekCounter();

var_dump(isset($counter["IdontCare"]));
var_dump($counter["IdontCare"]);
unset($counter["IdontCare"]);
$counter[] = 3;
$counter["IdontCare"] = 3;

```


``` php
on teste la clé  IdontCare 
bool(true)
on me demande la clé IdontCare 
int(42)
on unset la clé IdontCare 
on ajoute 3 
on change la clé IdontCare par 3 
```

On trouve la même idée dans les collections de doctrine.(l'interface `Collection` n'est qu'une surcharge);

Si on ne souhaite pas tout implémenter il suffit de surcharger la Classe `ArrayObject` 

Par exemple: 
``` php
class ZooDeBeauval extends ArrayObject {
    public function offsetSet($offset, $value) {
        if (!in_array($value, array("Panda", "Koala", "Otarie"))) {
            echo "non cet animal $value n'est pas autorisé";
        } else {
            parent::offsetSet($offset, $value);
        }

    }
}

```
Un exemple
``` php
$zoo = new ZooParcDeBeauval();
$zoo[] = 'Panda';
$zoo[] = 'Koala';

echo "liste :  ".implode(', ', iterator_to_array($zoo)) . PHP_EOL;
$zoo[] = 'Lama';
``` 

le résultat

``` php
liste :  Panda, Koala
non cet animal Lama n'est pas autorisé
```

Pour les `foreach` j'ai déjà parlé des iterators et des [générateurs](blog/2015/09/06/php-yield-les-generateurs/).

## Conclusion

Maintenant les interfaces `ArrayAccess` et `Countable` n'ont plus de secrets pour vous. Nous verrons dans un prochain Post les listes chainées. L'avantage de ces méthode est que l'on obtient une structure qui se comporte comme un `array` mais avec une occupation mémoire moindre.  

Dans un prochain post, je vais parler des listes chainées et des différentes structure de la SPL (j'ai déja parlé de la [SplStack](blog/2015/08/29/stacks-structures-meconnues/))

