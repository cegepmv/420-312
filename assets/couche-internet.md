+++

pre = "<b>1. </b>"

title = "Couche Internet"

weight = "300"

draft = false

+++

**-------------------**

# 3 - Couche Internet

Dans le modèle **TCP/IP**, la **couche Internet** est responsable de l'acheminement des paquets entre différents réseaux.

Elle permet notamment :

- d'identifier les interfaces réseau à l'aide d'**adresses IP**;
- de déterminer si une destination se trouve sur le réseau local ou sur un réseau distant;
- d'acheminer les paquets à travers plusieurs routeurs;
- d'échanger des messages de contrôle, d'erreur et de diagnostic.

Le principal protocole de cette couche est **IP (*Internet Protocol*)**.

Dans ce chapitre, nous étudierons principalement :

- le fonctionnement du protocole **IP**;
- l'adressage **IPv4**;
- les **masques et préfixes CIDR**;
- le calcul de **sous-réseaux**;
- le **VLSM**;
- le fonctionnement du **routage IP**;
- les **tables de routage**;
- le protocole **ICMP**;
- les limitations d'IPv4 et le **NAT**;
- la configuration du réseau sous **Linux**.

---

# 3.1 - Le protocole IP

## Rôle

Le protocole **IP (*Internet Protocol*)** permet d'acheminer des paquets entre différents réseaux.

Contrairement à Ethernet, qui assure principalement la communication sur une liaison ou un réseau local, IP permet d'interconnecter plusieurs réseaux.

```text
Réseau A                 Réseau B                 Réseau C

┌─────────┐              ┌─────────┐              ┌─────────┐
│  Hôte A │─── Routeur ──│ Routeur │─── Routeur ──│  Hôte B │
└─────────┘              └─────────┘              └─────────┘
```

Chaque paquet IP contient notamment :

- une **adresse IP source**;
- une **adresse IP destination**;
- des informations nécessaires à son traitement et à son acheminement.

IP est un protocole **sans connexion** et **best effort**.

Cela signifie qu'IP :

- ne garantit pas que le paquet arrivera à destination;
- ne garantit pas l'ordre d'arrivée des paquets;
- ne garantit pas l'absence de duplication;
- ne retransmet pas automatiquement les paquets perdus.

Les protocoles des couches supérieures, comme **TCP**, peuvent fournir certaines garanties supplémentaires.

---

## Paquet IP

Les données provenant de la couche transport sont encapsulées dans un **paquet IP**.

```text
┌───────────────────────────────────────────────┐
│                  En-tête IP                    │
├───────────────────────────────────────────────┤
│                                                 │
│                  Données                       │
│                                                 │
└───────────────────────────────────────────────┘
```

L'en-tête IP contient notamment les adresses IP source et destination.

Lorsqu'un paquet traverse un routeur, les adresses IP source et destination sont normalement conservées. Certaines fonctions, comme le **NAT**, peuvent toutefois modifier ces adresses.

---

# 3.2 - Adressage IPv4

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

---

## Préfixe CIDR

La notation **CIDR (*Classless Inter-Domain Routing*)** permet d'indiquer le nombre de bits utilisés pour identifier le réseau.

Exemple :

```text
192.168.1.25/24
```

Le `/24` signifie que les **24 premiers bits** appartiennent à la partie réseau.

Les **8 bits restants** appartiennent à la partie hôte.

```text
192.168.1.25/24

11000000.10101000.00000001.00011001
└────────────── 24 bits ──────────────┘└──8──┘
             partie réseau             hôte
```

Le préfixe est donc essentiel pour interpréter une adresse IPv4.

---

# 3.3 - Préfixe et masque de sous-réseau

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

> **À retenir :** le préfixe `/n` indique directement combien de bits sont réservés à la partie réseau. Les `32 - n` bits restants constituent la partie hôte.

---

# 3.4 - Conversion d'une adresse IPv4 en binaire

Pour comprendre le fonctionnement des masques et des sous-réseaux, il est important de savoir convertir les octets IPv4 en binaire.

Les valeurs possibles d'un octet sont :

