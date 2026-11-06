+++
pre = '<b>0. </b>'
title = "Environnement de laboratoire"
weight = "601"
draft = false
+++
-------------------
 
Cette section décrit le réseau utilisé dans **tous les laboratoires du chapitre**. Les machines sont créées une seule fois et réutilisées d'une section à l'autre. Revenez-y au besoin.
 
## Topologie
 
![Topologie du réseau du chapitre 6](/images/06/06-topologie.svg?width=45rem)
 
Le réseau `172.16.10.0/24` est un *LAN Segment* nommé **LAN-SERVICES**. La machine `dhcp-router` le relie au poste Windows et à Internet par son interface **WAN**.
 
## Machines
 
| Machine | VM | Cartes réseau | Configuration IP | Requise à partir de |
|---------|----|---------------|------------------|---------------------|
| **dhcp-router** | `rocky-10.2.ova` | `ens160` → NAT (WAN)<br>`ens224` → LAN-SERVICES | WAN : automatique<br>LAN : `172.16.10.1/24` | 6.1 |
| **dns-server** | `rocky-10.2.ova` | `ens160` → LAN-SERVICES | `172.16.10.2/24` (statique) | 6.1 |
| **client** | `ubuntu-desktop-26.04.ova` | `ensX` → LAN-SERVICES | Automatique (DHCP) | 6.2 |
| **web-server** | `ubuntu-server-26.04.ova` | `ens33` → LAN-SERVICES | `172.16.10.3/24` (statique) | 6.4 |
 
{{%notice style="note" title="Important"%}}
Les noms d'interfaces peuvent varier. Identifiez-les avec `ip a` et adaptez les commandes à votre environnement.
{{%/notice%}}
 
## Préparer les machines
 
### Noms d'hôtes
 
À la création de chaque machine, attribuez-lui son nom :
 
```bash
sudo hostnamectl set-hostname dns-server    # par exemple
```
 
### Paquets
 
Les machines du *LAN Segment* n'ont pas accès à Internet tant que le routeur n'est pas configuré (section 6.2). Installez donc **dès la création** les paquets utilisés dans tout le chapitre :
 
| Machine | Commande |
|---------|----------|
| **dhcp-router** | `sudo dnf install kea tcpdump nmap-ncat` |
| **dns-server** | `sudo dnf install bind bind-utils tcpdump nmap-ncat` |
| **client** | `sudo apt install bind9-dnsutils tcpdump netcat-openbsd curl openssh-server` |
| **web-server** | `sudo apt install nginx tcpdump netcat-openbsd curl` |
 
{{%notice style="info" title="Ubuntu Desktop et SSH"%}} Contrairement aux versions serveur, Ubuntu Desktop n'inclut pas de serveur SSH. Le paquet openssh-server permet d'administrer `client` par rebond depuis le poste Windows, comme les autres machines. {{%/notice%}}

`dhcp-router` a accès à Internet par son interface WAN. Pour les autres machines, comme au chapitre 4, ajoutez **temporairement** une carte réseau en **NAT**, installez les paquets, puis retirez la carte.
 
{{%notice style="tip" title="Après la section 6.2"%}}
Une fois le routeur configuré (*forwarding* et NAT), les machines du LAN accèdent à Internet par `dhcp-router`. La carte NAT temporaire ne sera plus nécessaire.
{{%/notice%}}


## Accéder aux machines
 
Le poste Windows ne peut joindre que l'interface WAN de `dhcp-router`. Les autres machines sont sur un *LAN Segment*, invisible depuis Windows.
 
Pour administrer toutes les machines depuis le terminal de Windows, on utilise un **rebond SSH** à travers `dhcp-router`. Sa mise en place fait l'objet de la section 6.1.
<!--  
## Capturer le trafic avec tcpdump
 
Wireshark, installé sur Windows, ne voit pas le trafic d'un *LAN Segment*. On capture donc le trafic **sur une machine Linux** avec `tcpdump`, puis on analyse la capture dans Wireshark.
 
