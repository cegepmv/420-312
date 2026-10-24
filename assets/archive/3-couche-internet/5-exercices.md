+++
pre = '<b>4. </b>'
title = "Exercices"
weight = "450"
draft=true
+++
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