| Bit | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| --- | --- | -- | -- | -- | - | - | - | - |

Pour convertir un nombre décimal :

1. commencer par `128`;
2. si le nombre restant est supérieur ou égal à la valeur du bit, écrire `1` et soustraire cette valeur;
3. sinon, écrire `0`;
4. continuer jusqu'au bit `1`.

### Exemple : `192`

```text
192 - 128 = 64
64 - 64 = 0
```

Donc :

```text
192 = 11000000
```

### Exemple : `54`

```text
54 < 128  → 0
54 < 64   → 0
54 - 32   = 22 → 1
22 - 16   = 6  → 1
6 < 8           → 0
6 - 4     = 2   → 1
2 - 2     = 0   → 1
0 < 1           → 0
```

Donc :

```text
54 = 00110110
```

### Exemple : `168`

```text
168 - 128 = 40
40 < 64   → 0
40 - 32   = 8  → 1
8 < 16          → 0
8 - 8     = 0   → 1
0 < 4            → 0
0 < 2            → 0
0 < 1            → 0
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
192.168.54.12

11000000.10101000.00110110.00001100
```

---

# 3.5 - Adresse réseau, adresses d'hôtes et broadcast

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
└────────────── partie réseau ──────────────┘
                               └ partie hôte ┘
```

---

## Adresse réseau

Pour trouver l'adresse réseau :

> conserver les bits de la partie réseau et mettre tous les bits de la partie hôte à `0`.

```text
11000000.10101000.00000001.00000000
```

Ce qui donne :

```text
192.168.1.0
```

---

## Première adresse hôte

Dans un sous-réseau IPv4 classique, la première adresse utilisable est généralement l'adresse réseau + 1 :

```text
192.168.1.1
```

---

## Adresse de broadcast

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

## Dernière adresse hôte

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

> Cette méthode correspond au modèle classique des sous-réseaux IPv4. Les préfixes `/31` et `/32` constituent des cas particuliers et ne suivent pas cette règle générale.

---

# 3.6 - Calculer le nombre d'adresses

Le nombre de bits disponibles pour les hôtes dépend du préfixe.

Pour un réseau `/24` :

```text
32 - 24 = 8 bits hôte
```

Le nombre total d'adresses est :

$$2^8 = 256$$

Dans un réseau IPv4 classique, l'adresse réseau et l'adresse de broadcast ne sont pas attribuées à des hôtes.

Il reste donc :

$$256 - 2 = 254$$

adresses utilisables.

### Formules

Pour un réseau IPv4 classique `/n` :

$$\text{bits hôte} = 32 - n$$

$$\text{adresses totales} = 2^{32-n}$$

$$\text{hôtes utilisables} = 2^{32-n} - 2$$

> Pour les exercices classiques de ce chapitre, on utilise cette formule. Les préfixes `/31` et `/32` sont des cas particuliers.

### Exemple : `/26`

```text
32 - 26 = 6 bits hôte
```

Donc :

$$2^6 = 64$$

adresses au total.

Et :

$$64 - 2 = 62$$

hôtes utilisables.

---

# 3.7 - Méthode complète de calcul d'un sous-réseau

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

---

## Étape 1 — Trouver le masque

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

---

## Étape 2 — Convertir l'adresse en binaire

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

---

## Étape 3 — Adresse réseau

Mettre tous les bits hôte à `0` :

```text
00001010.01100101.0110001|0.00000000
```

On obtient :

```text
10.101.98.0
```

---

## Étape 4 — Première adresse hôte

```text
10.101.98.1
```

---

## Étape 5 — Adresse de broadcast

Mettre tous les bits hôte à `1` :

```text
00001010.01100101.0110001|1.11111111
```

On obtient :

```text
10.101.99.255
```

---

## Étape 6 — Dernière adresse hôte

```text
10.101.99.254
```

---

## Résultat

| Type d'adresse         | Adresse           |
| ---------------------- | ------------------ |
| Adresse réseau         | `10.101.98.0/23`   |
| Première adresse hôte  | `10.101.98.1`      |
| Dernière adresse hôte  | `10.101.99.254`    |
| Broadcast              | `10.101.99.255`    |

Il reste 9 bits pour les hôtes :

$$2^9 = 512$$

Donc :

$$512 - 2 = 510$$

adresses hôtes utilisables.

---

# 3.8 - Méthode rapide : taille du bloc

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

$$256 - 192 = 64$$

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

> La méthode de la taille du bloc est particulièrement utile lorsque les calculs deviennent nombreux. La méthode binaire reste toutefois essentielle pour comprendre le fonctionnement des préfixes et des masques.

---

# 3.9 - Créer des sous-réseaux

Un réseau peut être divisé en plusieurs sous-réseaux.

Prenons :

```text
192.168.50.0/24
```

On souhaite créer **8 sous-réseaux de taille égale**.

---

## Étape 1 — Déterminer le nombre de bits à emprunter

On cherche le nombre de bits permettant de créer au moins 8 réseaux :

$$2^3 = 8$$

On emprunte donc **3 bits** à la partie hôte.

Le préfixe devient :

```text
/24 → /27
```

---

## Étape 2 — Trouver le masque

```text
/27 = 255.255.255.224
```

---

## Étape 3 — Calculer la taille du bloc

```text
256 - 224 = 32
```

Les sous-réseaux sont donc espacés de 32 adresses.

---

## Étape 4 — Énumérer les sous-réseaux

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

Chaque réseau possède :

$$2^5 = 32$$

adresses.

Donc :

$$32 - 2 = 30$$

adresses utilisables.

---

## Résultat

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

---

# 3.10 - Déterminer si deux hôtes sont sur le même réseau

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

---

# 3.11 - VLSM

Le **VLSM (*Variable Length Subnet Masking*)** permet de créer des sous-réseaux de **tailles différentes** à partir d'un même réseau.

Sans VLSM, tous les sous-réseaux auraient la même taille.

Avec VLSM, chaque réseau peut recevoir une taille adaptée à ses besoins.

Cela permet de réduire le gaspillage d'adresses IPv4.

---

## Exemple

Une entreprise possède :

```text
192.168.100.0/24
```

Elle doit créer quatre réseaux :

| Service         | Hôtes nécessaires |
| --------------- | ------------------ |
| Administration  | 60                  |
| Développement   | 30                  |
| Formation       | 14                  |
| Gestion         | 6                   |

---

## Étape 1 — Classer les besoins

Toujours commencer par le plus grand réseau :

```text
60
30
14
6
```

---

## Étape 2 — Déterminer le préfixe de chaque réseau

### 60 hôtes

$$2^6 - 2 = 62$$

Il faut donc 6 bits hôte :

```text
/26
```

### 30 hôtes

$$2^5 - 2 = 30$$

Donc :

```text
/27
```

### 14 hôtes

$$2^4 - 2 = 14$$

Donc :

```text
/28
```

### 6 hôtes

$$2^3 - 2 = 6$$

Donc :

```text
/29
```

### Résumé

| Service         | Hôtes | Préfixe | Adresses totales | Hôtes utilisables |
| --------------- | ----- | ------- | ------------------ | ------------------- |
| Administration  | 60    | `/26`   | 64                  | 62                   |
| Développement   | 30    | `/27`   | 32                  | 30                   |
| Formation       | 14    | `/28`   | 16                  | 14                   |
| Gestion         | 6     | `/29`   | 8                   | 6                    |

---

## Étape 3 — Attribuer les sous-réseaux

### Administration

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

### Développement

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

### Formation

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

### Gestion

```text
192.168.100.112/29
```

Plage :

```text
Réseau    : 192.168.100.112
Hôtes     : 192.168.100.113 → 192.168.100.118
Broadcast : 192.168.100.119
```

---

## Résultat final

| Service         | Réseau                | Première adresse | Dernière adresse | Broadcast |
| --------------- | ---------------------- | ------------------ | ------------------- | --------- |
| Administration  | `192.168.100.0/26`     | `.1`                | `.62`                | `.63`     |
| Développement   | `192.168.100.64/27`    | `.65`               | `.94`                | `.95`     |
| Formation       | `192.168.100.96/28`    | `.97`               | `.110`               | `.111`    |
| Gestion         | `192.168.100.112/29`   | `.113`              | `.118`               | `.119`    |

L'espace restant commence à :

```text
192.168.100.120
```

et se termine à :

```text
192.168.100.255
```

---

# 3.12 - Réseau local ou réseau distant ?

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

> Le fonctionnement détaillé d'ARP a déjà été présenté dans le chapitre précédent. ARP permet de déterminer l'adresse MAC correspondant à une adresse IPv4 située sur le réseau local.

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

---

# 3.13 - Routage IP

Le **routage** consiste à déterminer par quel chemin un paquet doit être acheminé pour atteindre sa destination.

Un **routeur** possède généralement plusieurs interfaces réseau, chacune étant connectée à un réseau différent.

```text
              Réseau A
          192.168.1.0/24
                │
                │
           ┌────┴────┐
           │ Routeur │
           └────┬────┘
                │
          Réseau B
          10.0.0.0/24
