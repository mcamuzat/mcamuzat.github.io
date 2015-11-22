---
layout: post
title: "Décorer PHPUnit avec les Listeners"
date: 2015-08-04 19:44:52 +0200
comments: true
categories: ["PHPUnit", "GitHub", "PHP", "QA"]
---

## Des test unitaires plus jolis

Comme tout les programmeurs vous faites des tests unitaires. En php, On utilise souvent PHPUnit. 
quand je lance mes tests je vois cela.

{% img center /images/phpunit_screenshot.png 499 168 'Screenshot de phpunit' 'Mon terminal n\'est pas triste..' %}


C'est un peu triste. Et encore j'ai activé la couleur !.

il existe des variantes avec `--testdox`

<!--more-->

``` sh
$ bin/phpunit --testdox
PHPUnit 4.8-ge1cc585 by Sebastian Bergmann and contributors.

Spark\Spark
 [x] It creates a string from data
 [x] It dont care if input is string
 [x] It works with float number
 [x] It s not divide by 0
```

C'est un peu mieux.

Il existe aussi `--debug`
``` sh
PHPUnit 4.8-ge1cc585 by Sebastian Bergmann and contributors.

Starting test 'Spark\SparkTest::testItCreatesAStringFromData'.
.
Starting test 'Spark\SparkTest::testItDontCareIfInputIsString'.
.
Starting test 'Spark\SparkTest::testItWorksWithFloatNumber'.
.
Starting test 'Spark\SparkTest::testItSNotDivideBy0'.
.

Time: 69 ms, Memory: 4.50Mb

OK (4 tests, 4 assertions)
```


Mais on a un peu fait le tour

## Les Listeners de PHPUnit

On peux surcharger l'affichage de PHPunit et cela grâce au listener.

### Comment enregistrer un listener

Il suffit d'éditer `phpunit.xml` et de rajouter les lignes suivantes

``` xml
  <listeners>
    <listener class="SparkListener" 
	    file="../src/un-projet-pro/FooBundle/Tests/Listener/SparkListener.php">
    </listener>
  </listeners>

```

### Comment implémenter un listener.

Le plus propre est d'implémenter tout les méthodes de l'interface 

``` php
<?php

class Monlistener implements PHPUnit_Framework_TestListener {
    public function addError(PHPUnit_Framework_Test $test, Exception $e, $time) {}
    public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time) {}
    public function addIncompleteTest(PHPUnit_Framework_Test $test, Exception $e, $time) {}
    public function addSkippedTest(PHPUnit_Framework_Test $test, Exception $e, $time) {}
    public function startTest(PHPUnit_Framework_Test $test) {}
    public function endTest(PHPUnit_Framework_Test $test, $time) {}
    public function startTestSuite(PHPUnit_Framework_TestSuite $suite) {}
    public function endTestSuite(PHPUnit_Framework_TestSuite $suite) {}

}

```

Mais pour aujourd'hui, Je vais faire plus simple je vais hériter de la classe `PHPUnit_TextUI_ResultPrinter` si je n'ai pas envie de réécrire toutes les méthodes.
  

## Plein d'utilisation de Listener

### Des statistiques sur les tests
Quel est le test qui prend le plus de temps ? Facile avec le Listener suivant.

```
<?php
class MaxListener extends PHPUnit_TextUI_ResultPrinter
{
    public $maxTime = 0;

    private $suites = 0;
    private $endedSuites = 0;
    public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
    {

        $this->suites++;
    }
    public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->endedSuites++;
        if ($this->suites > $this->endedSuites) {
            return;
        }
        echo PHP_EOL;
        echo "le test le plus long prend $this->maxTime seconde(s)";

    }

    public function endTest(PHPUnit_Framework_Test $test, $time)
    {
        $this->maxTime = max($time, $this->maxTime);
    }
}
```

Avec ma librairie que j'ai développé dans les [posts](/blog/2015/07/19/histogramme-et-ligne-de-commande/) précédents.
``` php
use Spark\Spark;
class SparkListener extends PHPUnit_TextUI_ResultPrinter
{
    private $suites = 0;
    private $endedSuites = 0;
    public $testTimes = array();

    public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->suites++;
    }
    public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->endedSuites++;
        if ($this->suites > $this->endedSuites) {
            return;
        }
        echo "\n";
        echo Spark::spark($this->testTimes);

    }

    public function endTest(PHPUnit_Framework_Test $test, $time)
    {
        $this->testTimes[] = $time;
    }
}

```

Voici le résultat
```
bin/phpunit -c build/phpunit.xml
PHPUnit 4.5.1 by Sebastian Bergmann and contributors.

Configuration read from /home/marc/prog/Un-projet-pro/build/phpunit.xml

.....................................................
█▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▁▂▁▁▁▁▁▁▁▁▁▁▁▂▁▁▁▁▁▁▁▂▁▁▁▁▁▁▁▁
le test le plus long prend 0.2183518409729 seconde(s)

Time: 971 ms, Memory: 17.25Mb

OK (53 tests, 98 assertions)

```

