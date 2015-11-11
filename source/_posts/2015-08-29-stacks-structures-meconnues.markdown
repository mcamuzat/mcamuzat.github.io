---
layout: post
title: "Les stacks des structures méconnues"
date: 2015-08-29 16:34:41 +0200
comments: true
categories: ["php", "spl", "algorithme", "Structure"] 
---

## Introduction

Je vais parler de `SplStack` une structure de donnée qui fait partie de La SPL (pour **S**tandart **P**HP **L**ibrairie). Nous allons voir trois façons de nous servir de cette structure.

## Les Stacks ou Piles

La pile n'a que deux opérations.

 * Empiler ou Push On ajoute une donnée sur le haut de la pile.
 * Dépiler ou Pop On retire une donnée du haut de la pile. 

Il n'y a que le haut de la pile qui est visible. La pile est une mémoire LIFO (**L**ast **I**n **F**irst **O**ut).

Quelques exemples.

``` php
$pile = new SplStack(); // la pile est vide []
$pile->push(34) // [34]
$pile->push(45) // [34, 45]
$value = $pile->pop() // [34]
$pile->push('a') // [34, 'a']
$value = $pile->top() // $value = 'a' pile [34, 'a']
```

On peux utiliser les array comme des piles avec `array_pop` et `array_push`. Mais depuis PHP 5.0 il existe une Classe tout faite `SplStack`

## Premiere application la machine à pile

Il faut d'abord que je vous parle de la notation polonaise inverse. (RPN en anglais pour **R**everse **P**olish **N**otation).
`1 + 3` devient `1 3 +`. Pour faire simple je mets l'opérateur à la fin.

des exemples un peu plus complexe. 

 `1 + 2 * 3` devient `2 3 * 1 +` et `( 1 + 3 ) * ( 3 - 4 )` devient `1 3 + 3 4 - *`

C'est un peu compliqué comme notation (en tout cas pas naturelle) mais nous allons voir que l'algorithme pour le calcul est très simple.

Voici l'algorithme :
 
 * Si l'entrée est un entier : je l'empile
 * Si c'est une opération : je dépile deux valeurs, je fais l'opération et j'empile le résultat

Un exemple

``` 
soit 1 3 + 3 4 - * 
# je prend la premiere valeur "1" c'est un chiffre je l'empile
ma pile [ *1* ] 
# je prend la seconde valeur "3" c'est un chiffre je l'empile
ma pile [ 1 *3* ]
# je prend la valeurs 3 c'est une operation "+", je depile deux valeurs d'abords "3" puis "1". je fais l'addition. "4" que j'empile
ma pile [ *4* ]
# je prend la quatrieme valeur "3" c'est un chiffre j'empile
ma pile [ 4 *3* ]
# la cinquieme valeurs est un chiffre
ma pile [ 4 3 *4*]
# la sixieme valeur est une opération. je dépile deux valeurs "4" et "3" que je soustrait et je rempile
ma pile [ 4 *-1*]
# la septième valeur est une opération je dépile "-1" et "4" que je multiplie
ma pile ["-4"]
```

L'avantage de la notation est qu'elle n'a pas besoin de parenthèse. Il n'y pas d'ambigüité `( 1 + 3 ) * ( 3 - 4 )` est différent de  `1 + 3 * 3 - 4 `. 

L'implémentation est simple

``` php
function execute(array $ops)
{
    $stack = new \SplStack();

    foreach ($ops as $op) {
        if (is_numeric($op)) {
            $stack->push((int) $op);
            continue;
        }

        switch ($op) {
            case '+':
                $stack->push($stack->pop() + $stack->pop());
                break;
            case '-':
                $n = $stack->pop();
                $stack->push($stack->pop() - $n);
                break;
            case '*':
                $stack->push($stack->pop() * $stack->pop());
                break;
            case '/':
                $n = $stack->pop();
                $stack->push($stack->pop() / $n);
                break;
            default:
                throw new \InvalidArgumentException(sprintf('Invalid operation: %s', $op));
                break;
        }
    }

    return $stack->top();
}

```
essayons notre exemple.

``` php
var_dump(execute(explode(' ', '1 3 + 3 4 - *');
int(-4)
```

Félicitation nous venons d'implémenter notre première machine à pile. La plus célèbre est la `Java Virtual Machine`. Il existe aussi des langages qui sont basé sur la notion de pile, le plus célèbre est le [Forth](https://fr.wikipedia.org/wiki/Forth_%28langage%29) et le [Postscript](https://fr.wikipedia.org/wiki/PostScript)(si si le format de adobe). L'avantage des machines à pile est qu'elle n'utilise aucun autre registre que la pile. 

