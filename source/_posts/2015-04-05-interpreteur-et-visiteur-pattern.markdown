---
layout: post
title: "Interpréteur et Visiteur Pattern"
date: 2015-04-05 18:16:10 +0200
comments: true
categories: [design-pattern, interpret, visiteur, php]
---

#Introduction
Nous allons voir ensemble sur une série de deux posts

- le design-pattern interpréteur
- les limitations et une solution qui va introduire le visiteur pattern

#Mise en place

Nous allons créer un simple calculatrice.

Nous définissons l'interface suivante

``` php
/**
 * Une expression arithmétique
 */
interface Expression
{
    public function interpret(Context $context = null);
}


```

##Evaluer des constantes

voici le code pour evaluer des constantes
``` php
Class Constant implements Expression
{
    private $value;
    public function __construct($value)
    {
        $this->value = $value;
    }

    public function interpret(Context $context = null) 
    {
        return $this->value;
    }
}
```

Un exemple 
```
$constante = new Constant(5);
echo $constante->interpret(); // affiche 5
```

jusqu'ici rien de complexe. Si j'interprète la constante que j'ai définie à 5, j'obtiens 5.

##Evaluer des additions

voici le code pour interpréter les additions

```
Class Addition Implements Expression
{
    private $left;
    private $right;
    public function __construct(Expression $left, Expression $right) {
        $this->right = $right;
        $this->left = $left;
    }
    public function interpret(Context $context = null) {
        return $this->left->interpret($context) + $this->right->interpret($context);
    }

}

```

Un exemple 
```
$addition = new Addition(new Constant(5), new Constant(6));
echo $constante->interpret(); // affiche 11
```
On utilise la **récursion** pour interpreter la partie droite et gauche

```
$addition = new Addition(new Addition( new Constant(5), new Constant(6)), new Constante(4));
echo $constante->interpret(); // affiche 15
```

Faire la multiplication, la soustraction, la division ne sont pas plus compliquées. il suffit de changer le signe dans la fonction interpret()

```
    // muliplication 
    public function interpret(Context $context = null) {
        return $this->left->interpret($context) * $this->right->interpret($context);
    }
```

##Ajouter d'autres méthodes

Ajoutons la methode Abso qui renvoie la valeur absolue, la fonction min qui renvoie le minimum 
``` php
Class Abso Implements Expression
{
    public function __construct($value) {
        $this->value = $value;
    }
    public function interpret(Context $context = null) {
        return abs($this->value->interpret($context));
    }

}

Class Minimum Implements Expression
{
    public function __construct(Expression $left, Expression $right) {
        $this->right = $right;
        $this->left = $left;
    }
    public function interpret(Context $context = null) {
        return min($this->right->interpret($context),$this->left->interpret($context));
    }
}
```

un exemple 
```
$min = new Minimum(new Abso(-10), new Addition(new Constant(24), new Constant(2)));
echo $min->interpret(); // renvoie 10
```

##Tout n'est qu'une question de contexte

Nous allons ajouter les variables.

Il nous faut d'abord implémenter le Context

Voici la définition
``` php
/**
 * Interface Context
 */
interface Context
{
    // write a value in memory
    public function write($name, $value);
    // get a value from the memory
    public function read($name);
    //return all the value
    public function getAll()
}

/**
 * A Memory
 */
class Memory implements Context
{
    private $memory = array();
    // write a value in memory
    public function write($name, $value)
    {
        $this->memory[$name] = $value;
        return $this;
    }

    // get a value from the memory
    public function read($name)
    {
        return $this->memory[$name];
    }
    
    public function getAll()
    {
        return $this->memory;
    }

}

```

Il ne nous reste plus qu'à implémenter la variable.

``` php
class Variable implements Expression
{    public function __construct($name) {
        $this->name = $name;
    }
    public function interpret(Context $context = null){
        return $context->read($this->name);
    }
}

```
On comprend l'intéret du context. il nous permet de passer un pseudo-scope..

un exemple:
```
$memory = new Memory();
$memory->write('i', 10);
$expression = new Addition(new Constant(10), new Variable('i'));
echo $expression->interpret($memory); // 20

$memory->write('i', 0);
echo $expression->interpret($memory); // 10
```

On peux rajouter plein d'autre expression. l'avantage est qu'il suffit de rajouter une méthode `->interpret(..)` pour chaque objet.

#mais si on change le cahier des charges...

changeons le cahier des charges. je souhaite transformer mon Expression en chaine de caractères. je peux m'en sortir en surchargeant la methode `__tostring`

par exemple :
``` php
$expression = new Addition (new Addition(new Constant(3), new Constant(4)), new Constante(4));
$expression->__toString() // me donne ((3 + 4) + 4);
```

```
// pour la constante
        public function __toString() {
            return $this->value;
        }

// pour l'addition
         public function __toString() {
                // this->left->__toString()
                return '(' . $this->left . ' + ' . $this->right .')';
         }
```

rechangeons le cahier des charges : je veux la traduction en Php

```
$expression = new Addition (new Addition(new Variable('i'), new Constant(4)), new Constante(4));
$expression->__toPhp() // me donne (($i + 4) + 4);

```
je suis un peu bloqué, je dois rajouter à chaque fois une méthode dans chaque object. Je perd un peu de la simplicité du pattern..


##Visiteur Pattern à la rescousse !

Je vais définir une méthode `accept(Visitor $visitor)`
``` php
interface Expression{
     public function accept(VisitorExpression $v);
}

```

