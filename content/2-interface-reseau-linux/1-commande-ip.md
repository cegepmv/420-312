+++
pre = '<b>1. </b>'
title = 'La commande ip'
draft = false
weight = "210"
+++
---------------------

La commande `ip` est l'outil moderne utilisé sous Linux pour consulter et manipuler la configuration réseau.

Elle remplace progressivement les anciens outils comme `ifconfig` et `route`.

Quelques commandes importantes :

| Commande | Utilité |
|----------|---------|
|`ip a`|	 Afficher les adresses des interfaces|
|`ip link`|	Afficher les interfaces et leur état|
|`ip route`|	Afficher la table de routage|
|`ip neigh`| Afficher le cache ARP|
|`ip a show ens160`|	Afficher une interface précise|
|`ip link set ens160 up`| Activer une interface|
|`ip link set ens160 down`|	 Désactiver une interface|


### Modifier temporairement une adresse IP

Il est possible d'ajouter une adresse IP à une interface avec :
```bash
ip addr add 192.168.230.132/24 dev ens160
```
On peut vérifier le résultat avec :
```bash
ip a show ens160
```
Il est possible d'avoir plusieurs adresses IP sur une même interface :
```bash
ens160
 ├── 192.168.230.10/24
 └── 192.168.230.132/24
```
Pour supprimer une adresse :
```bash
ip addr del 192.168.230.132/24 dev ens160
```
{{%notice style="note" title="Important"%}}
Les modifications effectuées directement avec `ip` sont généralement **temporaires**. Elles peuvent disparaître lors du redémarrage de la machine ou lorsqu'un gestionnaire réseau réapplique sa configuration.
{{%/notice%}}

La commande `ip` est donc particulièrement utile pour :
+ observer la configuration ;
+ effectuer des tests ;
+ modifier temporairement une configuration ;
+ diagnostiquer des problèmes réseau.

Pour une configuration permanente, on utilise le système de configuration réseau de la distribution.

### Ajouter une passerelle temporairement

La passerelle par défaut peut également être configurée avec `ip` :
```bash
ip route add default via 192.168.230.2
```
On peut ensuite vérifier la configuration :
```bash
ip route
```
On devrait retrouver :
```bash
default via 192.168.230.2
```
Comme pour l'adresse IP, cette modification est temporaire.

## ifconfig : l'ancien outil

`ifconfig` est un ancien outil permettant notamment d'afficher et de modifier la configuration des interfaces réseau.

Pour afficher les interfaces :
```bash
ifconfig
```
Pour afficher une interface précise :
```bash
ifconfig ens160
```
Pour désactiver une interface :
```bash
ifconfig ens160 down
```
Pour l'activer :
```bash
ifconfig ens160 up
```
Cependant, `ifconfig` est aujourd'hui considéré comme un outil ancien et n'est généralement plus installé par défaut sur les distributions Linux modernes.

On privilégiera :
```bash
ip a
```
et :
```bash
ip link
```

<!-- {{%notice style="warning" title="Attention"%}}
La résolution d'un nom ne consiste pas simplement à « toujours consulter /etc/hosts avant DNS ». L'ordre de résolution dépend de la configuration du système, notamment de /etc/nsswitch.conf. Sur une configuration Linux classique, files est généralement placé avant dns.
{{%/notice%}} -->