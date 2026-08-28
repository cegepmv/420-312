
# 2 - Couche(s) d'accès réseau

La couche d'accès réseau du modèle *TCP/IP* est divisée en deux couches dans le *modèle OSI* : **La couche physique** (L1) et **la couche liaison de données** (L2). Dans ce chapitre, nous allons explorer leurs rôles, le matériel, les normes et protocoles associés à ces couches.

## Couche physique
### Rôle

+ Transmission physique des données entre deux équipements réseaux. 
+ Tout ce qui a trait au bas-niveau, au matériel : la transmission des bits, leur encodage, la synchronisation entre deux cartes réseau, etc.
+ Définit les standards des câbles réseaux, des fils de cuivre, du WIFI, de la fibre optique, ou de tout autre support électronique de transmission. 

![Couches 1 et 2](./images/02-2.png?width=600px)

### Normes

|Organisme de Normalisation|Normes réseau|
|---|---|
|ISO|ISO 8877: adoption officielle des connecteurs RJ (RJ-11 et RJ-45, notamment)<br>ISO11801: norme de câblage réseau similaire à la normeEIA/TIA 568|
|EIA/TIA|TIA-568-C: normes de câblage de télécommunication utilisées par presque tous les réseaux de voix, de vidéo et de données<br>TIA-569-B: normes des immeubles commerciaux pour les voies d'accès et les espaces de télécommunications<br>TIA-598-C: code couleur de la fibre optique<br>TIA-942: norme d'infrastructure de télécommunications pour les data centers|
|ANSI|568-C: brochage RJ-45 – Développée en collaboration avec les organismes EIA et TIA|
|UIT-T|G.992:ADSL|
|IEEE|802.3: Ethernet<br>802.11: LAN sans fil (WLAN) et maillé (certification Wi-Fi)<br>802.15: Bluetooth|


## Couche liaison de données

### Rôle
La couche liaison de données assure deux services de base :

+ Accepter les paquets de couche 3 et les encapsuler dans des PDU appelées **des trames**.

+ **Contrôler l'accès au support** et **détecter les erreurs**.

### Concepts

![Couches 1 et 2](./images/02-5.png?width=700px)
- **Bande passante:** Capacité d'un support à transporter des données. La bande passante numérique mesure la quantité de données pouvant circuler d'un emplacement à un autre pendant une période donnée.

- **Débit:** Mesure du transfert de bits sur le support pendant une période donnée. De nombreux facteurs influencent le débit. Notamment: la quantité de trafic, le type de trafic, ou la latence créée par le nombre de périphériques réseau rencontrés entre la source et la destination.

<!-- ![Couches 1 et 2](../images/02-7.png?width=700px) -->

+++
## 2.1 - Supports de transmission

-----------------------

Pour que deux ordinateurs ou équipements réseau communiquent entre eux, il faut qu'ils soient reliés par quelque chose qui leur permet de transmettre de l'information. Ce quelque chose est ce qu'on appelle un **support de transmission**.

Deux classes de supports de transmission:

- **Supports de transmission guidés (physique) :** les paires torsadées, les câbles coaxiaux, les fibres optiques

- **Support de transmission non-guidés (sans-fil):** les ondes hertziennes


### Supports de transmission guidés

#### Câble coaxial

![Alignement](../images/020101-cable-coaxial.png?width=35vw)

+ Composé d’une partie centrale (fil de cuivre), enveloppée dans un isolant, puis d’un blindage métallique tressé et enfin d'une gaine extérieure.

+ Utilisé dans plusieurs domaines :
    + Entre une antenne TV et un récepteur de télévision;
    + Dans le réseau câblé urbain;
    + Entre un émetteur et l'antenne d'émission, par exemple une carte électronique Wi-Fiet son antenne;
    + Entre des équipements de traitement du son
    + Dans les anciennes versions de réseaux Ethernet.

+ Longueur maximale jusqu’à 500 mètres.

+ Débit allant 10 Mb/s jusqu’à 600 Mb/s.

+ Le câble coaxial est maintenant remplacé par la fibre optique sur les longues distances (supérieures à quelques kilomètres).

#### Paire torsadée

