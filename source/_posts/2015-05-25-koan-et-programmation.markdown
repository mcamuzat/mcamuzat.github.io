---
layout: post
title: "Koan et Programmation"
date: 2015-05-25 20:53:23 +0200
comments: true
categories: ["php", "ruby", "javascript", "koan", "awesome"] 
---



Un koan est dixit wikipedia 
>> koan est une une brève anecdote ou un court échange entre un maître et son disciple, absurde, énigmatique ou paradoxal, ne sollicitant pas la logique ordinaire.

Un exemple de Koan

>> Quel est le son d’une seule main qui applaudit ? 


Il existe des Koan pour la programmation. Ce sont des mini problèmes pour s'initier à une technologie.

## RubyKoan
Le plus connu et le premier est [RubyKoan](http://rubykoans.com/).

Regardons ensemble la première utilisation.

``` bash
cd koans
/home/marc/.rvm/rubies/ruby-2.2.0/bin/ruby path_to_enlightenment.rb
AboutAsserts#test_assert_truth has damaged your karma.

The Master says:
  You have not yet reached enlightenment.

The answers you seek...
  Failed assertion.

Please meditate on the following code:
  /home/marc/prog/ruby_koans/koans/about_asserts.rb:10:in `test_assert_truth'

mountains are merely mountains
your path thus far [X_________________________________________________] 0/282

```

J'ouvre le fichier `about_asserts.rb`.

``` ruby
#!/usr/bin/env ruby
# -*- ruby -*-

require File.expand_path(File.dirname(__FILE__) + '/neo')

class AboutAsserts < Neo::Koan

  # We shall contemplate truth by testing reality, via asserts.
  def test_assert_truth
    assert false                # This should be true
  end


```

Il suffit de changer le `false` en `true`


Relancons, On avance petit à petit et toutes les notions du ruby sont expliquées.


``` bash
cd koans
/home/marc/.rvm/rubies/ruby-2.2.0/bin/ruby path_to_enlightenment.rb
AboutAsserts#test_assert_truth has expanded your awareness.
AboutAsserts#test_assert_with_message has damaged your karma.

The Master says:
  You have not yet reached enlightenment.
  You are progressing. Excellent. 1 completed.

The answers you seek...
  This should be true -- Please fix this

Please meditate on the following code:
  /home/marc/prog/ruby_koans/koans/about_asserts.rb:16:in `test_assert_with_message'

learn the rules so you know how to break them properly
your path thus far [.X________________________________________________] 1/282
```


C'est un moyen facile et rapide pour apprendre un langage. Ceci dit j'ai personnellement fini les rubykoans. Mais 1 mois plus tard, j'étais incapable d'aligner une ligne de code en ruby. Faire passer les tests n'est pas très compliqué quelque soit le langage.

Il y a aussi les Katas de programmation, Mais c'est plus un cahier des charges avec des tests d'acceptations (On parle de BDD) alors que les koan sont plutôt dans la notion d'unitaire.


##Quelques Koan.


Bien sur les [RubyKoans](http://rubykoans.com/) il existe une version [en ligne](http://koans.herokuapp.com/en/about_asserts).

Les [python-koans](https://github.com/gregmalcolm/python_koans) sont vraiment chouettes Il y a python 2.7 et python 3. Si les premiers problèmes sont faciles. Les derniers sont plutôt durs. 

En javascript on peux citer

 * [javascript-koans](https://github.com/mrdavidlaing/javascript-koans)
 * [backbone-koans](https://github.com/larrymyers/backbone-koans)
 * [angular-koans](https://github.com/bjpbakker/angular-koans)


J'ai été un peu déçus de ne pas trouver des Koans en php. Cela reste très basique. 
je citerai:

* [PHPUnit-koans](https://github.com/TorontoPHPSoftwareCraftsmanship/PHPUnit-Koans)
* et celui-ci par votre serviteur (mais c'était il y a bien longtemps) [mcamuzat/PHPUnitRegexKoan](https://github.com/mcamuzat/PHPUnitRegexKoan)

## Conclusion
Je vous avais parler des [awesome-list](/blog/2015/04/29/awesome-et-liste-de-liens/) et bien il existe une liste de [awesome-koans](https://github.com/ahmdrefat/awesome-koans).



