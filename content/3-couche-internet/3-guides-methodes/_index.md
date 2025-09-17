+++
pre = '<b>A. </b>'
title = "Guides et méthodes"
weight = "320"
+++
-------------------

## Conversion binaire
Pour convertir une adresse IP en binaire, une méthode fonctionne à tous les coups, car sait que chaque nombre de l'adresse IP (4 en tout) est un octet, soit 8 bits.



+ *Exemple :* `192.168.54.12`

1. On commence par le premier nombre : `192`

| Bit | Nombre | Méthode | Valeur|
|-|-|------------|-|
|`128`|`192`|<ul><li>`192 >= 128` ? oui</li><li>On met le bit de `128` à `1`<li>On soustrait `128` à `192` : `192 - 128 = 64`</li><li>On passe au bit suivant</li></ul>  |  `1` |
| `64` |`64` | <ul><li>`64 >= 64` ? oui </li><li>On met le bit de `64` à `1`, <li>On soustrait `64` à `64` : `64 - 64 = 0`</li></ul>| `1` |
| `32` |`0` | Lorsqu'on arrive à `0`, on met les bits restant à `0` jusqu'à avoir 8 bits au total (1 octet)| `0` |
| `16`|`0`|--- | `0` |
| `8` |`0` | ---| `0` |
| `4` |`0` |--- | `0` |
| `2` |`0` | ---| `0` |
| `1` |`0` | ---| `0` |

**Résultat:** `1 1 0 0 0 0 0 0`

2. Deuxième nombre (même procédure) : `168`

| Bit | Nombre | Méthode | Valeur|
|-|-|------------|-|
|`128`|`168`| <ul><li>`168 >= 128` ? oui </li><li>On met le bit de `128` à `1`, <li>On soustrait `128` à `168` : `192 - 168 = 40`</li><li>On passe au bit suivant</li></ul>  |  `1` |
| `64`|`40`|<ul><li>`40 >= 64` ? non </li><li>On met le bit de `64` à `0`<li>On passe au bit suivant</li></ul>| `0` |
| `32`|`40`|<ul><li>`40 >= 32` ? oui </li><li>On met le bit de `32` à `1`, <li>On soustrait `32` à `40` : `40 - 32 = 8`</li><li>On passe au bit suivant</li></ul>| `1` |
| `16`|`8` |<ul><li>`8 >= 16` ? non </li><li>On met le bit de `16` à `0`<li>On passe au bit suivant</li></ul>| `0` |
| `8` |`8` |<ul><li>`8 >= 8` ? oui </li><li>On met le bit de `8` à `1`, <li>On soustrait `8` à `8` : `8 - 8 = 0`</li><li>On passe au bit suivant</li></ul>| `1` |
| `4` |`0` |Lorsqu'on arrive à `0`, on met les bits restant à `0` jusqu'à avoir 8 bits au total (1 octet)| `0` |
| `2` |`0` |---| `0` |
| `1` |`0` |---| `0` |

**Résultat:** `1 0 1 0 1 0 0 0`

3. Troisième nombre (même procédure) : `54` 

| Bit | Nombre | Méthode | Valeur|
|-|-|------------|-|
|`128`|`54`|<ul><li>`54 >= 128` ? non </li><li>On met le bit de `128` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`64` |`54`|<ul><li>`54 >= 64` ? non </li><li>On met le bit de `64` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`32` |`54`|<ul><li>`54 >= 32` ? oui </li><li>On met le bit de `32` à `1`, <li>On soustrait `32` à `54` : `54 - 32 = 22`</li><li>On passe au bit suivant</li></ul>| `1` |
|`16` |`22`|<ul><li>`22 >= 16` ? oui </li><li>On met le bit de `16` à `1` <li>On soustrait `16` à `22` : `22 - 16 = 6`</li><li>On passe au bit suivant</li></ul>| `1` |
|`8`  |`6` |<ul><li>`6 >= 8` ? non </li><li>On met le bit de `8` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`4`  |`6` |<ul><li>`6 >= 4` ? oui </li><li>On met le bit de `4` à `1` <li>On soustrait `4` à `6` : `6 - 4 = 2`</li><li>On passe au bit suivant</li></ul>| `1` |
|`2`  |`2` |<ul><li>`2 >= 2` ? oui </li><li>On met le bit de `2` à `1` <li>On soustrait `2` à `2` : `6 - 4 = 2`</ul>| `1` |
|`1`  |`0` |Lorsqu'on arrive à `0`, on met les bits restant à `0` jusqu'à avoir 8 bits au total (1 octet)| `0` |