```

Lorsqu'un routeur reçoit un paquet IP, il consulte sa **table de routage** afin de déterminer où transmettre le paquet.

Le routeur transmet ensuite le paquet par l'interface correspondant à la route sélectionnée.

---

# 3.14 - Table de routage

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

- `default` : route utilisée lorsqu'aucune route plus précise ne correspond;
- `via 192.168.1.1` : passerelle utilisée;
- `dev ens160` : interface utilisée.

La deuxième ligne :

```text
192.168.1.0/24 dev ens160
```

indique que le réseau `192.168.1.0/24` est directement accessible par `ens160`.

---

# 3.15 - Route par défaut

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

---

# 3.16 - Choix d'une route

Une table peut contenir plusieurs routes correspondant à une même destination.

Le système utilise généralement la route ayant le **préfixe le plus spécifique** (*longest prefix match*).

Exemple :

```text
10.0.0.0/8
10.1.0.0/16
10.1.2.0/24
default
```

Pour joindre :

```text
10.1.2.50
```

les trois premières routes correspondent.

Cependant :

```text
/24 > /16 > /8
```

La route :

```text
10.1.2.0/24
```

est donc la plus spécifique.

---

# 3.17 - Configurer une route avec `ip`

La commande `ip` permet de consulter et de modifier la configuration réseau sous Linux.

### Ajouter une route

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254
```