avec VisitorExpression définit ainsi
```
abstract class VisitorExpression{
    public abstract function visite(Expression $expr);
}
```

Voici comment se transforme l'addition, la constante et la variable (je ne mets pas tout..)
``` php
Class Constant implements Expression
{
    private $value;
    public function __construct($value)
    {
        $this->value = $value;
    }
    public function getValue() {
        return $this->value;
    }
    public function accept(VisitorExpression $v)
    {
        return $v->visit($this);
    }

}

Class Addition Implements Expression
{
    public function __construct(Expression $left, Expression $right) {
        $this->right = $right;
        $this->left = $left;
    }
    public function getLeft() {
        return $this->left;
    }
    public function getRight() {
        return $this->right;
    }
    public function accept(VisitorExpression $v)
    {
        return $v->visit($this);
    }

}

class Variable implements Expression
{
    private $name;
    public function __construct($name) {
        $this->name = $name;
    }
    public function getName() {
        return $this->name;
    }
    public function accept(VisitorExpression $v)
    {
        return $v->visit($this);
    }
}

```

Voici l'implémentation de notre Visiteur
```
class VisitorEvaluation extends VisitorExpression {
    protected $context;
    function __construct($context){
        $this->context = $context;
    }

    public function visit(Expression $expr){
        $class = 'visit'.get_class($expr);
        return $this->$class($expr);
    }
    public function visitAddition(Expression $expr)
    {
        return $expr->getLeft()->accept($this) +
            $expr->getRight()->accept($this);

    }
    public function visitConstant(Expression $expr)
    {
        return $expr->getValue();

    }
    public function visitVariable(Expression $expr)
    {
         return $this->context->read($expr->getName());
    }

```
en pratique. On appelle la méthode `accept`. Celle-ci appelle la methode `visit($this)`. la méthode visit détermine la fonction à appeller. 
Si c'est une constante alors `visistConstant()` celle-ci résout la valeur. pour une addition c'est un plus compliqué on ré-appelle récursivement `accept` sur chaque partie de l'addition.

voici comment s'en servir

```php
// j'ai besoin d'une mémoire
$memory = new Memory();
$memory->write('i', 10);
// j'ai besoin d'un visiteur
$ve = new VisitorEvaluation($memory);
// une expression
$expression = new Addition(new Constant(10), new Variable('i'));
// appelle le visiteur
echo $expression->accept($ve); // 20
```

On se rend compte qu'il n'y a plus de logique dans mes objet. tout est sous-traité dans le visiteur.

l'avantage de cette méthode est qu'il est très simple de changer le visiteur sans changer la logique.

par exemple le visiteur qui convertit en php

``` php
}
class VisitorToPhp extends VisitorEvaluation {
    public function visitAddition(Expression $expr)
    {
        return '(' .  $expr->getLeft()->accept($this) . '+'
            . $expr->getRight()->accept($this). ')';

    }
    public function visitVariable(Expression $expr)
    {
         return '$'. $expr->getName();
    }

    public function convertMemory()
    {
        $output = '';

        foreach($this->context->getAll() as $key => $value) {
            $output .= '$'.$key . ' = ' . $value . ';';
        }
        return $output;
    }

    public function getOutput()
    {
        return 'echo';
    }

    public function translate(Expression $exp) {
        return $this->convertMemory() . $this->getOutput() . $exp->accept($this);
    }
}

```

et celui qui convertit en Javascript !
```
class VisitorToJs extends VisitorToPhp {
    public function visitVariable(Expression $expr)
    {
         return $expr->getName();
    }

    public function convertMemory()
    {
        $output = '';
        foreach($this->context->getAll() as $key => $value) {
            $output .= 'var '. $key . ' = ' . $value . ';';
        }
        return $output;
    }
    public function getOutput()
    {
        return 'console.log';
    }

}

```

```

$memory = new Memory();
$memory->write('i', 10);
$ve = new VisitorEvaluation($memory);
// une expression
$expression = new Addition(new Constant(10), new Variable('i'));
// appelle le visiteur evaluation simple
echo $expression->accept($ve); // 20
// evaluation conversion php 
$php = new VisitorToPhp($memory);
echo $expression->accept($php); // (10 + $i)
$js = new VisitorToJs($memory);
echo $expression->accept($js); // (10 + i)
// j'ai rajouté une méthode translate qui est un raccourci
echo $php->translate($expression); // $i = 10;echo(10+$i)
echo $js->translate($expression); // var i = 10;console.log(10+i)
```

Les limitations du visiteur pattern

- toute la logique est sur le visiteur. s'il y a un beaucoup de type d'expression (dans notre cas Addition, Constant, Variable, Abso, Multiplication ..) c'est autant de ligne à rajouter dans celui-ci.
- rajouter un *type*, oblige à le ré-implementer partout.

Les avantages du visiteur pattern.
On peut parfaitement imaginer un type document, et lui ajouter un visiteur `toJson`, `toPdf`, `toEbook`, `toHtml`. sans jamais changer le modèle.

Nous continuerons avec le visiteur pattern dans un prochain post. Nous ajouterons un visiteur pour les expressions booléenes. puis nous ajouterons un visiteur pour des instructions. nous allons créer un mini-langage..

Ce projet vient des notes que j'avais prise quand j'étais au CNAM sur le cours de Design-Pattern en Java. J'avais adoré! 




