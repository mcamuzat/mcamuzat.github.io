---
layout: post
title: "Processing et Souvenir"
date: 2015-07-17 22:52:27 +0200
comments: true
categories: ["java", "arduino", "processing", "programmation", "javascript"] 
---

{% img center /images/voiture.png 499 300 'Vrooum!!' 'Screenshot du programme avec le micro' %}


Aujourd'hui, je ressors un vieux code que j'avais écris pour le CNAM. Le sujet était faire que l'image à l'écran bouge quand on parle dans un microphone. Mais pas d'animation à la Winamp (j'ai des vieilles références :-) ou le player de Windows. J'avais donc programmé une voiture sur un circuit, pour le réalisme j'avais connecté un Wii-chuck via un Arduino pour faire le volant. Imaginez une personne avec un Wiichuk et un micro en train de faire "Vrrrooum" et vous aurez un idée de ma soutenance (qui s'est bien passée d'ailleurs).

Les conditions étaient :

 * Utiliser un Micro
 * La langage imposé est Processing

<!--more-->

## Processing Késako

Processing est une variante de Java en plus simple. C'est un langage destiné à faire des oeuvres d'arts numériques. Il est souvent enseigné en école d'art. Il vient avec son propre éditeur. à noter que les [Arduinos](https://fr.wikipedia.org/wiki/Arduino) se programment aussi en processing.

{% img center /images/processing.png 499 533 'Processing' 'Screenshot de processing avec son interface' %}


Il suffit d'implémenter deux méthodes :

```
void setup() // sera appelé à l'initialisation
void draw() // sera appellé 30 à 50 fois par seconde
```

Un exemple très simple 
``` java
void setup() {
  size(640, 360); //j'initialise la taille 640*
  stroke(255); // le trait est blanc
  fill(255); // remplissage de blanc
  background(0); // couleur de fond blanche

}

void draw() {
  // si la souris est appuyer
  if (mousePressed) {
     // ellipse x, y ,hauteur, largeur
    ellipse(mouseX, mouseY, 5, 5);
  } 
}
```

Appuyez sur la touche play 

{% img center /images/processing_exemple.png 535 375 'Processing' 'le résultat' %}

Je viens de faire un petit painbrush. C'est assez facile d'obtenir un truc plutôt joli avec assez peu de lignes de code. Il y a des librairies toutes prêtes pour la video, webcam.

Il y a de nombreux livres donc le [Visualizing Data](http://www.amazon.com/Visualizing-Data-Explaining-Processing-Environment/dp/0596514557). Pour 2007, le livre est impressionnant. On parle de traitement de donnée et acquisitions des données bien avant la mode du Big data. 

# Installez Processing

Nous allons d'abord installer Java si vous n'avez pas. J'installe le paquet officiel
```
sudo apt-get purge openjdk*    // Supprime opendjdk
sudo apt-get install software-properties-common   //ajout de librairie
sudo add-apt-repository ppa:webupd8team/java // ajout du depot
sudo apt-get update                 // et on met à jour les videos
sudo apt-get install oracle-java7-installer // On installe
sudo apt-get install oracle-java7-set-default // et on mets par défaut
```

Puis nous allons installer Processing
On télécharge le binaire à l'[adresse officielle](https://processing.org/download/?processing)

Puis on décompresse
```
tar xf processing-*.tgz
sudo mkdir /usr/share/processing
sudo mv processing-* /usr/share/processing
```
Il n'y a plus qu'à lancer processing
```
sh /usr/share/processing/processing-2.2.1/processing
```
Vous devriez voir apparaitre l'interface

Faire marcher mon code:
 
 * Il faut utiliser la librairie Ess pour analyser le signal (marrant c'était déja vieux à l'époque)
 * Aller sur le site [suivant](http://www.tree-axis.com/Ess).
 * Décompressez et copier le répertoire ESS dans /usr/share/processing/processing-2.2.1/modes/java/libraries/

## Le code 

Il est disponible [ici](https://github.com/mcamuzat/processing-car) sous Github. J'ai commenté la partie avec le Numchuck+Arduino (car peux de gens ont le matériel). A l'époque impossible de faire marcher la connection USB/Wiichuck sous Linux, j'ai passé la soutenance sous un vieux PC sous windows XP. Tout est mis dans le même fichier car j'avais pas trouver comment découper un projet. C'est d'ailleurs ce que je reproche un peu à Processing, c'est un langage simple pour débuter, mais faire un MVC ou un programme très complexe. Il faut quasiment tout refaire à la main. 

## Quelques liens

 * le [site officiel](https://processing.org/)
 * Des [galeries interactives](http://www.openprocessing.org/) 
 * [ProcessingJs](http://processingjs.org/) (fait par le programmeur de JQuery Jon Resig)
 

