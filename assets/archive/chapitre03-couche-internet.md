# 4. Couche internet

![Couche réseau](./images/03-1.png)


Ce chapitre porte sur le rôle de la couche réseau (modèle OSI) ou Internet (modèle TCP/IP) . Il examine comment cette dernière divise les réseaux en groupes d’hôtes pour gérer le flux de paquets de données dans un réseau.

Ce chapitre aborde également la communication entre les réseaux (appelée routage) ainsi que l’adressage IP.

## Rôles
La couche réseau/internet utilise trois processus de base :

**Adressage des périphériques finaux :** Les périphériques finaux doivent être configurés avec une adresse IP unique pour être identifiés sur le réseau. Un périphérique final disposant d’une adresse IP est qualifié *d’hôte*.

**Encapsulation/Désencapsulation :** La couche réseau reçoit une unité de données de protocole (PDU) de la couche transport. Dans le cadre du processus **l’encapsulation**, la couche réseau ajoute des informations d’en-tête IP, telles que l’adresse IP des hôtes source (expéditeur) et de destination (destinataire). Une fois les informations d’en-tête ajoutées à la PDU, celle-ci est appelée **paquet**. Lorsque le paquet arrive au niveau de la couche réseau de l’hôte de destination, l’hôte vérifie l’en-tête du paquet IP. Si l’adresse IP de destination dans l’en-tête correspond à l’adresse IP de l’hôte qui effectue la vérification, l’en-tête IP est supprimé du paquet. Ce processus de suppression des en-têtes des couches inférieures est appelé **la désencapsulation**. Une fois la désencapsulation effectuée par la couche réseau, la PDU de couche 4 est transmise au service approprié au niveau de la couche transport.

**Routage :** La couche réseau fournit des services permettant de diriger les paquets vers un hôte de destination sur un autre réseau. Pour voyager vers d’autres réseaux, le paquet doit être traité par **un routeur**. Le rôle du routeur est de sélectionner les chemins afin de diriger les paquets vers l’hôte de destination. Ce processus est appelé **le routage**. Un paquet peut passer par de nombreux périphériques intermédiaires avant d’atteindre l’hôte de destination. 


## Protocoles de la couche réseau
+ **IP** (*Internet Protocol*)

+ **ICMP** (*Internet Control Message Protocol*)

+ **RIP** (*Routing Information protocol*)

+ **EIGRP** (*Enhanced Interior Gateway Routing*)

+ **OSPF** (*Open ShortestPath First*)

# 4.1 - Protocole IP

## Caractéristiques

Les principales caractéristiques du protocole IP sont les suivantes:

+ **Sans connexion :** Aucun établissement de connexion avec la destination avant d’envoyer un paquet.

+ **Acheminement au mieux (peu fiable) :** Livraison de paquets non garantie.

+ **Indépendant du support :** Le fonctionnement est indépendant du support transportant les données.

### Sans connexion
![IP est sans connexion](../images/03-4.png)

### Acheminement au mieux
![Acheminement au mieux](../images/03-5.png)

### Indépendant du support
![Indépendant du support](../images/03-6.png)

### Exercice 
Lisez chaque description du protocole IP puis dites à quelle caractéristique elle correspond : 

+ Aucun contact n'est établi avec l'hôte de destination avant d'envoyer un paquet.
+ La livrtaison des paquets n'est pas garantie.
+ Envoie un paquet même si l'hôte de destination ne peut pas le recevoir.

## En-tête de paquet IPv4 (20 octets)

![En-tête IP](../images/03-11.png)


+ **Version :** contient une valeur binaire de 4bits indiquant la version du paquetIP. Pour les paquetsIPv4, ce champ est toujours 0100.

+ **Services différenciés (aussi appelé champ de type de service) :** un champ de 8bits utilisé pour définir la priorité de chaque paquet. 

<!-- Les 6 premiers bits définissent la valeur DCSP (DifferentiatedServices Code Point) qui est utilisée par un mécanisme de qualité de service. Les 2 derniers bits identifient la valeur de notification explicite de congestion qui peut être utilisée pour empêcher l’abandon de paquets pendant les périodes d’encombrement du réseau. -->

