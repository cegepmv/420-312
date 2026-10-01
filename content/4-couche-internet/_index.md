+++
pre = "<b>4. </b>"
title = "Couche Internet"
weight = "400"
draft = false
+++
-------------

Dans le modèle **TCP/IP**, la **couche Internet** est responsable de l'acheminement des paquets entre différents réseaux.

Elle permet notamment :

- d'identifier les interfaces réseau à l'aide d'**adresses IP**;
- de déterminer si une destination se trouve sur le réseau local ou sur un réseau distant;
- d'acheminer les paquets à travers plusieurs routeurs;
- d'échanger des messages de contrôle, d'erreur et de diagnostic.

Le principal protocole de cette couche est **IP (*Internet Protocol*)**.

<!-- Dans ce chapitre, nous étudierons principalement :

- le fonctionnement du protocole **IP**;
- l'adressage **IPv4**;
- les **masques et préfixes CIDR**;
- le calcul de **sous-réseaux**;
- le **VLSM**;
- le fonctionnement du **routage IP**;
- les **tables de routage**;
- le protocole **ICMP**;
- les limitations d'IPv4 et le **NAT**;
- la configuration du réseau sous **Linux**. -->

## Le protocole IP

### Rôle

Le protocole **IP (*Internet Protocol*)** permet d'acheminer des paquets entre différents réseaux.

Contrairement à Ethernet, qui assure principalement la communication sur une liaison ou un réseau local, IP permet d'interconnecter plusieurs réseaux.

![Exemple d'une topologie avec plusieurs réseaux](/images/04-reseaux-routeur.png)

Chaque paquet IP contient notamment :

- une **adresse IP source**;
- une **adresse IP destination**;
- des informations nécessaires à son traitement et à son acheminement.

IP est un protocole **sans connexion** et **best effort (acheminement au mieux)**.

Cela signifie qu'IP :

- ne garantit pas que le paquet arrivera à destination;
- ne garantit pas l'ordre d'arrivée des paquets;
- ne garantit pas l'absence de duplication;
- ne retransmet pas automatiquement les paquets perdus.

Les protocoles des couches supérieures, comme **TCP**, peuvent fournir certaines garanties supplémentaires.

### Paquet IP

Les données provenant de la couche transport sont encapsulées dans un **paquet IP**.

![Entête IP](/04-01-entete-ip.png?width=40rem)

L'en-tête IP contient notamment les adresses IP source et destination.

|Champ| Fonction|
|-----|---------|
|**Version**|Version du paquet IP (pour IPv4: `0100`)|
|**Services différenciés**| Définit la priorité du paquet|. 
|**Time-to-live (TTL)**|  Indique la durée de vie d’un paquet. L’expéditeur définit la valeur de durée de vie initiale et celle-ci diminue à chaque fois que le paquet est traité par un routeur. Si sa valeur arrive à zéro, le routeur rejette le paquet.|
|**Protocole**| Indique le type de données transportées par les couches supérieures. Les valeurs habituelles sont notamment ICMP (1), TCP (6) et UDP (17).|
|**Adresse IP source**| Adresse IP source du paquet|
|**Adresse IP de destination**| Adresse IP de destination du paquet|
|**Longueur d’en-tête**| Indique le la taille de l'entête du paquet (en nombre de mots de 32 bits)|
**Longueur de paquet**| Indique la taille globale du paquet (y compris l’en-tête et les données) en octets|





<!-- 
Suite TTL : et envoie un message de dépassement de délai ICMP à l’adresse IP source. La commande traceroute utilise ce champ pour identifier les routeurs utilisés entre la source et la destination
 -->