![Paire torsadée](../images/01-2.png?width=30vw)

Le câble à paire torsadée (TWISTED-PAIR) est composé de 4 ou 8 fils placés en paires et organisés en spirale et d’une enveloppe isolante.

L’entrelacement permet de limiter les interférences extérieures mais la protection d’un blindage est bien plus efficace pour diminuer les risques d’interférences.

La paire torsadée est utilisée en téléphonie et dans les réseaux locaux en raison de son faible coût économique.

Il pose des problèmes dans les transmissions à grande vitesse, dès que la distance dépasse 100m.

***Caractéristiques :***

- Utilisée pour les réseaux locaux.
- Une longueur maximale de 100 mètres.
- Un câblage peu coûteux, c’est le moins cher.
- Une installation et des connexions simples.
- La plus grande flexibilité du câble.
- Vulnérabilité aux interférences.
- Un choix fiable mais qui ne garantit pas l’intégrité des données transmises sur de longues distances et à des débits élevés...

Il existe des **paires torsadées non blindées** *(UTP ou Unshielded Twisted Pair)* ou **blindées** *(STP ou Shielded Twisted Pair)*.

##### Paires torsadées non blindées (UTP)

***Caractéristiques :***

- Deux ou quatre paires de cuivre entrelacées (torsadées)
- Une enveloppe isolante
- Utilisée à l’origine pour les lignes téléphoniques (2 paires)
- Très utilisée pour les réseaux locaux (4 paires)
- Une longueur maximale de 100 mètres

##### Paires torsadées blindées (STP)

***Caractéristiques :***
- Paires de cuivre entrelacées:
- Blindage autour de chaque paire.
- Une enveloppe isolante.
- Le blindage permet de réduire les interférences (mélanges des signaux électriques de plusieurs lignes,…).
- Le blindage permet aussi des transferts de données à des débits plus importants et sur des distances plus grandes que l’UTP.

##### Norme EIA/TIA 568 des câbles UTP et STP

- La norme «Commercial Building Wiring Standard 568» de l’EIA/TIA (Electronic Industries Association / Telecommunication Industries Association) a été mise au point aux USA pour garantir la qualité et les conditions d’utilisation des câbles de l’industrie américaine.
- Cette norme classe les câbles UTP en 8 catégories.
- Elle définit la vitesse maximale de transfert des données numériques qui est mesurée en Méga Bit par seconde (Mb/s) ou Giga Bit par senconde (Gb/s).
- Elle détermine le nombre de torsions par «pied» (33 centimètres) que peut subir un câble UTP.

**Catégories des câbles UTP**

|Catégorie|Fonction|Vitesse de transmission (100m)|
|---|---|---|
|1|Voix analogique||
|2|Données numériques|4 Mb/s|
|3|Données numériques|10 Mb/s|
|4|Données numériques|16 Mb/s|
|5|Données numériques|100 Mb/s|
|5e améliorée|Données numériques|1000 Mb/s|
|6|Données numériques|1000 Mb/s (1 Gb/s)|
|6a|Données numériques|10000 Mb/s (10 Gb/s)|
|7|Données numériques|10000 Mb/s (10 Gb/s)|
|8|Données numériques|25 Gb/s ou 40 Gb/s|


***Alignement des fils (norme américaine) :***

1. Blanc/Vert
2. Vert
3. Blanc/Orange
4. Bleu 
5. Blanc/Bleu
6. Orange
7. Blanc/Brun(Marron)
8. Brun(Marron)

Pour concevoir un câble catégorie 5, positionner le connecteur RJ-45 de façon à ce que le fixateur soit vers le bas.

![Alignement](../images/01-3.png?width=20vw)
![Alignement](../images/01-4.png?width=20vw)


***Alignement des fils (norme européenne) :***

1. Blanc/Orange
2. Orange
3. Blanc/Vert
4. Bleu
5. Blanc/Bleu
6. Vert
7. Blanc/Brin(Marron)
8. Brin (Marron)

![Alignement](../images/01-5.png?width=20vw)
![Alignement](../images/01-6.png?width=20vw)

#### Fibre optique

![Alignement](../images/01-8.png?width=300px)