On peut préciser l'interface :

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254 dev ens160
```

La passerelle `192.168.1.254` doit elle-même être accessible par l'interface utilisée.

### Ajouter une route par défaut

```bash
sudo ip route add default via 192.168.1.1
```

### Supprimer une route

```bash
sudo ip route del 10.10.20.0/24
```

### Consulter les routes

```bash
ip route
```

### Déterminer la route utilisée pour une destination

```bash
ip route get 8.8.8.8
```

> Les modifications effectuées directement avec `ip` modifient l'état réseau courant. Elles ne sont généralement pas persistantes après un redémarrage. Une configuration persistante doit être réalisée avec le gestionnaire réseau utilisé par la distribution.

---

# 3.18 - Configurer des routes avec `nmcli`

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

> `ip` modifie l'état réseau courant, tandis que `nmcli` permet de modifier une configuration gérée par **NetworkManager**.

---

# 3.19 - Configurer des routes avec Netplan

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

> La configuration exacte dépend de la distribution, de la version de Netplan et du gestionnaire réseau utilisé. Il faut éviter de configurer simultanément la même interface avec plusieurs gestionnaires réseau.

---

# 3.20 - ICMP

**ICMP (*Internet Control Message Protocol*)** est un protocole utilisé avec IP pour échanger des messages de :

- contrôle;
- erreur;
- diagnostic.

ICMP ne sert pas à transporter directement les données d'une application comme HTTP ou SSH.

---

## `ping`

La commande `ping` utilise notamment des messages **ICMP Echo Request** et **ICMP Echo Reply**.

```bash
ping 192.168.1.1
```

Échange simplifié :

```text
Hôte A                         Hôte B
  │                              │
  │── ICMP Echo Request ────────►│
  │                              │
  │◄── ICMP Echo Reply ──────────│
  │                              │
