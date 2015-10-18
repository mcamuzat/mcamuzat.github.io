---
layout: post
title: "Listes chainées : implémentation"
date: 2015-10-10 21:12:47 +0200
comments: true
categories: ["php", "SPL"] 
---

Dans la [partie 1](/blog/2015/10/03/spl-surcharge-magique/) nous avons appris à surcharger le `count` ainsi que les différentes méthodes de `ArrayAccess`. Pour faire un exemple un peu plus concret, je vais impémenter les listes chainées. Les listes doublement chainée sont **déja** implémentées dans la SPL via [SplDoublyLinkedList](http://php.net/manual/fr/class.spldoublylinkedlist.php).

Le liste chainée (linked list en anglais) est une structure de donnée. Nous allons essayer d'implémenter une liste chainée en PHP. Cela nous permettra de comprendre l'idée. Nous allons implémenter l'interface `Countable`. (J'implémente `ArrayAccess` et `Iterator` dans le post suivant).

Une liste chainée est constituée de `Node` ou noeud/chainon.

Un node a deux propriétés.

 * Sa valeurs 
 * Le liens vers le noeud suivant

En php 

``` php
class Node {
    private $data;
    private $next;
    public function __construct($data, Node $next = null)
    {
        $this->data = $data;
        $this->next = $next;
    }
    public function setData($data)
    {
        $this->data = $data;
    }
    public function getData()
    {
        return $this->data;
    }

    public function getNext()
    {
        return $this->next;
    }

    public function setNext(Node $next = null)
    {
        $this->next = $next;
    }

}
```

pour créer une liste rien de bien compliqué.

``` php 
$noeud1 = new Node(12);
$noeud2 = new Node(99);
$noeud3 = new Node(37);
$noeud1->setNext($noeud2);
$neoud2->setNext($noeud3);
```

Résultat le dessin suivant (wikipedia)

