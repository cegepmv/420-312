+++
slug = '2-routage-linux-vmware'
title = "2- Routage Linux/VMWare"
weight = "462"
draft = false
+++
-------------

## Contexte

Dans le laboratoire précédent, nous avons utilisé *Cisco Packet Tracer* et des routeurs *Cisco* pour relier deux réseaux.

Dans ce laboratoire, nous allons reproduire **la même architecture avec des machines Linux** (une jouera le rôle de **routeur**).

La topologie est la suivante :

![Topologie du laboratoire de routage avec des machines virtuelles Linux sur VMWare](/images/04-topologie-labo2-routage-linux1.png)

Le routeur Linux possèdera d'abord deux interfaces :
```text
Interface 1 → LAN Segment 1
Interface 2 → LAN Segment 2
```

Nous ajouterons par la suite une troisième (WAN) :
```text
Interface 3 → WAN / Internet
```


### Plan d'adressage
Nous allons utiliser le plan d'adressage suivant :

|Machine|	Interface|	Adresse|
|-------|----------|---------|
|**CLIENT1**|	*LAN-1*|	`10.20.10.10/24`|
|**ROUTEUR**|	*LAN-1*|	`10.20.10.1/24`|
|**ROUTEUR**|	*LAN-2*|	`10.20.20.1/24`|
|**CLIENT2**|	*LAN-2*|	`10.20.20.10/24`|

## Étapes préliminaires
### Machines nécessaires
Vous aurez besoin de trois machines virtuelles Linux :

|Machine|	Rôle| VM	|Configuration|
|------|-----|------|-------------|
|**CLIENT1**|	Client LAN 1|`rocky-10.2.ova`|	`nmcli`|
|**CLIENT2**|	Client LAN 2|`ubuntu-server-26.04.ova`|	`Netplan`|
|**ROUTEUR**|	Routeur| `rocky-10.2.ova`|`nmcli` / `firewall-cmd`|

Les noms des interfaces peuvent varier selon votre distribution et votre configuration VMware.

Avant de commencer, identifiez les interfaces avec `ip link` ou `ip a`

Dans la suite du laboratoire, nous utiliserons des noms génériques :

+ **CLIENT1 →** ens160
+ **CLIENT2 →** ens160
+ **ROUTEUR**
  + `ens160` → LAN-1
  + `ens224` → LAN-2

{{%notice style="note" title="Important"%}}
Adaptez les noms d'interfaces à votre environnement.
{{%/notice%}}

### Configuration VMware

Créez deux *LAN Segments* dans VMware :
```text
LAN-1
LAN-2
```

Configurez les cartes réseau des machines comme suit.

###### CLIENT1
```text
Carte réseau 1 → LAN-1
```
###### CLIENT2
```text
Carte réseau 1 → LAN-2
```
###### ROUTEUR
```text
Carte réseau 1 → LAN-1
Carte réseau 2 → LAN-2
```

Dans un premier temps, **ne configurez pas l'interface WAN**. Nous allons d'abord reproduire la communication entre les deux LAN.

### Configuration des noms d'hôtes

Sur chaque machine, associez le bon nom d'hôte : 

```bash
sudo hostnamectl set-hostname CLIENT1 # sur CLIENT1
sudo hostnamectl set-hostname CLIENT2 # sur CLIENT2
sudo hostnamectl set-hostname ROUTEUR # sur ROUTEUR

```
Puis vérifiez :
```bash
hostname
```
ou 
```bash
exit
```
puis reconnectez-vous.

