+++
pre = '<b>4. </b>'
title = "Protocoles des couches basses"
draft = false
weight = "240"
+++

## Ethernet (IEEE 802.3)

- La technologie LAN la plus répandue.

- Fonctionne au niveau de la couche liaison de données et de la couche physique.

- Famille de technologies réseau définies par les normes IEEE 802.2 et IEEE802.3.

- Prend en charge des bandes passantes de données de 10, 100, 1000, 10000, 40000 et 100000Mbit/s (100Gbit/s).

***Normes Ethernet:***

- Définissent les protocoles de couche 2 et les technologies de couche 1.

- Deux sous-couches distinctes de la couche liaison de données pour fonctionner: LLC (Logical Link Control) et MAC (Media Access Control).

![Couches 1 et 2](../images/02-14.png?width=700px)

### Les sous-couches LLC et MAC

#### LLC

- Gère la communication entre la couche supérieure et la couche inférieure

- Prend les données du protocole réseau et ajoute des informations de contrôle pour faciliter la remise du paquet à sa destination

- Implémenté dans le logiciel.

#### MAC

- Constitue la sous-couche inférieure de la couche liaison de données

- Implémentée par le matériel, généralement dans la carte réseau de l'ordinateur

- Responsable du positionnement et de la récupération des trames sur les supports

- Deux rôles essentiels: **Encapsulation des données** et **contrôle d'accès au support**

<!-- ### La sous-couche MAC

![Couches 1 et 2](../images/02-15.png?width=700px) -->

#### Encapsulation des données

- Assemblage des trames avant la transmission et désassemblage des trames à leur réception

- La couche MAC ajoute un en-tête et un code de fin (trailer) à l'unité de données de protocole de la couche réseau (PDU)

Trois fonctions principales:

- Délimitation des trames: identification d'un groupe de bits formant une trame, synchronisation entre les noeuds de transmission et les noeuds de réception

- Adressage: chaque en-tête Ethernet ajouté à la trame contient l'adresse physique (MAC) qui permet de remettre celle-ci au noeud de destination

- Détection des erreurs: chaque trame Ethernet contient un code de fin (trailer) avec un contrôle par redondance cyclique (CRC, Cyclic Redundancy Check) du contenu des trames

<!-- #### Le contrôle d'accès au support

![Couches 1 et 2](../images/02-16.png?width=700px) -->

#### Adresse Mac : identité Ethernet

Une adresse MAC Ethernet de couche 2 est une valeur binaire de 48 bits constituée de 12 chiffres hexadécimaux

L'IEEE demande aux revendeurs de suivre deux règles simples:

- L'adresse doit utiliser dans ses 3 premiers octets l'identifiant unique (OUI) attribué au revendeur.

- Toutes les adresses MAC ayant le même identifiant OUI doivent utiliser une valeur unique dans les 3 derniers octets.

![Couches 1 et 2](../images/02-17.png?width=700px)

#### Traitement des trames

Adresses MAC attribuées aux stations de travail, aux serveurs, aux imprimantes, aux commutateurs et aux routeurs.

Exemples d'adresses MAC: `00-05-9A-3C-78-00`, `00:05:9A:3C:78:00` ou `0005.9A3C.7800`.

Message transféré à un réseau Ethernet: informations d'en-tête jointes au paquet, adresses MAC source et de destination.

Chaque carte réseau observe ces informations pour déterminer si l'adresse MAC de destination fournie dans la trame correspond à l'adresse MAC physique du périphérique stockée dans la mémoire vive (RAM).

Si elle ne correspond pas, le périphérique rejette la trame.

Si elle correspond, la carte réseau transmet la trame aux couches OSI, et la désencapsulation est effectuée.

#### Encapsulation Ethernet

Les versions précédentes d'Ethernet étaient relativement lentes, de l'ordre de 10Mbit/s.

Les réseaux Ethernet offrent désormais au moins 10Gbit/s par seconde.

Dans une trame Ethernet, un en-tête et un code de fin sont ajoutés autour de l'unité de données de protocole de couche 3 pour encapsuler le message envoyé.

Ethernet II est le format de trame Ethernet utilisé par les réseaux TCP/IP.

![Couches 1 et 2](../images/02-18.png?width=700px)

#### La taille de la trame Ethernet

Les normes EthernetII et IEEE802.3 définissent une taille de trame minimale de 64octets et maximale de 1518 octets.

Une trame faisant moins de 64octets est considérée comme «fragment de collision» ou «trame incomplète».

Si la trame transmise est plus petite ou plus grande que les limites minimale et maximale, le périphérique récepteur l'ignore.

Au niveau de la couche physique, les versions d'Ethernet varient dans leur manière de détecter et de placer les données sur le support.

##### Représentation des adresses MAC

![Couches 1 et 2](../images/02-20.png?width=600px)

##### Adresse MAC unicast (monodiffusion)

![Couches 1 et 2](../images/02-21.png?width=600px)

##### Adresse MAC broadcast (diffusion)

![Couches 1 et 2](../images/02-22.png?width=600px)

<!-- ##### Adresse MAC de multidiffusion

![Couches 1 et 2](../images/02-23.png?width=600px) -->

## Le protocole ARP

ARP : **A**ddress **R**esolution **P**rotocol

#### Rôle du protocole ARP

- Le noeud expéditeur a besoin d'un moyen de trouver l'adresse MAC de destination pour une liaison Ethernet donnée

Le protocole ARP assure deux fonctions de base:

- La résolution des adresses IPv4 en adresses MAC

- La tenue d'une table des mappages

![Couches 1 et 2](../images/02-24.png?width=600px)

Table ARP :

- Sert à trouver l'adresse de la couche liaison de données qui est mappée à l'adresse IPv4 de destination.

- Quand un noeud reçoit des trames en provenance du support, il enregistre les adresses MAC et IP source dans la table ARP sous forme de mappages.

Requête ARP :

- Diffusion de couche 2 vers tous les périphériques du LAN Ethernet.

- Le noeud qui correspond à l'adresse IP de la diffusion répond.

- Si aucun périphérique ne répond à la requête ARP, le paquet est abandonné du fait qu'il est impossible de créer une trame.

{{% notice info %}}
Des entrées de mappage statiques peuvent également être ajoutées dans la tableARP, ce qui est rare.
{{% /notice %}}

#### Fonctionnement du protocole ARP

![Couches 1 et 2](../images/02-25.png?width=600px)

![Couches 1 et 2](../images/02-26.png?width=600px)

![Couches 1 et 2](../images/02-27.png?width=600px)

![Couches 1 et 2](../images/02-28.png?width=600px)

![Couches 1 et 2](../images/02-29.png?width=600px)

![Couches 1 et 2](../images/02-30.png?width=600px)

#### Rôle de ARP dans les communications à distance

Si l'hôte IPv4 de destination se trouve sur le réseau local, la trame utilise l'adresse MAC de ce périphérique comme adresse MAC de destination.

Si l'hôte IPv4 de destination n'est pas sur le réseau local, l'émetteur utilise la méthode ARP pour déterminer une adresseMAC pour l'interface du routeur qui sert de passerelle.

Si la table ne contient pas d'entrée pour la passerelle, une requête ARP est utilisée pour récupérer l'adresseMAC associée à l'adresseIP de l'interface du routeur.