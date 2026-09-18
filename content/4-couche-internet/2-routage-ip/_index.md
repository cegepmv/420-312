+++
pre = '<b>2. </b>'
title = "Routage IP"
weight = "420"
draft=true
+++

## Réseau local ou réseau distant ?

Lorsqu'un hôte veut communiquer avec une adresse IP, il doit déterminer si la destination se trouve :

- sur le **même réseau**;
- sur un **réseau distant**.

Cette décision est prise à partir de l'adresse IP et du préfixe.

### Destination locale

Supposons :

```text
Hôte A : 192.168.1.10/24
Hôte B : 192.168.1.20/24
```

Les deux hôtes appartiennent au réseau :

```text
192.168.1.0/24
```

A peut donc communiquer directement avec B.

Dans un réseau Ethernet, la trame est envoyée vers la MAC de B.

> Le fonctionnement détaillé d'ARP a déjà été présenté dans le chapitre précédent. ARP permet de déterminer l'adresse MAC correspondant à une adresse IPv4 située sur le réseau local.

### Destination distante

Supposons :

```text
Hôte A :     192.168.1.10/24
Passerelle : 192.168.1.1
Serveur :    8.8.8.8
```

`8.8.8.8` n'appartient pas à :

```text
192.168.1.0/24
```

L'hôte transmet donc le paquet à sa **passerelle par défaut**.

```text
Hôte A
192.168.1.10
     │
     │ trame Ethernet
     ▼
Passerelle
192.168.1.1
     │
     ▼
   Internet
     │
     ▼
Serveur
8.8.8.8
```

À cette étape, la destination IP du paquet reste `8.8.8.8`, tandis que l'adresse MAC de destination de la trame Ethernet est celle de la passerelle.


## Routage IP

Le **routage** consiste à déterminer par quel chemin un paquet doit être acheminé pour atteindre sa destination.

Un **routeur** possède généralement plusieurs interfaces réseau, chacune étant connectée à un réseau différent.

```text
              Réseau A
          192.168.1.0/24
                │
                │
           ┌────┴────┐
           │ Routeur │
           └────┬────┘
                │
          Réseau B
          10.0.0.0/24
```

Lorsqu'un routeur reçoit un paquet IP, il consulte sa **table de routage** afin de déterminer où transmettre le paquet.

Le routeur transmet ensuite le paquet par l'interface correspondant à la route sélectionnée.


## Table de routage

Toute machine connectée à un réseau IP possède une **table de routage**.

Elle contient les informations nécessaires pour déterminer comment atteindre différentes destinations.

Sous Linux :

```bash
ip route
```

Sous Windows :

```powershell
route print
```

Exemple :

```text
default via 192.168.1.1 dev ens160
192.168.1.0/24 dev ens160 proto kernel scope link src 192.168.1.10
```

La première ligne :

```text
default via 192.168.1.1 dev ens160
```

signifie :

- `default` : route utilisée lorsqu'aucune route plus précise ne correspond;
- `via 192.168.1.1` : passerelle utilisée;
- `dev ens160` : interface utilisée.

La deuxième ligne :

```text
192.168.1.0/24 dev ens160
```

indique que le réseau `192.168.1.0/24` est directement accessible par `ens160`.


## Route par défaut

La **route par défaut** indique où envoyer les paquets lorsqu'aucune route plus précise n'existe.

Elle est représentée par :

```text
0.0.0.0/0
```

Exemple :

```text
default via 192.168.1.1 dev ens160
```

Cela signifie :

> Pour toute destination ne correspondant pas à une route plus spécifique, utiliser `192.168.1.1`.

<!-- ---

## Choix d'une route

Une table peut contenir plusieurs routes correspondant à une même destination.

Le système utilise généralement la route ayant le **préfixe le plus spécifique** (*longest prefix match*).

Exemple :

```text
10.0.0.0/8
10.1.0.0/16
10.1.2.0/24
default
```

Pour joindre :

```text
10.1.2.50
```

les trois premières routes correspondent.

Cependant :

```text
/24 > /16 > /8
```

La route :

```text
10.1.2.0/24
```

est donc la plus spécifique. -->