+ **Time-to-live (durée de vie, TTL) :**  contient une valeur binaire de 8bits utilisée pour limiter la durée de vie d’un paquet. Cette durée est indiquée en secondes mais est généralement appelée «nombre de sauts». L’expéditeur du paquet définit la valeur de durée de vie initiale et celle-ci diminue de un chaque fois que le paquet est traité par un routeur, ou effectue un saut. Si la valeur du champ TTL (durée de vie) arrive à zéro, le routeur rejette le paquet et envoie un message de dépassement de délai ICMP à l’adresseIP source. La commande tracerouteutilise ce champ pour identifier les routeurs utilisés entre la source et la destination.

+ **Protocole :** Vette valeur binaire de 8 bits indique le type de données utiles transportées par le paquet, ce qui permet à la couche réseau de transmettre les données au protocole de couche supérieure approprié. Les valeurs habituelles sont notamment ICMP (1), TCP (6) et UDP (17).

+ **Adresse IP source :** contient une valeur binaire de 32 bits qui représente l’adresse IP source du paquet.

+ **Adresse IP de destination :** contient une valeur binaire de 32 bits qui représente l’adresse IP de destination du paquet.

+ **Longueur d’en-tête Internet :** contient une valeur binaire de 4bits indiquant le nombre de mots de 32bits contenus dans l’en-tête. Cette valeur varie en fonction des champs d’options et de remplissage. La valeur minimale de ce champ est 5 (c.-à-d., 5x32=160bits=20octets) et la valeur maximale 15 (c.-à-d., 15x32=480bits= 60octets).

+ **Longueur de paquet :** Ce champ de 16 bits indique la taille globale du paquet, y compris l’en-tête et les données, en octets. Sa valeur minimale est de 20 octets (un en-tête de 20octets + 0octet de données) et sa valeur maximale est de 65535octets.

+ **Somme de contrôle de l’en-tête :** champ de 16 bits utilisé pour le contrôle des erreurs sur l’en-tête IP. 

<!-- La somme de contrôle de l’en-tête est recalculée et comparée à la valeur contenue dans le champ de somme de contrôle. Si les valeurs ne correspondent pas, le paquet est rejeté. -->

<!-- Un routeur peut devoir fragmenter un paquet lors de la transmission dudit paquet d’un support à un autre de MTU inférieure. Dans ce cas, la fragmentation se produit et le paquetIPv4 utilise les champs suivants pour suivre les fragments:

Identification ce champ de 16bits identifie de manière unique le fragment d’un paquetIP d’origine.

Indicateurs ce champ de 3bits indique la façon dont le paquet est fragmenté. Il est utilisé avec les champs de décalage du fragment et d’identification pour reconstituer le paquet d’origine.

Décalage du fragment ce champ de 13bits indique la position dans laquelle placer le fragment de paquet pour reconstituer le paquet d’origine. -->


# 4.2 Adressage IP

