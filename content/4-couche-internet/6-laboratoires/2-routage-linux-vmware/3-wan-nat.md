+++
slug = '3-wan-et-nat'
title = "3- WAN et NAT"
weight = "465"
draft = false
+++
-------------

Nous allons maintenant ajouter une troisième interface au routeur afin de permettre aux deux LAN d'accéder à Internet.

La topologie devient :

![Nouvelle topologie avec une troisième interface WAN pour le routeur](/images/04-topologie-labo2-routage-linux2.png?width=50rem)

### 1. Ajouter l'interface WAN

Dans *VMware*, ajoutez une troisième carte réseau à **ROUTEUR**. Configurez-la en **NAT** ou **Bridged**.


<!-- Dans un environnement de laboratoire, NAT VMware est généralement plus simple puisqu'il permet à la machine virtuelle d'accéder à Internet sans exposer directement la VM sur le réseau physique.

Démarrez ou redémarrez le routeur si nécessaire. -->

<!-- Identifiez la nouvelle interface :

ip link

Supposons qu'elle soit :

ens224

Configurer l'interface WAN

La configuration dépend du réseau VMware utilisé.

Avec un réseau VMware NAT, l'interface WAN peut généralement recevoir automatiquement une adresse IPv4 avec DHCP.

Avec NetworkManager :

sudo nmcli connection show

Puis configurez la connexion correspondante en DHCP :

sudo nmcli connection modify "WAN" \
    ipv4.method auto

Activez-la :

sudo nmcli connection up "WAN"

Vérifiez :

ip addr

Puis :

ip route

Vous devriez retrouver une route par défaut fournie par VMware, par exemple :

default via 192.168.x.2 dev ens224

L'adresse exacte dépend de la configuration du réseau NAT VMware. Ne réutilisez pas nécessairement les valeurs de cet exemple. -->

### 2. Tester Internet depuis le routeur

Avant de configurer le **NAT**, vérifiez que le routeur lui-même peut accéder à Internet.

Par exemple :
```bash
ping 8.8.8.8
```

Puis :
```bash
ping google.com
```
{{%notice style="tip" title="Rappel"%}}
Si `8.8.8.8` fonctionne mais pas `google.com`, le problème est probablement lié à la résolution DNS.
{{%/notice%}}

<!-- 21. Activer le forwarding IPv4

Le routeur doit continuer à transférer les paquets entre ses interfaces.

Vérifiez :

cat /proc/sys/net/ipv4/ip_forward

Si nécessaire :

sudo sysctl -w net.ipv4.ip_forward=1 -->
### 3. Comprendre le problème

Avant d'utiliser le **NAT**, un paquet provenant de **CLIENT1** pourrait avoir :
```text
Source      : 10.20.10.10
Destination : 8.8.8.8
```
Le paquet arrive au routeur.

Le routeur peut le transmettre vers Internet.

Cependant, `10.20.10.10` est une adresse IPv4 privée. Elle ne peut pas être utilisée comme adresse source publique sur Internet. Nous devons donc traduire l'adresse source.

### 3. Configurer le NAT masquerade

Nous allons utiliser `firewall-cmd` pour configurer un **NAT** de type *masquerade*.

Sur **ROUTEUR** :
```bash
sudo firewall-cmd --zone=public --add-masquerade --permanent
```

Cette règle permet, pour les paquets qui quittent le routeur par son interface WAN, de remplacer leur adresse source par l'adresse de l'interface WAN.

Ainsi, un paquet provenant de **CLIENT1** :

**Avant NAT**
```text
Source      : 10.20.10.10
Destination : 8.8.8.8
```
**Après NAT**
```text
Source      : adresse WAN du routeur
Destination : 8.8.8.8
```

Le routeur conserve les informations nécessaires pour pouvoir associer la réponse au client interne.

### 4. Tester l'accès Internet

Depuis **CLIENT1** :
```bash
ping 8.8.8.8
```
Puis :
```bash
ping google.com
```
Effectuez les mêmes tests depuis **CLIENT2**.

###### **Questions**
1. Le deuxième `ping` fonctionne-t-il?
2. À votre avis, pourquoi?
3. Essayez de corriger ce problème.

<!-- 
Si tout est correctement configuré :

CLIENT1
192.168.10.10
     |
     v
ROUTEUR
192.168.10.1
     |
     | NAT
     v
WAN
     |
     v
INTERNET

et :

CLIENT2
192.168.20.10
     |
     v
ROUTEUR
192.168.20.1
     |
     | NAT
     v
WAN
     |
     v
INTERNET
25. Observer les règles NAT

Affichez les règles :

sudo iptables -t nat -L -n -v

Observez notamment la chaîne :

POSTROUTING

Vous devriez voir la règle de masquerade et le nombre de paquets ayant correspondu à celle-ci. -->

### 5. Vérifier le chemin vers Internet

Depuis **CLIENT1** :
```bash
traceroute 8.8.8.8
```
Vous devriez observer au minimum le routeur comme premier saut.

Le premier saut correspond à la passerelle :
```text
10.20.10.1
```
Vous pouvez faire le même test depuis **CLIENT2**. Le premier saut devrait être :
```text
10.20.20.1
```

### 6. Questions de synthèse

1. Pourquoi **CLIENT1** ne peut-il pas directement envoyer un paquet destiné à `10.20.20.10` sans route ou passerelle ?
2. Quel est le rôle du routeur Linux dans cette topologie ?
3. Quelle différence y a-t-il entre :
    ```bash
    ip route add 192.168.20.0/24 via 192.168.10.1
    ```
    et :
    ```bash
    ip route add default via 192.168.10.1
    ```
4. Pourquoi devons-nous activer : `net.ipv4.ip_forward = 1` sur le routeur ?
5. Quelle est l'adresse source d'un paquet envoyé par **CLIENT1** vers `8.8.8.8` avant le **NAT** ?
6. Quelle adresse source est utilisée après le **NAT** ?
7. Pourquoi le **NAT** *masquerade* est-il nécessaire dans cette topologie ?
8. Quelle différence y a-t-il entre le rôle du **NAT** et celui du routage ?
9. Que se passerait-il si le **NAT** était configuré, mais que le *forwarding* IPv4 était désactivé ?
10. Que se passerait-il si le *forwarding* était activé, mais que le NAT n'était pas configuré ?