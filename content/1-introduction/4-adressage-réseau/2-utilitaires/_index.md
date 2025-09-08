+++
pre = '<b>2. </b>'
title = 'Commandes utilitaires'
draft = false
weight = "142"
+++

## Ping
`ping` est une commande utilitaire d'administration réseau utilisée pour tester l'accessibilité d'un hôte sur un réseau.

`ping` mesure le temps aller-retour des messages envoyés depuis l'hôte d'origine vers un ordinateur de destination, qui sont renvoyés vers la source.

Pour faire un `ping` à une machine du réseau :
```bash
ping <adresse IP>
```

## SSH
SSH (*Secure Shell*) est un protocole qui permet, entre autres, de prendre en main une autre machine.

+ Il est possible de spécifier l’utilisateur avec lequel on se connecte (l’utilisateur doit exister sur la machine cible) :
```bash
$ ssh user@host
```
+ `ssh` est disponible sur toutes les distributions Linux et Unix.
+ `ssh` permet aussi d’exécuter une commande à distance :
```bash
$ ssh user@host commande
```

## ARP
ARP (*Address Resolution Protocol*) est un protocole qui permet de faire correspondre une adresse IP à une adresse MAC dans un réseau local (LAN). 

+ Pour connaitre le tableau de correspondance ARP d'une machine Linux : 
```bash
arp -a
```

+ Pour supprimer le cache ARP d'une machine :
```bash
arp -a -d
```

