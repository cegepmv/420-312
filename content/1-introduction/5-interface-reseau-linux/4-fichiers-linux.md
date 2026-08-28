+++
title = 'Fichiers réseau Linux'
draft = false
weight = "154"
+++
---------------------

## Le fichier /etc/resolv.conf

Le fichier `/etc/resolv.conf` contient des informations utilisées pour la résolution DNS.

On peut notamment y retrouver :
```bash
nameserver 8.8.8.8
nameserver 8.8.4.4
```
Chaque directive `nameserver` indique un serveur DNS à utiliser.

On peut également retrouver :
```bash
search linux.local
```
qui définit un ou plusieurs domaines de recherche.

{{%notice style="warning" title="Attention"%}}
Sur les distributions Linux modernes, /etc/resolv.conf peut être généré automatiquement par NetworkManager, systemd-resolved ou un autre gestionnaire. Il ne faut donc pas nécessairement modifier ce fichier directement.

Il est généralement préférable de configurer les DNS dans le système de gestion réseau utilisé par la distribution, par exemple **Netplan** ou **NetworkManager**.
{{%/notice%}}


## Le fichier /etc/hosts

Le fichier `/etc/hosts` permet d'associer manuellement des noms d'hôtes à des adresses IP.

Sa syntaxe est : `<adresse IP> <nom>`

Par exemple :
```bash
192.168.230.122 www.example.local
192.168.230.123 serveur1
```
La machine pourra alors résoudre `serveur1` vers `192.168.230.123`

Ce fichier est particulièrement pratique dans un laboratoire ou un petit réseau lorsque l'on ne dispose pas encore d'un serveur DNS.
