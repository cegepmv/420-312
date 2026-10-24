+++
pre = '<b>1. </b>'
title = "Ports et sockets"
slug = 'ports-et-sockets'
weight = "510"
draft = false
+++
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

<!-- {{%notice style="info" title="Remarque"%}}
La plage réellement utilisée pour les ports éphémères dépend du système d'exploitation. Par exemple, Linux utilise par défaut la plage `32768` à `60999`.
{{%/notice%}} -->

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

<!-- {{%notice style="note" title="Rappel : NAT"%}}
Au chapitre 4, nous avons vu que le NAT permet à plusieurs postes ayant des adresses privées de partager une même adresse publique. Pour savoir à quel poste renvoyer une réponse, le routeur conserve une table de correspondance qui utilise les **numéros de port** : il peut modifier le port source de chaque conversation pour qu'il reste unique.
{{%/notice%}} -->

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
