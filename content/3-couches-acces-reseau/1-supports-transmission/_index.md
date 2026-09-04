+++
pre = '<b>1. </b>'
title = "Supports de transmission"
draft = false
weight = "210"
+++

-----------------------

Pour que deux ordinateurs ou équipements réseau communiquent entre eux, il faut qu'ils soient reliés par quelque chose qui leur permet de transmettre de l'information. Ce quelque chose est ce qu'on appelle un **support de transmission**.

Deux classes de supports de transmission:

- **Supports de transmission guidés (physique) :** les paires torsadées, les câbles coaxiaux, les fibres optiques

- **Support de transmission non-guidés (sans-fil):** les ondes hertziennes


## Supports de transmission guidés

### Câble coaxial

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

### Paire torsadée

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

#### Paires torsadées non blindées (UTP)

***Caractéristiques :***

- Deux ou quatre paires de cuivre entrelacées (torsadées)
- Une enveloppe isolante
- Utilisée à l’origine pour les lignes téléphoniques (2 paires)
- Très utilisée pour les réseaux locaux (4 paires)
- Une longueur maximale de 100 mètres

#### Paires torsadées blindées (STP)

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

### Fibre optique

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

## Supports de transmission non-guidés (sans-fil)

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



