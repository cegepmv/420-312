# 5. Couche transport
-------------------

Dans le chapitre précédent, nous avons vu que la **couche Internet** permet d'acheminer un paquet jusqu'à l'hôte de destination grâce à l'adresse IP.

Mais un hôte exécute généralement plusieurs applications réseau en même temps : un navigateur, un client de courriel, un service SSH, etc.

Lorsqu'un paquet arrive, comment l'hôte sait-il à **quelle application** les données sont destinées ?

C'est le rôle de la **couche transport**.

Elle permet notamment :

- d'identifier les applications à l'aide de **numéros de port**;
- de faire circuler plusieurs conversations en même temps sur une même interface réseau;
- de découper les données des applications en blocs adaptés au réseau;
- de fournir, selon le protocole utilisé, une communication **fiable** ou une communication **rapide et légère**.

Les deux principaux protocoles de cette couche sont **TCP (*Transmission Control Protocol*)** et **UDP (*User Datagram Protocol*)**.

## Rôle de la couche transport

La couche transport assure la communication **entre deux applications**, alors que la couche Internet assure la communication **entre deux hôtes**.

| Couche | Communication | Identifiant |
|--------|---------------|-------------|
| **Internet** | d'un hôte à un autre hôte | Adresse IP |
| **Transport** | d'une application à une autre application | Numéro de port |

### Suivi des conversations

Chaque échange de données entre une application source et une application de destination est appelé une **conversation**.

Un même hôte peut mener plusieurs conversations simultanément, avec un ou plusieurs hôtes distants.

Exemple :
![Schéma représentant plusieurs conversations](/images/05/05-conversation.png)

La couche transport doit garder ces conversations séparées : les données de l'onglet 1 ne doivent pas être remises au client de courriel.

### Segmentation et reconstitution

Les données d'une application (un fichier, une page web) peuvent être beaucoup trop volumineuses pour être transmises en un seul bloc.

- **À l'envoi**, la couche transport découpe les données en blocs et ajoute un **en-tête** à chacun.
- **À la réception**, elle utilise les informations de l'en-tête pour reconstituer les données et les remettre à la bonne application.

L'unité de données de la couche transport s'appelle :

- un **segment** avec TCP;
- un **datagramme** avec UDP.

