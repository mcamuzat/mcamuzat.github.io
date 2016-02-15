---
layout: post
title: "Suite visiteur pattern : Visiteur Booleen"
date: 2015-04-06 19:55:44 +0200
comments: true
categories:  [design-pattern, interpret, visiteur, php]
---

##Introduction:
Nous allons refaire la même chose que notre interpréteur d'expressions.
Mais avec des expressions booléennes.
Par exemple
```php
$expression = new Or( new And(New False(), New True()), new False);
```
Nous allons ensuite rajouter les comparaisons `==`, `<`, etc ..
```php
$expression = new Not(new NotEqual(new Constant(5), new Variable('i')));
```

Beaucoup de code. mais si vous avez compris la première partie. cela devrait aller.

##Expression Booléenne

Nous définissons l'interface suivante.

```php
/**
 * Une expression Booléenne
 */
interface BoolExpression
{
    public function accept(VisitorBoolExpression $v);
}
/**
 * Une classe abstraite.
 */
abstract class Unary implements BoolExpression
{
    public function accept(VisitorBoolExpression $v)
    {
        return $v->visit($this);
    }
}
```

Pour faire l'algèbre booléen j'ai besoin de `False` et de `True`

Voici le code.

```php
class True extends Unary {}

class False extends Unary {}


```

J'ai aussi besoin de la négation

```php
class Not extends Unary {
    private $value;
    public function __construct(BoolExpression $expr) {
        $this->value = $expr;
    }
    public function getValue() {
        return $this->value;
    }
}
```

Je vais rajouter la condition And, Or, Nand (No-et), Nor(Non-ou)

Je définis une classe avec deux arguments dans le constructeur.
```php
class Binary extends Unary
{
    private $left;
    private $right;
    public function __construct(BoolExpression $left, BoolExpression $right) {
        $this->left = $left;
        $this->right = $right;
    }
    public function getLeft() {
        return $this->left;
    }
    public function getRight() {
        return $this->left;
    }
}
```

Les classes sont alors très simples.
```php
// Or et And sont des mots réservés en Php.
class BinaryOr extends Binary{}
class BinaryAnd extends Binary{}
class BinaryNand extends Binary{}
class BinaryNor extends Binary{}
```

J'ai un peu près tout.

On peut passer au Visiteur.
```php
interface VisitorBoolExpression{
    public function visit(BoolExpression $expr);
}


class VisitorBoolEvaluation implements VisitorBoolExpression {
    protected $context;
    function __construct($context){
        $this->context = $context;
    }

    public function visit(BoolExpression $expr){
        $class = 'visit'.get_class($expr);
        return $this->$class($expr);
    }
    public function visitTrue(BoolExpression $expr)
    {
        return true;
    }
    public function visitFalse(BoolExpression $expr)
    {
        return false;
    }
    public function visitNot(BoolExpression $expr)
    {
        return !$expr->getValue()->accept($this);
    }
    public function visitBinaryOr(BoolExpression $expr)
    {
        return $expr->getLeft()->accept($this)||$expr->getRight()->accept($this);
    }
    public function visitBinaryAnd(BoolExpression $expr)
    {
        return $expr->getLeft()->accept($this)&&$expr->getRight()->accept($this);

    }
    public function visitBinaryNor(BoolExpression $expr)
    {
        return !($expr->getLeft()->accept($this)||$expr->getRight()->accept($this));

    }
    public function visitBinaryNand(BoolExpression $expr)
    {
        return !($expr->getLeft()->accept($this) && $expr->getRight()->accept($this));
    }
}
```

Quelques exemples. 

On réutilise notre mémoire du billet précédent. On utilise aussi `var_dump` plutôt que `echo` car `echo false` ne renvoie rien.

```php
$memory = new Memory();
$memory->write('i', 10);

$ve = new VisitorBoolEvaluation($memory);
// une expression
$expression =  new True();
// appelle le visiteur
var_dump($expression->accept($ve)) // affiche bool(true);
$expression =  new Not(new False());
var_dump($expression->accept($ve)) // affiche bool(true);
$expression =  new BinaryAnd(new Not(new False()), new BinaryOr(new True(), new False()));
var_dump($expression->accept($ve)) //Affiche bool(true);
```

