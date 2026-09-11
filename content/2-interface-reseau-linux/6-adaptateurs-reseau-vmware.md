+++
pre = '<b>5. </b>'
title = 'Adaptateurs réseau (VMWare)'
draft = false
weight = "260"
+++
----------------

Lorsqu’on crée une machine virtuelle dans **VMware**, une des composantes du “PC” qu’on virtualise est la **carte réseau** (*Network Adapter*). **VMware** propose plusieurs modes pour connecter cette carte réseau au réseau de la machine hôte, chacun ayant un comportement différent.

Les quatre modes suivants sont particulièrement utiles dans nos laboratoires :

+ **NAT (Network Address Translation)**
+ **Bridged**
+ **Host-Only**
+ **LAN Segment**

### Mode NAT

Dans ce mode, la VM passe par l’ordinateur hôte pour accéder à Internet.

+ La VM reçoit une adresse IP sur un réseau virtuel géré par VMware.
+ VMware fournit généralement un **service DHCP** pour ce réseau.
+ La VM peut accéder à Internet.
+ La VM n'est généralement pas directement accessible depuis les autres machines du réseau local.
+ L'hôte peut communiquer avec la VM.

{{%notice style="tip" title="Analogie"%}}
À imaginer comme : **La VM est cachée derrière l’ordinateur hôte.**
{{%/notice%}}

Le schéma conceptuel est :
![Schéma conceptuel carte réseau VMWare en mode NAT](../images/VMWare-NAT.png)

Le **NAT** est particulièrement pratique lorsqu'on veut simplement **donner un accès Internet à une VM** sans la connecter directement au réseau physique.

### Mode Bridged

En mode **Bridged**, la carte réseau virtuelle est connectée directement au réseau physique utilisé par l'ordinateur hôte.

La VM se comporte alors comme une machine physique supplémentaire sur le réseau.

+ La VM reçoit généralement une adresse IP du même réseau que l'hôte.
+ Elle peut communiquer avec les autres machines du réseau.
+ Elle peut accéder à Internet si le réseau le permet.
+ Les autres machines du réseau peuvent communiquer directement avec la VM.
+ La VM possède sa propre adresse MAC virtuelle.

{{%notice style="tip" title="Analogie"%}}
À imaginer comme : **"La VM est un vrai PC branché sur le même réseau que vous."**
{{%/notice%}}

![Schéma conceptuel carte réseau VMWare en mode Bridged](../images/VMWare-Bridge.png)

Le mode **Bridged** est notamment utile lorsqu'une VM doit être **accessible depuis d'autres machines du réseau**.

### Mode Host-Only

En mode **Host-Only**, VMware crée un réseau virtuel privé entre l'ordinateur hôte et les machines virtuelles connectées à ce réseau.

+ La VM peut communiquer avec l'hôte.
+ Plusieurs VMs connectées au même réseau Host-Only peuvent communiquer entre elles.
+ Le réseau est isolé du réseau physique.
+ La VM n'a normalement pas accès à Internet par cette interface.
+ L'hôte peut communiquer directement avec les VMs.

{{%notice style="tip" title="Analogie"%}}
À imaginer comme : **"L’hôte et les VMs sont dans une petite pièce fermée : ils peuvent se parler, mais le réseau physique n’y entre pas."**
{{%/notice%}}

![Schéma conceptuel carte réseau VMWare en mode Host-Only](../images/VMWare-HostOnly.png)

{{%notice style="tip" title=""%}}
Le mode **Host-Only** est très pratique pour créer un **laboratoire privé** tout en permettant à l'ordinateur hôte d'accéder aux machines virtuelles.
{{%/notice%}}

### Mode LAN Segment

Le mode **LAN Segment** permet de créer un **réseau virtuel complètement isolé** auquel plusieurs machines virtuelles peuvent être connectées.

Contrairement au mode *Host-Only*, le réseau **n'est pas directement connecté à l'ordinateur hôte**.

Une VM connectée à un *LAN Segment* peut donc communiquer avec les autres VMs connectées au **même LAN Segment**, mais elle ne peut pas communiquer directement avec :
+ l'ordinateur hôte ;
+ Internet ;
+ le réseau physique.


![Schéma conceptuel carte réseau VMWare en mode LAN Segment](../images/VMWare-LANSEGMENT.png)


{{%notice style="info" title="Remarque"%}}
Il est toutefois possible de connecter une VM à plusieurs réseaux afin d'en faire un routeur.

Par exemple :
![Schéma conceptuel VM en tant que routeur](../images/VMWARE-VM-Routeur.png)


Dans cet exemple, le routeur possède deux interfaces réseau :

+ `ens160` → NAT
+ `ens224` → LAN Segment

Il peut alors servir de passerelle entre le LAN Segment et un autre réseau.

Nous verrons cela plus en détail dans le chapitre portant sur le routage.
{{%/notice%}}


{{%notice style="info" title="LAN Segment ou Host-Only ?"%}}

Ces deux modes permettent de créer des réseaux virtuels isolés, mais ils ont une différence importante.

**Host-Only :** L'ordinateur hôte peut participer au réseau.

**LAN Segment :** Le réseau est entièrement constitué de machines virtuelles. L'hôte n'en fait pas partie.

Le **LAN Segment** est donc particulièrement intéressant pour construire des laboratoires où l'on souhaite contrôler entièrement la topologie réseau.

{{%/notice%}}

### Récapitulatif
|Mode|	Accès Internet|	Hôte↔VM|	VM↔VM|	VM visible sur réseau physique?|
|----|-----------|------|-----|-------|
|**NAT**	|Oui	|Oui	|Oui*|	|Non|
|**Bridged**	|Oui**	|Oui	|Oui	|Oui|
|**Host-Only**	|Non***	|Oui	|Oui	|Non|
|**LAN Segment**	|Non***	|Non	|Oui	|Non|

/* Les VMs doivent utiliser le même réseau NAT.

/** Si le réseau physique fournit un accès Internet.

/*** Une autre interface réseau peut toutefois fournir un accès Internet.
<!-- 
{{%notice style="tip" title="À retenir"%}}

Le choix du mode d'adaptateur détermine à **quel réseau une interface réseau virtuelle est connectée**.

Une même VM peut posséder plusieurs cartes réseau et donc être connectée simultanément à plusieurs réseaux.

Par exemple :

                    VM Routeur
               +------------------+
               |                  |
        ens160 |                  | ens224
               |                  |
             NAT            LAN Segment
               |                  |
            Internet       +------+------+
                           |             |
                         VM 1          VM 2

Cette possibilité sera utilisée dans les laboratoires pour construire des réseaux virtuels.
{{%/notice%}} -->