**Résultat :** `0 0 1 1 0 1 1 0`

3. Quatrième nombre (même procédure) : `12` 

| Bit | Nombre | Méthode | Valeur|
|-|-|------------|-|
|`128`|`12`|<ul><li>`54 >= 128` ? non </li><li>On met le bit de `128` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`64` |`12`|<ul><li>`12 >= 64` ? non </li><li>On met le bit de `64` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`32` |`12`|<ul><li>`12 >= 32` ? non </li><li>On met le bit de `32` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`16` |`12`|<ul><li>`12 >= 16` ? non </li><li>On met le bit de `16` à `0`<li>On passe au bit suivant</li></ul>| `0` |
|`8`  |`12`|<ul><li>`12 >= 8` ? oui </li><li>On met le bit de `8` à `1`<li>On soustrait `8` à `12` : `12 - 8 = 4`</li><li>On passe au bit suivant</li></ul>| `1` |
|`4`  |`4` |<ul><li>`4 >= 4` ? oui </li><li>On met le bit de `4` à `1`<li>On soustrait `4` à `4` : `4 - 4 = 0`</li></ul>| `1` |
|`2`  |`0` |Lorsqu'on arrive à `0`, on met les bits restant à `0` jusqu'à avoir 8 bits au total (1 octet)| `0` |
|`1`  |`0` |---| `0` |

**Résultat :** `0 0 0 0 1 1 0 0`

|Résultat final|
|:--------------:|
|`1 1 0 0 0 0 0 0`. `1 0 1 0 1 0 0 0`  .   `0 0 1 1 0 1 1 0`   .   `0 0 0 0 1 1 0 0`  |

## Trouver les adresses réseaux, d’hôtes et de diffusion

**Exemple :** `10.101.99.17/23`

### Étape 1
+ Convertir l’IP et le CIDR en binaire (le préfixe représente le nombre de bits à `1` dans le masque de sous-réseau)
+ Séparer la partie réseau de la partie hôte : 
    + Les bits qui sont à `1`dans le masque de sous-réseau/CIDR sont les bits de la partie réseau,
    + Les bits à `0`sont les bits de la partie hôte.

**Adresse IP :** `10.101.99.17/23`

|            |       Partie réseau       | Partie hôte |
|------------|---------------------------|-------------|
|   **IP**   |`00001010.01100101.0110001`| `1.00010001`|
| **Masque** |`11111111.11111111.1111111`| `0.00000000`|

**Masque de sous-réseau obtenu :** `255.255.254.0`



### Étape 2
+ Calculer l’adresse réseau en mettant seulement des `0` à tous les bits de la partie hôte.
|            |       Partie réseau       | Partie hôte |
|------------|---------------------------|-------------|
|   **IP**   |`00001010.01100101.0110001`| `0.00000000`|

**Adresse réseau :** `10.101.98.0/23`

### Étape 3
+ Calculer la **première adresse hôte disponible** (en ajoutant un 1 au bit de poids le plus faible)
|            |       Partie réseau       | Partie hôte |
|------------|---------------------------|-------------|
|   **IP**   |`00001010.01100101.0110001`| `0.00000001`|

**Première adresse hôte disponible :** `10.101.98.1`
 
### Étape 4
+ Calculer l’**adresse de diffusion (broadcast)** (en ajoutant des `1` à tous les bits de la partie hôte)

|            |       Partie réseau       | Partie hôte |
|------------|---------------------------|-------------|
|   **IP**   |`00001010.01100101.0110001`| `1.11111111`|

### Étape 5
+ Calculer la **dernière adresse hôte disponible** (en enlevant `1` à l’adresse boradcast)

|            |       Partie réseau       | Partie hôte |
|------------|---------------------------|-------------|
|   **IP**   |`00001010.01100101.0110001`| `1.11111110`|

### Résultat final 
| Type d'adresse            |   Adresse IP   |
| --------------------- | -------------- |
|Adresse réseau         | `10.101.98.0`  |
|1ère adresse hôte      | `10.101.98.1`  |
|Dernière adresse hôte  | `10.101.99.254`|
|Adresse de diffusion   | `10.101.99.255`|