## Le Shunting-yard de Dijkstra

Je présente une version simplifié. *Shunting-yard* peut se traduire en **Aiguillage**. Il permet d'évaluer une expression mathématique.

Soit la chaîne suivante:

``` php
var_dump($calculate(explode(' ', '( ( 1 + 3 ) * ( 3 - 4 ) )')));

# int(-4)
```

 * Il y a des parenthèses partout
 * L'arité des fonction est 2:  l'arité est le nombre d'argument par exemple 1 + 2 est d'arité 2 deux arguments. L'algorithme que je présente est incapable de faire `1 + 2 + 3` mais fera très bien `(1 + ( 2 + 3 ))`.

Voici l'algorithme:
 
 * Si parenthèse ouvrante:  je passe
 * Si c'est une opération `+`, `-`, `/`, `*`:  Je stocke dans une pile l'opération.
 * Si c'est un chiffre : je stocke dans une pile de valeurs
 * Si c'est une parenthèse fermante: Je dépile une opération et je dépile deux arguments. Je fais l'opération avec mes deux arguments et je remets le résultats dans ma pile.

Voici le code

```
function calculate(array $input)
{
    $operators = new SplStack();
    $values = new SplStack();

    foreach ($input as $token) {
        switch ($token) {
        case "(":
            break;
        case "+":
        case "-":
        case "*":
        case "/":
            $operators->push($token);
            break;
        case ")":
            $op = $operators->pop();
            $value = $values->pop();
            switch ($op) {
            case "+":
                $value = $values->pop() + $value;
                break;
            case "-":
                $value = $values->pop() - $value;
                break;
            case "*":
                $value = $values->pop() * $value;
                break;
            case "/":
                $value = $values->pop() / $value;
                break;
            }
            $values->push($value);
            break;
        default:
            $values->push($token);

        }

    }
    return $values->top();
}

```

On utilise deux piles. Une pour les opérations, Une pour les valeurs
je propose de faire le même exemple que plus haut
je vais représenter les deux piles et l'entrée actuelle

```
operators :  [] values:  []  expression : ( ( 1 + 3 ) * ( 3 - 4 ) )

# '(' on ignore

operators [] values []  expression : ( 1 + 3 ) * ( 3 - 4 ) )

# '(' on ignore
operators [] values []  expression: 1 + 3 ) * ( 3 - 4 ) )

# '1' on ajoute dans values
operators [] values [*1*] expression:  + 3 ) * ( 3 - 4 ) )

# '+' on ajoute dans opérator
operators [*+*] values [1] expression: 3 ) * ( 3 - 4 ) )

# '3' on ajoute dans value
operators [*+*] values [1, *3*] expression: ) * ( 3 - 4 ) )

# ')' on dépile deux valeur de value et on depile une valeurs de l'operators et on empile le résultats dans values
operators [] values [4] expression:  * ( 3 - 4 ) )`

# '*' on ajoute dans opérator 
operators [*] values [4] expression:  ( 3 - 4 ) )

# '(' on ignore
operators [*] values [4] expression:  3 - 4 ) )

# '3' on ajoute dans values 
operators [*] values [4 *3*] expression:  - 4 ) )

# '-' on ajoute dans operators
operators [*, *-*] values [4 3] expression:  4 ) )

# '4' on ajoute dans values 
operators [*, ] values [4 3 4] expression:  ) )

# ')' on dépile deux valeur de value et on depile une valeur de operators et on empile le résultat dans values
operators [*] values [4 -1] expression:  )

# ')' on dépile deux valeur de values et on depile une valeur de operators et on empile le résultat dans values
operators [] values [-4] expression: 

