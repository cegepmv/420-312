+++
pre = "<b>4. </b>"
title = "Couche Internet"
weight = "400"
draft = false
+++

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

![Exemple d'une topologie avec plusieurs réseaux](/04-reseaux-routeur.png)

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

+ **Version :** contient une valeur binaire de 4bits indiquant la version du paquetIP. Pour les paquetsIPv4, ce champ est toujours 0100.

+ **Services différenciés (aussi appelé champ de type de service) :** un champ de 8bits utilisé pour définir la priorité de chaque paquet. 

+ **Time-to-live (durée de vie, TTL) :**  contient une valeur binaire de 8bits utilisé pour limiter la durée de vie d’un paquet. Cette durée est indiquée en secondes mais est généralement appelée «nombre de sauts». L’expéditeur du paquet définit la valeur de durée de vie initiale et celle-ci diminue de un chaque fois que le paquet est traité par un routeur, ou effectue un saut. Si la valeur du champ TTL (durée de vie) arrive à zéro, le routeur rejette le paquet et envoie un message de dépassement de délai ICMP à l’adresseIP source. La commande tracerouteutilise ce champ pour identifier les routeurs utilisés entre la source et la destination.

+ **Protocole :** Cette valeur binaire de 8 bits indique le type de données utiles transportées par le paquet, ce qui permet à la couche réseau de transmettre les données au protocole de couche supérieure approprié. Les valeurs habituelles sont notamment ICMP (1), TCP (6) et UDP (17).

+ **Adresse IP source :** contient une valeur binaire de 32 bits qui représente l’adresse IP source du paquet.

+ **Adresse IP de destination :** contient une valeur binaire de 32 bits qui représente l’adresse IP de destination du paquet.

+ **Longueur d’en-tête Internet :** contient une valeur binaire de 4bits indiquant le nombre de mots de 32bits contenus dans l’en-tête. Cette valeur varie en fonction des champs d’options et de remplissage. La valeur minimale de ce champ est 5 (c.-à-d., 5x32=160bits=20octets) et la valeur maximale 15 (c.-à-d., 15x32=480bits= 60octets).

+ **Longueur de paquet :** Ce champ de 16 bits indique la taille globale du paquet, y compris l’en-tête et les données, en octets. Sa valeur minimale est de 20 octets (un en-tête de 20octets + 0octet de données) et sa valeur maximale est de 65535octets.