```

`ping` permet notamment de vérifier :

- si une destination répond;
- si le chemin réseau semble fonctionner;
- le temps de réponse;
- d'éventuelles pertes de paquets.

> L'absence de réponse à `ping` ne signifie pas nécessairement que la machine est hors ligne. Un pare-feu ou une politique réseau peut bloquer les messages ICMP.

---

## `traceroute`

`traceroute` permet d'observer les différents routeurs traversés pour atteindre une destination.

Sous Linux :

```bash
traceroute 8.8.8.8
```

Sous Windows :

```powershell
tracert 8.8.8.8
```

Cette commande est utile pour diagnostiquer les problèmes de routage.

> Selon l'implémentation et les options utilisées, `traceroute` peut utiliser différents types de paquets, notamment UDP ou ICMP.

---

# 3.21 - Limitation d'IPv4

IPv4 utilise des adresses de **32 bits**.

Cela représente :

$$2^{32} = 4\,294\,967\,296$$

valeurs d'adresses possibles, soit environ **4,3 milliards**.

Toutes ces adresses ne sont cependant pas disponibles comme adresses publiques attribuables à des appareils sur Internet.

Avec l'augmentation du nombre d'ordinateurs, de téléphones, de serveurs et d'autres appareils connectés, les adresses IPv4 publiques sont devenues une ressource limitée.

Plusieurs mécanismes permettent de conserver l'utilisation d'IPv4, notamment le **NAT**.

À long terme, **IPv6** fournit un espace d'adressage beaucoup plus vaste.

---

# 3.22 - NAT

Le **NAT (*Network Address Translation*)** permet de traduire des adresses IP lorsqu'un paquet traverse un routeur ou un dispositif de traduction.

Dans les réseaux résidentiels et de nombreuses organisations, le NAT permet notamment à plusieurs appareils utilisant des adresses IPv4 privées de partager une ou plusieurs adresses IPv4 publiques.

Exemple :

```text
Réseau privé

192.168.1.10 ─┐
192.168.1.11 ─┼──► Routeur NAT ───► Internet
192.168.1.12 ─┘       │
                       │
                 IP publique
                 203.0.113.10
```

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

Le NAT n'implique pas nécessairement une traduction de ports. Plusieurs formes de NAT existent.

---

## PAT

Dans les réseaux domestiques et de nombreuses infrastructures, le NAT est généralement associé au **PAT (*Port Address Translation*)**.

Le PAT utilise également les numéros de port afin de permettre à plusieurs connexions internes de partager une même adresse IPv4 publique.

Exemple simplifié :

```text
192.168.1.10:51500 ──┐
                      │
192.168.1.11:51501 ──┼──► Routeur NAT/PAT
                      │       │
192.168.1.12:51502 ──┘        │
                               ▼
                        203.0.113.10
```

Le routeur conserve une table de traduction permettant d'associer les connexions externes aux connexions internes correspondantes.

---

## Connexions entrantes et redirection de port

Une connexion provenant d'Internet vers une machine privée n'est pas automatiquement dirigée vers cette machine.

Le routeur peut être configuré pour effectuer une **redirection de port (*port forwarding*)**.

Exemple :

```text
Internet
    │
    │ TCP 443
    ▼
Routeur
203.0.113.10:443
    │
    │ redirection
    ▼