``` 

On se rend compte que cette algorithme très simple permet de calculer toutes les expressions que l'on passe du moment qu'elles sont bien formées. 

``` php
$expression = "( ( 1 + 3 ) * ( 3 - 4 ) )"
var_dump(calculate(explode(" ", $expression)));
``` 

Félicitation vous venez d'écrire votre premier interpréteur.

la version originale prend en compte la priorité des opérations (cela rend certaines parenthèses inutiles) c'est un peu plus complexe, mais pas tant que cela.

## Exemple 3 conversion vers RPN

Nous allons utiliser notre pile pour traduire notre expression vers la RPN.

C'est à dire  `( ( 1 + 3 ) * ( 3 - 4 ) )` -> `1 3 + 3 4 - *`

Voici l'algorithme.

 * Si l'entrée est un parenthèse ouvrante :  je passe
 * Si c'est une opération : je stocke cela dans une pile
 * Si c'est un entier : Je pousse cela dans une file d'attente
 * Si c'est une parenthèse fermante : je vide la pile dans la file d'attente.
 

```
function transformate(array $input)
{
    $stack = new SplStack();
    $output = new SplQueue();

    foreach ($input as $token) {
        switch ($token) {
        case "(":
            break;
        case "+":
        case "-":
        case "*":
        case "/":
            $stack->push($token);
            break;
        case ")":
            while(count($stack)> 0 && $stack->top()) {
                $output->enqueue($stack->pop());
            }
            break;
        default:
            $output->enqueue($token);
        }
    }
    return iterator_to_array($output);
}
```
regardons avec le même exemple

```
stack :  [] output:  []  expression : ( ( 1 + 3 ) * ( 3 - 4 ) )

# '(' on ignore

stack : [] output: []  expression : ( 1 + 3 ) * ( 3 - 4 ) )

# '(' on ignore
stack :  [] output []  expression: 1 + 3 ) * ( 3 - 4 ) )

# '1' on ajoute dans output
stack: [] output [1] expression:  + 3 ) * ( 3 - 4 ) )

# '+' on ajoute dans la stack
stack: [*+*] output [1] expression: 3 ) * ( 3 - 4 ) )

# '3' on ajoute dans output
stack: [*+*] output [1, *3*] expression: ) * ( 3 - 4 ) )

# ')' on vide stack dans outputs
stack: [] output [ 1, 3, *+*] expression:  * ( 3 - 4 ) )`

# '*' on ajoute dans la stack
stack: [*] output [1 , 3 , +] expression:  ( 3 - 4 ) )

# '(' on ignore
stack: [*] output [1, 3, +] expression:  3 - 4 ) )

# '3' on ajoute dans output.
stack: [*] output [1, 3, +, *3*] expression:  - 4 ) )

# '-' on ajoute dans la stack
stack: [*, *-*] output [1, 3, +, 3] expression:  4 ) )

# '4' on ajoute dans output.
stack: [*,-] output [1, 3, +, 3, *4*] expression:  ) )

# ')' on depile la stack dans output, on dépile d'abords - puis *
stack: [] output [1, 3, +, 3, 4, -, *] expression:  )

# ')' on re depile la stack mais ici elle est déja vide. 

``` 

Il suffit de transformer en array `$output` pour avoir le résultats suivants
`( ( 1 + 3 ) * ( 3 - 4 ) )` -> `1 3 + 3 4 - *`

## Tous ensemble.

Je ne resiste pas au plaisir d'utiliser l'instruction tabou du php `eval()`

> If eval() is the answer, you're almost certainly asking the wrong question. -- Rasmus Lerdorf, BDFL of PHP

```
$operation = "( ( 1 + 3 ) * ( 3 - 4 ) )";
$input = explode(" ", $operation);
var_dump(calculate($input));
var_dump(execute(transformate($input)));
var_dump($value = eval("return ($operation);"));
```

Nous avons sans surprise le même résultat
```
int(-4)
int(-4)
int(-4)
```

## En conclusion

 * Nous avons créé une *VM* Notre machine à pile.
 * Nous avons crée un *interpréteur*:  notre algorithme de shunting-yard
 * Nous avons fait un traducteur de notre expression vers notre machine à pile. C'est un *compilateur*. 
 * La notion de pile existe partout, on parle de pile d'appel (*stack-frame*), de dépassement de la pile (*stack overflow*), `git stash` est aussi un stockage en pile.

## Des références.

 * L'algorithme simplifie viens du livre [Algorithms](http://www.amazon.fr/dp/032157351X) de Sedgewick (j'ai traduis du Java vers Php)
 * L'exemple le plus complet sur les stacks-machine est [Igor.io](https://igor.io/archive.html) la série est superbe, l'auteur explique vraiment bien.
 * L'article de wikipedia sur le [shunting-yard](https://en.wikipedia.org/wiki/Shunting-yard_algorithm) Les illustrations montrent bien la notion d'aiguillage.
 * [Edsger W. Dijkstra](https://fr.wikipedia.org/wiki/Edsger_Dijkstra) est surtout connus pour son algorithme sur le plus court chemin. Mais c'est une légende de l'informatique. A voir si vous ne connaissez pas.



