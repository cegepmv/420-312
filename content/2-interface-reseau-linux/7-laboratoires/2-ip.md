+++
title = '2- La commande ip'
draft = false
weight = "272"
+++
---------------------

Dans ce laboratoire, nous allons nous concentrer sur la gestion d'une interface réseau Linux.

## Objectifs

+ consulter une interface ;
+ modifier son état ;
+ ajouter et supprimer une adresse IP ;
+ observer les routes ;
+ observer ARP ;
+ effectuer des modifications temporaires.

{{%notice style="warning" title="Rappel"%}}

Les modifications effectuées directement avec la commande `ip` ne constituent généralement pas une configuration permanente.

Elles sont utilisées ici pour expérimenter et comprendre le fonctionnement du réseau.
{{%/notice%}}

## 1 — Identifier les interfaces
```bash
ip link
```
Puis :
```bash
ip address
```
Identifiez :

+ l'interface loopback ;
+ l'interface Ethernet ;
+ l'adresse MAC ;
+ l'adresse IP ;
+ l'état de l'interface.

## 2 — Activer et désactiver une interface

Désactivez votre interface Ethernet :
```bash
sudo ip link set <interface> down
```
Vérifiez :
```bash
ip link show <interface>
```
Réactivez-la :
```bash
sudo ip link set <interface> up
```
Vérifiez à nouveau.

### Question

Quelle différence faites-vous entre l'état UP et DOWN ?

## Partie 3 — Ajouter une adresse IP

Ajoutez temporairement :
```bash
sudo ip addr add 192.168.20.10/24 dev <interface>
```
Vérifiez :
```bash
ip a show <interface>
```
Ajoutez ensuite :
```bash
sudo ip addr add 192.168.20.11/24 dev <interface>
```

### Questions
+ Combien d'adresses IPv4 l'interface possède-t-elle maintenant ?
+ Est-il possible d'avoir plusieurs adresses IP sur une même interface ?

Supprimez ensuite l'adresse :
```bash
sudo ip addr del 192.168.20.11/24 dev <interface>
```

## 4 — Observer les routes
```bash
ip route
```
Identifiez :

+ les réseaux directement connectés ;
+ la route par défaut ;
+ l'interface utilisée.

## 5 — Observer ARP

Exécutez :
```bash
ip neigh
```
Puis effectuez un ping vers une autre machine du réseau :
```bash
ping -c 3 192.168.20.20
```
Observez de nouveau :
```bash
ip neigh
```
Expliquez l'apparition éventuelle d'une nouvelle entrée.