### La commande tcpdump
 
```bash
sudo tcpdump -i ens224 -n                         # afficher le trafic en direct
sudo tcpdump -i ens224 -n -w /tmp/capture.pcap    # enregistrer dans un fichier
```
 
| Option | Rôle |
|--------|------|
| `-i` | Interface à écouter |
| `-n` | Affiche les adresses et les ports en numérique (pas de résolution de noms) |
| `-v` | Mode détaillé |
| `-w` | Enregistre les paquets dans un fichier (sans les afficher) |
| `-c N` | S'arrête après `N` paquets |
 
`Ctrl+C` arrête la capture.
 
### Filtres
 
Un filtre placé à la fin de la commande limite les paquets capturés :
 
```bash
sudo tcpdump -i ens224 -n port 22                       # SSH
sudo tcpdump -i ens224 -n udp port 67 or udp port 68    # DHCP
sudo tcpdump -i ens224 -n host 172.16.10.2              # tout ce qui concerne dns-server
```
 
On peut combiner les filtres avec `and`, `or` et `not`.
 
{{%notice style="info" title="Pourquoi /tmp?"%}}
Par sécurité, `tcpdump` abandonne les privilèges de `root` après avoir ouvert l'interface. Il ne peut donc pas toujours écrire dans votre répertoire personnel. Enregistrez vos captures dans `/tmp`.
{{%/notice%}}
 
### Analyser une capture dans Wireshark
 
1. Copiez le fichier de capture sur le poste Windows avec `scp` (section 6.1) :
```powershell
    scp routeur:/tmp/capture.pcap .
```
2. Ouvrez le fichier dans Wireshark (**Fichier → Ouvrir**).
Toutes les fonctions vues au chapitre 5 (filtres d'affichage, **Suivre → TCP Flux**, **Graphique des flux**) sont disponibles. -->
 
## Diagnostiquer un service
 
Lorsqu'un service ne fonctionne pas, procédez **dans l'ordre**, du serveur vers le client :
 
| # | Question | Commande |
|---|----------|----------|
| 1 | La configuration est-elle valide? | Commande de validation du service (tableau ci-dessous) |
| 2 | Le service est-il démarré? | `systemctl status <service>` |
| 3 | Pourquoi a-t-il échoué? | `sudo journalctl -xeu <service>` |
| 4 | Écoute-t-il sur le bon port et la bonne adresse? | `sudo ss -tulnp` |
| 5 | Le pare-feu laisse-t-il passer les requêtes? | `sudo firewall-cmd --list-all` (Rocky)<br>`sudo ufw status` (Ubuntu) |
| 6 | Le client joint-il le serveur? | `ping`, puis un test propre au service (`ssh`, `dig`, `curl`) |
 
### Valider une configuration
 
Chaque service fournit une commande qui vérifie sa configuration **sans la charger**. Utilisez-la avant chaque redémarrage du service.
 
| Service | Commande de validation |
|---------|------------------------|
| SSH | `sudo sshd -t` |
| DHCP (Kea) | `sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf` |
| DNS (BIND) | `sudo named-checkconf` et `named-checkzone <zone> <fichier>` |
| Web (nginx) | `sudo nginx -t` |
 
### Le pare-feu (Rocky)
 
```bash
sudo firewall-cmd --add-service=dns                  # temporaire (jusqu'au prochain redémarrage)
sudo firewall-cmd --add-service=dns --permanent      # permanent, appliqué après un rechargement
sudo firewall-cmd --reload                           # recharge la configuration permanente
```
 
{{%notice style="warning" title="Attention"%}}
`--permanent` sans `--reload` ne change rien immédiatement. Et `--reload` efface les règles temporaires qui n'ont pas été ajoutées avec `--permanent`.
{{%/notice%}}
 
{{%notice style="tip" title="À retenir"%}}
Valider la configuration, vérifier le service, vérifier l'écoute, vérifier le pare-feu, puis tester depuis le client. La plupart des problèmes se trouvent dans les trois premières étapes.
{{%/notice%}}