Serveur
192.168.1.50:443
```

Le routeur transmet alors les connexions reçues sur son port `443` vers le serveur interne.

> Le NAT peut avoir des effets sur l'accessibilité depuis Internet, mais il ne doit pas être considéré comme un mécanisme de sécurité à part entière. La sécurité repose notamment sur les pare-feu, les contrôles d'accès et la configuration des services.

---

# 3.23 - Adresses IPv4 privées

Les principales plages privées IPv4 définies par RFC 1918 sont :

| Plage                                | Préfixe           |
| ------------------------------------- | ------------------ |
| `10.0.0.0` à `10.255.255.255`         | `10.0.0.0/8`        |
| `172.16.0.0` à `172.31.255.255`       | `172.16.0.0/12`     |
| `192.168.0.0` à `192.168.255.255`     | `192.168.0.0/16`    |

Ces adresses sont destinées aux réseaux privés et ne sont pas annoncées comme des adresses IPv4 publiques sur Internet.

---

# 3.24 - IPv4, routage et couches inférieures

Lorsqu'une application communique avec un serveur distant, les différentes couches travaillent ensemble.

Exemple :

```text
Application
    │
    │ données
    ▼
Transport
    │
    │ segment TCP/UDP
    ▼
Internet
    │
    │ paquet IP
    │ source = 192.168.1.10
    │ destination = 8.8.8.8
    ▼
Liaison de données
    │
    │ trame Ethernet
    │ MAC destination = passerelle
    ▼
Physique
    │
    │ bits
    ▼
Support réseau
```

Le paquet IP peut traverser plusieurs réseaux et plusieurs routeurs.

Les informations de couche 2 sont généralement **recréées à chaque liaison**.

Par exemple :

```text
Hôte A             Routeur              Routeur             Serveur
   │                   │                   │                   │
   │ Trame Ethernet    │                   │                   │
   ├──────────────────►│                   │                   │
   │                   │ Nouvelle trame    │                   │
   │                   ├──────────────────►│                   │
   │                   │                   │ Nouvelle trame    │
   │                   │                   ├──────────────────►│
