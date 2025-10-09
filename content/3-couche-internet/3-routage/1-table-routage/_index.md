+++
pre = "<b>1. </b>"
title = "Tables de routage"
weight = "310"
draft = false
+++
-------------------

Lorsqu'un hôte envoie un paquet à un autre hôte, il utilise sa table de routage pour déterminer où envoyer le paquet. Si l'hôte de destination se trouve sur un réseau distant, le paquet est transmis à l'adresse d'un périphérique passerelle (généralement un routeur).

Toute machine (hôte linux, windows, routeur ou autre) connectée au réseau possède une table de routage.

Pour afficher la table de routage d'une machine :

+ Sous Windows
```bash
route print
# ou
netstat -r
```
+ Sous linux
```bash
ip route
# ou
netstat -r
```

Plusieurs commandes sont utilisées pour consulter la table de routage sous Linux :
### `ip route show` ou `ip route list`
```bash
$ ip route show (ou ip route list)

default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1
```

+ **Ligne 1 :** indique que la route par défaut pour n’importe quel paquet (c’est-à-dire la route empruntée par un paquet lorsqu’aucune autre route n’est appliquée) passe par le périphérique réseau `enp0s3` via la passerelle par défaut (le routeur) dont l’adresse IP est `10.0.2.2`.

    + `default` (`0.0.0.0/0`) : correspond à n’importe quel réseau
    + `10.0.2.0/24` correspond au réseau de destination (à atteindre),
    + `via 10.0.2.2` : adresse IP du tronçon suivant via lequel on peut joindre le réseau de destination
    + `dev enp0s3` : interface réseau à utiliser pour acheminer le paquet IP,
    + `proto` (`kernel`, `dhcp` ou `static`) : signifie que cette entrée dans la table de routage a été créée par le noyau lors de la configuration automatique, par dhcp ou configurée manuellement
    + `scope link src 10.0.2.15` : « lien de portée » signifie que les adresses IP de destination au sein de 10.0.2.0/24 ne sont valides que sur l’interface réseau `enp0s3`,
    + `metric 100` : signifie la mesure locale pour atteindre la destination en empruntant ce chemin.


### `netstat`
La commande `netstat -rn` nous permet aussi d'accéder à la table de routage :
```bash
$ netstat -rn

Table de routage IP du noyau
Destination     Passerelle      Genmask         Indic   MSS Fenêtre irtt Iface
0.0.0.0         192.168.130.2   0.0.0.0         UG        0 0          0 ens160
192.168.130.0   0.0.0.0         255.255.255.0   U         0 0          0 ens160
```

### Règles et tables de routage

Linux gère plusieurs tables de routage et dispose d’un système de règles pour choisir la table à utiliser. Ces règles peuvent être configurées avec la commande `ip rule`.

Par défaut, il en existe trois :
```bash
$ ip rule show

0: from all lookup local
32766: from all lookup main
32767: from all lookup default
```
Linux va d’abord utiliser la table `local` et en cas d’échec se rabattre sur `main` puis `default`.

#### Table `local`
La table `local` contient les routes pour la livraison locale :

```bash
$ ip route show table local

local 127.0.0.0/8 dev lo proto kernel scope host src 127.0.0.1 
local 127.0.0.1 dev lo proto kernel scope host src 127.0.0.1 
broadcast 127.255.255.255 dev lo proto kernel scope link src 127.0.0.1 
local 192.168.121.221 dev ens160 proto kernel scope host src 192.168.121.221 
broadcast 192.168.121.255 dev ens160 proto kernel scope link src 192.168.121.221 
```

Cette table est gérée automatiquement par le noyau quand des adresses IP sont configurées.

#### Table `main`
La table `main` contient habituellement toutes les autres routes :

```bash
$ ip route show table main

default via 192.168.121.2 dev ens160 proto dhcp src 192.168.121.221 metric 100 
192.168.121.0/24 dev ens160 proto kernel scope link src 192.168.121.221 metric 100 
c
```

La route `default` a été mise en place par un démon DHCP. La route connectée (`scope link`) a été ajoutée automatiquement par le noyau (`proto kernel`) lors de la configuration de l’adresse IP sur l’interface `ens160`.

### Table `default`
La table `default` est vide et est rarement utilisée. Elle reste là depuis Linux 2.1.68 en hommage à la première tentative de routage avancé dans Linux 2.1.15.

```bash
$ ip route show table default

Error: ipv4: FIB table does not exist.
Dump terminated

```