- Fil en verre ou en plastique très fin qui conduit la lumière.
- Entourée d'une gaine protectrice, elle peut être utilisée pour conduire de la lumière entre deux lieux distants de plusieurs centaines, voire milliers, dekilomètres.
- En rendant possible les communications à très longue distance et à des débits importants, la fibre optique est l'un des éléments clef de la révolution des télécommunications optiques.

***Caractéristiques :***

- Matériau très léger, ce qui peut être précieux là où les contraintes de poids interviennent comme dans le cas de son utilisation comme conducteur électrique dans les avions et les satellites.

- Taux d'erreur de transmission très faible, estimé à 1bit erroné sur 109 bits transmis, ce qui allège les temps de détection et de retransmission.

- Elle supporte des transmissions de 1Gb/s, 43Térabits/s en laboratoire(2014).

- Elle n'est pas sensible aux interférences électriques et électromagnétiques, elle n'émet pas non plus de bruit électrique, ce qui en fait un conducteur de choix utilisable en télécommunication à très haute vitesse.

***Exemples d'utilisation :***

- **Les réseaux d'entreprise:** relier les périphériques d'infrastructure.

- **Les réseaux FTTH et d'accès:** la technologie FTTH (fiber to the home - fibre optique jusqu'au domicile) utilisée pour fournir des services haut débit disponibles en permanence aux particuliers et aux petites entreprises (le télétravail, la télémédecine et la vidéo à la demande).

- **Les réseaux longue distance:** les fournisseurs d'accès utilisent des réseaux terrestres longue distance à fibre optique pour connecter les pays et les villes. Ces réseaux vont généralement de quelques dizaines à quelques milliers de kilomètres et utilisent des systèmes proposant jusqu'à 10Gbit/s.

- **Les réseaux sous-marins:** des câbles à fibre spéciaux sont utilisés pour fournir des solutions haut débit et haute capacité fiables, à l'épreuve des environnements sous-marins sur des distances à l'échelle d'un océan.

### Supports de transmission non-guidés (sans-fil)

![Wifi](../images/02-8.png?width=30rem)

**Avantages :**

- Mobilité
- Non limité par le support

**Contraintes :**

- Zone de couverture
- Interférences
- Sécurité


### Normes des supports de transmission non-guidés 

![Wifi](../images/02-9.png?width=50vw)



+++
## 2.2 - Matériel des couches basses

### Répéteur

Quand un signal électrique est transmis sur un support de communication, il a tendance à s'atténuer rapidement avec la distance. Cela vaut aussi bien pour les transmissions sans-fils, que pour les signaux guidés par un câble en cuivre ou une fibre optique. L'atténuation est surtout un problème sur les connexions sans-fils, dont la portée ne dépasse pas quelques mètres pour les technologies domestiques. Mais elle pose problème pour les câbles réseaux si la distance parcourue devient assez grande. Par exemple, les grands câbles téléphoniques qui parcourent les villes et les campagnes subissent une atténuation non-négligeable.

![Répéteur](../images/02-repeteur.jpg?width=15rem)

- Un répéteur permet d'éviter les problèmes liés à l'atténuation des signaux
- Matériel qui régénère le signal perçu en entrée: Il reçoit sur son entrée le signal transmis, et produit en sortie le même signal amplifié, similaire au signal non-atténué. 

### Concentrateur

![Concentrateur](../images/02-hub.jpg?width=30rem)

- Version multi-port du répéteur : quand il reçoit un flux de bits sur un port, il recopie celui-ci sur tous les autres ports. 
- Chaque donnée envoyée par un ordinateur est redistribuée à tous les autres. 
- Quand il est placé au centre d'un réseau en étoile, il permet de **simuler un réseau en bus**. 

### Commutateur

![Commutateur](../images/02-switch.png?width=30rem)

- Équipement de couche liaison
- Utilisé dans les réseaux locaux en étoile, au même titre que les concentrateurs. 
- La différence avec le concentrateur : il redirige les trames reçues vers l'ordinateur de destination uniquement, il ne diffuse pas la trame à tous les ordinateurs du réseau local comme le ferait un concentrateur. Lorsqu'il reçoit une trame, il la renvoie sur le port qui est associée à l'ordinateur de destination. 

