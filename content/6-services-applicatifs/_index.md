+++
pre = '<b>6. </b>'
title = "Services applicatifs"
weight = "600"
draft = false
+++
-------------------
 
Dans le chapitre précédent, nous avons vu que la **couche transport** remet les données à la bonne application grâce au **numéro de port**.
 
<!-- Mais que font ces applications? Comment un poste qui vient de démarrer obtient-il son adresse IP? Comment le nom `www.wikipedia.org` devient-il une adresse IP? Comment un navigateur obtient-il une page web? -->
 
Ce chapitre porte sur les **services applicatifs** : les programmes qui écoutent sur ces ports et les protocoles qu'ils utilisent pour dialoguer avec leurs clients.
 
## La couche application
 
Dans le modèle **TCP/IP**, la **couche application** est la couche la plus haute. C'est la seule avec laquelle l'utilisateur interagit directement.
 
Un **protocole applicatif** définit :
 
- les **messages** que le client et le serveur peuvent échanger;
- le **format** de ces messages;
- l'**ordre** dans lequel ils sont échangés.
Par exemple, en HTTP, le client envoie une requête `GET /index.html` et le serveur répond par un code `200 OK` suivi de la page.
 
Un protocole applicatif ne s'occupe pas de l'acheminement : il confie ses messages à **TCP** ou à **UDP**, qui les confie à **IP**, et ainsi de suite.
 
{{%notice style="note" title="Rappel"%}}
Le choix entre TCP et UDP est fait par les concepteurs du protocole applicatif, selon ses besoins (chapitre 5) : un service qui exige des données complètes utilise TCP; un service qui échange de courts messages utilise UDP.
{{%/notice%}}
 
## Services réseau
 
Côté serveur, un **service** est un programme qui s'exécute en arrière-plan et qui **écoute sur un port** en attendant les requêtes des clients. Sous Linux, on appelle ce programme un **démon** (*daemon*). Il est géré par `systemd`, avec la commande `systemctl`.
 
Voici les services étudiés dans ce chapitre :
 
| Service | Rôle | Transport et port | Logiciel utilisé | Service `systemd` |
|---------|------|-------------------|------------------|-------------------|
| **SSH** | Administration à distance | TCP `22` | OpenSSH | `sshd` (Rocky), `ssh` (Ubuntu) |
| **DHCP** | Configuration IP automatique | UDP `67` et `68` | ISC Kea | `kea-dhcp4` |
| **DNS** | Traduction des noms en adresses IP | UDP `53` (et TCP `53`) | BIND | `named` |
| **HTTP / HTTPS** | Web | TCP `80` / `443` | nginx | `nginx` |
 
## Fil conducteur
 
Imaginons un poste qui vient de démarrer et dont l'utilisateur ouvre `http://www.votrenom.lan`.
 
![Services utilisés lors de l'ouverture d'une page web par un poste qui vient de démarrer](/images/06/06-fil-conducteur.png)
 
1. **DHCP** : le poste obtient son adresse IP, son masque, sa passerelle et l'adresse de son serveur DNS.
2. **DNS** : le poste demande l'adresse IP de `www.votrenom.lan`.
3. **TCP** : le poste établit une connexion avec le serveur web (chapitre 5).
4. **HTTP** : le poste demande la page et le serveur la lui envoie.
Chaque étape dépend de la précédente : sans DHCP, le poste ne connaît pas son serveur DNS; sans DNS, il ne connaît pas l'adresse du serveur web.
 
Pendant ce temps, l'administrateur configure tous ces serveurs à distance avec **SSH**.
 
Dans les laboratoires de ce chapitre, vous construirez progressivement ce réseau : un service par section, jusqu'à l'atelier synthèse.
 
{{%notice style="tip" title="À retenir"%}}
Un **service** est un programme qui écoute sur un **port**. Le **protocole applicatif** définit les messages qu'il échange avec ses clients. Le transport de ces messages est confié à TCP ou UDP.
{{%/notice%}}