### Libérer de la mémoire

On peux libérer de la mémoire en mettant à `null` les mocks à la fin du test. (exemple trouvé sur github [mybuilder/phpunit-accelerator](https://github.com/mybuilder/phpunit-accelerator))

```
class FreeListener implements PHPUnit_Framework_TestListener
{
    // .. 
    // les autres methodes vides.
    public function endTest(PHPUnit_Framework_Test $test, $time)
    {
        $refl = new ReflectionObject($test);
        foreach ($refl->getProperties() as $prop) {
            if (!$prop->isStatic() && 0 !== strpos($prop->getDeclaringClass()->getName(), 'PHPUnit_')) {
                $prop->setAccessible(true);
                $prop->setValue($this, null);
            }
        }
    }
}
```

On pourrait faire cela sur un `tearDown()` 

### Jouer des fixtures

Normalement il n'y a pas de fixture dans PHPUnit. Mais en pratique pour tester certaines méthodes dans les repository, ben il n'y pas beaucoup le choix.  On peux refaire la base, dropper le schéma, un truncate à chaque test ou `memory::sqlite`. Mais sur certaines bases de données, c'est un peu compliqué. Une solution est de vider la base entre chaque suites de tests. Une proposition 

``` php
class DBListener implements PHPUnit_Framework_TestListener
{
    // .. 
    // les autres methodes vides.

    public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->truncateDb();
    }

    public function truncateDb() {
        // vide la table..
    }
    public function fixtureDb() {
        // joue les fixtures
    }
    public function startTest(PHPUnit_Framework_Test $test)
    {
        // si le nom du test contient truncate
        if (strpos($test->getName(), 'truncate')) {
            $this->truncateDB();
        }
        //
        if (strpos($test->getName(), 'fixture')) {
            $this->truncateDB();
            $this->fixtureDB();
        }
    }
}
```

Si le nom du test contient `truncate` et `fixture` alors on force le truncate et/ou fixture. Une autre possibilité est d'implémenter la méthode `setUpBeforeClass` dans le test. Cette méthode est jouée juste avant l'instantiation de la classe. C'est du statique, donc pas forcement la joie..


```
    public static function setUpBeforeClass()
    {
        parent::setUpBeforeClass();
        self::TruncateDB()

    }

``` 

### Relancer les tests qui ne passent pas.

Pour le fun..

```
<?php
class FailureListener extends PHPUnit_TextUI_ResultPrinter
{
    private $suites = 0;
    private $endedSuites = 0;
    public $failTest = array();

    public function startTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->suites++;
    }

    public function addFailure(PHPUnit_Framework_Test $test, PHPUnit_Framework_AssertionFailedError $e, $time) {
       $this->failTest[] = $test->getName();
    }
    public function startTest(PHPUnit_Framework_Test $test) {}

    public function endTestSuite(PHPUnit_Framework_TestSuite $suite)
    {
        $this->endedSuites++;
        if ($this->suites > $this->endedSuites) {
            return;
        }
        $cli = implode('|', $this->failTest);
        echo PHP_EOL . "Pour relancer seulement les tests qui ne passent pas ajoutez" . PHP_EOL;
        echo "--filter '($cli)'";
    }

}

```

Le script en action.
``` sh
bin/phpunit -c build/phpunit.xml 
PHPUnit 4.5.1 by Sebastian Bergmann and contributors.

Configuration read from /home/marc/prog/un-projet-pro/build/phpunit.xml

............................................................................................F.F...........
Pour relancer seulement les tests qui ne passent pas ajoutez
--filter '(testObjectNeedUpdate|testUpdateObject)'

Time: 958 ms, Memory: 17.25Mb

```

La seconde fois avec la commande qui va bien.
```
bin/phpunit -c build/phpunit.xml --filter '(testObjectNeedUpdate|testUpdateObject)'                       
PHPUnit 4.5.1 by Sebastian Bergmann and contributors.

Configuration read from /home/marc/prog/un-projet-pro/build/phpunit.xml

FF

Time: 116 ms, Memory: 9.25Mb
```

## Conclusion

Il y a encore pas mal d'utilisation je pense au [nyan-cat](https://github.com/whatthejeff/nyancat-phpunit-resultprinter) ! Ou l'utilisation avec des notifications sur le bureau par exemple [ici](https://github.com/llaville/phpunit-LoggerTestListener).

La librairie [mcamuzat/spark](https://packagist.org/packages/mcamuzat/spark) a été initialement fait pour ce post. J'ai passé finalement plus de temps sur la création de la librairie.