### Carte réseau

![Carte réseau](../images/02-NIC.jpg?width=30rem)

- C'est le composant qui permet à un ordinateur de communiquer sur un réseau (local ou internet).
- Elle permet d'envoyer ou de recevoir des informations sur un câble réseau ou une connexion WIFI. 
- Elle communique avec le reste de l'ordinateur via le bus de la carte mère.


+++
pre = '<b>3. </b>'
title = "Protocoles des couches basses"
draft = false
weight = "240"
+++

## Ethernet (IEEE 802.3)

+ La technologie LAN la plus répandue.
+ Fonctionne au niveau de la **couche liaison de données** et de la **couche physique**.

### Normes Ethernet

+ Définissent les protocoles de couche 2 et les technologies de couche 1.
+ Deux sous-couches distinctes de la couche liaison de données pour fonctionner: **LLC** (*Logical Link Control*) et **MAC** (*Media Access Control*).

![Couches 1 et 2](../images/02-14.png?width=700px)

### Les sous-couches LLC et MAC

#### LLC

+ Gère la communication entre la couche supérieure et la couche inférieure
+ Prend les données du protocole réseau et ajoute des informations de contrôle pour faciliter la remise du paquet à sa destination
+ Implémenté dans le logiciel.

#### MAC

+ Constitue la sous-couche inférieure de la couche liaison de données
+ Implémentée par le matériel, généralement dans la carte réseau de l'ordinateur
+ Responsable du positionnement et de la récupération des trames sur les supports
+ Deux rôles essentiels: **encapsulation des données** et **contrôle d'accès au support**

<!-- ### La sous-couche MAC

![Couches 1 et 2](../images/02-15.png?width=700px) -->

##### Encapsulation des données

+ Encapsulation/Désencapsulation : Assemblage des trames avant la transmission et désassemblage des trames à leur réception. La couche MAC ajoute un en-tête et un code de fin (trailer) à l'unité de données de protocole (*PDU*) de la couche réseau.

+ Deux fonctions principales:

    + **Délimitation des trames :** identification d'un groupe de bits formant une trame, synchronisation entre les noeuds de transmission et les noeuds de réception

    + **Adressage :** Chaque en-tête Ethernet ajouté à la trame contient l'adresse physique (MAC) qui permet de remettre celle-ci au noeud de destination

### Structure de trame

![Couches 1 et 2](../images/02-12.png?width=700px)

![Couches 1 et 2](../images/02-13.png?width=700px)

+ **Indicateurs de début et de fin de trame:** ils sont utilisés par la sous-couche MAC pour identifier les limites de début et de fin de la trame.

+ **Adressage :** utilisé par la sous-couche MAC pour identifier les noeuds source et de destination.

+ **Type :** permet à la sous-couche LLC pour identifier le protocole de couche 3.

+ **Contrôle :** permet d'identifier les services de contrôle de flux spécifiques.

+ **Données :** contient les données utiles de la trame (le paquet).

+ **Détection d'erreur :** inclus après les données pour constituer la fin de trame, ces champs de trame sont utilisés pour la détection des erreurs.

#### Adresse Mac : identité Ethernet

Une adresse MAC Ethernet de couche 2 est une valeur binaire de 48 bits constituée de 12 chiffres hexadécimaux

L'IEEE demande aux revendeurs de suivre deux règles simples:

- L'adresse doit utiliser dans ses 3 premiers octets l'identifiant unique (OUI) attribué au revendeur.

- Toutes les adresses MAC ayant le même identifiant OUI doivent utiliser une valeur unique dans les 3 derniers octets.

![Couches 1 et 2](../images/02-17.png?width=700px)

#### Traitement des trames

+ Les Adresses MAC attribuées aux stations de travail, aux serveurs, aux imprimantes, aux commutateurs et aux routeurs.

+ Exemples d'adresses MAC: `00-05-9A-3C-78-00`, `00:05:9A:3C:78:00` ou `0005.9A3C.7800`.

+ Le message est transféré à un réseau *Ethernet*: informations d'en-tête jointes au paquet, adresses MAC source et de destination.