```

Le paquet IP, lui, continue son chemin à travers les différents routeurs.

> Le chapitre précédent a détaillé Ethernet et ARP. Ici, l'idée essentielle est de comprendre que la couche Internet fournit l'adressage logique et l'acheminement, tandis que la couche de liaison assure la transmission sur chaque liaison.

---

# 3.25 - Exercices — Adressage IPv4

## Exercice 1 — Conversion binaire

Convertissez en binaire chaque adresse suivante :

1. `192.168.1.10`
2. `10.20.30.40`
3. `172.16.254.1`
4. `255.255.255.0`

Pour chaque octet, indiquez les calculs utilisés.

---

## Exercice 2 — Préfixe et masque

Complétez le tableau :

| Préfixe | Masque | Bits réseau | Bits hôte |
| ------- | ------ | ------------ | --------- |
| `/8`    | ?      | ?            | ?         |
| `/16`   | ?      | ?            | ?         |
| `/24`   | ?      | ?            | ?         |
| `/25`   | ?      | ?            | ?         |
| `/26`   | ?      | ?            | ?         |
| `/27`   | ?      | ?            | ?         |
| `/28`   | ?      | ?            | ?         |
| `/29`   | ?      | ?            | ?         |
| `/30`   | ?      | ?            | ?         |

---

## Exercice 3 — Calcul d'un sous-réseau

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

---

## Exercice 4 — Calcul avec `/27`

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

---

## Exercice 5 — Calcul binaire complet

Pour :

```text
10.20.35.200/20
```

Effectuez le calcul **en binaire**.

Déterminez :

1. l'adresse IP en binaire;
2. le masque en binaire;
3. la séparation réseau/hôte;
4. l'adresse réseau;
5. la première adresse hôte;
6. la dernière adresse hôte;
7. le broadcast.

---

## Exercice 6 — Même réseau ou réseau différent ?

Déterminez si les deux hôtes appartiennent au même sous-réseau.

| Hôte A               | Hôte B                |
| --------------------- | ---------------------- |
| `192.168.1.10/24`     | `192.168.1.200/24`     |
| `192.168.1.10/25`     | `192.168.1.200/25`     |
| `10.0.5.20/16`        | `10.0.200.30/16`       |
| `172.16.10.15/20`     | `172.16.20.25/20`      |
| `192.168.50.65/26`    | `192.168.50.120/26`    |

Justifiez chaque réponse à l'aide du masque.

---

# 3.26 - Exercices — Création de sous-réseaux

## Exercice 7 — Diviser un `/24`

On dispose du réseau :

```text
192.168.20.0/24
```

On souhaite créer **4 sous-réseaux de taille égale**.

Déterminez :

1. le nouveau préfixe;
2. le masque;
3. le nombre d'adresses par sous-réseau;
4. le nombre d'hôtes utilisables;
5. les quatre adresses réseau;
6. les quatre adresses de broadcast;
7. les plages d'adresses utilisables.

---

## Exercice 8 — Diviser un réseau en 8

On dispose de :

```text
192.168.50.0/24
```

Créez **8 sous-réseaux de taille égale**.

Présentez les résultats sous forme de tableau :

| Réseau | Première adresse | Dernière adresse | Broadcast |
| ------ | ------------------ | ------------------- | --------- |
|        |                    |                     |           |

---

## Exercice 9 — Répondre à un besoin en hôtes

Quel préfixe choisir pour créer un réseau pouvant accueillir au minimum :

1. 10 hôtes;
2. 25 hôtes;
3. 50 hôtes;
4. 100 hôtes;
5. 500 hôtes?

Pour chaque cas, indiquez :

- le nombre de bits hôte;
- le préfixe;
- le masque;
- le nombre d'hôtes utilisables.

---

# 3.27 - Exercices — VLSM

## Exercice 10 — VLSM simple

Une entreprise possède :

```text
192.168.10.0/24
```

Elle doit créer les réseaux suivants :

| Service          | Hôtes nécessaires |
| ----------------- | ------------------- |
| Administration    | 50                   |
| Développement     | 25                   |
| Support           | 12                   |
| Direction         | 5                    |

Utilisez le **VLSM**.

Pour chaque service :

1. déterminez le préfixe nécessaire;
2. déterminez le masque;
3. attribuez une adresse réseau;
4. déterminez la première adresse hôte;
5. déterminez la dernière adresse hôte;
6. déterminez le broadcast.

Présentez le résultat dans un tableau.

---

## Exercice 11 — VLSM et optimisation

Une entreprise dispose du réseau :

```text
10.10.0.0/23
```

Elle doit créer les réseaux suivants :

| Service          | Hôtes nécessaires |
| ----------------- | ------------------- |
| Serveurs          | 100                  |
| Employés          | 60                   |
| Wi-Fi             | 40                   |
| Administration    | 20                   |
| Imprimantes       | 10                   |
| Gestion réseau    | 5                    |

Utilisez le VLSM afin de minimiser l'espace gaspillé.

Vous devez :

1. classer les besoins du plus grand au plus petit;
2. déterminer le préfixe de chaque réseau;
3. attribuer les réseaux sans chevauchement;
4. respecter l'alignement des adresses réseau;
5. calculer les plages d'hôtes;
6. calculer les broadcasts;
7. indiquer l'espace d'adressage restant.

---

# 3.28 - Exercices — Routage

## Exercice 12 — Lire une table de routage

On considère la table suivante :

```text
default via 192.168.1.1 dev ens160
10.0.0.0/8 via 192.168.1.254 dev ens160
10.10.0.0/16 via 192.168.1.253 dev ens160
192.168.1.0/24 dev ens160
```

Pour chacune des destinations suivantes, indiquez quelle route sera utilisée :

1. `8.8.8.8`
2. `10.20.30.40`
3. `10.10.20.30`
4. `192.168.1.50`

Justifiez vos réponses en utilisant le principe du **longest prefix match**.

---

## Exercice 13 — Configuration Linux

Une machine possède :

```text
Adresse IP : 192.168.1.10/24
Passerelle : 192.168.1.1
Interface : ens160
```

On souhaite atteindre le réseau :

```text
10.20.30.0/24
```

via :

```text
192.168.1.254
```

Écrivez les commandes `ip` permettant :

1. d'ajouter la route;
2. de vérifier la table de routage;
3. de vérifier la route choisie pour `10.20.30.10`;
4. de supprimer la route.

---

# 3.29 - Exercice de synthèse

Une entreprise possède le réseau :

```text
192.168.200.0/24
```

Elle possède trois départements :

| Département      | Nombre d'hôtes |
| ------------------ | --------------- |
| Développement     | 70               |
| Administration    | 30               |
| Direction         | 10               |

L'entreprise possède également un routeur connecté à Internet.

### Partie A — VLSM

Utilisez le VLSM pour créer les trois réseaux.

Commencez par le plus grand besoin.

Déterminez :

- le préfixe;
- le masque;
- l'adresse réseau;
- la plage d'hôtes;
- le broadcast.

### Partie B — Routage

Le réseau de Développement est attribué en premier et utilise :

```text
192.168.200.0/25
```

Le routeur possède l'adresse :

```text
192.168.200.1
```

dans ce réseau.

Expliquez pourquoi une machine du réseau Administration ne peut pas communiquer directement avec une machine du réseau Développement au niveau de la couche 2.

Expliquez le rôle du routeur.

### Partie C — Passerelle

En utilisant le plan d'adressage VLSM que vous avez construit, indiquez :

- le réseau du département Administration;
- une adresse IP valide pour la passerelle de ce réseau;
- une adresse IP valide pour une machine d'Administration.

### Partie D — Internet

Expliquez le chemin suivi par un paquet envoyé par cette machine vers :

```text
8.8.8.8
```

Identifiez :

- l'adresse IP source avant le NAT;
- l'adresse IP destination;
- la passerelle;
- le rôle du routeur;
- le rôle du NAT;
- le rôle d'Ethernet sur chaque liaison.

---

# 3.30 - À retenir

## Couche Internet

- La couche Internet permet l'acheminement des paquets entre différents réseaux.
- Le principal protocole est **IP**.
- IP utilise des **adresses source et destination**.
- IP est un protocole **sans connexion** et **best effort**.

## IPv4

- Une adresse IPv4 contient **32 bits**.
- Elle est représentée par quatre octets.
- Le préfixe `/n` indique le nombre de bits appartenant à la partie réseau.
- Les bits restants constituent la partie hôte.
- Le préfixe peut être représenté sous forme de masque.

Exemple :

```text
192.168.1.10/24
```

équivaut à :

```text
192.168.1.10
255.255.255.0
```

## Sous-réseaux

Pour calculer un sous-réseau :

```text
Préfixe
   ↓
