+++
pre = '1- '
title = "Établissement de connexion"
slug = '1-etablissement-de-connexion'
weight = "521"
draft = false
+++
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

<!-- Sur un réseau Ethernet, il est généralement de **1460 octets** (1500 octets de données dans une trame Ethernet, moins 20 octets d'en-tête IP et 20 octets d'en-tête TCP). -->

{{%notice style="tip" title="Dans Wireshark"%}}
Wireshark affiche par défaut des numéros **relatifs** : il soustrait le numéro initial pour que chaque côté commence à `0`. Le handshake apparaît donc ainsi : `SEQ=0`, puis `SEQ=0 ACK=1`, puis `SEQ=1 ACK=1`.
{{%/notice%}}