+ Chaque carte réseau observe ces informations pour déterminer si l'adresse MAC de destination fournie dans la trame correspond à l'adresse MAC physique du périphérique stockée dans la mémoire vive (RAM).

+ Si elle ne correspond pas, le périphérique rejette la trame.

+ Si elle correspond, la carte réseau transmet la trame aux couches OSI, et la désencapsulation est effectuée.

#### La taille de la trame Ethernet

+ Les normes EthernetII et IEEE802.3 définissent une taille de trame minimale de 64 octets et maximale de 1518 octets.

+ Une trame faisant moins de 64octets est considérée comme «fragment de collision» ou «trame incomplète».

+ Si la trame transmise est plus petite ou plus grande que les limites minimale et maximale, le périphérique récepteur l'ignore.

##### Représentation des adresses MAC

![Couches 1 et 2](../images/02-20.png?width=600px)

##### Adresse MAC *unicast* (monodiffusion)

![Couches 1 et 2](../images/02-21.png?width=600px)

##### Adresse MAC *broadcast* (diffusion)

![Couches 1 et 2](../images/02-22.png?width=600px)

<!-- ##### Adresse MAC de multidiffusion

![Couches 1 et 2](../images/02-23.png?width=600px) -->

## ARP

ARP : **A**ddress **R**esolution **P**rotocol

### Rôle du protocole ARP

+ Le noeud expéditeur a besoin d'un moyen de trouver l'adresse MAC de destination pour une liaison Ethernet donnée

+ Le protocole ARP assure deux fonctions de base :

    + La résolution des adresses IPv4 en adresses MAC

    + La tenue d'une table des mappages

![Couches 1 et 2](../images/02-24.png?width=600px)

+ La table ARP :

    + Sert à trouver l'adresse de la couche liaison de données qui est mappée à l'adresse IPv4 de destination.

    + Quand un noeud reçoit des trames en provenance du support, il enregistre les adresses MAC et IP source dans la table ARP sous forme de mappages.

+ Une requête ARP :

    + Diffusion de couche 2 vers tous les périphériques du réseau local (LAN).

    + Le noeud qui correspond à l'adresse IP de la diffusion répond.

    + Si aucun périphérique ne répond à la requête ARP, le paquet est abandonné du fait qu'il est impossible de créer une trame.

### Fonctionnement du protocole ARP

![Couches 1 et 2](../images/02-25.png?width=600px)

![Couches 1 et 2](../images/02-26.png?width=600px)

![Couches 1 et 2](../images/02-27.png?width=600px)

![Couches 1 et 2](../images/02-28.png?width=600px)

![Couches 1 et 2](../images/02-29.png?width=600px)

![Couches 1 et 2](../images/02-30.png?width=600px)

#### ARP dans les communications à distance

Si l'hôte IPv4 de destination se trouve sur le réseau local, la trame utilise l'adresse MAC de ce périphérique comme adresse MAC de destination.

Si l'hôte IPv4 de destination n'est pas sur le réseau local, l'émetteur utilise la méthode ARP pour déterminer une adresse MAC pour l'interface du routeur qui sert de passerelle.

Si la table ne contient pas d'entrée pour la passerelle, une requête ARP est utilisée pour récupérer l'adresse MAC associée à l'adresseIP de l'interface du routeur.

{{% notice style="tip" title="Astuce : Trouver le fabriquant d'un périphrique du réseau"  %}}
Dans le cas où nous connaissons l'adresse IP d'un équipement sur le réseau (par exemple `192.168.10.50`), comment faire pour connaître le constructeur et déduire la nature de cette machine ?

1. Utiliser la commande `ping` pour communiquer pour la première fois avec cette machine. Cela remplit la table ARP de notre machine avec l'adresse MAC associée à l'adresse `192.168.10.50` : 
```bash
ping 192.168.10.50
``` 

2. Lancer la commande `arp -a` pour afficher la table ARP. Dans la table, indentifier l'adresse MAC associée à l'adresse IP `192.168.10.50`.

3. Récupérer l'adresse MAC et recherchez le fabriquant de ce périphérique sur internet ([exemple de site](https://dnschecker.org/mac-lookup.php)).
{{% /notice %}}
