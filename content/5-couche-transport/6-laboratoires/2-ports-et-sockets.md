+++
pre = '2- '
title = "Ports et sockets"
slug = '2-ports-et-sockets'
weight = "562"
draft = false
+++
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

<!-- 1. `nc` affiche-t-il une erreur au moment de l'envoi? -->
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

<!-- 
4. Un service ne répond pas. La commande `nc -v` vers son port affiche *Connection refused*. Qu'est-ce que cela indique : la machine est-elle joignable? Le service est-il démarré?

## Validation

+ [ ] Les deux machines communiquent sur le réseau Host-only.
+ [ ] Wireshark capture le trafic sur la carte VMnet1.
+ [ ] Le socket en écoute et son processus sont identifiés avec `ss`.
+ [ ] La paire de sockets d'une connexion TCP est relevée des deux côtés.
+ [ ] Deux connexions simultanées vers le même port sont observées.
+ [ ] La fermeture d'une connexion est observée dans Wireshark.
+ [ ] La réponse à un port TCP fermé (RST) est observée.
+ [ ] La réponse à un port UDP fermé (ICMP) est observée.
+ [ ] L'absence de connexion en UDP est vérifiée avec `ss` et Wireshark.
+ [ ] Les questions de synthèse sont complétées. -->