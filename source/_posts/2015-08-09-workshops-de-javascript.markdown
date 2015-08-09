---
layout: post
title: "Workshops de javascript"
date: 2015-08-09 16:44:33 +0200
comments: true
categories: ["tutoriel", "javascript", "node", "awesome"] 
---


*Et si on essayait un workshop...*

Les workshops de javascript sont des cours interactifs de javascript.

## Installation

Pour installer le cours de Node.js c'est très simple. A condition d'avoir node.js installé.

```
npm install -g learnyounode
learnyounode
```

Voila ce que vous devriez voir.

{% img center /images/workshop.png 585 397 'screenshot de workshop' 'On a un menu interactif' %}

Lançons nous dans le premier exercice.

```

 LEARN YOU THE NODE.JS FOR MUCH WIN!
─────────────────────────────────────
 HELLO WORLD
 Exercise 1 of 13

Write a program that prints the text "HELLO WORLD" to the console (stdout).

-------------------------------------------------------------------------------

## HINTS

To make a Node.js program, create a new file with a .js extension and start writing JavaScript! Execute your program by running it with the
node command. e.g.:

    $ node program.js

You can write to the console in the same way as in the browser:

    console.log("text")

When you are done, you must run:

    $ learnyounode verify program.js

to proceed. Your program will be tested, a report will be generated, and the lesson will be marked 'completed' if you are successful.

-------------------------------------------------------------------------------

 » To print these instructions again, run: learnyounode print
 » To execute your program in a test environment, run: learnyounode run program.js
 » To verify your program, run: learnyounode verify program.js
 » For help run: learnyounode help
```

donc je resume on me demande de programmer un **Hello world".

donc je crée un `hello.js`

``` js
console.log('HELLO WORLD');
```

Je peux tester celui-ci avec un programme de test avec la commande suivante
``` sh
$ learnyounode run hello.js
```

Si je suis content du résultat alors je peux faire vérifier le programme par le logiciel.

``` sh
$ learnyounode verify program.js
```
Si le programme passe, le niveau est marquer comme `[COMPLETED]` et on peux passer au suivant. 

Bon l'étape 1 n'est pas très compliqué passons à l'étape 2.

```
 LEARN YOU THE NODE.JS FOR MUCH WIN!
─────────────────────────────────────
 BABY STEPS
 Exercise 2 of 13

Write a program that accepts one or more numbers as command-line arguments and prints the sum of those numbers to the console (stdout).

-------------------------------------------------------------------------------

## HINTS

You can access command-line arguments via the global process object. The process object has an argv property which is an array containing the complete command-line. i.e. process.argv.

To get started, write a program that simply contains:

    console.log(process.argv)

Run it with node program.js and some numbers as arguments. e.g:

    $ node program.js 1 2 3

In which case the output would be an array looking something like:

    [ 'node', '/path/to/your/program.js', '1', '2', '3' ]

You'll need to think about how to loop through the number arguments so  you can output just their sum. The first element of the process.argv array is always 'node', and the second element is always the path to your program.js file, so you need to start at the 3rd element (index 2), adding each item to the total until you reach the end of the array.

Also be aware that all elements of process.argv are strings and you may need to coerce them into numbers. You can do this by prefixing the property with + or passing it to Number(). e.g. +process.argv[2] or Number(process.argv[2]).

learnyounode will be supplying arguments to your program when you run learnyounode verify program.js so you don't need to supply them yourself. To test your program without verifying it, you can invoke it with learnyounode run program.js. When you use run, you are invoking the test environment that learnyounode sets up for each exercise.

-------------------------------------------------------------------------------

 » To print these instructions again, run: learnyounode print
 » To execute your program in a test environment, run: learnyounode run program.js
 » To verify your program, run: learnyounode verify program.js
 » For help run: learnyounode help
```

Donc il s'agit de créer un programme qui prend les nombres en entrée et faire la somme à la fin.

Voici ma solution (atroce ... )

``` js
var i = process.argv;
i.shift(); 
i.shift();// supprime les deux premiers arguments ('node', 'programme.js')
console.log(i.reduce(function(a,b){return a + Number(b)},0));
```

Le logiciel donne une implémentation beaucoup plus simple(pas difficile)
``` js
   var result = 0
    
    for (var i = 2; i < process.argv.length; i++)
      result += Number(process.argv[i])
    
    console.log(result)
```

Etc etc ..

## Une liste de workshop.

Le site officiel donne la liste suivante en module de base.

 * [javascripting](https://www.github.com/sethvincent/javascripting) Apprendre les bases du javascript.
 * [git-it](https://www.github.com/jlord/git-it) pour apprendre Git et GitHub.
 * [Scope Chains & Closures](https://www.github.com/jesstelford/scope-chains-closures) Comprendre les scopes, les closures etc..
 * [learnyounode](https://www.github.com/workshopper/learnyounode) les bases de node asynchronous i/o, http.
 * [How to npm](https://github.com/npm/how-to-npm) Comment créer des modules Npm
 * [stream-adventure](https://www.github.com/substack/stream-adventure) apprennez les streams et comment les composer avec `.pipe()`.



## Mais il y en a plus.

On peux apprendre un peu près n'importe quel technologie en pratique.

 * [Functional Javascript](https://github.com/timoxley/functional-javascript-workshop) : Base de la programmation fonctionnelle en javascript.
 * [ExpressWorks](https://github.com/azat-co/expressworks): Apprendre le framework Express.js.
 * [Promise It Won't Hurt](https://github.com/stevekane/promise-it-wont-hurt):  Apprendre les promesses pour les opération asynchrone.
 * [Async](https://github.com/bulkan/async-you):  La librairie Async.
 * [Planet Proto](https://github.com/sporto/planetproto):  Comprendre l'héritage prototypal.
 * [Test Anything](https://github.com/finnp/test-anything):  Comment tester son code
 * [learnyoumongo](https://github.com/evanlucas/learnyoumongo):  Débuter avec MongoDB et node.js
 * [Shader School](https://github.com/gl-modules/shader-school):  Comprendre les shaders.
 * [Bug Clinic](https://github.com/othiym23/bug-clinic):  Apprendre de nouveaux outils et debugger plus facilement.
 * [Intro to WebGL](https://github.com/alexmackey/IntroToWebGLWithThreeJS):  Débuter avec [three.js]() et le WebGL.
 * [LololoDash](https://github.com/mdunisch/lololodash): Apprendre Lo-Dash (fork de underscore)
 * [learnyoucouchdb](https://github.com/robertkowalski/learnyoucouchdb):  Apprendre CouchDB.
 * [learnyoureact](https://github.com/tako-black/learnyoureact):  Apprenez React.js.

## Des liens

 * le [site officiel](http://nodeschool.io) et la version [française](http://nodeschool.io/fr-fr/)
 * J'ai déjà parlé des [awesome-list](/blog/2015/04/29/awesome-et-liste-de-liens/). bien entendu elle existe pour les [workshops](https://github.com/therebelrobot/awesome-workshopper).

## Conclusion

Vous voulez apprendre le js, react.js, écrire des tests je crois que vous savez par ou commencer. 
