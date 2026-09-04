+++
pre = '<b>2. </b>'
title = "Matériel des couches basses"
draft = false
weight = "220"
+++

-------------------------

## Répéteur

Quand un signal électrique est transmis sur un support de communication, il a tendance à s'atténuer rapidement avec la distance. Cela vaut aussi bien pour les transmissions sans-fils, que pour les signaux guidés par un câble en cuivre ou une fibre optique. L'atténuation est surtout un problème sur les connexions sans-fils, dont la portée ne dépasse pas quelques mètres pour les technologies domestiques. Mais elle pose problème pour les câbles réseaux si la distance parcourue devient assez grande. Par exemple, les grands câbles téléphoniques qui parcourent les villes et les campagnes subissent une atténuation non-négligeable.

![Répéteur](../images/02-repeteur.jpg?width=15rem)

- Un répéteur permet d'éviter les problèmes liés à l'atténuation des signaux
- Matériel qui régénère le signal perçu en entrée: Il reçoit sur son entrée le signal transmis, et produit en sortie le même signal amplifié, similaire au signal non-atténué. 

## Concentrateur

![Concentrateur](../images/02-hub.jpg?width=30rem)

- Version multi-port du répéteur : quand il reçoit un flux de bits sur un port, il recopie celui-ci sur tous les autres ports. 
- Chaque donnée envoyée par un ordinateur est redistribuée à tous les autres. 
- Quand il est placé au centre d'un réseau en étoile, il permet de **simuler un réseau en bus**. 

## Commutateur

![Commutateur](../images/02-switch.png?width=30rem)

- Équipement de couche liaison
- Utilisé dans les réseaux locaux en étoile, au même titre que les concentrateurs. 
- La différence avec le concentrateur : il redirige les trames reçues vers l'ordinateur de destination uniquement, il ne diffuse pas la trame à tous les ordinateurs du réseau local comme le ferait un concentrateur. Lorsqu'il reçoit une trame, il la renvoie sur le port qui est associée à l'ordinateur de destination. 

## Carte réseau

![Carte réseau](../images/02-NIC.jpg?width=30rem)

- C'est le composant qui permet à un ordinateur de communiquer sur un réseau (local ou internet).
- Elle permet d'envoyer ou de recevoir des informations sur un câble réseau ou une connexion WIFI. 
- Elle communique avec le reste de l'ordinateur via le bus de la carte mère.