![Encapsulation d'un segment](/images/05/05-encapsulation.png)
{{%center%}}
*Encapsulation : les données de l'application sont placées dans un segment, lui-même placé dans un paquet IP, puis dans une trame.*
{{%/center%}}

{{%notice style="note" title="Rappel"%}}
Au chapitre 4, nous avons vu que le champ **Protocole** de l'en-tête IP indique le contenu du paquet : `6` pour TCP et `17` pour UDP. C'est grâce à ce champ que l'hôte de destination sait à quel protocole de transport remettre les données.
{{%/notice%}}

### Multiplexage

Grâce à la segmentation, les segments de plusieurs applications peuvent être **entremêlés** sur la même interface réseau. C'est le **multiplexage**.

![Multiplexage](/images/05/05-multiplexage.png)

À la réception, l'opération inverse (**démultiplexage**) consiste à lire le **port de destination** de chaque segment pour le remettre à la bonne application.

Sans ce mécanisme, une seule application pourrait utiliser le réseau à la fois. Par exemple, un téléchargement monopoliserait le lien et empêcherait de consulter ses courriels en même temps.

{{%notice style="tip" title="À retenir"%}}
L'adresse IP permet d'atteindre le bon **hôte**. Le numéro de port permet d'atteindre la bonne **application** sur cet hôte.
{{%/notice%}}

# 5.1. Ports et sockets
----------------

## Numéros de port

Un **numéro de port** est une valeur de **16 bits** (de `0` à `65535`) qui identifie une application sur un hôte.

Chaque segment TCP et chaque datagramme UDP contient :

- un **port source** : identifie l'application qui envoie;
- un **port de destination** : identifie l'application qui doit recevoir.

### Client et serveur

Un **serveur** attend les requêtes sur un port connu à l'avance. Par exemple, un serveur web écoute sur le port `80`.

Un **client** utilise comme port source un numéro choisi automatiquement par son système d'exploitation, appelé **port éphémère**.

Dans la réponse, les ports sont **inversés** :

![Ports source et destination entre un client et un serveur](/images/05/05-ports-client-serveur.png)

Le port éphémère permet au client de recevoir la réponse dans la bonne application.

## Plages de ports

L'**IANA (*Internet Assigned Numbers Authority*)** est responsable de l'attribution des numéros de port.

| Plage | Nom | Utilisation |
|-------|-----|-------------|
| `0` à `1023` | **Ports réservés** (*well-known*) | Services courants (HTTP, SSH, DNS, etc.) |
| `1024` à `49151` | **Ports enregistrés** | Applications ou services moins universels |
| `49152` à `65535` | **Ports dynamiques** | Ports éphémères des clients |

### Ports à connaître

| Port | Protocole de transport | Service |
|------|------------------------|---------|
| `20`, `21` | TCP | FTP (données, contrôle) |
| `22` | TCP | SSH |
| `23` | TCP | Telnet |
| `25` | TCP | SMTP (envoi de courriels) |
| `53` | UDP et TCP | DNS |
| `67`, `68` | UDP | DHCP (serveur, client) |
| `69` | UDP | TFTP |
| `80` | TCP | HTTP |
| `123` | UDP | NTP (synchronisation de l'heure) |
| `161` | UDP | SNMP |
| `443` | TCP | HTTPS |
| `3389` | TCP | RDP (bureau à distance) |

## Sockets

Un **socket** est l'association d'une **adresse IP** et d'un **numéro de port**, pour un protocole de transport donné.

```text
192.168.1.25:51544    (TCP)
```

Un socket représente l'une des deux extrémités d'une conversation.

Une conversation est donc identifiée par une **paire de sockets** :

```text
Socket client :  192.168.1.25:51544
Socket serveur : 203.0.113.80:80
Protocole :      TCP
```

### Plusieurs conversations vers un même service

Si l'utilisateur ouvre deux onglets vers le même serveur web, les deux conversations utilisent le même socket serveur.

C'est le **port source du client** qui les distingue :

```text
Onglet 1 : 192.168.1.25:51544  ⇄  203.0.113.80:80
Onglet 2 : 192.168.1.25:51545  ⇄  203.0.113.80:80
```

De la même façon, un serveur peut servir des milliers de clients sur le port `80` : chaque paire de sockets est unique.

## Observer les sockets d'un hôte

### Sous Linux : `ss`

```bash
ss -tan     # sockets TCP (t), tous les états (a), adresses numériques (n)
ss -uan     # sockets UDP
sudo ss -tlnp   # sockets TCP en écoute (l), avec le processus associé (p)
```

Exemple :

```text
State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port
LISTEN  0       128            0.0.0.0:22           0.0.0.0:*
ESTAB   0       0         192.168.1.10:22      192.168.1.42:51544
```

- `LISTEN` : une application attend des connexions sur ce port (ici, le serveur SSH);
- `ESTAB` (*established*) : une connexion est établie; la ligne montre la paire de sockets.

### Sous Windows : `netstat`

```powershell
netstat -ano
```

Les colonnes *Adresse locale* et *Adresse distante* indiquent les sockets; la dernière colonne indique le numéro du processus (PID).

{{%notice style="tip" title="À retenir"%}}
**Socket** = adresse IP + port. Une **paire de sockets** identifie une conversation de façon unique.
{{%/notice%}}


## Exercices
----------------

### 1. Classer des ports

Pour chaque port, indiquez sa plage (réservé, enregistré ou dynamique) et, s'il y a lieu, le service associé :

```text
22   53   443   3389   51544   8080   67   60123
```

{{% expand title="Solution" %}}
| Port | Plage | Service |
|------|-------|---------|
| `22` | Réservé | SSH |
| `53` | Réservé | DNS |
| `443` | Réservé | HTTPS |
| `3389` | Enregistré | RDP |
| `51544` | Dynamique | Port éphémère d'un client |
| `8080` | Enregistré | Souvent utilisé comme port HTTP alternatif |
| `67` | Réservé | DHCP (serveur) |
| `60123` | Dynamique | Port éphémère d'un client |
{{% /expand %}}

### 2. Lire une sortie de `ss`

La commande `ss -tan` exécutée sur une machine Linux affiche :

```text
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port
LISTEN  0       128            0.0.0.0:22            0.0.0.0:*
LISTEN  0       511            0.0.0.0:80            0.0.0.0:*
ESTAB   0       0             10.0.0.5:22          10.0.0.42:51544
ESTAB   0       0             10.0.0.5:80          10.0.0.42:51560
ESTAB   0       0             10.0.0.5:80          10.0.0.77:49170
ESTAB   0       0             10.0.0.5:59812       93.184.216.34:443
```

1. Quels services cette machine offre-t-elle?
2. Combien de conversations sont établies?
3. Pour chaque conversation, la machine `10.0.0.5` joue-t-elle le rôle de client ou de serveur? Justifiez.
4. Les lignes 4 et 5 utilisent le même socket local `10.0.0.5:80`. Comment la machine distingue-t-elle ces deux conversations?

{{% expand title="Solution" %}}
1. SSH (port `22`) et HTTP (port `80`) : ce sont les ports à l'état `LISTEN`.
2. Quatre conversations (lignes `ESTAB`).
3. Elle est **serveur** pour les trois premières : le port local est un port réservé (`22`, `80`) et le port distant est éphémère. Elle est **client** pour la dernière : son port local `39812` est éphémère et le port distant `443` est celui d'un serveur HTTPS.
4. Par le socket distant : `10.0.0.42:51560` et `10.0.0.77:49170` sont différents, donc les deux paires de sockets sont différentes.
{{% /expand %}}


# 5.2. TCP
-------------

**TCP (*Transmission Control Protocol*)** est un protocole de transport **fiable** et **orienté connexion**, défini à l'origine dans la [RFC 793](https://datatracker.ietf.org/doc/html/rfc793).

Il ajoute au-dessus d'IP les garanties qu'IP n'offre pas.

| Caractéristique | Signification |
|-----------------|---------------|
| **Orienté connexion** | Une connexion est établie entre les deux hôtes avant l'échange de données, puis fermée à la fin. |
| **Acheminement fiable** | Le récepteur accuse réception des données; les données perdues sont retransmises. |
| **Reconstitution ordonnée** | Les données sont remises à l'application dans l'ordre d'envoi, même si les segments arrivent dans le désordre. |
| **Contrôle de flux** | L'émetteur n'envoie pas plus de données que le récepteur ne peut en accepter. |

{{%notice style="note" title="Rappel"%}}
IP est un protocole **best effort** : il ne garantit ni la livraison, ni l'ordre d'arrivée des paquets (chapitre 4). TCP compense ces limites au niveau des hôtes, sans rien exiger des routeurs.
{{%/notice%}}

## En-tête TCP

![En-tête TCP](/images/05/05-entete-tcp.png)

| Champ | Fonction |
|-------|----------|
| **Port source** | Port de l'application qui envoie le segment |
| **Port destination** | Port de l'application qui doit recevoir le segment |
| **Numéro d'ordre (SEQ)** | Position, dans le flux, du premier octet de données du segment |
| **Numéro d'accusé de réception (ACK)** | Numéro du **prochain octet attendu** de l'autre hôte |
| **Longueur d'en-tête** | Taille de l'en-tête, en mots de 32 bits (minimum `5`, soit 20 octets) |
| **Bits de contrôle** |  Indiquent la fonction du segment |
| **Fenêtre** |  Nombre d'octets que l'émetteur de ce segment peut encore recevoir |
| **Somme de contrôle** | Détection d'erreurs sur l'en-tête et les données |
| **Options** |  Notamment le **MSS**, annoncé à l'ouverture de la connexion |

L'en-tête TCP mesure **au minimum 20 octets**.

{{%notice style="info" title="Remarque"%}}
Les autres champs de l'en-tête (bits réservés, pointeur d'urgence, autres options) ne sont pas étudiés dans ce cours.
{{%/notice%}}

## Bits de contrôle

Les bits de contrôle (*flags*) indiquent la nature du segment. Plusieurs bits peuvent être activés dans un même segment.

| Bit | Nom | Signification |
|-----|-----|---------------|
| **SYN** | *Synchronize* | Demande d'ouverture de connexion |
| **ACK** | *Acknowledgment* | Le champ ACK contient un accusé de réception valide |
| **FIN** | *Finish* | L'émetteur n'a plus de données à envoyer : demande de fermeture |
| **RST** | *Reset* | Interruption immédiate de la connexion, ou refus de connexion |
| **PSH** | *Push* | Les données doivent être remises sans attendre à l'application |



# 5.2.1. Établissement de connexion
-------------

Avant d'échanger des données, le client et le serveur établissent une connexion en trois étapes : c'est le ***3-way handshake***.

![TCP 3-Way Handshake](/images/05/05-tcp-handshake.png)

1. Le client demande l'ouverture de la connexion (**SYN**) et annonce son numéro d'ordre initial `x`.
2. Le serveur accepte : il accuse réception (**ACK** = `x+1`) et demande à son tour l'ouverture dans l'autre sens (**SYN**), avec son propre numéro d'ordre initial `y`.
3. Le client accuse réception (**ACK** = `y+1`).

La connexion est **bidirectionnelle** : chaque hôte peut envoyer des données à l'autre. C'est pourquoi chacun envoie un SYN et chacun reçoit un ACK.

Les numéros d'ordre initiaux `x` et `y` sont choisis **aléatoirement** par chaque hôte.

{{%notice style="info" title="Pourquoi x+1?"%}}
Un segment SYN ne contient pas de données, mais il **compte pour un octet** dans la numérotation. Le prochain octet attendu est donc `x+1`. Il en va de même pour le segment FIN.
{{%/notice%}}

Pendant l'établissement de la connexion, chaque hôte annonce aussi son **MSS (*Maximum Segment Size*)** : la quantité maximale de données qu'il accepte dans un seul segment. 

{{%notice style="tip" title="Dans Wireshark"%}}
Wireshark affiche par défaut des numéros **relatifs** : il soustrait le numéro initial pour que chaque côté commence à `0`. Le handshake apparaît donc ainsi : `SEQ=0`, puis `SEQ=0 ACK=1`, puis `SEQ=1 ACK=1`.
{{%/notice%}}


# 5.2.2. Fiabilité et contrôle de flux
-------------

## Numéros d'ordre et accusés de réception

TCP numérote chaque **octet** envoyé.

- Le **SEQ** d'un segment indique le numéro de son premier octet de données.
- L'**ACK** indique le numéro du **prochain octet attendu**. Il confirme donc la réception de tous les octets précédents.

On a donc toujours :

```text
ACK renvoyé = SEQ reçu + nombre d'octets de données reçus
```

### Exemple

Après le handshake (en numéros relatifs), le client envoie une requête de 100 octets. Le serveur répond avec 500 octets.

![Numéros d'ordre et accusés de réception](/images/05/05-tcp-seq-ack.png)
{{%center%}}
*L'ACK indique le prochain octet attendu : SEQ reçu + nombre d'octets reçus.*
{{%/center%}}

- Le serveur a reçu les octets `1` à `100` : il attend l'octet `101` → `ACK=101`.
- Le client a reçu les octets `1` à `500` : il attend l'octet `501` → `ACK=501`.
- Le prochain segment du client commencera à l'octet `101` → `SEQ=101`.

{{%notice style="tip" title="SEQ différent pour le client et le serveur"%}}
Chaque côté a sa **propre numérotation** : le SEQ du client et le SEQ du serveur évoluent indépendamment.
{{%/notice%}}
## Retransmission

Lorsqu'il envoie un segment, l'émetteur démarre un délai d'attente.

Si l'accusé de réception n'arrive pas avant la fin de ce délai, il considère que le segment est perdu et le **retransmet**.

![Retransmission TCP](/images/05/05-tcp-retransmission.png)
{{%center%}}
*Sans accusé de réception avant la fin du délai, l'émetteur retransmet le segment.*
{{%/center%}}


## Reconstitution ordonnée

Les segments peuvent arriver dans le désordre, par exemple s'ils empruntent des chemins différents.

Le récepteur place les segments reçus dans une **mémoire tampon** et utilise les numéros d'ordre pour les remettre dans le bon ordre avant de transmettre les données à l'application.

## Contrôle de flux : la fenêtre

Le récepteur dispose d'une mémoire tampon de taille limitée.

Dans chaque segment, il indique dans le champ **Fenêtre** combien d'octets il peut encore recevoir. L'émetteur peut envoyer cette quantité de données **sans attendre d'accusé de réception**.


### Exemple

Avec une fenêtre de `3000` octets et un MSS de `1000` octets, l'émetteur peut envoyer 3 segments avant de devoir attendre un ACK :

![Exemple de taille de fenêtre](/images/05/05-taille-de-fenetre.png?width=45rem)


Si le récepteur est surchargé, il annonce une fenêtre plus petite, ce qui ralentit l'émetteur. Une fenêtre de `0` lui demande d'arrêter temporairement d'envoyer :

![Exemple de changement de taille de fenêtre après encombrement du serveur](/images/05/05-changement-taille-de-fenetre.png)

{{%notice style="tip" title="MSS ou fenêtre?"%}}
Le **MSS** limite la taille d'**un** segment; il est annoncé une fois, à l'ouverture de la connexion. La **fenêtre** limite la quantité de données envoyées **sans accusé de réception**; elle est annoncée dans chaque segment et peut varier.
{{%/notice%}}

# 5.2.3. Fermeture de connexion
-------------

Chaque sens de la connexion est fermé séparément.

![Fermeture de connexion TCP](/images/05/05-tcp-fermeture.png)

1. Le client n'a plus de données à envoyer : il envoie un segment **FIN**.
2. Le serveur accuse réception.
3. Lorsqu'il a lui aussi terminé, le serveur envoie son propre **FIN**.
4. Le client accuse réception.

{{%notice style="info" title="Remarque"%}}
En pratique, le serveur combine souvent les étapes 2 et 3 dans un seul segment `FIN, ACK`. On observe alors seulement **trois segments**.
{{%/notice%}}

## Le segment RST

Un segment **RST** interrompt une connexion immédiatement, sans l'échange FIN/ACK.

Le cas le plus courant : un client tente de se connecter à un port sur lequel **aucune application n'écoute**. Le système de l'hôte de destination répond au SYN par un RST.

![Segment RST et refus de connexion](/images/05/05-tcp-rst.png)

{{%notice style="tip" title="À retenir"%}}
Une communication TCP suit toujours trois étapes : **établissement** (SYN, SYN-ACK, ACK), **échange de données** (SEQ/ACK), **fermeture** (FIN/ACK).
{{%/notice%}}


# 5.2.4. Exercices
-------------

### 1 — Compléter un échange

Après l'établissement d'une connexion (numéros relatifs), on observe l'échange suivant. Complétez les valeurs manquantes.

| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 80 |
| 2 | Serveur → Client | `1` | ? | 1200 |
| 3 | Client → Serveur | ? | ? | 0 |
| 4 | Client → Serveur | ? | ? | 40 |
| 5 | Serveur → Client | ? | ? | 0 |

{{% expand title="Solution" %}}
| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 80 |
| 2 | Serveur → Client | `1` | `81` | 1200 |
| 3 | Client → Serveur | `81` | `1201` | 0 |
| 4 | Client → Serveur | `81` | `1201` | 40 |
| 5 | Serveur → Client | `1201` | `121` | 0 |

Le segment 3 ne contient pas de données : il ne fait pas avancer le SEQ du client. Le segment 4 commence donc aussi à `81`.
{{% /expand %}}


### 2 — Identifier un segment

Pour chaque segment, indiquez sa fonction.

1. Bits de contrôle : `SYN`
2. Bits de contrôle : `SYN, ACK`
3. Bits de contrôle : `ACK`, 0 octet de données, juste après un SYN-ACK
4. Bits de contrôle : `PSH, ACK`, 350 octets de données
5. Bits de contrôle : `FIN, ACK`
6. Bits de contrôle : `RST, ACK`, en réponse à un SYN

{{% expand title="Solution" %}}
1. Demande d'ouverture de connexion (étape 1 du handshake).
2. Acceptation de la connexion par le serveur (étape 2).
3. Dernière étape du handshake : la connexion est établie.
4. Envoi de données (avec accusé de réception des données reçues).
5. Demande de fermeture d'un sens de la connexion.
6. Connexion refusée : aucune application n'écoute sur le port demandé.
{{% /expand %}}

### 3 — Raisonner

1. Pourquoi TCP a-t-il besoin d'un échange en trois étapes plutôt qu'en deux?
2. Un client reçoit un segment avec `ACK=5001`. Que peut-il en conclure?
3. Un serveur annonce une fenêtre de `0`. Que doit faire le client?

{{% expand title="Solution" %}}
1. La connexion est bidirectionnelle : chaque hôte doit annoncer son numéro d'ordre initial (SYN) **et** recevoir la confirmation que l'autre l'a reçu (ACK). Le SYN du serveur et son ACK sont combinés dans un seul segment, ce qui donne trois segments au total.
2. L'autre hôte a reçu correctement tous les octets jusqu'au numéro `5000` et attend maintenant l'octet `5001`.
3. Il doit cesser temporairement d'envoyer des données, jusqu'à ce que le serveur annonce une fenêtre plus grande.
{{% /expand %}}



### 4 — Compléter un échange (2)

Après l'établissement d'une connexion (numéros relatifs), on observe l'échange suivant. Complétez les valeurs manquantes.

| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 250 |
| 2 | Serveur → Client | `1` | ? | 0 |
| 3 | Serveur → Client | ? | ? | 900 |
| 4 | Serveur → Client | ? | ? | 600 |
| 5 | Client → Serveur | ? | ? | 0 |
| 6 | Client → Serveur | ? | ? | 30 |
| 7 | Serveur → Client | ? | ? | 0 |

{{% expand title="Solution" %}}
| # | Sens | SEQ | ACK | Données (octets) |
|---|------|-----|-----|------------------|
| 1 | Client → Serveur | `1` | `1` | 250 |
| 2 | Serveur → Client | `1` | `251` | 0 |
| 3 | Serveur → Client | `1` | `251` | 900 |
| 4 | Serveur → Client | `901` | `251` | 600 |
| 5 | Client → Serveur | `251` | `1501` | 0 |
| 6 | Client → Serveur | `251` | `1501` | 30 |
| 7 | Serveur → Client | `1501` | `281` | 0 |

- Le segment 2 ne contient pas de données : le segment 3 commence donc aussi à `SEQ=1`.
- Le segment 4 suit le segment 3 : `1 + 900 = 901`.
- Avec le segment 5, le client confirme avoir reçu les octets `1` à `1500` du serveur (`900 + 600`).
- Le client n'a rien reçu de nouveau entre les segments 5 et 6 : son ACK reste `1501`.
{{% /expand %}}

### 5 — Du handshake à la fermeture

Cette fois, les numéros sont **bruts** : le client a choisi le numéro d'ordre initial `4000` et le serveur, `9000`. Complétez les valeurs manquantes.

| # | Sens | Bits | SEQ | ACK | Données (octets) |
|---|------|------|-----|-----|------------------|
| 1 | Client → Serveur | SYN | `4000` | — | 0 |
| 2 | Serveur → Client | SYN, ACK | `9000` | ? | 0 |
| 3 | Client → Serveur | ACK | ? | ? | 0 |
| 4 | Client → Serveur | PSH, ACK | ? | ? | 120 |
| 5 | Serveur → Client | ACK | ? | ? | 1460 |
| 6 | Serveur → Client | PSH, ACK | ? | ? | 540 |
| 7 | Client → Serveur | ACK | ? | ? | 0 |
| 8 | Client → Serveur | FIN, ACK | ? | ? | 0 |
| 9 | Serveur → Client | ACK | ? | ? | 0 |

{{%notice style="tip" title="Astuce"%}}
Les segments SYN et FIN ne contiennent pas de données, mais chacun **compte pour un octet** dans la numérotation.
{{%/notice%}}

{{% expand title="Solution" %}}
| # | Sens | Bits | SEQ | ACK | Données (octets) |
|---|------|------|-----|-----|------------------|
| 1 | Client → Serveur | SYN | `4000` | — | 0 |
| 2 | Serveur → Client | SYN, ACK | `9000` | `4001` | 0 |
| 3 | Client → Serveur | ACK | `4001` | `9001` | 0 |
| 4 | Client → Serveur | PSH, ACK | `4001` | `9001` | 120 |
| 5 | Serveur → Client | ACK | `9001` | `4121` | 1460 |
| 6 | Serveur → Client | PSH, ACK | `10461` | `4121` | 540 |
| 7 | Client → Serveur | ACK | `4121` | `11001` | 0 |
| 8 | Client → Serveur | FIN, ACK | `4121` | `11001` | 0 |
| 9 | Serveur → Client | ACK | `11001` | `4122` | 0 |

- Le SYN compte pour un octet : `4000 + 1 = 4001` et `9000 + 1 = 9001`.
- Le segment 3 ne contient pas de données : le segment 4 commence donc aussi à `4001`.
- Le segment 6 suit le segment 5 : `9001 + 1460 = 10461`.
- Le client a reçu les octets `9001` à `11000` du serveur (`1460 + 540 = 2000` octets) : il attend l'octet `11001`.
- Le FIN compte pour un octet : le serveur répond `ACK = 4121 + 1 = 4122`.
- Le segment 1 n'a pas de valeur ACK, car le bit ACK n'y est pas activé.
{{% /expand %}}


# 5.3. UDP
-------------

**UDP (*User Datagram Protocol*)** est un protocole de transport **léger**, défini dans la [RFC 768](https://datatracker.ietf.org/doc/html/rfc768).

Comme TCP, il utilise des numéros de port pour identifier les applications. Mais il n'offre **aucune** des autres garanties de TCP.

| Caractéristique | TCP | UDP |
|-----------------|-----|-----|
| Établissement de connexion | Oui (handshake) | **Non** : les données sont envoyées immédiatement |
| Accusés de réception et retransmission | Oui | **Non** : un datagramme perdu est perdu |
| Reconstitution ordonnée | Oui | **Non** : les données sont remises dans l'ordre d'arrivée |
| Contrôle de flux | Oui (fenêtre) | **Non** : un récepteur surchargé abandonne les datagrammes |

{{%notice style="info" title="Remarque"%}}
On décrit généralement UDP par ce qu'il **ne fait pas** par rapport à TCP. Cette simplicité est justement son avantage : moins d'en-tête, aucun échange préalable, aucune attente d'accusé de réception.
{{%/notice%}}

## En-tête UDP

![En-tête UDP](/images/05/05-entete-udp.png)

| Champ | Fonction |
|-------|----------|
| **Port source**  | Port de l'application qui envoie |
| **Port destination** | Port de l'application qui doit recevoir |
| **Longueur** | Taille totale du datagramme (en-tête + données), en octets |
| **Somme de contrôle** | Détection d'erreurs |

L'en-tête UDP mesure **8 octets**, contre **20 octets minimum** pour TCP.

## Fiabilité gérée par l'application

Si une application utilisant UDP a besoin de fiabilité, c'est **l'application elle-même** qui doit la gérer.

Exemple : un client DNS qui ne reçoit pas de réponse à sa requête après un certain délai envoie simplement une nouvelle requête.

## Exemples de services utilisant UDP

- **DNS** (`53`) : requêtes et réponses courtes (chapitre suivant);
- **DHCP** (`67`, `68`) : attribution automatique d'adresses (chapitre suivant);
- **NTP** (`123`) : synchronisation de l'heure;
- **TFTP** (`69`) : transfert de fichiers simplifié;
- la **voix sur IP**, la **visioconférence** et les **jeux en ligne**.


{{%notice style="tip" title="À retenir"%}}
UDP = ports + longueur + somme de contrôle. Pas de connexion, pas d'accusé de réception, pas de retransmission, pas d'ordre.
{{%/notice%}}


# 5.4. Choisir entre TCP et UDP
-------------

Le choix du protocole de transport est fait par les **développeurs de l'application**, selon ses besoins.

| Critère | TCP | UDP |
|---------|-----|-----|
| Fiabilité | Toutes les données arrivent, dans l'ordre | Des données peuvent être perdues ou désordonnées |
| Délai avant le premier envoi | Handshake nécessaire | Aucun |
| Taille de l'en-tête | 20 octets minimum | 8 octets |
| Réaction à une perte | Retransmission (introduit un délai) | Aucune |
| Usage typique | Les données doivent être **complètes et exactes** | Les données doivent être **à temps** |

## Exemples

**TCP** convient lorsqu'il manque une seule donnée et que le résultat est inutilisable :

- une page web (HTTP, HTTPS);
- un courriel (SMTP);
- un transfert de fichier (FTP);
- une session d'administration à distance (SSH).

**UDP** convient lorsqu'une donnée arrivée en retard est aussi inutile qu'une donnée perdue :

- un appel vocal ou vidéo : retransmettre un morceau de son d'il y a une seconde n'aurait aucun sens, il vaut mieux continuer;
- un jeu en ligne : seule la position actuelle des joueurs compte;
- une requête courte (DNS, DHCP, NTP) : établir une connexion coûterait plus cher que l'échange lui-même; en cas de perte, on renvoie simplement la requête.

## DNS : un service qui utilise les deux

Le service **DNS** utilise le port `53` avec les deux protocoles :

- **UDP** pour les requêtes courantes, courtes et nombreuses;
- **TCP** lorsque la réponse est trop volumineuse pour un datagramme, ou pour copier l'ensemble des informations d'un serveur DNS vers un autre.

Le choix dépend donc du **type d'échange**, et pas seulement de l'application.

{{%notice style="tip" title="À retenir"%}}
TCP privilégie la **fiabilité**. UDP privilégie la **rapidité** et la **légèreté**. Aucun n'est meilleur que l'autre : chacun répond à des besoins différents.
{{%/notice%}}


# 5.5. Exercices
-------------

### 1. Choisir le protocole

Pour chaque situation, indiquez le protocole le plus approprié et justifiez votre choix.

1. Téléchargement d'une mise à jour de système d'exploitation.
2. Capteur de température qui envoie une mesure chaque seconde à un serveur.
3. Appel en visioconférence.
4. Envoi d'un formulaire d'inscription sur un site web.
5. Demande d'adresse IP par un poste qui vient de démarrer.
6. Copie d'une base de données vers un serveur de sauvegarde.

{{% expand title="Solution" %}}
1. **TCP** : un fichier incomplet ou corrompu est inutilisable.
2. **UDP** : une mesure perdue n'est pas grave, la suivante arrive une seconde plus tard; l'en-tête léger convient aussi aux petits appareils.
3. **UDP** : une image ou un son retransmis arriverait trop tard; mieux vaut une courte distorsion qu'un délai.
4. **TCP** : toutes les données du formulaire doivent arriver intactes.
5. **UDP** : c'est DHCP; le poste n'a pas encore d'adresse IP et l'échange est court.
6. **TCP** : la copie doit être complète et exacte.
{{% /expand %}}

### 2. Synthèse

1. Expliquez pourquoi un serveur web peut servir plusieurs clients en même temps sur le seul port `80`.
2. Un client envoie un datagramme UDP à un serveur qui est éteint. Le client en est-il informé par UDP? Comparez avec la même situation en TCP.
3. Calculez le nombre d'octets d'en-tête de transport économisés en envoyant 1000 petits messages avec UDP plutôt qu'avec TCP (en-tête TCP de 20 octets).
4. Une application de clavardage doit garantir que les messages arrivent tous et dans l'ordre. Un développeur propose d'utiliser UDP « parce que c'est plus rapide ». Que lui répondez-vous?

{{% expand title="Solution" %}}
1. Chaque conversation est identifiée par une paire de sockets. Le socket du serveur (`IP:80`) est le même, mais le socket de chaque client (adresse IP et port éphémère) est différent.
2. Non : UDP n'attend aucune réponse et n'envoie aucun accusé de réception, donc le client ne sait pas si le datagramme est arrivé. En TCP, le client n'obtiendrait jamais de réponse à son SYN : après plusieurs tentatives, la connexion échouerait et l'application serait informée de l'erreur.
3. `20 × 1000 - 8 × 1000  = 12 000 octets`.
4. Avec UDP, l'application devrait elle-même gérer les accusés de réception, les retransmissions et la remise en ordre, c'est-à-dire réimplémenter TCP. TCP est le choix approprié.
{{% /expand %}}


# 5.6. Laboratoires

# 5.6.1. Introduction à Wireshark
-------------

## Contexte

**Wireshark** est un analyseur de trafic réseau. Il capture les trames qui passent par une interface et décode chaque couche : Ethernet, IP, TCP ou UDP, puis le protocole d'application.

Dans ce laboratoire, vous allez capturer et analyser deux échanges entre votre poste et un serveur web du département :

1. une **requête DNS** (UDP), pour obtenir l'adresse IP du serveur;
2. une **requête HTTP** (TCP), pour obtenir une page web.

Vous observerez ainsi, dans du vrai trafic, les notions vues dans ce chapitre : ports, en-tête TCP et UDP, établissement de connexion, numéros d'ordre et d'accusé de réception, fermeture de connexion.

| Élément | Valeur |
|---------|--------|
| **Serveur web** | `ftp.lan` |
| **Protocole** | HTTP (port `80`) |
| **Poste** | Poste Windows du laboratoire, avec Wireshark |


## 1- Préparation

### 1. Relever la configuration du poste

Dans une invite de commandes :

```powershell
ipconfig /all
```

Complétez le tableau (interface `Ethernet`) :

| Élément | Valeur |
|---------|--------|
| **Adresse IPv4 du poste** | |
| **Adresse MAC du poste** | |
| **Passerelle par défaut** | |
| **Serveur DNS** | |

### 2. Prédire avant d'observer

Avant toute capture, complétez le tableau à partir de ce que vous savez du chapitre.

| Question | Requête DNS | Requête HTTP |
|----------|-------------|--------------|
| Protocole de transport utilisé | | |
| Port de destination | | |
| Plage du port source | | |
| Y aura-t-il un établissement de connexion? | | |

Vous comparerez vos prédictions aux résultats à la fin du laboratoire.

### 3. Démarrer la capture

1. Videz le cache DNS du poste :
    ```powershell
    ipconfig /flushdns
    ```
2. Ouvrez **Wireshark**.
3. Sélectionnez l'interface `Ethernet`
4. Démarrez la capture.
5. Dans l'invite de commandes, exécutez :
    ```powershell
    nslookup ftp.lan
    curl.exe http://ftp.lan/
    ```
6. Arrêtez la capture.

{{%notice style="tip" title="Conseil"%}}
Enregistrez votre capture (**Fichier → Sauvegarder Sous**). Vous pourrez la rouvrir si vous devez poursuivre l'analyse plus tard.
{{%/notice%}}

## 2- Requête DNS (UDP)

### 1. Trouver la requête

Dans la barre de filtre, saisissez :

```text
dns
```

Repérez la requête (*Standard query*) de type **A** pour `ftp.lan`, ainsi que sa réponse (*Standard query response*).

{{%notice style="info" title="Remarque"%}}
Vous pourriez voir plusieurs requêtes : par exemple une de type **A** (adresse IPv4) et une de type **AAAA** (adresse IPv6), ou des requêtes avec le suffixe DNS du réseau ajouté au nom. Concentrez-vous sur la requête de type **A** qui a reçu une réponse contenant une adresse.
{{%/notice%}}

### 2. Analyser la requête

Sélectionnez la requête et développez chaque couche dans le volet de détails.

Complétez le tableau :

| Couche | Champ | Valeur |
|--------|-------|--------|
| **Ethernet II** | MAC source | |
| | MAC destination | |
| **IPv4** | IP source | |
| | IP destination | |
| | Protocole | |
| **UDP** | Port source | |
| | Port destination | |
| | Longueur | |


### 3. Analyser la réponse

Sélectionnez la réponse DNS.

1. Comparez les ports source et destination avec ceux de la requête. Que constatez-vous?
2. Dans la section **Answers** de la réponse DNS, relevez l'adresse IP du serveur web.

### Questions

1. Combien de champs l'en-tête UDP contient-il? Lesquels?
2. Avant la requête DNS, y a-t-il un échange préalable entre le poste et le serveur DNS? Qu'est-ce que cela confirme au sujet d'UDP?

## 3- Connexion TCP

### 1. Isoler la conversation

Remplacez le filtre par (en utilisant l'adresse IP relevée dans la réponse DNS) :

```text
ip.addr == ADRESSE_IP_DU_SERVEUR && tcp.port == 80
```

### 2. Analyser l'établissement de connexion

Repérez les trois premiers segments de la conversation.

Pour chacun, développez l'en-tête TCP puis la section **Flags**. Complétez le tableau :

| Segment | Sens | Port source | Port destination | Bits activés | SEQ (relatif) | ACK (relatif) |
|---------|------|-------------|------------------|--------------|---------------|---------------|
| 1 | | | | | | |
| 2 | | | | | | |
| 3 | | | | | | |

Dans le premier segment, relevez aussi :

- le **numéro d'ordre brut** (*Sequence Number (raw)*) : c'est le numéro initial réel, choisi aléatoirement;
- la valeur du **MSS** annoncée dans les **Options**.

### Questions

1. Le port source du client correspond-il à votre prédiction? Dans quelle plage se trouve-t-il?
2. Dans le segment 2, pourquoi l'ACK vaut-il `1` alors que le segment 1 ne contenait aucune donnée?
3. Comparez le numéro d'ordre brut du client avec celui du serveur. Pourquoi sont-ils différents?

## 4- Échange de données et fermeture

### 1. Vérifier les numéros d'ordre et d'accusé de réception

Repérez le segment qui contient la requête HTTP (`GET / HTTP/1.1`), puis le premier segment de la réponse du serveur.

Complétez :

| Segment | Sens | SEQ | ACK | Longueur des données (*TCP Segment Len*) |
|---------|------|-----|-----|------------------------------------------|
| Requête `GET` | Client → Serveur | | | |
| Réponse du serveur | Serveur → Client | | | |

Vérifiez que :

```text
ACK de la réponse = SEQ de la requête + longueur de la requête
```

### 2. Lire la conversation

Faites un clic droit sur un segment de la conversation, puis **Suivre → TCP Flux**.

Wireshark reconstitue les données échangées : la requête du client et la réponse du serveur.

1. Quelle méthode HTTP le client utilise-t-il?
2. Quel code de réponse le serveur renvoie-t-il?

Fermez la fenêtre (le filtre de la conversation reste appliqué).

### 3. Observer la fermeture

Repérez les derniers segments de la conversation.

1. Quels bits de contrôle sont activés?
2. Combien de segments observez-vous pour la fermeture : trois ou quatre? Expliquez.
3. Qui (client ou serveur) a demandé la fermeture en premier?

### 4. Vue d'ensemble

Ouvrez **Statistiques → Graphique des flux** et cochez **Limiter au filtre d'affichage**.

Wireshark affiche la conversation sous forme de diagramme, semblable à ceux de ce chapitre.

Identifiez-y les trois étapes : établissement, échange de données, fermeture.

## Questions de synthèse

1. Comparez vos résultats avec vos prédictions de la partie 1. Lesquelles étaient correctes?
2. Comparez la taille de l'en-tête UDP de la requête DNS avec celle de l'en-tête TCP du premier segment (champ *Header Length*). Pourquoi l'en-tête TCP est-il plus grand?
3. Combien de segments TCP ont été échangés au total pour obtenir une seule page web? Combien de datagrammes UDP pour la requête DNS? Qu'est-ce que cela illustre?
4. Pourquoi la requête DNS utilise-t-elle UDP, alors que la page web utilise TCP?


# 5.6.2. Ports et sockets
-------------

## Contexte

Dans ce laboratoire, vous allez mettre en place une communication **client/serveur** entre deux machines Linux à l'aide de la commande **`nc` (*netcat*)**.

`nc` permet :

- côté serveur, d'**ouvrir un port** et d'attendre des connexions ou des messages;
- côté client, de **se connecter** à un port et d'envoyer du texte.

Vous observerez les sockets avec `ss` et le trafic avec Wireshark, afin de comparer concrètement TCP et UDP.

## Préparation

### Machines nécessaires

Réutilisez les machines virtuelles des chapitres précédents :

| Machine | Rôle | VM | Configuration |
|---------|------|----|---------------|
| **SERVEUR** | Serveur | `rocky-10.2.ova` | `nmcli` / `firewall-cmd` |
| **CLIENT** | Client | `ubuntu-server-26.04.ova` | `Netplan` |

### Configuration VMware

Dans les paramètres des deux machines, connectez la carte réseau au réseau **Host-only (VMnet1)**.

{{%notice style="info" title="Pourquoi Host-only?"%}}
Contrairement à un *LAN Segment*, le réseau **Host-only** est aussi relié au poste Windows par une carte virtuelle (*VMware Network Adapter VMnet1*). Wireshark, installé sur Windows, peut donc capturer le trafic échangé entre les deux machines virtuelles.
{{%/notice%}}

### Plan d'adressage

Sur le poste Windows, relevez l'adresse de la carte **VMware Network Adapter VMnet1** avec `ipconfig`. Elle devrait avoir l'adresse IP `192.168.20.1`.

Utilisez ensuite le plan d'adressage suivant :

| Machine | Interface | Adresse |
|---------|-----------|---------|
| **Poste Windows** | VMware Network Adapter VMnet1 | `192.168.20.1/24` (déjà configurée) |
| **SERVEUR** | `ens160` | `192.168.20.10/24` |
| **CLIENT** | `ens160` | `192.168.20.20/24` |

1. Configurez **SERVEUR** et **CLIENT** avec `nmcli`, comme au chapitre 4. Aucune passerelle n'est nécessaire.
2. Vérifiez depuis **CLIENT** :
    ```bash
    ping 192.168.20.10
    ```

### Outils

Sur **SERVEUR**, installez `nc` :

```bash
sudo dnf install nmap-ncat
```

Sur **CLIENT** (Ubuntu), `nc` est normalement déjà installé. Sinon :

```bash
sudo apt install netcat-openbsd
```

{{%notice style="tip" title="Pas d'accès Internet?"%}}
Comme au chapitre 4, ajoutez temporairement une carte réseau en **NAT** pour installer les paquets, puis retirez-la.
{{%/notice%}}

### Plusieurs terminaux

Plusieurs commandes doivent rester actives en même temps. Ouvrez donc **plusieurs sessions** sur chaque machine, par exemple avec des connexions SSH depuis le terminal sur le poste Windows :

```bash
ssh student@192.168.20.10
```

### Pare-feu de SERVEUR

Ouvrez les ports utilisés dans ce laboratoire :

```bash
sudo firewall-cmd --add-port=5000/tcp --add-port=5001/udp --add-port=6000/tcp --add-port=6001/udp
```

Vérifiez :

```bash
sudo firewall-cmd --list-ports
```

{{%notice style="info" title="Pourquoi ouvrir 6000 et 6001?"%}}
Aucune application n'écoutera sur ces deux ports. On les ouvre quand même dans le pare-feu pour observer la réaction normale du **système** lorsqu'aucune application n'écoute. Si le port était bloqué, c'est le **pare-feu** qui répondrait à sa place, avec un message différent.
{{%/notice%}}

Ces règles sont temporaires : elles disparaîtront au prochain redémarrage.

### Capture

Sur le poste Windows, démarrez une capture Wireshark sur l'interface **VMware Network Adapter VMnet1**.

## 1- Ouvrir un service TCP

### 1. Lancer le serveur

Sur **SERVEUR** (session 1) :

```bash
nc -l -k 5000
```

- `-l` : écoute (*listen*);
- `-k` : accepte plusieurs connexions et reste ouvert après la fin d'une connexion.

### 2. Observer le socket en écoute

Sur **SERVEUR** (session 2) :

```bash
sudo ss -tlnp
```

Vous devriez obtenir une ligne similaire à :

```text
State   Recv-Q  Send-Q  Local Address:Port   Peer Address:Port  Process
LISTEN  0       10            0.0.0.0:5000        0.0.0.0:*      users:(("nc",pid=6049,fd=4))
```

{{%notice style="info" title="Remarque"%}}
Vous verrez probablement aussi une ligne `[::]:5000` : `nc` écoute également en IPv6. Vous verrez aussi le port `22`, sur lequel le serveur SSH écoute.
{{%/notice%}}

### Questions

1. Dans quel état se trouve le socket du port `5000`?
2. Que signifie l'adresse locale `0.0.0.0`?
3. Quel processus utilise ce port? Quel est son PID?
4. Quel autre service est en écoute sur SERVEUR? Sur quel port?

## 2- Connexion d'un client

### 1. Se connecter

Dans Wireshark, appliquez le filtre :

```text
tcp.port == 5000
```

Sur **CLIENT** (session 1) :

```bash
nc 192.168.20.10 5000
```

Tapez un message, par exemple `bonjour`, puis `Entrée`. Le message doit s'afficher sur SERVEUR.

### 2. Relever la paire de sockets

Sur **SERVEUR** (session 2) et sur **CLIENT** (session 2) :

```bash
ss -tn | grep 5000
```

Complétez le tableau :

| | Adresse IP | Port |
|---|-----------|------|
| **Socket client** | | |
| **Socket serveur** | | |

### 3. Observer dans Wireshark

1. Repérez les trois segments de l'établissement de connexion. Ont-ils été envoyés au moment de la commande `nc` ou au moment où vous avez tapé le message?
2. Repérez le segment contenant votre message. Combien d'octets de données contient-il? Pourquoi un octet de plus que le nombre de lettres tapées?
3. Repérez l'accusé de réception du serveur. Vérifiez la valeur de son ACK.

## 3- Plusieurs clients sur le même port

### 1. Ouvrir une deuxième connexion

Sans fermer la première connexion, sur **CLIENT** (session 3) :

```bash
nc 192.168.20.10 5000
```

Envoyez un message différent.

### 2. Observer les sockets

Sur **SERVEUR** (session 2) :

```bash
ss -tn | grep 5000
```

### 3. Fermer une connexion

Dans la session 1 de **CLIENT**, appuyez sur `Ctrl+C`. Observez les segments correspondants dans Wireshark.

### Questions

1. Combien de connexions établies voyez-vous sur SERVEUR? Quel élément de la paire de sockets est différent?
2. Comment le système de SERVEUR sait-il à quelle connexion appartient chaque message reçu?
3. Quels bits de contrôle observez-vous lors de la fermeture de la première connexion?
4. La deuxième connexion est-elle toujours active? Vérifiez avec `ss`.

Fermez ensuite la deuxième connexion (`Ctrl+C`), puis arrêtez le serveur (`Ctrl+C` dans la session 1 de SERVEUR).

## 4- Port fermé : TCP et UDP

### 1. TCP

Dans Wireshark, appliquez le filtre :

```text
tcp.port == 6000
```

Sur **CLIENT** :

```bash
nc -v 192.168.20.10 6000
```

1. Quels segments observez-vous dans Wireshark? Quels bits de contrôle sont activés dans la réponse de SERVEUR?

### 2. UDP

Dans Wireshark, appliquez le filtre :

```text
udp.port == 6001 || icmp
```

Sur **CLIENT** :

```bash
nc -u 192.168.20.10 6001
```

Tapez un message, puis `Entrée`.

Quels paquets observez-vous dans Wireshark? Développez la réponse de SERVEUR : de quel protocole s'agit-il et quel est son type?

{{%notice style="info" title="Remarque"%}}
Selon la version de `nc`, le client peut se fermer lorsque vous envoyez un deuxième message. Ce n'est pas UDP qui l'en informe : c'est la réception du message **ICMP** envoyé par SERVEUR.
{{%/notice%}}

### Questions

1. En TCP, comment le client apprend-il qu'aucune application n'écoute sur le port?
2. En UDP, le protocole UDP lui-même informe-t-il le client? Quel protocole le fait à sa place?
3. Le message UDP a-t-il été envoyé, même si personne ne pouvait le recevoir? Qu'est-ce que cela montre?

Appuyez sur `Ctrl+C` pour fermer `nc`.

## 5- Service UDP

### 1. Lancer le serveur

Sur **SERVEUR** (session 1) :

```bash
nc -u -l 5001
```

Dans Wireshark, appliquez le filtre :

```text
udp.port == 5001
```

### 2. Envoyer des messages

Sur **CLIENT** (session 1) :

```bash
nc -u 192.168.20.10 5001
```

Envoyez deux ou trois messages.

### 3. Observer

Sur **SERVEUR** (session 2) :

```bash
sudo ss -uanp | grep 5001
```

{{%notice style="info" title="Remarque"%}}
Sur CLIENT, `ss -uan` peut afficher l'état `ESTAB` pour le socket UDP. Cela signifie seulement que `nc` a mémorisé **localement** l'adresse de destination. Aucun échange n'a eu lieu pour l'établir : Wireshark le confirme.
{{%/notice%}}

### Questions

1. Quel est l'état du socket UDP sur SERVEUR? Comparez avec l'état d'un socket TCP en écoute.
2. Observez-vous un établissement de connexion dans Wireshark? Un accusé de réception après chaque message?
3. Combien de paquets ont été nécessaires pour envoyer un message en UDP? Comparez avec la partie 2 en TCP (établissement de connexion, message, accusé de réception).

Fermez `nc` sur les deux machines (`Ctrl+C`).

## Questions de synthèse

1. Expliquez avec vos propres mots la différence entre un **socket** et une **paire de sockets**. Illustrez avec les valeurs relevées dans ce laboratoire.
2. Pourquoi un serveur peut-il accepter plusieurs clients sur un seul port, alors qu'un client utilise un port différent pour chaque connexion?
3. Résumez dans un tableau les différences observées entre TCP et UDP dans ce laboratoire : établissement de connexion, accusés de réception, réaction à un port fermé, état affiché par `ss`.