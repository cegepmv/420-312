# 4. Couche Internet
-------------

Dans le modèle **TCP/IP**, la **couche Internet** est responsable de l'acheminement des paquets entre différents réseaux.

Elle permet notamment :

- d'identifier les interfaces réseau à l'aide d'**adresses IP**;
- de déterminer si une destination se trouve sur le réseau local ou sur un réseau distant;
- d'acheminer les paquets à travers plusieurs routeurs;
- d'échanger des messages de contrôle, d'erreur et de diagnostic.

Le principal protocole de cette couche est **IP (*Internet Protocol*)**.

## Le protocole IP

### Rôle

Le protocole **IP (*Internet Protocol*)** permet d'acheminer des paquets entre différents réseaux.

Contrairement à Ethernet, qui assure principalement la communication sur une liaison ou un réseau local, IP permet d'interconnecter plusieurs réseaux.

![Exemple d'une topologie avec plusieurs réseaux](/images/04-reseaux-routeur.png)

Chaque paquet IP contient notamment :

- une **adresse IP source**;
- une **adresse IP destination**;
- des informations nécessaires à son traitement et à son acheminement.

IP est un protocole **sans connexion** et **best effort (acheminement au mieux)**.

Cela signifie qu'IP :

- ne garantit pas que le paquet arrivera à destination;
- ne garantit pas l'ordre d'arrivée des paquets;
- ne garantit pas l'absence de duplication;
- ne retransmet pas automatiquement les paquets perdus.

Les protocoles des couches supérieures, comme **TCP**, peuvent fournir certaines garanties supplémentaires.

### Paquet IP

Les données provenant de la couche transport sont encapsulées dans un **paquet IP**.

![Entête IP](/04-01-entete-ip.png?width=40rem)

L'en-tête IP contient notamment les adresses IP source et destination.

|Champ| Fonction|
|-----|---------|
|**Version**|Version du paquet IP (pour IPv4: `0100`)|
|**Services différenciés**| Définit la priorité du paquet|. 
|**Time-to-live (TTL)**|  Indique la durée de vie d’un paquet. L’expéditeur définit la valeur de durée de vie initiale et celle-ci diminue à chaque fois que le paquet est traité par un routeur. Si sa valeur arrive à zéro, le routeur rejette le paquet.|
|**Protocole**| Indique le type de données transportées par les couches supérieures. Les valeurs habituelles sont notamment ICMP (1), TCP (6) et UDP (17).|
|**Adresse IP source**| Adresse IP source du paquet|
|**Adresse IP de destination**| Adresse IP de destination du paquet|
|**Longueur d’en-tête**| Indique le la taille de l'entête du paquet (en nombre de mots de 32 bits)|
|**Longueur de paquet**| Indique la taille globale du paquet (y compris l’en-tête et les données) en octets|

# 4.1. Adressage IP
-------------------

Une adresse **IPv4** est une valeur de **32 bits**, généralement représentée sous la forme de quatre nombres décimaux séparés par des points.

Exemple :

```text
192.168.1.25
```

Chaque nombre représente un **octet** et peut avoir une valeur comprise entre `0` et `255`.

Une adresse IPv4 complète contient donc :

```text
4 octets × 8 bits = 32 bits
```

Une adresse IPv4 seule ne permet cependant pas de déterminer quelle partie identifie le réseau et quelle partie identifie l'hôte.

Cette information est fournie par le **préfixe réseau**.


## Préfixe CIDR

La notation **CIDR (*Classless Inter-Domain Routing*)** permet d'indiquer le nombre de bits utilisés pour identifier le réseau.

Exemple :

![Exemple de séparation IP en partie réseau et partie hôte](/images/04/ip-reseau-hote.png?width=35rem)

Le `/24` signifie que les **24 premiers bits** appartiennent à la partie réseau.

Les **8 bits restants** appartiennent à la partie hôte.

Le préfixe est donc essentiel pour interpréter une adresse IPv4.

---

## Préfixe et masque de sous-réseau

Le préfixe CIDR peut également être représenté à l'aide d'un **masque de sous-réseau**.

Par exemple :

```text
/24
```

signifie :

```text
11111111.11111111.11111111.00000000
```

Les bits à `1` correspondent à la **partie réseau**.

Les bits à `0` correspondent à la **partie hôte**.

En notation décimale :

```text
11111111 = 255
11111111 = 255
11111111 = 255
00000000 = 0
```

Donc :

```text
/24 = 255.255.255.0
```

On peut donc représenter la même information de deux façons :

```text
192.168.1.25/24

ou

192.168.1.25
255.255.255.0
```

### Quelques équivalences importantes

| Préfixe | Masque            | Bits réseau | Bits hôte |
| ------- | ------------------ | ----------- | --------- |
| `/8`    | `255.0.0.0`         | 8           | 24        |
| `/16`   | `255.255.0.0`       | 16          | 16        |
| `/24`   | `255.255.255.0`     | 24          | 8         |
| `/25`   | `255.255.255.128`   | 25          | 7         |
| `/26`   | `255.255.255.192`   | 26          | 6         |
| `/27`   | `255.255.255.224`   | 27          | 5         |
| `/28`   | `255.255.255.240`   | 28          | 4         |
| `/29`   | `255.255.255.248`   | 29          | 3         |
| `/30`   | `255.255.255.252`   | 30          | 2         |

{{%notice style="tip" title="À retenir"%}}
Le préfixe `/n` indique directement combien de bits sont réservés à la partie réseau. Les `32 - n` bits restants constituent la partie hôte.
{{%/notice%}}


# 4.1.1. Calcul d'adresses

## Conversion d'une adresse IPv4 en binaire

Pour comprendre le fonctionnement des masques et des sous-réseaux, il est important de savoir convertir les octets IPv4 en binaire.

Les valeurs possibles d'un octet sont :

| Bit | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| --- | --- | -- | -- | -- | - | - | - | - |

Pour convertir un nombre décimal :

1. commencer par `128`;
2. si le nombre restant est supérieur ou égal à la valeur du bit, écrire `1` et soustraire cette valeur;
3. sinon, écrire `0`;
4. continuer jusqu'au bit `1`.

##### Exemple : `192`

```text
192 - 128 = 64
64 - 64 = 0
```

Donc :

```text
192 = 11000000
```

##### Exemple : `54`

```text
54 < 128        → 0
54 < 64         → 0
54 - 32   = 22  → 1
22 - 16   = 6   → 1
6 < 8           → 0
6 - 4     = 2   → 1
2 - 2     = 0   → 1
0 < 1           → 0
```

Donc :

```text
54 = 00110110
```

##### Exemple : `168`

```text
168 - 128 = 40  → 1
40 < 64         → 0
40 - 32   = 8   → 1
8 < 16          → 0
8 - 8     = 0   → 1
0 < 4           → 0
0 < 2           → 0
0 < 1           → 0
```

Donc :

```text
168 = 10101000
```

### Exemple complet

Pour :

```text
192.168.54.12
```

on obtient :

```text
192 = 11000000
168 = 10101000
54  = 00110110
12  = 00001100
```

Donc :

```text
   192  .  168   .   54   .   12

11000000.10101000.00110110.00001100
```


## Adresses réseau, d'hôtes et broadcast

Une adresse IPv4 accompagnée d'un préfixe permet de déterminer plusieurs informations.

Prenons :

```text
192.168.1.25/24
```

Le masque est :

```text
255.255.255.0
```

La partie réseau contient 24 bits et la partie hôte 8 bits.

```text
IP :

11000000.10101000.00000001.00011001

Masque :

11111111.11111111.11111111.00000000
└────── partie réseau ────┘
                          └ partie hôte ┘
```

### Adresse réseau

Pour trouver l'adresse réseau :

> conserver les bits de la partie réseau et mettre tous les bits de la partie hôte à `0`.

```text
11000000.10101000.00000001.00000000
```

Ce qui donne :

```text
192.168.1.0
```

### Première adresse hôte

Dans un sous-réseau IPv4 classique, la première adresse utilisable est généralement l'adresse réseau + 1 :

```text
192.168.1.1
```

### Adresse de broadcast

Pour trouver l'adresse de broadcast :

> conserver les bits de la partie réseau et mettre tous les bits de la partie hôte à `1`.

```text
11000000.10101000.00000001.11111111
```

Ce qui donne :

```text
192.168.1.255
```

---

### Dernière adresse hôte

Dans un sous-réseau IPv4 classique, la dernière adresse utilisable est généralement l'adresse de broadcast - 1 :

```text
192.168.1.254
```

### Résultat

| Type                   | Adresse         |
| ---------------------- | --------------- |
| Adresse réseau         | `192.168.1.0`   |
| Première adresse hôte  | `192.168.1.1`   |
| Dernière adresse hôte  | `192.168.1.254` |
| Broadcast              | `192.168.1.255` |


## Calculer le nombre d'adresses

Le nombre de bits disponibles pour les hôtes dépend du préfixe.

Pour un réseau `/24` :

```text
32 - 24 = 8 bits hôte
```

Le nombre total d'adresses est :
```text
2^8 = 256
```

Dans un réseau IPv4 classique, l'adresse réseau et l'adresse de broadcast ne sont pas attribuées à des hôtes.

Il reste donc :
```text
256 - 2 = 254
```

adresses utilisables.

### Formules

Pour un réseau IPv4 classique `/n` :
```text

bits hôte = 32 - n

adresses totales = 2^{32-n}

hôtes utilisables = 2^{32-n}-2
```

<!-- > Pour les exercices classiques de ce chapitre, on utilise cette formule. Les préfixes `/31` et `/32` sont des cas particuliers. -->

#### Exemple : `/26`

```text
32 - 26 = 6 bits hôte
```

Donc :
```text
2^6 = 64
```

adresses au total.

Et :
```text
64 - 2 = 62
```
hôtes utilisables.



# 4.1.2. Calcul de sous-réseaux

## Méthode complète

Considérons :

```text
10.101.99.17/23
```

Nous voulons déterminer :

- le masque;
- l'adresse réseau;
- la première adresse hôte;
- la dernière adresse hôte;
- l'adresse de broadcast.


##### 1 — Trouver le masque

Le `/23` signifie :

```text
23 bits réseau
32 - 23 = 9 bits hôte
```

Le masque binaire est :

```text
11111111.11111111.11111110.00000000
```

Donc :

```text
255.255.254.0
```

##### 2 — Convertir l'adresse en binaire

```text
10  = 00001010
101 = 01100101
99  = 01100011
17  = 00010001
```

Donc :

```text
10.101.99.17

00001010.01100101.01100011.00010001
```

Le masque :

```text
11111111.11111111.11111110.00000000
```

Les 23 premiers bits sont la partie réseau et les 9 derniers bits sont la partie hôte :

```text
00001010.01100101.0110001|1.00010001
                         ↑
                    séparation
```

##### 3 — Adresse réseau

Mettre tous les bits hôte à `0` :

```text
00001010.01100101.0110001|0.00000000
```

On obtient :

```text
10.101.98.0
```

##### 4 — Première adresse hôte

```text
10.101.98.1
```

##### 5 — Adresse de broadcast

Mettre tous les bits hôte à `1` :

```text
00001010.01100101.0110001|1.11111111
```

On obtient :

```text
10.101.99.255
```

##### 6 — Dernière adresse hôte

```text
10.101.99.254
```

##### Résultat

| Type d'adresse         | Adresse           |
| ---------------------- | ------------------ |
| Adresse réseau         | `10.101.98.0/23`   |
| Première adresse hôte  | `10.101.98.1`      |
| Dernière adresse hôte  | `10.101.99.254`    |
| Broadcast              | `10.101.99.255`    |

Il reste 9 bits pour les hôtes :
```text
2^9 = 512
```
Donc :
```text
512 - 2 = 510
```
adresses hôtes utilisables.

---

## Méthode rapide : taille du bloc

La méthode binaire permet de comprendre le fonctionnement du masque, mais il est possible d'effectuer les calculs plus rapidement.

Prenons :

```text
192.168.10.130/26
```

Le masque est :

```text
255.255.255.192
```

Le dernier octet du masque est `192`.

On calcule :
```text
256 - 192 = 64
```
La taille du bloc est donc **64**.

Les réseaux commencent tous les 64 :

```text
192.168.10.0
192.168.10.64
192.168.10.128
192.168.10.192
```

`130` se trouve dans l'intervalle :

```text
128 → 191
```

Donc :

```text
Adresse réseau :     192.168.10.128
Broadcast :          192.168.10.191
Première adresse :   192.168.10.129
Dernière adresse :   192.168.10.190
```
{{%notice style="tip" title=""%}}
La méthode de la taille du bloc est particulièrement utile lorsque les calculs deviennent nombreux. La méthode binaire reste toutefois essentielle pour comprendre le fonctionnement des préfixes et des masques.
{{%/notice%}}


## Exercices

### 1 — Conversion binaire

Convertissez en binaire chaque adresse suivante :
```text
192.168.1.10
10.20.30.40
172.16.254.1
255.255.255.0
```

Pour chaque octet, indiquez les calculs utilisés.

### 2 — Préfixe et masque

Complétez le tableau :

|Préfixe	|Masque|	Bits réseau|	Bits hôte|
|---------|-----|--------------|-----------|
|**/8**	|?|	?|	?|
|**/16**|	?|	?|	?|
|**/24**|	?|	?|	?|
|**/25**|	?|	?|	?|
|**/26**|	?|	?|	?|
|**/27**|	?|	?|	?|
|**/28**|	?|	?|	?|
|**/29**|	?|	?|	?|
|**/30**|	?|	?|	?|

### 3 — Calcul d'un sous-réseau

Pour :
```text
192.168.10.75/26
```
Déterminez :

1. le masque;
2. le nombre de bits hôte;
3. le nombre total d'adresses;
4. le nombre d'hôtes utilisables;
5. l'adresse réseau;
6. la première adresse hôte;
7. la dernière adresse hôte;
8. l'adresse de broadcast.

### 4 — Calcul avec /27

Pour :
```text
172.16.50.137/27
```
Déterminez :

1. le masque;
2. la taille du bloc;
3. l'adresse réseau;
4. la première adresse hôte;
5. la dernière adresse hôte;
6. l'adresse de broadcast;
7. le nombre d'hôtes utilisables.

### 5 — Calcul binaire complet

Pour :
```text
10.20.35.200/20
```
Effectuez le calcul en binaire.

Déterminez :

1. l'adresse IP en binaire;
2. le masque en binaire;
3. la séparation réseau/hôte;
4. l'adresse réseau;
5. la plage d'adresse d'hôte disponible (première et dernière adresses hôte);
6. le broadcast.


# 4.1.3. Création de sous-réseaux
-----------

Un réseau peut être divisé en plusieurs sous-réseaux.

Prenons :

```text
192.168.50.0/24
```

On souhaite créer **8 sous-réseaux de taille égale**.

##### 1 — Déterminer le nombre de bits à emprunter

On cherche le nombre de bits permettant de créer au moins 8 réseaux :

```text
2^3 = 8
```
On emprunte donc **3 bits** à la partie hôte.

Le préfixe devient :

```text
/24 → /27
```

##### 2 — Trouver le masque

```text
/27 = 255.255.255.224
```


##### 3 — Calculer la taille du bloc

```text
256 - 224 = 32
```

Les sous-réseaux sont donc espacés de 32 adresses.

##### 4 — Énumérer les sous-réseaux

```text
192.168.50.0/27
192.168.50.32/27
192.168.50.64/27
192.168.50.96/27
192.168.50.128/27
192.168.50.160/27
192.168.50.192/27
192.168.50.224/27
```

Chaque réseau possède `2^5 = 32` adresses.

Donc `32 - 2 = 30` adresses utilisables.


##### Résultat

| Réseau                | Plage d'hôtes   | Broadcast |
| ---------------------- | --------------- | --------- |
| `192.168.50.0/27`      | `.1 – .30`      | `.31`     |
| `192.168.50.32/27`     | `.33 – .62`     | `.63`     |
| `192.168.50.64/27`     | `.65 – .94`     | `.95`     |
| `192.168.50.96/27`     | `.97 – .126`    | `.127`    |
| `192.168.50.128/27`    | `.129 – .158`   | `.159`    |
| `192.168.50.160/27`    | `.161 – .190`   | `.191`    |
| `192.168.50.192/27`    | `.193 – .222`   | `.223`    |
| `192.168.50.224/27`    | `.225 – .254`   | `.255`    |

## Déterminer si deux hôtes sont sur le même réseau

Le préfixe permet de déterminer si deux machines appartiennent au même sous-réseau.

Exemple :

```text
Hôte A : 192.168.1.50/26
Hôte B : 192.168.1.100/26
```

Le masque `/26` correspond à :

```text
255.255.255.192
```

Les sous-réseaux possibles sont :

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

`192.168.1.50` appartient à :

```text
192.168.1.0/26
```

`192.168.1.100` appartient à :

```text
192.168.1.64/26
```

Ils appartiennent donc à **deux sous-réseaux différents**.

## Exercices

### 1 — Même réseau ou réseau différent ?

Déterminez si les deux hôtes appartiennent au même sous-réseau.

|Hôte A|	Hôte B|
|-------|-------|
|`192.168.1.10/24`|	`192.168.1.200/24`|
|`192.168.1.10/25`|	`192.168.1.200/25`|
|`10.0.5.20/16`|	`10.0.200.30/16`|
|`172.16.10.15/20`|	`172.16.20.25/20`|
|`192.168.50.65/26`|	`192.168.50.120/26`|

Justifiez chaque réponse à l'aide du masque.


### Création de sous-réseaux

#### 1 — Diviser un /24

On dispose du réseau :
```text
192.168.20.0/24
```

On souhaite créer 4 sous-réseaux de taille égale.

Déterminez :

1. le nouveau préfixe;
2. le masque;
3. le nombre d'adresses par sous-réseau;
4. le nombre d'hôtes utilisables;
5. les quatre adresses réseau;
6. les quatre adresses de broadcast;
7. les plages d'adresses utilisables.

#### 2 — Diviser un réseau en 8

On dispose de :
```text
192.168.50.0/24
```
Créez 8 sous-réseaux de taille égale.

Présentez les résultats sous forme de tableau :

|Réseau|	Première adresse|	Dernière adresse|	Broadcast|
|-----|-----|-----| -----|
| | | | |

#### 3 — Répondre à un besoin en hôtes

Quel préfixe choisir pour créer un réseau pouvant accueillir au minimum :

1. 10 hôtes;
2. 25 hôtes;
3. 50 hôtes;
4. 100 hôtes;
5. 500 hôtes?

Pour chaque cas, indiquez :

+ le nombre de bits hôte;
+ le préfixe;
+ le masque;
+ le nombre d'hôtes utilisables.


# 4.1.4. VLSM

Le **VLSM (*Variable Length Subnet Masking*)** permet de créer des sous-réseaux de tailles différentes à partir d'un même réseau.

Sans VLSM, tous les sous-réseaux auraient la même taille.

Avec VLSM, chaque réseau peut recevoir une taille adaptée à ses besoins.

Cela permet de réduire le gaspillage d'adresses IPv4.

## Exemple

Une entreprise possède :
```text
192.168.100.0/24
```
Elle doit créer quatre réseaux :

|Service|	Hôtes nécessaires|
|--------|--------|
|Administration|	60|
|Développement|	30|
|Formation|	14|
|Gestion|	6|

##### 1 — Classer les besoins

Toujours commencer par le plus grand réseau :

```text
60
30
14
6
```

##### 2 — Déterminer le préfixe de chaque réseau

###### **60 hôtes**
```text
2^6-2=62
```

Il faut donc 6 bits hôte :
```text
/26
```
###### **30 hôtes**
```text
2^5-2=30
```

Donc :
```text
/27
```

###### **14 hôtes**

```text
2^4-2=14
```

Donc :
```text
/28
```

###### **6 hôtes**

```text
2^3-2=6
```

Donc :
```text
/29
```

###### **Résumé**
|Service|	Hôtes|	Préfixe|	Adresses totales|	Hôtes utilisables |
|------|------|------|------|------|
|**Administration**|	60|	/26|	64|	62|
|**Développement**|	30|	/27|	32|	30|
|**Formation**|	14|	/28|	16|	14|
|**Gestion**|	6|	/29|	8|	6|

##### 3 — Attribuer les sous-réseaux

###### **Administration**
```text
192.168.100.0/26
```
Plage :
```text
Réseau    : 192.168.100.0
Hôtes     : 192.168.100.1 → 192.168.100.62
Broadcast : 192.168.100.63
```
Le prochain espace disponible commence à :
```text
192.168.100.64
```
###### **Développement**
```text
192.168.100.64/27
```
Plage :
```text
Réseau    : 192.168.100.64
Hôtes     : 192.168.100.65 → 192.168.100.94
Broadcast : 192.168.100.95
```
Le prochain espace commence à :
```text
192.168.100.96
```

###### **Formation**
```text
192.168.100.96/28
```
Plage :
```text
Réseau    : 192.168.100.96
Hôtes     : 192.168.100.97 → 192.168.100.110
Broadcast : 192.168.100.111
```
Le prochain espace commence à :
```text
192.168.100.112
```
###### **Gestion**
```text
192.168.100.112/29
```
Plage :
```text
Réseau    : 192.168.100.112
Hôtes     : 192.168.100.113 → 192.168.100.118
Broadcast : 192.168.100.119
```
###### **Résultat final**
|Service|	Réseau|	Plage d'adresses hôte	|Broadcast|
|-----|-----|----|----|
|**Administration**|	`192.168.100.0/26`|	`.1` ->	`.62`|	`.63`|
|**Développement**|	`192.168.100.64/27`|	`.65` ->	`.94`|	`.95`|
|**Formation**|	`192.168.100.96/28`|	`.97` ->	`.110`|	`.111`|
|**Gestion**|	`192.168.100.112/29`|	`.113` ->	`.118`|	`.119`|

L'espace restant commence à :
```text
192.168.100.120
```
et se termine à :
```text
192.168.100.255
```

## Exercices

### 1 — VLSM simple

Une entreprise possède :
```text
192.168.10.0/24
```
Elle doit créer les réseaux suivants :

|Service|	Hôtes nécessaires|
|----|-------|
|**Développement**|	25|
|**Administration**|	50|
|**Direction**|	5|
|**Support**|	12|

Utilisez le VLSM.

Pour chaque service :

1. déterminez le préfixe nécessaire;
2. déterminez le masque;
3. attribuez une adresse réseau;
4. déterminez la plage d'adresses hôte disponible (première-dernière adresse hôte);
5. déterminez le broadcast.

Présentez le résultat dans un tableau.

### 2 — VLSM et optimisation

Une entreprise dispose du réseau :
```text
10.10.0.0/23
```
Elle doit créer les réseaux suivants :

|Service|	Hôtes nécessaires|
|-------|-----------------|
|**Employés** |	60 |
|**Serveurs** |	100 |
|**Imprimantes** |	10|
|**Administration** |	20|
|**Wi-Fi** |	40|
|**Gestion réseau** |	5|

Utilisez le VLSM afin de minimiser l'espace gaspillé.

Vous devez :

1. classer les besoins du plus grand au plus petit;
2. déterminer le préfixe de chaque réseau;
3. attribuer les réseaux sans chevauchement;
4. respecter l'alignement des adresses réseau;
5. calculer les plages d'hôtes;
6. calculer les broadcasts;
7. indiquer l'espace d'adressage restant.


# 4.2. Routage IP
-----------

## Réseau local ou distant ?

Lorsqu'un hôte veut communiquer avec une adresse IP, il doit déterminer si la destination se trouve :

- sur le **même réseau**;
- sur un **réseau distant**.

Cette décision est prise à partir de l'adresse IP et du préfixe.

### Destination locale

Supposons :

```text
Hôte A : 192.168.1.10/24
Hôte B : 192.168.1.20/24
```

Les deux hôtes appartiennent au réseau :

```text
192.168.1.0/24
```

A peut donc communiquer directement avec B.

Dans un réseau Ethernet, la trame est envoyée vers la MAC de B.
{{%notice style="note" title="Rappel"%}}
Le fonctionnement détaillé d'ARP a déjà été présenté dans le chapitre précédent. ARP permet de déterminer l'adresse MAC correspondant à une adresse IPv4 située sur le réseau local.
{{%/notice%}}

### Destination distante

Supposons :

```text
Hôte A :     192.168.1.10/24
Passerelle : 192.168.1.1
Serveur :    8.8.8.8
```

`8.8.8.8` n'appartient pas à :

```text
192.168.1.0/24
```

L'hôte transmet donc le paquet à sa **passerelle par défaut**.

```text
Hôte A
192.168.1.10
     │
     │ trame Ethernet
     ▼
Passerelle
192.168.1.1
     │
     ▼
   Internet
     │
     ▼
Serveur
8.8.8.8
```

À cette étape, la destination IP du paquet reste `8.8.8.8`, tandis que l'adresse MAC de destination de la trame Ethernet est celle de la passerelle.


## Routage IP

Le **routage** consiste à déterminer par quel chemin un paquet doit être acheminé pour atteindre sa destination.

Un **routeur** possède généralement plusieurs interfaces réseau, chacune étant connectée à un réseau différent.

![Exemple d'un routeur avec deux interfaces, chacune dans un réseau.](/images/04-routage.png)
{{%center%}}
*Exemple d'un routeur avec deux interfaces, chacune dans un réseau.*
{{%/center%}}


Lorsqu'un routeur reçoit un paquet IP, il consulte sa **table de routage** afin de déterminer où transmettre le paquet.

Le routeur transmet ensuite le paquet par l'interface correspondant à la route sélectionnée.


## Table de routage

Toute machine connectée à un réseau IP possède une **table de routage**.

Elle contient les informations nécessaires pour déterminer comment atteindre différentes destinations.

Sous Linux :

```bash
ip route
```

Sous Windows :

```powershell
route print
```

Exemple :

```text
default via 192.168.1.1 dev ens160
192.168.1.0/24 dev ens160 proto kernel scope link src 192.168.1.10
```

La première ligne :

```text
default via 192.168.1.1 dev ens160
```

signifie :

- `default` : route utilisée lorsqu'aucune route plus précise ne correspond (c'est la passerelle par défaut);
- `via 192.168.1.1` : passerelle utilisée;
- `dev ens160` : interface utilisée.

La deuxième ligne :

```text
192.168.1.0/24 dev ens160
```

indique que le réseau `192.168.1.0/24` est directement accessible par `ens160`.


## Route par défaut

La **route par défaut** indique où envoyer les paquets lorsqu'aucune route plus précise n'existe.

Elle est représentée par :

```text
0.0.0.0/0
```

Exemple :

```text
default via 192.168.1.1 dev ens160
```

Cela signifie :

> Pour toute destination ne correspondant pas à une route plus spécifique, utiliser `192.168.1.1`.



# 4.2.1. Configurer des routes sur Linux
-----------

## Configurer une route avec `ip`

La commande `ip` permet de consulter et de modifier la configuration réseau sous Linux.

###### Ajouter une route

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254
```

On peut préciser l'interface :

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254 dev ens160
```

La passerelle `192.168.1.254` doit elle-même être accessible par l'interface utilisée.

###### Ajouter une route par défaut

```bash
sudo ip route add default via 192.168.1.1
```

###### Supprimer une route

```bash
sudo ip route del 10.10.20.0/24
```

###### Consulter les routes

```bash
ip route
```

###### Déterminer la route utilisée pour une destination

```bash
ip route get 8.8.8.8
```

{{%notice style="note" title="Rappel"%}}
Les modifications effectuées directement avec `ip` modifient l'état réseau courant. Elles ne sont généralement pas persistantes après un redémarrage. Une configuration persistante doit être réalisée avec le gestionnaire réseau utilisé par la distribution (*NetworkManager*, *Netplan* ou autre).
{{%/notice%}}

## Configurer des routes avec nmcli

Sur les distributions utilisant **NetworkManager**, `nmcli` permet de gérer les connexions réseau.

Pour ajouter une route :

```bash
sudo nmcli connection modify ens160 +ipv4.routes "10.10.20.0/24 192.168.1.254"
```

Réactiver la connexion :

```bash
sudo nmcli connection down ens160
sudo nmcli connection up ens160
```

Vérifier la configuration :

```bash
nmcli connection show ens160
ip route
```

Pour supprimer la route :

```bash
sudo nmcli connection modify ens160 -ipv4.routes "10.10.20.0/24 192.168.1.254"
```

## Configurer des routes avec Netplan

Sur Ubuntu, **Netplan** permet de définir la configuration réseau dans des fichiers YAML situés dans :

```text
/etc/netplan/
```

Exemple :

```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 192.168.1.10/24
      routes:
        - to: default
          via: 192.168.1.1
        - to: 10.10.20.0/24
          via: 192.168.1.254
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

La route :

```yaml
- to: 10.10.20.0/24
  via: 192.168.1.254
```

indique que le réseau `10.10.20.0/24` est accessible via `192.168.1.254`.

Pour tester temporairement la configuration :

```bash
sudo netplan try
```

Pour appliquer la configuration :

```bash
sudo netplan apply
```

# 4.3. ICMP

-------------

**ICMP (*Internet Control Message Protocol*)** est un protocole utilisé avec IP pour échanger des messages de contrôle, d'erreur et de diagnostic.

ICMP ne sert pas à transporter directement les données d'une application comme HTTP ou SSH.


## ping

La commande `ping` utilise notamment des messages **ICMP Echo Request** et **ICMP Echo Reply**.

```bash
ping 192.168.1.1
```

Échange simplifié :

![Diagramme d'échange ICMP entre deux hôtes lors d'un ping](/images/04-ping.png)
{{% center %}}
*Échange ICMP lors d'un ping*
{{% /center %}}

`ping` permet notamment de vérifier :

- si une destination répond;
- si le chemin réseau semble fonctionner;
- le temps de réponse;
- d'éventuelles pertes de paquets.

{{%notice style="note" title="Remarque"%}}
L'absence de réponse à `ping` ne signifie pas nécessairement que la machine est hors ligne. Un pare-feu ou une politique réseau peut bloquer les messages ICMP.
{{%/notice%}}


## traceroute

`traceroute` permet d'observer les différents routeurs traversés pour atteindre une destination.

Sous Linux :

```bash
traceroute 8.8.8.8
```

Sous Windows :

```bash
tracert 8.8.8.8
```

Cette commande est utile pour diagnostiquer les problèmes de routage.


{{%notice style="info" title="Note"%}}
Selon l'implémentation et les options utilisées, `traceroute` peut utiliser différents types de paquets, notamment UDP ou ICMP.
{{%/notice%}}

# 4.4. Limitations d'IPv4: IPv6 et NAT
----------

## Limitation d'IPv4

IPv4 utilise des adresses de **32 bits**.

Cela représente : `2^32 = 4,294,967,296`
valeurs d'adresses possibles, soit environ **4,3 milliards**.

Toutes ces adresses ne sont cependant pas disponibles comme adresses publiques attribuables à des appareils sur Internet.

Avec l'augmentation du nombre d'ordinateurs, de téléphones, de serveurs et d'autres appareils connectés, les adresses IPv4 publiques sont devenues une ressource limitée.


## IPv6 : une réponse aux limites d'IPv4


Une solution au problème fondamental de manque d'adresses publique est **IPv6 (*Internet Protocol version 6*)**.

### Un espace d'adressage beaucoup plus vaste

IPv6 utilise des adresses de **128 bits**, contre 32 bits pour IPv4.

Cela représente :

+ **IPv4 :** 2^32  ≈ 4,3 milliards d'adresses
+ **IPv6 :** 2^128 ≈ 340 undecillions d'adresses (340 avec 36 zéros !!)

L'espace d'adressage d'*IPv6* est donc suffisamment vaste pour attribuer des adresses uniques à un très grand nombre d'appareils.

{{%notice style="info" title=" "%}}
*IPv6* ne constitue pas seulement une augmentation du nombre d'adresses. Il a également été conçu avec différentes améliorations au niveau du protocole IP, notamment une simplification de l'en-tête et la possibilité d'utiliser la **configuration automatique des adresses**.
{{%/notice%}}

### Pourquoi n'a-t-il pas déjà remplacé IPv4 ?

On pourrait penser qu'il suffirait de remplacer progressivement *IPv4* par *IPv6*. En pratique, la transition est plus complexe.

Internet repose sur un très grand nombre de réseaux, de routeurs, de serveurs et d'appareils qui doivent pouvoir communiquer entre eux. *IPv4* et *IPv6* ne sont pas directement compatibles : un appareil utilisant uniquement *IPv4* ne peut pas simplement communiquer avec un appareil utilisant uniquement *IPv6*.

La transition vers *IPv6* nécessite donc la coexistence des deux protocoles et l'utilisation de différentes techniques de transition.

De plus, certaines solutions ont permis de prolonger l'utilisation d'IPv4, notamment le **NAT**.

## NAT

Le **NAT (*Network Address Translation*)** permet de traduire des adresses IP lorsqu'un paquet traverse un routeur ou un dispositif de traduction.

Dans les réseaux résidentiels et de nombreuses organisations, le NAT permet notamment à plusieurs appareils utilisant des adresses IPv4 privées de partager une ou plusieurs adresses IPv4 publiques.

Exemple :

![Exemple de fonctionnement d'un routeur NAT](/images/04-NAT.png)
{{%center%}}
*Fonctionnement d'un routeur NAT (traduction de l'adresse privée vers son adresse publique*
{{%/center%}}

Les appareils internes utilisent des adresses privées :

```text
192.168.1.10
192.168.1.11
192.168.1.12
```

Le routeur possède une adresse publique :

```text
203.0.113.10
```

Lorsqu'un appareil interne communique avec Internet, le routeur peut traduire l'adresse IP source du paquet.

## Adresses IPv4 privées

Les principales plages privées IPv4 définies par [RFC 1918](https://www.rfc-editor.org/rfc/rfc1918.html) sont :

| Plage                                | Préfixe           |
| ------------------------------------- | ------------------ |
| `10.0.0.0` à `10.255.255.255`         | `10.0.0.0/8`        |
| `172.16.0.0` à `172.31.255.255`       | `172.16.0.0/12`     |
| `192.168.0.0` à `192.168.255.255`     | `192.168.0.0/16`    |

Ces adresses sont destinées aux réseaux privés et ne sont pas annoncées comme des adresses IPv4 publiques sur Internet.


# 4.5. IPv4, routage et couches inférieures
---------------

Lorsqu'une application communique avec un serveur distant, les différentes couches travaillent ensemble.

Exemple :


![Exemple d'encapsulation d'un paquet IP pour un serveur distant](/images/04-encapsulation-paquet-serveur-distant.png)
{{%center%}}
*Exemple d'encapsulation d'un paquet IP à destination d'un serveur distant (`8.8.8.8`)*
{{%/center%}}


Le paquet IP peut traverser plusieurs réseaux et plusieurs routeurs.

Les informations de couche 2 sont généralement **recréées à chaque liaison**.

Par exemple :

![Schéma illustrant le changement de trame à chaque liaison, contrairement aux adresses IP source et destination qui ne changent pas](/images/04-changement-trame-liaison.png)
{{%center%}}
*Schéma illustrant le changement de trame à chaque liaison, avec les adresses IP source et destination qui ne changent pas*
{{%/center%}}


Le paquet IP, lui, continue son chemin à travers les différents routeurs.

{{%notice style="tip" title=" "%}}
Les chapitres précédents ont détaillé Ethernet et ARP. Ici, l'idée essentielle est de comprendre que **la couche Internet fournit l'adressage logique et l'acheminement**, tandis que **la couche de liaison assure la transmission sur chaque liaison**.
{{%/notice%}}

# 4.6. Laboratoires

# 4.6.1. Introduction à Packet Tracer
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


# 4.6.2. Routage Linux/VMWare
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


## 1- Routage statique

### 1. Configurer CLIENT1 avec nmcli
Configurez l'interface de **CLIENT1** avec `nmcli`

{{%notice style="note" title="Adresse IP et CIDR seulement !"%}}
Ne configurez pas sa passerelle par défaut et son DNS, seulement son adresse IP/préfixe :
```bash
sudo nmcli con mod "CONNECTION" \
    ipv4.method manual \
    ipv4.addresses 10.20.10.10/24
```
Remplacez `CONNECTION` par le nom réel de votre connexion. 

Si aucune connection n'est assignée à l'interface, créez-en une.

{{%/notice%}}

Vérifiez :
```bash
ip a
```

<!-- 
Commencez par identifier la connexion :
```bash
nmcli con show
```
Vous devriez obtenir une connexion associée à l'interface réseau. 
-->

### 2. Configurer CLIENT2 avec Netplan
Configurez l'interface de **CLIENT2** avec `Netplan`.

{{%notice style="note" title="Adresse IP et CIDR seulement !"%}}
Ne configurez pas sa passerelle par défaut et son DNS, seulement son adresse IP/préfixe :
```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 10.20.20.10/24
```
Adaptez `ens160` au nom réel de votre interface
{{%/notice%}}

Vérifiez :
```bash
ip a
```

### 3. Configurer le routeur Linux

Sur **ROUTEUR** configurez les interfaces avec `nmcli` :
1. Interface du *LAN-1* : `10.20.10.1/24` 
2. Interface du *LAN-2* : `10.20.20.1/24`

Vérifiez :
```bash
ip a
```
Puis :
```bash
ip route
```

### 4. Premiers tests

Depuis **CLIENT1** :
```bash
ping 10.20.10.1
```
Depuis **CLIENT2** :
```bash
ping 10.20.20.1
```
Ces tests doivent fonctionner.

Testez ensuite à partir de **CLIENT1** :
```bash
ping 10.20.20.10
```
Le test devrait échouer.

{{%notice style="info" title="Pourquoi ?"%}}
Les deux machines sont sur des réseaux différents et, pour le moment, aucune route permettant à **CLIENT1** d'atteindre `10.20.20.0/24` n'a été configurée sur **CLIENT1**.
{{%/notice%}}

### 5. Ajouter une route statique avec ip
En utilisant la commande `ip`:
1. Sur **CLIENT1**, ajoutez une route statique pour le réseau *LAN-2*. 
2. Sur **CLIENT2**, ajoutez une route statique pour le réseau *LAN-1*. 
3. Sur les deux machines, vérifiez avec `ip route`.
4. À partir de **CLIENT1** testez :
```bash
ping 10.20.20.10
```
Le test peut toutefois encore échouer.
{{%notice style="info" title="Pourquoi ?"%}}
La route permet maintenant à **CLIENT1** d'envoyer le paquet au routeur, mais le routeur doit également être autorisé à transférer les paquets IPv4 entre ses interfaces.
{{%/notice%}}

### 6. Activer le transfert (*forwarding*) IPv4

Sur **ROUTEUR** :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Si la valeur retournée est `0`, le transfert IPv4 est désactivé.

Activez-le temporairement :
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Vérifiez :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Vous devriez obtenir : `1`

### 7. Tester le routage
```text
CLIENT1 : ping 10.20.20.10
CLIENT2 : ping 10.20.10.10
```
Les deux communications devraient maintenant fonctionner.
<!-- 
{{%notice style="info" title="Chemin du paquet"%}}
Le chemin d'un paquet de **CLIENT1** vers **CLIENT2** est :
```text
CLIENT1
   |
   | 192.168.10.10
   v
ROUTEUR
192.168.10.1
   |
   | 192.168.20.1
   v
CLIENT2
```
Le routeur reçoit le paquet sur `ens160` et le transmet sur `ens224`.
{{%/notice%}} -->

### 8. Observer le chemin avec traceroute

Depuis **CLIENT1** :
```bash
traceroute 10.20.20.10
```
Le résultat devrait montrer au minimum le routeur comme intermédiaire.

{{%notice style="tip" title="traceroute non installé ?"%}}
Si `traceroute` n'est pas installé, ajoutez temporairement un adaptateur réseau en **NAT** ou **Bridged** (pour avoir une connexion internet), puis installez :
```bash
sudo apt install traceroute # Debian/Ubuntu
sudo dnf install traceroute # Rocky/CentOS/RHEL
```
{{%/notice%}}

### 9. Configuration permanente 
La commande `ip route add` modifie la configuration courante du noyau. Elle est donc utile pour expérimenter, mais la route peut disparaître après un redémarrage.

De même lorsqu'on active le transfert (*forwarding*) des paquets IPv4 sur le routeur avec la commande : 
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Il ne sera actif que temporairement.

#### Ajouter les routes avec les outils de configuration

1. Supprimez les routes configurées avec `ip`
2. Configurez les routes de manière persistante en utilisant :
    + `nmcli` sur **CLIENT1** 
    + `Netplan` sur **CLIENT2**.
3. Vérifiez votre configuration avec `ip route`.

#### Activer le routage IPv4

Pour activer le *forwarding* IPv4 de façon permanente, ajoutez au fichier `/etc/sysctl.conf` du routeur la ligne : 
```text
net.ipv4.ip_forward=1
```
Ensuite, pour activer les changements : 
```bash
sudo sysctl -p /etc/sysctl.conf
```

### 10. Tests finaux
Depuis **CLIENT1** :
```bash
ping 10.20.10.1
ping 10.20.20.1
ping 10.20.20.10
```

Depuis **CLIENT2** :
```bash
ping 10.20.20.1
ping 10.20.10.1
ping 10.20.10.10
```




## 2- Routes par défaut
-------------

Dans la partie précédente, chaque machine possédait une route spécifique vers l'autre réseau.

Cette approche devient rapidement difficile à gérer lorsque le réseau possède plusieurs sous-réseaux.

Nous allons donc utiliser une **route par défaut**.

### 1. Routes par défaut

En utilisant `nmcli` sur **CLIENT1** et `Netplan` sur **CLIENT2**: 
1. Supprimez la route spécifique
2. Ajoutez une route par défaut
3. Vérifiez avec `ip route`

{{%notice style="info" title="Tables de routage"%}}
La table de routage de **CLIENT1** devrait maintenant contenir une entrée similaire à :
```bash
default via 10.20.10.1
10.20.10.0/24 dev ens160
```
Tout paquet dont la destination ne correspond pas à une route plus spécifique sera envoyé vers : `10.20.10.1`

La table de routage de **CLIENT2** devrait maintenant contenir une entrée similaire à 
```bash
default via 10.20.20.1
10.20.20.0/24 dev ens160
```
{{%/notice%}}

{{%notice style="tip" title="Comprendre le changement"%}}
La route par défaut signifie :

> « Si aucune route plus précise ne correspond à la destination, envoyer le paquet à cette passerelle. »

Le routeur devient ainsi la **passerelle par défaut** des clients.
{{%/notice%}}

### 2. Tests

Depuis **CLIENT1** :
```bash
ping 10.20.20.10
```
Depuis **CLIENT2** :
```bash
ping 10.20.10.10
```
Puis :
```bash
traceroute 10.20.20.10
```

Observez le chemin emprunté par les paquets.



# 4.7. Atelier synthèse

## Mise en situation

Une entreprise souhaite concevoir le réseau informatique de son organisation.

L’entreprise dispose du bloc d’adresses privé suivant :

```text
172.16.0.0/16
```
Elle est composée de quatre départements :

| Département                  | Nombre d’hôtes requis |
| ---------------------------- | --------------------: |
| **Direction**                |                   100 |
| **Ressources humaines (RH)** |                    50 |
| **Informatique (IT)**        |                    25 |
| **Finance**                  |                    10 |

L’entreprise dispose de **deux routeurs**.

Chaque routeur dessert deux départements :

+ **Routeur R1**
  + réseau de la Direction
  + réseau des RH
  + liaison WAN vers R2
+ **Routeur R2**
  + réseau de l’IT
  + réseau de la Finance
  + liaison WAN vers R1

La liaison entre les deux routeurs est une liaison **point à point**.

### Topologie logique

![Topologie de l'atelier synthèse](/images/04-atelier-synthese-topologie.png)


L’objectif est de concevoir le réseau, puis de l’implémenter et de le tester dans **Cisco Packet Tracer**.

# 4.7.1. Plan d’adressage avec VLSM
-------------

### 1. Analyse des besoins

À partir du nombre d’hôtes requis pour chaque département, déterminez pour chacun :

1. Le nombre minimal de bits nécessaires pour les hôtes.
2. Le nombre total d'adresses disponibles dans le sous-réseau.
3. Le préfixe CIDR approprié.
4. Le masque de sous-réseau correspondant.

Complétez le tableau :

| Département     | Hôtes requis | Bits hôtes nécessaires | Préfixe | Nombre d’adresses |
| --------------- | -----------: | ---------------------: | ------: | ----------------: |
| **Direction**   |          100 |                        |         |                   |
| **RH**          |           50 |                        |         |                   |
| **IT**          |           25 |                        |         |                   |
| **Finance**     |           10 |                        |         |                   |
| **WAN**         |            2 |                        |         |                   |

{{%notice style="tip" title="Rappel"%}}
Pour un réseau *IPv4* classique, deux adresses sont réservées : l’adresse réseau et l’adresse de diffusion (broadcast).
{{%/notice%}}

### 2. Déterminer l’ordre d’allocation

Avec la méthode **VLSM**, les sous-réseaux doivent être attribués en commençant généralement par les besoins les plus importants.

Classez donc les réseaux du plus grand au plus petit.

### 3. Construire le plan d’adressage

À partir du bloc :
```text
172.16.0.0/16
```
Découpez l’espace d’adressage à l’aide de **VLSM**.

Pour chaque réseau, déterminez :

* l’adresse réseau ;
* le préfixe CIDR ;
* le masque de sous-réseau ;
* la plage d'adresses hôte (1ère -> dernière adresse hôte)
* l’adresse de broadcast ;
* l’adresse à utiliser comme passerelle par défaut.

### Tableau à compléter

| Réseau        | Besoin | Adresse réseau | Préfixe | Masque | Plage d'adresses | Broadcast | Passerelle |
| ------------- | -----: | -------------- | ------- | ------ | ---------------- | --------- | ---------- |
| **Direction** |    100 |                |         |        |                  |           |            |
| **RH**        |     50 |                |         |        |                  |           |            |
| **IT**        |     25 |                |         |        |                  |           |            |
| **Finance**   |     10 |                |         |        |                  |           |            |
| **WAN R1–R2** |      2 |                |         |        |                  |           |            |

### Consigne importante

Pour chacun des quatre réseaux locaux, **la première adresse hôte doit être attribuée à l’interface du routeur** et servir de passerelle par défaut.

Par exemple, si un réseau est :
```text
172.16.0.0/25
```

alors la passerelle devra être :
```text
172.16.0.1
```

# 4.7.2. Implémentation Packet Tracer

### 1. Construire la topologie

Dans *Cisco Packet Tracer*, créez une topologie comprenant :

+ **2 routeurs** : R1 et R2 ;
+ **4 commutateurs** ;
+ au moins **1 ordinateur par département** ;
+ une liaison WAN entre R1 et R2.

La topologie doit respecter la structure suivante :

![Topologie de l'atelier sur Packet Tracer](/images/04-atelier-synthese-topologie-packet-tracer.png)


R1 doit donc posséder **trois interfaces** :
* une interface vers la Direction ;
* une interface vers les RH ;
* une interface vers la liaison WAN.

R2 doit également posséder **trois interfaces** :
* une interface vers l’IT ;
* une interface vers Finance ;
* une interface vers la liaison WAN.

### 2. Élaborer le plan d’adressage des équipements

Avant de configurer une seule interface dans *Packet Tracer*, vous devez établir le **plan d’adressage complet de la topologie**.

À partir du plan *VLSM* réalisé dans la partie précédente, attribuez une adresse IPv4 à chaque interface réseau des équipements et construisez un tableau récapitulatif permettant de vérifier l'ensemble du plan d'adressage.

|Équipement   |	Interface / NIC      |	Adresse IPv4|	Préfixe|	Masque|	Passerelle|
|-------------|----------------------|--------------|--------|--------|-----------|
|**R1**       | GigabitEthernet0/0/0 |	            |        |        |     —     |
|**R1**       | GigabitEthernet0/0/1 |	            |        |        |     —     |
|**R1**       | GigabitEthernet0/0/2 |              |        |        |     —     |
|**R2**       | GigabitEthernet0/0/0 |	            |        |        |     —     |
|**R2**       | GigabitEthernet0/0/1 |	            |        |        |     —     |
|**R2**       | GigabitEthernet0/0/2 |              |        |        |     —     |
|**PC-DIR-01**| NIC                  |              |        |        |           |	
|**PC-RH-01** | NIC	                 |              |        |        |           |
|**PC-IT-01** | NIC	                 |              |        |        |           |
|**PC-FIN-01**| NIC	                 |              |        |        |           |
	
	
Vous êtes libre de choisir les adresses des postes à l’intérieur de la plage d’adresses hôtes disponible, à condition de respecter les règles suivantes :

+ L’adresse ne doit pas être l’adresse réseau ;
+ L’adresse ne doit pas être l’adresse de broadcast ;
+ L’adresse ne doit pas être déjà attribuée à une autre interface ;
+ Le poste doit utiliser comme passerelle l’adresse de l’interface du routeur correspondant à son réseau.
+ La passerelle doit correspondre à **la première adresse hôte du sous-réseau**.

{{%notice style="tip" title="Conseils"%}}
Utilisez une convention d’adressage cohérente. Par exemple, vous pouvez attribuer la première adresse hôte au routeur, puis commencer les postes à la deuxième adresse hôte.
{{%/notice%}}

Vérification avant configuration

Avant de poursuivre, vérifiez que :
+ [ ] Chaque interface possède une adresse unique.
+ [ ] Chaque adresse appartient au bon sous-réseau.
+ [ ] Les interfaces des routeurs utilisent la première adresse hôte de leur réseau.
+ [ ] Chaque PC utilise une adresse valide de son sous-réseau.
+ [ ] Chaque PC utilise la bonne passerelle.
+ [ ] Les deux interfaces de la liaison WAN appartiennent au même sous-réseau.
+ [ ] Aucune adresse réseau n'est attribuée à un équipement.
+ [ ] Aucune adresse de broadcast n'est attribuée à un équipement.
+ [ ] Aucun sous-réseau ne chevauche un autre.

{{%notice style="note" title="Important"%}}
Faites valider votre plan d’adressage avant de commencer la configuration des équipements.
{{%/notice%}}

### 2. Configurer les interfaces des routeurs

Configurez les interfaces de R1 et R2 à partir du plan d’adressage réalisé établi à l'étape précédente.

Pour chaque interface :
1. attribuez l’adresse IPv4 appropriée ;
2. configurez le masque ;
3. activez l’interface ;
4. vérifiez son état.


### 3. Configurer les postes clients

Configurez chaque poste avec les informations déterminées dans votre plan d’adressage.

Pour chaque poste, configurez :
+ son adresse IPv4 ;
+ son masque de sous-réseau ;
+ sa passerelle par défaut.

### 4. Configurer le routage

Les quatre réseaux sont situés sur des sous-réseaux différents.

Un poste de la Direction doit donc pouvoir communiquer avec un poste du réseau IT, même si ces deux réseaux sont directement connectés à des routeurs différents.

Configurez le **routage statique** entre R1 et R2 :
  + R1 doit connaître les réseaux situés derrière R2.
  + R2 doit connaître les réseaux situés derrière R1.

1. Sur R1, ajoutez les routes nécessaires vers le réseau **IT** et le réseau **Finance**.
2. Sur R2, ajoutez les routes nécessaires vers le réseau **Direction** et le réseau **RH**.
    Utilisez la syntaxe :
    ```bash
    ip route <réseau_destination> <masque> <prochain_saut>
    ```
3. Sur chacun des routeurs, affichez la table de routage :
    ```text
    show ip route
    ```
    Identifiez :
    + les réseaux directement connectés ;
    + les routes statiques ;
    + l’interface ou le prochain saut utilisé pour chaque réseau distant.

    Vous devez être capable d'expliquer pourquoi R1 sait atteindre le réseau IT et pourquoi R2 sait atteindre le réseau Direction.

### 5. Tester la communication

Effectuez les tests suivants.

###### Test 1 — Communication dans un même réseau

Depuis un poste de la Direction, effectuez un `ping` vers la passerelle :

```text
ping <adresse_de_la_passerelle>
```

Le test doit réussir.

###### Test 2 — Communication entre deux réseaux du même routeur

Depuis un poste de la Direction, testez la communication avec un poste du réseau RH.

```text
ping <adresse_IP_du_poste_RH>
```

Observez le chemin emprunté par les paquets.

###### Test 3 — Communication entre les deux routeurs

Depuis R1, testez l’adresse WAN de R2 :

```text
ping <adresse_WAN_de_R2>
```

Faites également le test inverse depuis R2.

###### Test 4 — Communication entre deux réseaux situés derrière des routeurs différents

Depuis un poste de la Direction, testez :

```text
ping <adresse_IP_du_poste_IT>
```

Puis :

```text
ping <adresse_IP_du_poste_Finance>
```

Ces communications doivent traverser **R1 → liaison WAN → R2**.

### 6. Observer le chemin avec traceroute

Utilisez la commande :

```text
tracert <adresse_IP_destination>
```

depuis un poste.

Observez le nombre de sauts nécessaires pour atteindre un réseau situé derrière l’autre routeur.

###### **Question**

Pourquoi le paquet passe-t-il par la passerelle du réseau local avant d’atteindre le réseau de destination ?

###  7. Validation finale

Avant de terminer l’atelier, vérifiez les éléments suivants :

+ [ ]  Les quatre sous-réseaux ont été calculés avec VLSM.
+ [ ]  Aucun sous-réseau ne chevauche un autre.
+ [ ]  L’espace d’adressage `172.16.0.0/16` est utilisé efficacement.
+ [ ]  La première adresse hôte de chaque LAN est utilisée comme passerelle.
+ [ ]  Les interfaces de R1 et R2 sont correctement configurées.
+ [ ]  La liaison WAN entre R1 et R2 fonctionne.
+ [ ]  Les tables de routage contiennent les routes nécessaires.
+ [ ]  Les postes utilisent la bonne passerelle.
+ [ ]  Les communications entre les quatre départements fonctionnent.
+ [ ]  `show ip route` permet d'observer les routes configurées.
+ [ ]  `tracert` permet d'observer le passage par les routeurs.


### Questions de synthèse

Répondez aux questions suivantes en vous basant sur votre configuration.

1. Pourquoi ne peut-on pas attribuer directement `172.16.0.0/16` à tous les postes de l’entreprise?
2. Quel est l’avantage d’utiliser *VLSM* plutôt que de créer quatre sous-réseaux de même taille?
3. Pourquoi la liaison entre **R1** et **R2** utilise-t-elle un sous-réseau beaucoup plus petit que les réseaux des départements?
4. Lorsqu’un poste de la Direction communique avec un poste du réseau **IT**, quelle est la première passerelle utilisée?
5. Pourquoi **R1** a-t-il besoin d’une route vers le réseau **IT** alors que **R1** ne possède aucune interface directement connectée à ce réseau?
6. Quelle différence y a-t-il entre :
    + un réseau directement connecté ;
    + une route statique ;
    + une passerelle par défaut ?
7. À partir de la table de routage de **R1**, expliquez comment le routeur détermine par quelle interface transmettre un paquet destiné au réseau **IT**.
8. Que se passerait-il si le poste de la **Direction** avait une mauvaise passerelle par défaut?
9. Que se passerait-il si la route vers le réseau **IT** était présente sur **R1**, mais que la route de retour vers le réseau Direction était absente sur **R2**?
10. Expliquez le trajet complet d’un paquet envoyé par un poste de la **Direction** vers un poste du réseau **Finance**, en identifiant:
    + le poste source ;
    + la passerelle par défaut ;
    + R1 ;
    + la liaison WAN ;
    + R2 ;
    + le réseau de destination ;
    + le poste destination.