Masque
   ↓
Bits réseau / bits hôte
   ↓
Adresse réseau
   ↓
Broadcast
   ↓
Plage d'hôtes
```

Pour une méthode rapide :

```text
Masque
   ↓
Taille du bloc
   ↓
Début du sous-réseau
   ↓
Broadcast
   ↓
Plage d'hôtes
```

## VLSM

Le VLSM permet de créer des sous-réseaux de tailles différentes.

Méthode :

```text
1. Classer les besoins du plus grand au plus petit
2. Déterminer le préfixe nécessaire
3. Respecter l'alignement des réseaux
4. Attribuer les réseaux
5. Calculer les plages d'hôtes
6. Vérifier qu'il n'y a aucun chevauchement
```

## Routage

- Les machines utilisent une **table de routage**.
- Les destinations locales sont généralement atteintes directement.
- Les destinations distantes sont transmises à une **passerelle**.
- La route la plus spécifique est généralement privilégiée.
- La route par défaut est représentée par `0.0.0.0/0`.

## ICMP

- ICMP sert notamment au contrôle, aux erreurs et au diagnostic.
- `ping` utilise notamment ICMP Echo Request/Reply.
- `traceroute` permet d'observer le chemin vers une destination.

## NAT

- IPv4 dispose d'un espace d'adressage limité.
- Les réseaux privés utilisent notamment les plages RFC 1918.
- Le NAT permet de traduire des adresses IP.
- Le PAT utilise également les numéros de port et permet couramment à plusieurs appareils de partager une adresse IPv4 publique.
- Le NAT n'est pas, à lui seul, un mécanisme de sécurité.

## Linux

Les principaux outils étudiés sont :

```bash
ip
nmcli
netplan
```

Ils permettent notamment de consulter et de configurer les interfaces, les adresses IP et les routes.