![Format d'une adresse IP](../images/32-1.png)

**Rappel :** les adresses IPv4 sont composées de 4 octets (32 bits) notés sous forme de 4 nombres décimauxde 0 à 255 séparés par des points.

L’originalité de ce format d’adressage réside dans l’association de l’identification du réseau avec l’identification de l’hôte :

+ La partie *réseau* est commune à l’ensemble des hôtes d’un même réseau.

+ La partie *hôte* est unique à l’intérieur d’un même réseau.

## Masque de sous-réseau

![Masque de sous-réseau](../images/32-2.png)

Pour définir les parties réseau et hôte d’une adresse, les périphériques utilisent un modèle 32 bits distinct appelé masque de sous-réseau.

Le masque de sous-réseau ne contient pas réellement le réseau ou la partie hôte d’une adresse IPv4; il indique simplement où rechercher ces parties dans une adresse IPv4 donnée.

## Longueur du préfixe /x

![Préfixe](../images/32-3.png)

## Monodiffusion et diffusion

+ **Monodiffusion (*unicast*) :** Consiste à envoyer un paquet d’un hôte à un autre
![Monodiffusion](../images/32-4.png)

+ **Diffusion (broadcast) :** Consiste à envoyer un paquet d’un hôte à tous les hôtes du réseau. Deux types :
    + **Diffusion limitée :** Utilisée dans le même sous réseau. Limite : **les routeurs ne transmettent pas une diffusion limitée** (voir l’exemple de image)!
    + **Diffusion dirigée :** Utilisée pour atteindre d’autres réseaux que le leréseau dont on ait.E xemple : pour atteindre le réseau `172.16.4.0/24` depuis un autre réseau que celui-ci, on en envoi à l’IP de diffusion de ce réseau, donc à 172.16.5.255.

+ **Multidiffusion  (multicast):** Quelques exemples de transmission multidiffusion : Diffusions vidéo et audio, Échange d’informations de routage entre des protocoles de routage, Distribution de logiciels, Jeu en ligne etc...
![Mutlidiffusion](../images/32-10.png)

## Types d’adresses IPv4
### Adresses publiques et privées
#### Adresses privées
Les hôtes d'un réseau privé peuvent utiliser des adresses privées. cette adresse IP ne sera pas utilisée pour les requêtes sur internet.

+ `10.0.0.0` à `10.255.255.255` (`10.0.0.0/8`)

+ `172.16.0.0` à `172.31.255.255` (`172.16.0.0/12`)

+ `192.167.0.0` à `192.168.255.255` (`192.168.0.0/16`)

<!-- ### Adresses d’un espace d’adressage partagé
Ne sont pas globalement routables

Destinées uniquement à un usage dans les réseaux des fournisseurs de services.

Bloc d’adresses: 100.64.0.0/10 -->

#### Adresses réservées
![Adresses IP réservées](../images/32-7.png)

## Adressage par classe
![Adressage IP par classe](../images/32-8.png)

### Limite de l'adressage par classe
![Limite adressage IP par classe](../images/32-9.png)

+ **CIDR :** Un nouvel ensemble de normes a été créé pour permettre aux fournisseurs de services d’allouer les adresses IPv4 sur n’importe quelle limite binaire (longueur de préfixe) plutôt que seulement avec une adresse de classe A, B ou C.

## Limitation IPv4

+ **Manque d’adresses IP :** l’IPv4 a un nombre limité d’adresses IP publiques disponibles. Bien qu’il existe environ 4 milliards d’adresses IPv4, le nombre croissant de périphériques IP, les connexions permanentes et la croissance potentielle des pays en voie de développement entraînent une hausse du nombre d’adresses devant être disponibles.

+ **Croissance de la table de routage Internet :** Une table de routage est utilisée par les routeurs pour déterminer les meilleurs chemins disponibles. À mesure que le nombre de serveurs (nœuds) connectés à Internet augmente, il en va de même pour le nombre de routes réseau. Ces routes IPv4 consomment beaucoup de mémoire et de ressources processeur sur les routeurs Internet.

+ **Absence de connectivité de bout en bout :** La technologie de traduction d’adresses réseau (NAT) est généralement implémentée dans les réseaux IPv4. Cette technologie permet à plusieurs périphériques de partager une adresse IP publique unique. Cependant, étant donné que l’adresse IP publique est partagée, l’adresse IP d’un hôte interne du réseau est masquée. Cela peut être problématique pour les technologies nécessitant une connectivité de bout en bout.

### Solution : IPv6
+ **Espace d’adressage plus important :** Les adresses IPv6 sont basées sur un adressage hiérarchique de 128 bits (32 bits pour l’IPv4), ce qui augmente considérablement le nombre d’adresses IP disponibles.

+ **Traitement des paquets plus efficace :** L’en-tête IPv6 a été simplifié et comporte moins de champs. Cela améliore le traitement des paquets par les routeurs intermédiaires et permet également la prise en charge d’extensions et d’options pour plus d’évolutivité et de longévité.

+ **Traduction d’adresses réseau non nécessaire :** Grâce au grand nombre d’adresses publiques IPv6, la technologie NAT n’est plus nécessaire. Les sites clients, des plus grandes entreprises aux sites de particuliers, peuvent obtenir une adresse réseau publique IPv6. Cela évite certains des problèmes d’application causés par la technologie NAT, qui sont rencontrés par des applications nécessitant une connectivité de bout en bout.

+ **Sécurité intégrée :** l’IPv6 prend nativement en charge les fonctions d’authentification et de confidentialité. Avec l’IPv4, d’autres fonctions devaient être mises en œuvre pour bénéficier de ces fonctionnalités.

+ 4 milliards d’adresses IPv4 (2 ^ 32 = 4000000000) [4294967296]

+ 340 undécillions d’adresses IPv6 (2^128 = 340x10^36)340000000000000000000000000000000000000 [340282366920938463463374607431768211456]

## Exercices


# 4.3 Routage

Le routage est un processus qui permet de sélectionner des chemins (routes) dans un réseau pour transmettre des données depuis un expéditeur jusqu’à un ou plusieurs destinataires.

Un hôte peut envoyer un paquet à :

+ **Lui-même :** il s'agit d'une adresse IP spécifique,`127.0.0.1`, appelée *interface de bouclage* (*loopback*). Cet adresse est utile à des fins de test. Toute adresse IP appartenant au réseau `127.0.0.0/8` se rapporte à l'hôte local.

+ **Hôte local :** un hôte sur le même réseau que l'hôte émetteur (ils partagent la même adresse réseau).

+ **Hôte sur un réseau distant** (ils ne partagent pas la même adresse réseau).

## Fonction de routage
Pour qu'une machine puisse envoyer des paquets à un hôte d'un autre réseau, elle doit être dotée :

+ D’une seule route par défaut afin de pouvoir sortir vers l’extérieur, ou
+ D’une route statique identifiée vers chaque sous-réseau.

{{% notice style="info" title="Note"  %}}
Sur une machine, on peut configurer plusieurs routes statiques et une seule route par défaut.
{{% /notice %}}

Une route définie sur une station est un chemin que doivent emprunter les paquets à destination d’un réseau.

Soit l’exemple (en image) d’une station, appelée *station 1*, d’adresse IP `112.65.77.8` sur un réseau `112.0.0.0/8` :

![Exemple de route](../images/30-01.png)

+ Elle est connectée à une passerelle qui a pour IP dans ce réseau `112.65.123.3` sur son interface `eth0`.

+ La passerelle est aussi connectée au réseau `192.168.0.0/24` via son interface `eth1` qui a pour IP `192.168.0.1`. Si la *station 1* veut communiquer directement avec la *station 6*, d’adresse IP `192.168.0.2` sur le réseau `192.168.0.0/24`, trois condition doivent être réunies :

+ Une route doit être définie sur la *station 1* indiquant que les paquets à destination du réseau `192.168.0.0/24` doivent passer par la passerelle `112.65.123.3`. Pour cela, on peut utiliser la commande `route` :
```bash
$ route add -net 192.168.0.0/24 gw 112.65.123.3
```
+ Une route doit être définie sur la station 6 indiquant que les paquets à destination du réseau `112.0.0.0/8` doivent passer par la passerelle `192.168.0.1` ; pour cela, on peut utiliser la commande `route` :
```bash
$ route add -net 112.0.0.0/8 gw 192.168.0.1
```
La passerelle doit être configurée pour transmettre (ou forwarder) les paquets IP d’un réseau à l’autre, ce qui se fait par la commande :
```bash
$ echo 1 > /proc/sys/net/ipv4/ip_forward
```
ou
```bash
$ sysctl -w net.ipv4.ip_forward=1
```
ou de façon permanente en ajoutant dans le fichier `/etc/sysctl.conf` la ligne : 
```bash
net.ipv4.ip_forward=1
```

Toute la configuration précédente est à refaire si on redémarre la machine. Afin d’éviter qu’à chaque redémarrage on doit retaper à nouveau toutes les commandes précédentes, on peut les mettre dans des scripts d’initialisation au démarrage avec la commande `update-rc.d` (sous *Debian/Ubuntu*) ou `chkconfig` (*Redhat/Alma/Rocky*).

Pour ajouter un script `my_ script` à l’initialisation :
```bash
$ mv my_script /etc/init.d
$ update-rc.d my_script defaults # sous debian/ubuntu
$ chkconfig --add my_script # sous redhat/alma/rocky
```

Dans le cas de cette topologie, on aurait pu remplacer la route statique définie précédemment dans les stations 1, 2 et 3 par une route par défaut.

La configuration d’une route par défaut peut se faire par l’une des méthodes suivantes :

+ Définir une passerelle par défaut (gateway)
```bash
$ ip route add default via 112.65.123.3
```
+ Ou rajouter la route en utilisant la commande :
```bash
$ route add -net 0.0.0.0/0 gw 112.65.123.3
```
+ De même du coté des stations 4, 5 et 6 vers la gateway `192.168.0.1`.


## Tables de routage

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