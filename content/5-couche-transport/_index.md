+++
pre = '<b>5. </b>'
title = "Couche transport"
slug = '5-couche-transport'
weight = "500"
draft = false
+++
-------------------
<!--
Adresse à remplacer dans le laboratoire 1 : SERVEUR_WEB (nom du serveur HTTP du département)
-->

Dans le chapitre précédent, nous avons vu que la **couche Internet** permet d'acheminer un paquet jusqu'à l'hôte de destination grâce à l'adresse IP.

Mais un hôte exécute généralement plusieurs applications réseau en même temps : un navigateur, un client de courriel, un service SSH, etc.

Lorsqu'un paquet arrive, comment l'hôte sait-il à **quelle application** les données sont destinées ?

C'est le rôle de la **couche transport**.

Elle permet notamment :

- d'identifier les applications à l'aide de **numéros de port**;
- de faire circuler plusieurs conversations en même temps sur une même interface réseau;
- de découper les données des applications en blocs adaptés au réseau;
- de fournir, selon le protocole utilisé, une communication **fiable** ou une communication **rapide et légère**.

Les deux principaux protocoles de cette couche sont **TCP (*Transmission Control Protocol*)** et **UDP (*User Datagram Protocol*)**.

## Rôle de la couche transport

La couche transport assure la communication **entre deux applications**, alors que la couche Internet assure la communication **entre deux hôtes**.

| Couche | Communication | Identifiant |
|--------|---------------|-------------|
| **Internet** | d'un hôte à un autre hôte | Adresse IP |
| **Transport** | d'une application à une autre application | Numéro de port |

### Suivi des conversations

Chaque échange de données entre une application source et une application de destination est appelé une **conversation**.

Un même hôte peut mener plusieurs conversations simultanément, avec un ou plusieurs hôtes distants.

Exemple :
![Schéma représentant plusieurs conversations](/images/05/05-conversation.png)

La couche transport doit garder ces conversations séparées : les données de l'onglet 1 ne doivent pas être remises au client de courriel.

### Segmentation et reconstitution

Les données d'une application (un fichier, une page web) peuvent être beaucoup trop volumineuses pour être transmises en un seul bloc.

- **À l'envoi**, la couche transport découpe les données en blocs et ajoute un **en-tête** à chacun.
- **À la réception**, elle utilise les informations de l'en-tête pour reconstituer les données et les remettre à la bonne application.

L'unité de données de la couche transport s'appelle :

- un **segment** avec TCP;
- un **datagramme** avec UDP.

![Encapsulation d'un segment](/images/05/05-encapsulation.png)
{{%center%}}
*Encapsulation : les données de l'application sont placées dans un segment, lui-même placé dans un paquet IP, puis dans une trame.*
{{%/center%}}

{{%notice style="note" title="Rappel"%}}
Au chapitre 4, nous avons vu que le champ **Protocole** de l'en-tête IP indique le contenu du paquet : `6` pour TCP et `17` pour UDP. C'est grâce à ce champ que l'hôte de destination sait à quel protocole de transport remettre les données.
{{%/notice%}}

### Multiplexage

Grâce à la segmentation, les segments de plusieurs applications peuvent être **entremêlés** sur la même interface réseau. C'est le **multiplexage**.

![Multiplexage](/images/05/05-multiplexage.png)

À la réception, l'opération inverse (**démultiplexage**) consiste à lire le **port de destination** de chaque segment pour le remettre à la bonne application.

Sans ce mécanisme, une seule application pourrait utiliser le réseau à la fois. Par exemple, un téléchargement monopoliserait le lien et empêcherait de consulter ses courriels en même temps.

{{%notice style="tip" title="À retenir"%}}
L'adresse IP permet d'atteindre le bon **hôte**. Le numéro de port permet d'atteindre la bonne **application** sur cet hôte.
{{%/notice%}}





