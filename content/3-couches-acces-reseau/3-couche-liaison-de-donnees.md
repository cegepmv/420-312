+++
pre = '<b>3. </b>'
title = "Couche liaison de données"
draft = false
weight = "330"
+++

----------------------
## Rôle

La **couche liaison de données (L2)** permet la communication entre des équipements directement connectés au même réseau local.

Elle assure notamment :

- l'**encapsulation des paquets IP dans des trames**;
- l'**adressage physique** à l'aide des adresses MAC;
- le **contrôle de l'accès au support**;
- la **détection de certaines erreurs de transmission**.

L'unité de données de la couche liaison de données est appelée une **trame**.


## Ethernet

![Ethernet: protocole des couches 1 et 2](../images/02-14.png?width=30rem)

**Ethernet** est la technologie dominante pour les réseaux locaux filaires.

La norme **IEEE 802.3** définit notamment les caractéristiques des réseaux Ethernet aux couches physique et liaison de données.

Ethernet utilise deux sous-couches au niveau de la liaison de données :

- **LLC** (*Logical Link Control*);
- **MAC** (*Media Access Control*).




### Sous-couche LLC

La sous-couche **LLC** fait le lien entre la couche liaison de données et les couches supérieures.

Elle permet notamment d'identifier et de gérer les protocoles de couche supérieure qui utilisent la liaison.

Dans les réseaux Ethernet modernes, une grande partie de cette logique est prise en charge par les logiciels et les pilotes réseau.

### Sous-couche MAC

La sous-couche **MAC** est responsable des fonctions directement liées à l'accès au support Ethernet.

Elle intervient notamment dans :

- la construction et la lecture des trames;
- l'utilisation des adresses MAC;
- l'accès au support de transmission.

La sous-couche MAC est étroitement liée à la **carte réseau** et à son fonctionnement matériel.