bien sur on peux refaire un autre visiteur pour traduire en chaînes de caractères
```php
class VisitorBoolPrint implements VisitorBoolExpression {
    protected $context;
    function __construct($context){
        $this->context = $context;
    }

    public function visit(BoolExpression $expr){
        $class = 'visit'.get_class($expr);
        return $this->$class($expr);
    }
    public function visitTrue(BoolExpression $expr)
    {
        return "true";
    }
    public function visitFalse(BoolExpression $expr)
    {
        return "false";
    }
    public function visitNot(BoolExpression $expr)
    {
        return "!" . $epr->getValue()->accept($this);
    }
    public function visitBinaryOr(BoolExpression $expr)
    {
        return "(" . $expr->getLeft()->accept($this)
        .'||' .$expr->getRight()->accept($this). ")";
    }
    ...
    ...
 }
```
Le même exemple .

```php
$memory = new Memory();
$memory->write('i', 10);
$ve = new VisitorBoolPrint($memory);
// une expression
$expression =  new True();
// appelle le visiteur
var_dump($expression->accept($ve)) // affiche true;
$expression =  new Not(new False());
var_dump($expression->accept($ve)) // affiche !false;
$expression =  new BinaryAnd(new Not(new False()), new BinaryOr(new True(), new False()));
var_dump($expression->accept($ve)) //Affiche (!false&&(true||false));
```

## Les comparaisons


Nous pouvons rajouter le `==`, `!=`, `>` , `<` !

ajoutons de nouveau objet. les object prennent en entrée des expressions mais sortent des boléens. 

```php
class BinaryComparaison extends Unary
{
    private $left;
    private $right;
    public function __construct(Expression $left, Expression $right) {
        $this->left = $left;
        $this->right = $right;
    }
    public function getLeft() {
        return $this->left;
    }
    public function getRight() {
        return $this->right;
    }
}

class Equal extends BinaryComparaison{}
class NotEqual extends BinaryComparaison{}
//Greater Than Equal
class Gte extends BinaryComparaison{}
// Lesser Than Equal
class Lte extends BinaryComparaison{}
// Lesser Than
class Lt extends BinaryComparaison{}
// Greater Than
class Gt extends BinaryComparaison{}
```

Pour mon visiteur je vais utiliser mon visiteur d'expression du post précédent.

donc je modifie le constructeur.
```php
    function __construct($context, $ve){
        $this->context = $context;
        $this->ve = $ve;
    }

```
je ne montre que le égal, mais vous avez un peu près l'idée pour le reste. 
```php
    public function visitEqual(BoolExpression $expr)
    {
        return ($expr->getLeft()->accept($this->ve)  == $expr->getRight()->accept($this->ve));
    }
```

Le visiteur booléen utilise un autre visiteur pour évaluer une expression. 

Un exemple d'utilisation
```php
$memory = new Memory();
$memory->write('i', 10);
// une visiteur d'expression
$ve = new VisitorEvaluation($memory);
// un visiteur pour les expressions booléennes
$vb = new VisitorBoolEvaluation($memory, $ve);
// une expression
$expression =  new Not(new Equal( new Constant(10), new Addition(new Constant(5), new Variable('i'))));
var_dump ( $expression->accept($vb) );// affiche bool(true)
```

si je reprend mon autre visiteur `VisitorToPhp` avec le `visitorBoolPrint`

```php
$memory = new Memory();
$memory->write('i', 10);
$ve = new VisitorToPhp($memory);
$vb = new VisitorBoolPrint($memory, $ve);
// une expression
$expression =  new Not(new Equal( new Constant(10), new Addition(new Constant(5), new Variable('i'))));
var_dump ( $expression->accept($vb) ); //affiche  "!(10==(5+$i))"
```

##Une conclusion.
* Dans le premier post : On a vu le visiteur pour évaluer/afficher des expressions.
* dans le second post : le visiteur pour les expressions booléennes et les comparaisons. Celui-ci utilise le premier visiteur pour faire les calculs.

dans un prochain post, je vais montrer un troisième visiteur `visitorInstruction` pour évaluer des instructions d'un langage très simple. Mais cela est un peu long à écrire. Il y a un peu de théorie et des figures à faire.

Merci de m'avoir lu.

