+++
pre = '<b>5. </b>'
title = "IPv4, routage et couches inférieures"
weight = "450"
draft = true
+++

# IPv4, routage et couches inférieures

Lorsqu'une application communique avec un serveur distant, les différentes couches travaillent ensemble.

Exemple :

```text
Application
    │
    │ données
    ▼
Transport
    │
    │ segment TCP/UDP
    ▼
Internet
    │
    │ paquet IP
    │ source = 192.168.1.10
    │ destination = 8.8.8.8
    ▼
Liaison de données
    │
    │ trame Ethernet
    │ MAC destination = passerelle
    ▼
Physique
    │
    │ bits
    ▼
Support réseau
```

Le paquet IP peut traverser plusieurs réseaux et plusieurs routeurs.

Les informations de couche 2 sont généralement **recréées à chaque liaison**.

Par exemple :

```text
Hôte A             Routeur              Routeur             Serveur
   │                   │                   │                   │
   │ Trame Ethernet    │                   │                   │
   ├──────────────────►│                   │                   │
   │                   │ Nouvelle trame    │                   │
   │                   ├──────────────────►│                   │
   │                   │                   │ Nouvelle trame    │
   │                   │                   ├──────────────────►│
```

Le paquet IP, lui, continue son chemin à travers les différents routeurs.

{{%notice style="tip" title=" "%}}
Les chapitres précédents ont détaillé Ethernet et ARP. Ici, l'idée essentielle est de comprendre que la couche Internet fournit l'adressage logique et l'acheminement, tandis que la couche de liaison assure la transmission sur chaque liaison.
{{%/notice%}}