{% img center /images/linked-list.png 408 41 'Une liste chainée' 'Une liste chainée %}


## Implementation de la liste

Nous allons créer des méthodes pour ajouter simplement nos chainons.

``` php
class LinkedList implements Countable, ArrayAccess, Iterator {
    
    private $first;
    private $last;
    private $count = 0;
    ... 
    // pour l'iterateur
    private $current;
    private $position = 0;
    
    // Permet d'afficher le contenu de la chaine. 
    public function printMe() {
        $current = $this->first;
        while ($current->getNext()) {
            echo "-{$current->getData()}-";
            $current = $current->getNext();

        }
        echo $current->getData();
    }


}

```

Nous allons traquer le premier élément de la chaine (`$this->first`) et le dernier (`$this->last`)

### Ajout d'un chainon à la fin

C'est assez simple. 

 * Créer un nouveau noeud
 * Récupérer le dernier chainon
 * Faire pointer la propriété `next` du dernier chainon vers notre nouveau noeud.
 * Notre nouveau noeud devient le dernier noeud.
 * On augmente la taille de 1

en code cela donne
``` php
    public function insertAtEnd($data) {
        // nouveau noeud
        $node = new Node($data);
        // si la liste est vide
        if ($this->first == null) {
            $this->first = $node;
        }
        // on ajoute le liens vers le suivant
        if (!$this->last == null) {
            $this->last->setNext($node);
        }
	// notre nouveau noeud devient le dernier
        $this->last = $node;
        // on augmente la taille.
        $this->count++;
        return $this;
    }

```

Exemple

``` php
$list = new LinkedList();
$list->insertAtEnd("ha")->insertAtEnd("hi");
$list->printMe() // -ha-hi
```
### Ajout d'un chainon au début

C'est un peu près la même idée.

 * Créer un nouveau noeud
 * Récupérer le premier noeud.
 * Notre noeud pointe vers le premier noeud.
 * On pointe le `first` vers notre nouveau noeud.

En code 
``` php 
    public function insertFirstValue($data)
    {
       $node = new Node($data, $this->first);
       $this->count++;
       // si la liste est vide
       if ($this->last == null) {
            $this->last = $this->first;
       }
       $this->first = $node;
       return $this;
    }
```

Un exemple

```
$list = new LinkedList();
$list->insertAtEnd("first")->insertFirstValue("second");
$list->printMe(); // -second-first
```
### Suppression d'un chainon au début.

Il faut faire dans l'autre sens.

En code 
``` php
    public function removeFirstValue() {
       if ($this->count == 0) {
            throw new \Exception('La liste est vide');
       }
       $value = $this->first->getData();
       $this->count--;
       $this->first = $this->first->getNext();
       // si la liste est vide , reinitialiser le last
       if ($this->first == null) {
           $this->last = null;
       }
       return $value;
    }
$list = new LinkedList();
$list->insertAtEnd("first")->insertAtEnd("second");
$list->printMe(); // -first-second
var_dump($list->removeFirstValue());
$list->printMe(); //

```

### Suppression d'un chainon à la fin

Comme le dernier chainon ne connait pas son prédécesseur. C'est beaucoup plus compliqué. On est obligé de repartir depuis le début. Donc pour supprimer le dernier chainon d'un liste d'un million de chainon, il nous faut parcourir les 1 millions de chainons.

En Code 
``` php
    public function RemoveLastValue()
    {
        // cas particulier la liste est vide
        if ($this->count == 0) {
            throw new \Exception('la Liste est vide');
        }
        // Il n'y a qu'un noeud.
        if ($this->count == 1) {
            $value = $this->last->getData();
            $this->first = null;
            $this->last = null;
            $this->count == 0;
            return $this->value;
        }
        // On parcours tout les chainons jusqu'à l'avant-dernier
        $current = $this->first->getNext();
        $previous = $this->first;
        while ($current->getNext()) {
             $previous = $current;
             $current = $current->getNext();
        }
        // on supprime le liens
        $previous->setNext(null);
        // On déplace le last
        $this->last = $previous;
        // on décremente
        $this->count--;
        return $current->getData();
    }

```

### Ajouter une valeurs au milieu de la chaine

Même punition que pour supprimer un lien à la fin de la liste. Si on a une liste de 1 Millions de chainons. Pour insérer à la position 99999, nous sommes obligés de parcourir les 99999 chainons. Et pour la suppression ce sera pareil..

{% img center /images/LinkedLists-addingnode.png 474 116 'Ajout d'un chainon' 'Ajout d'un chainon' %}

``` php
    public function insertAtPosition($position, $data)
    {
        if ($position <= 0) {
            return $this->insertFirstValue($data);
        }

        if ($position >= $this->count) {
            return $this->insertAtEnd($data);
        }

        $current = $this->first;
        for ($i = 1; $i < $position; $i++) {
            $current = $current->getNext();
        }

        $node = new Node($data, $current->getNext());
        $current->setNext($node);

        $this->count++;
        return $this;
    }
```

## Implementer le `count`

Si vous avez lu le [post précédent]() il suffit d'ajouter une méthode `count`

``` php
    public function count()
    {
        return $this->count;
    }

```

## Des applications avec la Liste chainée.

Si on renomme la méthode `insertAtEnd($data)` par `enqueue($job)` et la méthode `removeFirstValue()` par `dequeue()`

On obtient une file d'attente ou une `Queue` en anglais.
``` php 
$fileAttente = new LinkedList();
$fileAttente->enqueue("job1")->enqueue("job2");
var_dump($fileAttente->dequeue()); // job1
// je rajoute un travail 
$fileAttente->enqueue("OtherJob");
var_dump($fileAttente->dequeue()); // job2
var_dump($fileAttente->dequeue()); // OtherJob

```
Si on renomme la méthode `insertFirstValue` en `push` et la méthode `removeFirstValue()` par `pop()` On obtient une Stack.

Voici le code pour inverser un array sans utiliser `array_reverse`
``` php
$list1 = array(1,2,3,4,5);
$stack = new LinkedList();
foreach ($list as $value) {
    $stack->push($value);
}
$list2 = array();
while (stack->count()) {
    $list2[] = $stack->pop();   
}
var_dump($list2) //[5,4,3,2,1];
```

## Conclusion

Un ancien livre est titré
>>Algorithms + Data Structures = Programs

On a tendance en language php à penser tout en Object et en Array. Parfois la façon dont on représente nos données est importante.

* Certaines opérations comme ajouter un lien au début/fin de la chaine sont très peu couteuses (une étape) on parle de complexité O(1);
* supprimer un lien à la fin de la liste par contre prend N étapes On dit que la complexité est de O(N)

Pour résoudre ce problème on a inventé les listes doublements chainées. Voir le dessin (Wikipédia);

{% img center /images/Doubly-linked-list.png 610 41 'doubles listes chainée' 'double liste chainée' %}

Cela prend beaucoup plus de mémoire, mais on simplifie beaucoup l'ajout et la suppression au début et à la fin de liste. par contre la recherche dans une liste chainée est toujours aussi longue. 

Mon post sur les [Stack](blog/2015/08/29/stacks-structures-meconnues/).

Dans le post suivant on implémentera les méthodes de `ArrayAccess` et `Iterator`, ce qui nous permettra de faire des `foreach` ou `isset($list[2])` etc ..
