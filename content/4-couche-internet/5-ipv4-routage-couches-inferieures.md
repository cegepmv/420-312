+++
pre = '<b>5. </b>'
title = "IPv4, routage et couches inférieures"
weight = "450"
draft = false
+++
---------------

Lorsqu'une application communique avec un serveur distant, les différentes couches travaillent ensemble.

Exemple :


![Exemple d'encapsulation d'un paquet IP pour un serveur distant](/images/04-encapsulation-paquet-serveur-distant.png)
{{%center%}}
*Exemple d'encapsulation d'un paquet IP à destination d'un serveur distant (`8.8.8.8`)*
{{%/center%}}


Le paquet IP peut traverser plusieurs réseaux et plusieurs routeurs.

Les informations de couche 2 sont généralement **recréées à chaque liaison**.

Par exemple :

![Schéma illustrant le changement de trame à chaque liaison, contrairement aux adresses IP source et destination qui ne changent pas](/images/04-changement-trame-liaison.png)
{{%center%}}
*Schéma illustrant le changement de trame à chaque liaison, avec les adresses IP source et destination qui ne changent pas*
{{%/center%}}


Le paquet IP, lui, continue son chemin à travers les différents routeurs.

{{%notice style="tip" title=" "%}}
Les chapitres précédents ont détaillé Ethernet et ARP. Ici, l'idée essentielle est de comprendre que **la couche Internet fournit l'adressage logique et l'acheminement**, tandis que **la couche de liaison assure la transmission sur chaque liaison**.
{{%/notice%}}