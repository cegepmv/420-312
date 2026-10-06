+++
slug= '1-intro-packet-tracer'
title = "1- Introduction à Packet Tracer"
weight = "461"
draft = false
+++
-------------

## Contexte
Dans les laboratoires précédents, nous avons étudié le fonctionnement des réseaux locaux et la configuration d'interfaces Linux.

Dans ce laboratoire, nous allons introduire la **couche Internet** et le **routage** à l'aide de *Cisco Packet Tracer*.

L'objectif est de construire une topologie comprenant **deux réseaux locaux (LAN)** reliés par un routeur.

Le routeur possède deux interfaces :

+ une interface connectée au **LAN1** ;
+ une interface connectée au **LAN2** ;

La topologie sera donc similaire à celle-ci :

![Toplogie du laboratoire](/images/04-topologie-lab1-packet-tracer.png)

{{%notice style="info" title="Remarque"%}}
Selon le modèle de routeur choisi dans *Packet Tracer*, les noms des interfaces peuvent être différents. Par exemple, vous pourriez avoir `GigabitEthernet0/0`, `GigabitEthernet0/1` et `GigabitEthernet0/2`.
{{%/notice%}}
## Création de la topologie

### 1- Équipements nécessaires

Dans *Packet Tracer*, ajoutez :

+ 1 routeur Cisco possédant au moins 3 interfaces Ethernet (ex: `ISR-4331`) ;
+ 2 commutateurs ;
+ 2 ordinateurs ;
+ les câbles Ethernet nécessaires.

La topologie doit avoir la forme suivante :
![Topologie du laboratoire sur Cisco Packet Tracer](/images/04-topologie-lab1.png)

Les commutateurs sont utilisés pour représenter les réseaux locaux. Ils ne nécessitent pas de configuration particulière pour ce laboratoire.

### 2- Plan d'adressage
Utilisez le plan d'adressage suivant.

|Équipement|	Interface|	Adresse IPv4|	CIDR|	Passerelle|
|-----|----|-----|-----|-----|
|**PC1**| Ethernet| `10.20.10.10`	|`/24`|	`10.20.10.1`|
|**R1**|	G0/0/0|	  `10.20.10.1`|	`/24`|	—|
|**R1**|	G0/0/1|	  `10.20.20.1`|	`/24`|	—|
|**PC2**|	Ethernet|	`10.20.20.10`|	`/24`|	`10.20.20.1`|


<!-- |**R1**|	G0/2|	`203.0.113.1`|	`/24`	|—|

Le réseau `203.0.113.0/24` est utilisé ici uniquement comme réseau d'exemple pour l'interface WAN. -->

### 3- Configuration des postes clients

###### PC1

Dans Packet Tracer :

**PC1 → Desktop → IP Configuration**

Configurez :
```text
IP Address:      10.20.10.10
Subnet Mask:     255.255.255.0
Default Gateway: 10.20.10.1
```
###### PC2

Configurez :
```text
IP Address:      10.20.20.10
Subnet Mask:     255.255.255.0
Default Gateway: 10.20.20.1
```

### 4- Configuration du routeur

Ouvrez la console du routeur.

Passez en mode privilégié :
```bash
enable
```
Puis en mode de configuration :
```bash
configure terminal
```
Vous pouvez d'abord configurer le nom du routeur :
```bash
hostname R1
```

###### Configuration de l'interface du LAN 1
```bash
interface GigabitEthernet0/0/0
 description Réseau LAN 10.20.10.0/24
 ip address 10.20.10.1 255.255.255.0
 no shutdown
 exit
```

La commande `no shutdown` active l'interface.

###### Configuration de l'interface du LAN 2
```bash
interface gigabitEthernet0/0/1
 description Réseau LAN 10.20.20.0/24
 ip address 10.20.20.1 255.255.255.0 
 no shutdown 
 exit
```

Sauvegardez les changements :
```bash
write memory
```
{{%notice style="tip" title="Que fait cette commande ?"%}}
Cette commande enregistre la configuration dans la mémoire du routeur. Cela permet de la faire persister même après redémarrage.
{{%/notice%}}
<!-- ###### Configuration de l'interface WAN
```bash
interface gigabitEthernet 0/2 
ip address 203.0.113.1 255.255.255.0 
no shutdown 
exit
``` 

Cette interface ne sera pas utilisée pour accéder à Internet dans ce laboratoire.
-->

### 5- Vérification des interfaces

Utilisez :
```bash
show ip interface brief
```

Vous devriez obtenir quelque chose de similaire à :
```bash
Interface              IP-Address      Status       Protocol
GigabitEthernet0/0/0     10.20.10.1    up           up
GigabitEthernet0/0/1     10.20.20.1    up           up
```

Les états `up/up` indiquent que l'interface est active et que le protocole de couche liaison fonctionne.

### 6- Tester la connectivité

À partir de **PC1**, ouvrez **Command Prompt** et exécutez :
```bash
ping 10.20.10.1
```

Ce test vérifie la communication entre **PC1** et l'interface du routeur sur le **LAN1**.

Ensuite :
```bash
ping 10.20.20.1
```
Puis :
```bash
ping 10.20.20.10
```
Le dernier test vérifie que **PC1** peut communiquer avec **PC2** **à travers le routeur**.

Effectuez également les tests dans l'autre direction :
```text
PC2 → ping 10.20.20.1
PC2 → ping 10.20.10.1
PC2 → ping 10.20.10.10
```

### 7- Observer la table de routage

Sur le routeur :
```bash
show ip route
```
Vous devriez notamment retrouver les deux réseaux directement connectés :
```text
C    10.20.10.0/24 is directly connected
C    10.20.20.0/24 is directly connected
```
Le routeur connaît automatiquement les réseaux correspondant à ses interfaces directement connectées.

Il peut donc déterminer que :
```text
10.20.10.0/24 → G0/0
10.20.20.0/24 → G0/1
```

### Questions
1. Pourquoi **PC1** a-t-il besoin d'une passerelle par défaut ?
2. Pourquoi **PC1** et **PC2** appartiennent-ils à deux réseaux différents ?
3. Quelle interface du routeur reçoit un paquet provenant de **PC1** à destination de **PC2** ?
4. Quelle interface du routeur transmet ensuite le paquet vers **PC2** ?
5. Pourquoi le routeur n'a-t-il pas besoin d'une route statique vers `10.20.10.0/24` ou `10.20.20.0/24` ?
6. Que se passe-t-il si vous supprimez la passerelle par défaut de **PC1** ?
7. Que se passe-t-il si l'interface `G0/1` du routeur est désactivée ?
