+++
pre = '<b>A. </b>'
title = 'Guides et commandes'
draft = false
weight = "170"
+++
-----------------------

## Résumé du chapitre
{{%notice style="tip" title="À retenir"%}}

+ **Interface réseau →** point de connexion au réseau.
+ **MAC →** identifie l'interface à la couche 2.
+ **IP →** identifie l'hôte à la couche 3.
+ **Route →** indique où envoyer les paquets.
+ **Passerelle →** permet d'atteindre les autres réseaux.
+ **ARP →** associe une adresse IPv4 à une adresse MAC sur le LAN.
+ **DNS →** traduit les noms en adresses IP.
+ **ip →** consulter et tester la configuration réseau.
+ **nmcli / NetworkManager** → gérer les connexions réseau.
+ **Netplan →** définir une configuration réseau persistante sous Ubuntu.
{{%/notice%}}


## Ping
`ping` est une commande utilitaire d'administration réseau utilisée pour tester l'accessibilité d'un hôte sur un réseau.

`ping` mesure le temps aller-retour des messages envoyés depuis l'hôte d'origine vers un ordinateur de destination, qui sont renvoyés vers la source.

Pour faire un `ping` à une machine du réseau :
```bash
ping <adresse IP>
```

## SSH
SSH (*Secure Shell*) est un protocole qui permet, entre autres, de prendre en main une autre machine.

+ Il est possible de spécifier l’utilisateur avec lequel on se connecte (l’utilisateur doit exister sur la machine cible) :
```bash
$ ssh user@host
```
+ `ssh` est disponible sur toutes les distributions Linux et Unix.
+ `ssh` permet aussi d’exécuter une commande à distance :
```bash
$ ssh user@host commande
```

## Configuration temporaire vs permanente

Il est important de distinguer deux types de configuration.

### Configuration temporaire

Les commandes :
```bash
ip addr add ...
ip addr del ...
ip route add ...
ip link set ...
```
modifient directement l'état courant du réseau.

Elles sont très pratiques pour tester une configuration mais peuvent diparaître après un redémarrage.

### Configuration persistante

Pour conserver une configuration après un redémarrage, on utilise le système de configuration réseau de la distribution (**Netplan** ou **NetworManager** par exemple)

## Vérifier sa configuration réseau

Lorsqu'une configuration vient d'être effectuée, il est important de vérifier chaque élément séparément.

**1. Vérifier l'interface :**
```bash
ip link
```
L'interface doit être active.
**2. Vérifier l'adresse IP**
```bash
ip a show ens160
```
**3. Vérifier la table de routage**
```bash
ip route
```
On doit notamment retrouver une route par défaut si la machine doit communiquer avec d'autres réseaux.
**4. Vérifier le voisinage ARP**
```bash
ip neigh
```
On peut notamment vérifier que la passerelle possède bien une association IP/MAC.
**5. Tester la passerelle**
```bash
ping 192.168.230.2
```
**6. Tester une adresse IP externe**
```bash
ping 8.8.8.8
```
**7. Tester la résolution DNS**
```bash
ping google.com
```
Ces tests permettent de déterminer à quel niveau se situe un problème.

Par exemple :

+ `ping passerelle` → teste la connectivité locale
+ `ping 8.8.8.8`    → teste le routage vers l'extérieur
+ `ping google.com` → teste également la résolution DNS


## Connaitre la nature d'un équipement sur le réseau ?
Dans le cas où nous connaissons l'adresse IP d'un équipement sur le réseau (par exemple `192.168.10.50`), comment faire pour connaître le constructeur et déduire la nature de cette machine ?

1. Utiliser la commande `ping` pour communiquer pour la première fois avec cette machine. Cela remplit le cache  ARP de notre machine avec l'adresse MAC associée à l'adresse `192.168.10.50` : 
```bash
ping 192.168.10.50
``` 

2. Lancer la commande `arp -a` pour afficher la table ARP. Dans la table, indentifier l'adresse MAC associée à l'adresse IP `192.168.10.50`.

3. Récupérer l'adresse MAC et recherchez le fabriquant de ce périphérique sur internet ([exemple de site](https://dnschecker.org/mac-lookup.php)).