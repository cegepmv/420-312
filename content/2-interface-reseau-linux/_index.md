+++
pre = '<b>2. </b>'
title = 'Interface réseau Linux'
draft = false
weight = "200"
+++
---------------------

Nous avons vu que les communications réseau utilisent notamment deux types d'adresses :

+ **Adresse MAC (couche 2) :** utilisée sur le réseau local par les switches pour acheminer les trames.
+ **Adresse IP (couche 3) :** utilisée par les hôtes et les routeurs pour acheminer les paquets entre les réseaux.
+ **ARP :** permet de trouver l'adresse MAC associée à une adresse IPv4 sur le réseau local.

Nous allons maintenant voir comment retrouver et configurer ces informations sur une machine Linux.

## Interface réseau

Une **interface réseau** est un point de connexion qui permet à une machine de communiquer avec un réseau.

Une interface peut être :

+ **physique :** une carte réseau Ethernet, une carte Wi-Fi, etc. ;
+ **virtuelle :** une interface créée par le système d'exploitation ou par une technologie de virtualisation ou de réseau.

Une interface réseau possède généralement :

+ une **adresse MAC** ;
+ une ou plusieurs **adresses IP** lorsqu'elle est configurée ;
+ un état : **UP** ou **DOWN** ;
éventuellement une configuration de routage associée.

Par exemple, une machine virtuelle Linux peut posséder les interfaces suivantes :
```bash
ens160
ens224
lo
```
`ens160` et `ens224` peuvent représenter des cartes réseau Ethernet virtuelles, tandis que `lo` est l'interface **loopback**.

### L'interface loopback

L'interface `lo` est une interface virtuelle particulière qui permet à une machine de communiquer avec elle-même.

Son adresse IPv4 est généralement :
```bash
127.0.0.1
```
On peut donc utiliser :
```bash
ping 127.0.0.1
```
ou :
```bash
ping localhost
```
Cette communication ne quitte jamais la machine et ne passe donc ni par un switch ni par un routeur.

### Lire la configuration d'une interface

La commande moderne utilisée pour consulter les interfaces réseau sous Linux est :
```bash
ip address
```
ou, plus simplement :
```bash
ip a
```
**Exemple :**
```bash
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP>
    link/ether 00:0c:29:12:34:56
    inet 192.168.230.10/24
```

On peut y retrouver plusieurs informations importantes.

+ **Nom de l'interface :** `ens160`
+ **Adresse MAC :** la ligne `link/ether 00:0c:29:12:34:56` correspond à l'adresse utilisée à la couche 2.
+ **Adresse IP :** `inet 192.168.230.10/24` indique que l'interface possède l'adresse IPv4, soit `192.168.230.10` avec un préfixe `/24`.
{{%notice style="info" title=""%}}
Le `/24` correspond au masque :`255.255.255.0`. Cette information permet notamment au système de déterminer quelles adresses IP appartiennent au même réseau local (nous verrons cela plus en détail dans un chapitre ultérieur).
{{%/notice%}}
### État d'une interface

Une interface peut être **activée** ou **désactivée**.

Pour afficher uniquement les interfaces et leur état :
```bash
ip link
```
Exemple :
```bash
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP>
```
La présence de **UP** indique que l'interface est activée.

On peut également utiliser la commande suivante pour examiner une interface particulière :
```bash
ip link show ens160
```

Pour activer une interface :
```bash
ip link set ens160 up
```
Pour la désactiver :
```bash
ip link set ens160 down
```
{{%notice style="warning" title="Attention"%}}
Ces commandes modifient l'état courant de l'interface, mais ne constituent pas nécessairement une configuration permanente.
{{%/notice%}}

## Configuration réseau

Une machine Linux doit connaître différentes informations pour communiquer correctement :

+ Adresse IP → 192.168.230.10/24   
+ Passerelle → 192.168.230.2       
+ Serveur DNS → 8.8.8.8             

Ces informations ont des rôles différents.

+ **Adresse IP :** Identifie la machine sur le réseau IP.
+ **Préfixe / masque :** Permet de déterminer quelle partie de l'adresse représente le réseau et quelle partie représente l'hôte.
+ **Passerelle par défaut :** La passerelle est la "porte de sortie" du réseau local (généralement l'adresse du routeur), utilisée lorsqu'une destination n'est pas située sur le réseau local.
+ **Serveur DNS :** Un serveur DNS permet notamment de transformer un nom de domaine en adresse IP. Par exemple : `www.google.com` -> `142.250.x.x`

### Afficher les routes

La configuration IP ne suffit pas : la machine doit également savoir **où envoyer les paquets**.

Pour afficher la table de routage :
```bash
ip route
```
Exemple :
```bash
default via 192.168.230.2 dev ens160
192.168.230.0/24 dev ens160 proto kernel scope link src 192.168.230.10
```
La ligne :
```bash
default via 192.168.230.2 dev ens160
```
indique que la passerelle par défaut est `192.168.230.2`

La ligne :
```bash
192.168.230.0/24 dev ens160
```
indique que le réseau `192.168.230.0/24` est directement accessible par l'interface `ens160`.

{{%notice style="tip" title="À retenir"%}}
L'adresse IP indique qui je suis sur le réseau, tandis que la table de routage indique où envoyer les paquets.
{{%/notice%}}

## ARP sous Linux

Dans le chapitre précédent, nous avons vu qu'ARP permet d'associer une adresse IPv4 à une adresse MAC.

Sous Linux, on peut consulter le cache ARP avec :
```bash
ip neigh
```
Exemple :
```bash
192.168.230.2 dev ens160 lladdr 00:50:56:AA:BB:CC REACHABLE
```
On peut y lire :

`192.168.230.2` -> `00:50:56:AA:BB:CC`

Linux sait donc que l'adresse IP `192.168.230.2` correspond à cette adresse MAC.