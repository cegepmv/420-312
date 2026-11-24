# 6. Services applicatifs
-------------------
 
Dans le chapitre précédent, nous avons vu que la **couche transport** remet les données à la bonne application grâce au **numéro de port**.
 
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

# 6.0. Environnement de laboratoire
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

# 6.1. SSH
-------------------
 
**SSH (*Secure Shell*)** permet d'ouvrir une session à distance sur une machine et d'y exécuter des commandes, à travers une connexion **chiffrée**. C'est l'outil d'administration de presque tous les serveurs Linux.
 
Vous l'avez déjà utilisé à plusieurs reprises, et le cours de **Sécurité informatique** en a présenté les mécanismes cryptographiques. Cette section se concentre sur le point de vue **réseau** : ce qui circule, ce qui est visible, et comment atteindre des machines qui ne sont pas directement joignables.
 
{{%notice style="note" title="Authentification"%}}
SSH permet de s'authentifier par **mot de passe** ou par **paire de clés**. L'authentification par clé a été vue dans le cours de Sécurité informatique. Dans ce cours, nous utilisons simplement le **mot de passe** du compte.
{{%/notice%}}
 
## SSH vu du réseau
 
Le client `ssh` se connecte au serveur `sshd`, qui écoute sur le port **TCP 22**.
 
```bash
sudo ss -tlnp | grep :22
```
 
```text
LISTEN  0  128  0.0.0.0:22  0.0.0.0:*  users:(("sshd",pid=1012,fd=3))
```
 
{{%notice style="info" title="Remarque"%}}
Le service s'appelle `sshd` sous Rocky et `ssh` sous Ubuntu : `systemctl status sshd` ou `systemctl status ssh`.
{{%/notice%}}
 
### Déroulement d'une connexion
 
![Phases d'une connexion SSH](/images/06/06-ssh-phases.png)
 
1. **Établissement de la connexion TCP** : le *3-way handshake* (chapitre 5).
2. **Échange des versions** : chaque côté annonce sa version du protocole et de son logiciel, par exemple `SSH-2.0-OpenSSH_9.9`. Cet échange est **en clair**.
3. **Négociation des algorithmes et échange de clés** : le client et le serveur choisissent les algorithmes à utiliser et calculent ensemble une **clé de session**, sans jamais la transmettre sur le réseau. À partir de ce moment, **tout est chiffré**.
4. **Authentification** : le serveur prouve son identité, puis le client s'authentifie, par clé ou par mot de passe.
5. **Session** : commandes, résultats et transferts de fichiers.
Le mot de passe, s'il y en a un, n'est donc jamais transmis en clair : il est envoyé à l'étape 4, dans le canal déjà chiffré.
 
{{%notice style="tip" title="Ce qu'un observateur peut voir"%}}
Une personne qui capture une session SSH voit les adresses IP, les ports, les versions des logiciels, la liste des algorithmes proposés, ainsi que la taille et le moment de chaque paquet. Elle ne voit **ni les commandes, ni leurs résultats, ni le mot de passe**.
{{%/notice%}}
 
## Rebond SSH
 
### Le problème
 
Dans le réseau du chapitre, le poste Windows ne peut pas joindre `172.16.10.2` : il n'a aucune route vers `172.16.10.0/24`, et nous n'avons pas les droits pour en ajouter une.
 
En revanche, Windows peut joindre `dhcp-router` par son interface WAN, et `dhcp-router` peut joindre `172.16.10.2`.
 
### Le principe
 
On demande à `dhcp-router` de servir de **machine de rebond** (*jump host*) avec l'option `-J` :
 
```powershell
ssh -J student@192.168.110.130 student@172.16.10.2
```
 
![Rebond SSH à travers dhcp-router](/images/06/06-ssh-rebond.png)
 
1. Le client ouvre une session SSH avec `dhcp-router`.
2. Il lui demande d'ouvrir une **connexion TCP** vers `172.16.10.2:22` et de relayer les données.
3. Le client établit ensuite sa session SSH avec `dns-server` **à travers ce relais**.
On s'authentifie donc deux fois : auprès de `dhcp-router`, puis auprès de `dns-server`.
 
La session avec `dns-server` est **chiffrée de bout en bout**, entre le poste Windows et `dns-server` : `dhcp-router` relaie des octets qu'il ne peut pas déchiffrer.
 
### Deux connexions TCP
 
Pendant un rebond, `ss -tn` sur `dhcp-router` montre deux connexions :
 
```text
State  Recv-Q  Send-Q  Local Address:Port      Peer Address:Port
ESTAB  0       0       192.168.110.130:22      192.168.110.1:51544
ESTAB  0       0       172.16.10.1:41822       172.16.10.2:22
```
 
- La première vient du poste Windows : `dhcp-router` y est **serveur** (port `22`).
- La seconde est ouverte par `dhcp-router` lui-même : il y est **client** (port éphémère).
{{%notice style="info" title="Rebond ou routage?"%}}
Un rebond n'est **pas** du routage. `dhcp-router` ne transfère pas les paquets du poste Windows : il ouvre **sa propre** connexion TCP vers `dns-server`. Le rebond fonctionne donc même si le *forwarding* IPv4 est désactivé, et `dns-server` voit la connexion arriver de `172.16.10.1`.
{{%/notice%}}
 
## Le fichier de configuration du client
 
Taper `-J` et les adresses à chaque connexion devient vite fastidieux. Le fichier **`~/.ssh/config`** du client permet de définir des **alias** :
 
```text
Host routeur
    HostName 192.168.110.130
    User student
 
Host dns
    HostName 172.16.10.2
    User student
    ProxyJump routeur
 
Host web
    HostName 172.16.10.3
    User student
    ProxyJump routeur
```
 
La connexion devient simplement :
 
```powershell
ssh dns
```
 
| Directive | Rôle |
|-----------|------|
| `Host` | Nom de l'alias |
| `HostName` | Adresse IP (ou nom) réelle de la machine |
| `User` | Utilisateur à utiliser |
| `ProxyJump` | Machine de rebond (équivalent de `-J`) |
 
Sous Windows, le fichier se trouve dans `C:\Users\<vous>\.ssh\config` (sans extension).
 
{{%notice style="tip" title="Conseil"%}}
L'adresse WAN de `dhcp-router` peut changer d'une séance à l'autre. Avec ce fichier, une seule ligne est à modifier.
{{%/notice%}}
 
## Copier des fichiers : scp
 
`scp` copie des fichiers à travers une connexion SSH. Il utilise donc le même port, la même authentification, le même chiffrement, et les mêmes alias :
 
```powershell
scp index.html web:/tmp/                  # du poste vers le serveur
scp routeur:/tmp/capture.pcap .           # du serveur vers le poste (. = répertoire courant)
scp -r site/ web:/tmp/site                # un répertoire complet
```
 
La syntaxe est `scp <source> <destination>`, où une machine distante s'écrit `hôte:chemin`.
 
{{%notice style="tip" title="À retenir"%}}
SSH utilise **TCP 22**. Seul l'échange des versions et la négociation des algorithmes sont en clair : l'authentification et la session sont chiffrées. Un **rebond** permet d'atteindre une machine non joignable à travers une autre, avec un chiffrement de bout en bout.
{{%/notice%}}

# 6.1.1 Exercices
-----------------
 
### 1. Phases d'une connexion
 
Remettez les étapes suivantes dans l'ordre, puis indiquez pour chacune si elle est visible **en clair** ou **chiffrée** dans une capture.
 
1. Le client envoie la commande `ls`.
2. Le client annonce `SSH-2.0-OpenSSH_9.9`.
3. Le client envoie le SYN.
4. Le client envoie son mot de passe.
5. Le client et le serveur choisissent leurs algorithmes.

{{% expand title="Solution" %}}
| Ordre | Étape | Visibilité |
|-------|-------|------------|
| 1 | Le client envoie le SYN (3) | En clair (en-têtes TCP) |
| 2 | Le client annonce sa version (2) | En clair |
| 3 | Choix des algorithmes (5) | En clair (la liste des algorithmes est visible, mais pas la clé de session calculée) |
| 4 | Le client envoie son mot de passe (4) | Chiffrée |
| 5 | Le client envoie `ls` (1) | Chiffrée |
{{% /expand %}}
 
### 2. Lire un rebond
 
Pendant qu'un poste Windows est connecté à `dns-server` par rebond, on exécute `ss -tn` sur `dhcp-router` :
 
```text
State  Recv-Q  Send-Q  Local Address:Port      Peer Address:Port
ESTAB  0       0       192.168.110.130:22      192.168.110.1:50211
ESTAB  0       0       192.168.110.130:22      192.168.110.1:50236
ESTAB  0       0       172.16.10.1:39114       172.16.10.2:22
```
 
1. Quelle ligne correspond au relais vers `dns-server`? `dhcp-router` y est-il client ou serveur?
2. Le poste Windows a deux connexions vers `dhcp-router`. Proposez une explication.
3. Quelle adresse IP source `dns-server` voit-il pour cette connexion?
4. Faut-il activer le *forwarding* IPv4 sur `dhcp-router` pour que ce rebond fonctionne? Justifiez.

{{% expand title="Solution" %}}
1. La troisième ligne. `dhcp-router` est **client** : son port local `39114` est éphémère et le port distant est `22`.
2. Une connexion sert au rebond vers `dns-server`; l'autre est une session SSH ouverte directement sur `dhcp-router` (dans un autre terminal, par exemple).
3. `172.16.10.1`, l'adresse LAN de `dhcp-router`.
4. Non. `dhcp-router` ne transfère pas de paquets : il ouvre sa propre connexion TCP vers `dns-server` et relaie les données au niveau applicatif.
{{% /expand %}}

# 6.1.2. Laboratoire - Accès SSH au réseau du chapitre
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez mettre en place les premières machines du réseau du chapitre (section 6.0) : `dhcp-router`, `dns-server` et `web-server`.
 
Vous allez ensuite administrer ces machines depuis le poste Windows, grâce à un **rebond SSH**;
<!-- - en bonus, comparer dans une capture une communication **en clair** (`nc`) et une communication **chiffrée** (SSH). -->
| Machine | Interface | Adresse |
|---------|-----------|---------|
| **Poste Windows** | Carte réseau du poste | Attribuée automatiquement |
| **dhcp-router** | `ens160` (WAN, NAT) | Attribuée automatiquement (à relever) |
| **dhcp-router** | `ens224` (LAN-SERVICES) | `172.16.10.1/24` |
| **dns-server** | `ens160` (LAN-SERVICES) | `172.16.10.2/24` |
| **web-server** | `ens33` (LAN-SERVICES) | `172.16.10.3/24` |
 
Les machines doivent avoir été créées et leurs paquets installés selon la section 6.0.
 
## Préparation
 
### 1. Configurer dhcp-router
 
Sur la console de **dhcp-router** :
 
1. Identifiez les deux interfaces avec `ip a`.
2. Configurez l'interface LAN avec `nmcli`, comme au chapitre 4 : adresse `172.16.10.1/24`, méthode `manual`, **sans passerelle**.
3. Laissez l'interface WAN en configuration automatique.
4. Relevez l'adresse IP de l'interface WAN.
### 2. Configurer dns-server
 
Sur la console de **dns-server**, configurez son interface avec `nmcli` :
 
- adresse : `172.16.10.2/24`;
- passerelle : `172.16.10.1`;
- méthode : `manual`.
Le serveur DNS sera configuré à la section 6.3.
 
Vérifiez :
 
```bash
ping 172.16.10.1
```

### 2. Configurer web-server
 
Sur la console de **web-server**, configurez son interface avec `Netplan` :
 
- adresse : `172.16.10.3/24`;
- passerelle : `172.16.10.1`;
- méthode : `manual`.
Le serveur Web sera configuré à la section 6.4.
 
Vérifiez :
 
```bash
ping 172.16.10.1
```

 
Complétez le tableau :
 
| Élément | Valeur |
|---------|--------|
| Adresse WAN de dhcp-router | |
| Adresse IPv4 du poste Windows | |
 
## 1- Le service SSH
 
Sur **dhcp-router**, **dns-server** et **web-server** :
 
```bash
systemctl status sshd
sudo ss -tlnp | grep :22
sudo firewall-cmd --list-services
```
 
### Questions
 
1. Le service `sshd` est-il actif? Est-il activé au démarrage (*enabled*)?
2. Sur quelle(s) adresse(s) et quel port `sshd` écoute-t-il?
3. Le pare-feu autorise-t-il SSH? Comment le savez-vous?
## 2- Premier rebond
 
### 1. Connexion à dhcp-router
 
Dans un terminal PowerShell du poste Windows :
 
```powershell
ssh student@<IP_WAN>
```
 
Lors de la première connexion à une machine, SSH demande de confirmer l'identité du serveur : répondez `yes`. Gardez cette session ouverte.
 
### 2. Connexion à dns-server par rebond
 
Dans un **deuxième** terminal PowerShell :
 
```powershell
ssh -J student@<IP_WAN> student@172.16.10.2
```
 
Gardez cette session ouverte.
 
### 3. Observer les connexions
 
Dans la session ouverte sur **dhcp-router** :
 
```bash
ss -tn
cat /proc/sys/net/ipv4/ip_forward
```
 
Complétez le tableau pour les connexions liées au rebond :
 
| Connexion | Socket local (dhcp-router) | Socket distant | dhcp-router est client ou serveur? |
|-----------|----------------------------|----------------|------------------------------------|
| Poste Windows → dhcp-router | | | |
| dhcp-router → dns-server | | | |
 
Dans la session ouverte sur **dns-server** :
 
```bash
ss -tn | grep :22
```
 
### Questions
 
1. Combien de mots de passe avez-vous saisis pour vous connecter à `dns-server`? Pourquoi?
2. Quelle adresse IP source `dns-server` voit-il pour votre connexion? Pourquoi pas celle du poste Windows?
3. Quelle est la valeur de `ip_forward` sur dhcp-router? Le rebond fonctionne-t-il quand même? Expliquez.
4. `dhcp-router` peut-il lire les commandes que vous tapez sur `dns-server`? Justifiez.
## 3- Fichier de configuration
 
### 1. Créer les alias
 
Sur le poste Windows, créez le fichier de configuration :
 
```powershell
mkdir $env:USERPROFILE\.ssh
New-Item -Path "$env:USERPROFILE\.ssh\config" -ItemType File
notepad "$env:USERPROFILE\.ssh\config"
```

Ajoutez les alias `routeur`, `dns` et `web` (section 6.1), avec l'adresse WAN relevée en préparation. Enregistrez.
 
### 2. Tester
 
```powershell
ssh routeur
ssh dns
ssh web
```
 
### Questions
 
1. Combien de mots de passe la commande `ssh dns` vous demande-t-elle? Pour quelles machines?
2. Quelle directive du fichier remplace l'option `-J`?
3. À la prochaine séance, l'adresse WAN de dhcp-router a changé. Que devez-vous modifier?

## Questions de synthèse
 
1. Lors de la commande `ssh dns`, combien de connexions TCP et combien de sessions SSH sont établies? Entre quelles machines? Appuyez-vous sur les sorties de `ss` relevées dans la partie 2.
2. Expliquez pourquoi le rebond fonctionne alors que le poste Windows n'a aucune route vers `172.16.10.0/24` et que dhcp-router ne fait pas de *forwarding*.
3. Vous transférez un fichier avec `scp`. Ce transfert est-il protégé de la même façon qu'une session SSH? Justifiez.

# 6.2. DHCP
-------------------
 
Jusqu'ici, toutes les interfaces ont été configurées **manuellement** : adresse IP, préfixe, passerelle et serveur DNS. Cette méthode convient pour quelques serveurs, mais pas pour les centaines de postes, portables et téléphones d'une organisation : elle est longue et source d'erreurs (adresse en double, mauvaise passerelle).
 
**DHCP (*Dynamic Host Configuration Protocol*)** automatise cette configuration. Un poste qui se connecte au réseau demande sa configuration; un **serveur DHCP** lui fournit :
 
- une **adresse IP** et son **masque**;
- la **passerelle par défaut**;
- l'adresse des **serveurs DNS**;
- la **durée** pendant laquelle il peut utiliser cette adresse (le **bail**);
- d'autres options au besoin (nom de domaine, etc.).

DHCP utilise **UDP** :
 
| Rôle | Port |
|------|------|
| Serveur | `67` |
| Client | `68` |
 
{{%notice style="note" title="Rappel"%}}
DHCP est un bon exemple du choix d'UDP (chapitre 5) : les échanges sont courts, et le client ne pourrait de toute façon pas établir de connexion TCP, puisqu'il n'a pas encore d'adresse IP.
{{%/notice%}}
 
## L'échange DORA
 
Pour obtenir une adresse, le client et le serveur échangent quatre messages. On appelle cet échange **DORA**, d'après la première lettre de chaque message.
 
![Échange DORA entre un client et un serveur DHCP](/images/06/06-dhcp-dora.png)
 
| # | Message | Sens | Rôle |
|---|---------|------|------|
| 1 | **DISCOVER** | Client → serveur | « Y a-t-il un serveur DHCP sur ce réseau? » |
| 2 | **OFFER** | Serveur → client | « Je te propose l'adresse `172.16.10.100`, avec ces options. » |
| 3 | **REQUEST** | Client → serveur | « J'accepte l'offre de tel serveur. » |
| 4 | **ACK** | Serveur → client | « C'est confirmé : l'adresse est à toi pour la durée du bail. » |
 
Le client configure son interface seulement après avoir reçu l'**ACK**.
 
### Un échange en broadcast
 
Au départ, le client n'a **pas d'adresse IP** et ne connaît **pas l'adresse du serveur**. Il utilise donc :
 
- l'adresse IP source `0.0.0.0`;
- l'adresse IP de destination `255.255.255.255` (broadcast);
- l'adresse MAC de destination `ff:ff:ff:ff:ff:ff` (broadcast Ethernet, chapitre 3).
Le serveur, lui, a une adresse IP **statique** et répond depuis cette adresse.
 
| Message | IP source | IP destination | Ports |
|---------|-----------|----------------|-------|
| **DISCOVER** | `0.0.0.0` | `255.255.255.255` | `68` → `67` |
| **OFFER** | IP du serveur | `255.255.255.255` ou adresse proposée | `67` → `68` |
| **REQUEST** | `0.0.0.0` | `255.255.255.255` | `68` → `67` |
| **ACK** | IP du serveur | `255.255.255.255` ou adresse proposée | `67` → `68` |
 
## Le bail
 
Une adresse est attribuée pour une **durée limitée** : le **bail** (*lease*). Le serveur enregistre chaque bail : adresse, adresse MAC du client, date d'expiration.
 
Si le client ne renouvelle pas son bail (machine éteinte, retirée du réseau), l'adresse redevient disponible pour un autre client à l'expiration du bail. C'est ce qui permet à un réseau de servir **plus d'appareils qu'il n'a d'adresses**, tant qu'ils ne sont pas tous connectés en même temps.
 
### Renouvellement
 
Un client essaie de conserver son adresse. Il n'attend pas l'expiration du bail :
 
![Renouvellement et expiration d'un bail DHCP](/images/06/06-dhcp-bail.png)
 
| Moment | Nom | Action du client |
|--------|-----|------------------|
| **50 %** du bail | **T1** (*renew*) | Envoie un REQUEST en **unicast** au serveur qui lui a attribué l'adresse. |
| **87,5 %** du bail | **T2** (*rebind*) | Si le serveur n'a pas répondu, envoie un REQUEST en **broadcast**, à n'importe quel serveur DHCP. |
| **100 %** du bail | Expiration | Sans réponse, le client cesse d'utiliser l'adresse et recommence un DORA. |
 
Le renouvellement ne comporte que **deux messages** (REQUEST, ACK) : le client a déjà une adresse IP et connaît le serveur, il peut donc lui écrire directement.
 
{{%notice style="tip" title="Au redémarrage"%}}
Un client qui redémarre et se souvient de son ancienne adresse envoie directement un **REQUEST** pour cette adresse, sans DISCOVER. Si le serveur refuse (NAK), le client recommence un DORA complet.
{{%/notice%}}
 
### Choisir la durée du bail
 
- Un bail **trop long** immobilise des adresses attribuées à des appareils qui ne sont plus là.
- Un bail **trop court** multiplie les renouvellements et la charge du serveur.
Des durées de **quelques heures** conviennent à un réseau Wi-Fi public; de **un à huit jours** à un réseau d'entreprise.
 
## L'étendue
 
Une **étendue** (*scope*) regroupe tout ce que le serveur sait d'un réseau :
 
| Élément | Exemple |
|---------|---------|
| **Réseau** | `172.16.10.0/24` |
| **Plage(s) d'adresses à distribuer** (*pool*) | `172.16.10.100` à `172.16.10.199` |
| **Options** | Passerelle `172.16.10.1`, DNS `172.16.10.2` |
| **Durée du bail** | `3600` secondes |
 
Le masque n'est pas une option à configurer : il découle du réseau de l'étendue.
 
### Options courantes
 
| Option | Code | Rôle |
|--------|------|------|
| Masque de sous-réseau | `1` | Masque du réseau (déduit de l'étendue) |
| Routeur | `3` | Passerelle par défaut |
| Serveurs DNS | `6` | Adresses des serveurs DNS |
| Nom de domaine | `15` | Domaine ajouté aux noms courts (`www` → `www.votrenom.lan`) |
| Durée du bail | `51` | Durée du bail, en secondes |
| Identifiant du serveur | `54` | Adresse IP du serveur DHCP qui répond |
 
Le **code** est le numéro de l'option dans le message DHCP. C'est lui que Wireshark affiche, par exemple *Option: (3) Router*.
 
### Plan d'adressage et étendue
 
Toutes les adresses d'un réseau ne doivent pas être distribuées. Les équipements dont l'adresse doit être connue à l'avance (routeur, serveurs) reçoivent une **adresse statique**, en dehors de la plage distribuée.
 
Exemple pour le réseau du chapitre :
 
| Adresses | Usage |
|----------|-------|
| `172.16.10.1` à `172.16.10.9` | Routeur et serveurs (statiques) |
| `172.16.10.10` à `172.16.10.99` | Réservations |
| `172.16.10.100` à `172.16.10.199` | Plage distribuée |
| `172.16.10.200` à `172.16.10.254` | Libres (croissance future) |
 
{{%notice style="warning" title="Attention"%}}
Une adresse attribuée statiquement ne doit **jamais** se trouver dans la plage distribuée : le serveur pourrait l'attribuer à un client, et deux machines auraient la même adresse.
{{%/notice%}}
 
### Exclusions
 
Une **exclusion** retire des adresses de la plage distribuée, par exemple pour des imprimantes configurées statiquement.
 
Avec Kea, le serveur utilisé dans ce cours, il n'existe pas de directive d'exclusion : on découpe la plage en **plusieurs plages** qui contournent les adresses exclues.
 
Exemple : exclure `172.16.10.150` à `172.16.10.159` :
 
```json
"pools": [
    { "pool": "172.16.10.100 - 172.16.10.149" },
    { "pool": "172.16.10.160 - 172.16.10.199" }
]
```
 
### Réservations
 
Une **réservation** garantit qu'un client obtient **toujours la même adresse** par DHCP. Elle associe une **adresse MAC** à une adresse IP :
 
```json
"reservations": [
    {
        "hw-address": "00:0c:29:3a:5b:7c",
        "ip-address": "172.16.10.50",
        "hostname": "imprimante-01"
    }
]
```
 
Une réservation est utile pour un appareil dont l'adresse doit être connue (imprimante, caméra), mais qu'on souhaite gérer de façon centralisée plutôt que de le configurer à la main.
 
Avec Kea, l'adresse réservée peut se trouver **en dehors** de la plage distribuée. C'est même recommandé : on évite ainsi qu'elle soit attribuée à un autre client.
 
## Où placer le serveur DHCP?
 
Les messages DISCOVER et REQUEST sont envoyés en **broadcast**. Or un routeur **ne transmet pas** les broadcasts d'un réseau à un autre (chapitre 4).
 
Un serveur DHCP ne reçoit donc que les demandes de son propre réseau.
 
## Côté client
 
| Action | Linux (NetworkManager) | Windows |
|--------|------------------------|---------|
| Afficher la configuration obtenue | `nmcli -f DHCP4 device show ens160` | `ipconfig /all` |
| Libérer l'adresse | — | `ipconfig /release` |
| Redemander une adresse | `sudo nmcli con down ens160`<br>`sudo nmcli con up ens160` | `ipconfig /renew` |
 
{{%notice style="tip" title="Adresse 169.254.x.x"%}}
Un poste Windows qui ne reçoit aucune réponse DHCP s'attribue lui-même une adresse dans `169.254.0.0/16` (**APIPA**). Cette adresse lui permet seulement de communiquer avec les machines voisines dans le même cas. Une adresse `169.254.x.x` indique donc presque toujours un **problème de DHCP**.
{{%/notice%}}
 
{{%notice style="tip" title="À retenir"%}}
+ DHCP utilise **UDP 67/68**. 
+ Le client obtient son adresse par l'échange **DORA**, en broadcast, car il n'a pas encore d'adresse. 
+ L'adresse est attribuée pour la durée d'un **bail**, renouvelé à **T1** (unicast) puis à **T2** (broadcast). 
+ Le serveur ne reçoit que les demandes de son réseau.
{{%/notice%}}
 
# 6.2.1. Exercices
----------------
 
### 1. Compléter un échange DORA
 
Un client dont l'adresse MAC est `00:0c:29:aa:bb:cc` démarre dans le réseau `172.16.10.0/24`. Le serveur DHCP a l'adresse `172.16.10.1` et propose l'adresse `172.16.10.100`. Le serveur envoie ses réponses en broadcast.
 
Complétez le tableau.
 
| Message | MAC destination | IP source | IP destination | Port source | Port destination |
|---------|-----------------|-----------|----------------|-------------|------------------|
| DISCOVER | | | | | |
| OFFER | | | | | |
| REQUEST | | | | | |
| ACK | | | | | |
 
{{% expand title="Solution" %}}
| Message | MAC destination | IP source | IP destination | Port source | Port destination |
|---------|-----------------|-----------|----------------|-------------|------------------|
| DISCOVER | `ff:ff:ff:ff:ff:ff` | `0.0.0.0` | `255.255.255.255` | `68` | `67` |
| OFFER | `ff:ff:ff:ff:ff:ff` | `172.16.10.1` | `255.255.255.255` | `67` | `68` |
| REQUEST | `ff:ff:ff:ff:ff:ff` | `0.0.0.0` | `255.255.255.255` | `68` | `67` |
| ACK | `ff:ff:ff:ff:ff:ff` | `172.16.10.1` | `255.255.255.255` | `67` | `68` |
 
Le REQUEST part encore de `0.0.0.0` : le client n'utilise l'adresse qu'après avoir reçu l'ACK.
{{% /expand %}}

### 2. Concevoir une étendue
 
Une PME utilise le réseau `192.168.50.0/24` :
 
- le routeur a l'adresse `192.168.50.1`;
- les serveurs utilisent les adresses `192.168.50.2` à `192.168.50.10` (statiques);
- trois imprimantes doivent toujours avoir les adresses `192.168.50.21`, `.22` et `.23`, mais l'administrateur ne veut pas les configurer manuellement;
- les postes doivent obtenir une adresse entre `192.168.50.100` et `192.168.50.250`;
- les adresses `192.168.50.200` à `192.168.50.209` sont utilisées statiquement par des équipements de test.
1. Écrivez la ou les plages (`pools`) de l'étendue.
2. Combien d'adresses le serveur peut-il distribuer aux postes?
3. Comment attribuer leurs adresses aux imprimantes? De quelle information avez-vous besoin pour chacune?
4. Quelles options faut-il distribuer au minimum pour que les postes accèdent à Internet par nom?

{{% expand title="Solution" %}}
1. Plages :
```json
    "pools": [
        { "pool": "192.168.50.100 - 192.168.50.199" },
        { "pool": "192.168.50.210 - 192.168.50.250" }
    ]
```
2. `100 + 41 = 141` adresses.
3. Avec des **réservations**, en dehors des plages. Il faut l'**adresse MAC** de chaque imprimante.
4. Le **routeur** (passerelle par défaut) et les **serveurs DNS**. Le masque est déduit de l'étendue.
{{% /expand %}}

### 3. Raisonner
 
1. Pourquoi l'adresse IP du serveur DHCP doit-elle être statique?
2. Un utilisateur branche sur le réseau de l'entreprise un petit routeur Wi-Fi dont le serveur DHCP est activé. Que peut-il arriver aux postes qui démarrent ensuite?

{{% expand title="Solution" %}}
1. Le serveur ne peut pas obtenir son adresse de lui-même. De plus, les relais et les clients qui renouvellent leur bail (T1) lui écrivent directement à son adresse : elle ne doit pas changer.
2. Les postes acceptent la **première** offre reçue. Certains obtiendront donc une adresse, une passerelle et un DNS fournis par ce routeur Wi-Fi, dans un réseau différent : ils perdront l'accès aux ressources de l'entreprise. C'est aussi une attaque connue (faux serveur DHCP) : celui qui contrôle la passerelle et le DNS distribués peut intercepter le trafic.
{{% /expand %}}


# 6.2.2. Laboratoire - Routeur DHCP
----------------
 
## Contexte
 
Dans ce laboratoire, `dhcp-router` devient la **porte d'entrée** du réseau du chapitre :
 
1. il **route** le trafic du LAN vers Internet, avec du NAT (chapitre 4);
2. il **attribue** leur configuration IP aux clients avec le serveur DHCP **Kea**.
Vous capturerez ensuite l'échange DORA lors du démarrage du client.
 
| Machine | Interface | Configuration |
|---------|-----------|---------------|
| **dhcp-router** | `ens160` (WAN, NAT) | Automatique |
| **dhcp-router** | `ens224` (LAN-SERVICES) | `172.16.10.1/24` (statique, section 6.1) |
| **dns-server** | `ens160` (LAN-SERVICES) | `172.16.10.2/24` (statique, section 6.1) |
| **client** | `ens160` (LAN-SERVICES) | **Automatique (DHCP)** |
 
L'étendue à configurer :
 
| Élément | Valeur |
|---------|--------|
| Réseau | `172.16.10.0/24` |
| Plage distribuée | `172.16.10.100` à `172.16.10.199` |
| Passerelle | `172.16.10.1` |
| Serveur DNS | `172.16.10.2` |
| Bail | 1 heure |
 
## Préparation
 
- `dhcp-router` et `dns-server` sont configurés selon le laboratoire 6.1.1.
- La VM `client` est créée (section 6.0), avec sa carte réseau dans **LAN-SERVICES**. **Ne la démarrez pas encore.**
- Ouvrez une session `ssh routeur` depuis le poste Windows.
 
## 1- Activer le routage
 
### 1. Activer le transfert IPv4
 
Comme au chapitre 4, activez le *forwarding* IPv4 de façon permanente sur **dhcp-router** :
 
```bash
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p /etc/sysctl.conf
```
 
### 2. Activer le NAT
 
Les adresses `172.16.10.0/24` sont privées : elles doivent être traduites en l'adresse WAN de dhcp-router pour sortir du réseau (chapitre 4). Avec `firewalld`, cette traduction s'appelle le **masquerading** :
 
```bash
sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
```
 
### 3. Tester
 
Depuis **dns-server** (`ssh dns`), qui a déjà `172.16.10.1` comme passerelle :
 
```bash
ping -c 3 8.8.8.8
```
 
### Questions
 
1. Le test fonctionne-t-il? Quelles sont les deux conditions qui l'ont rendu possible?
2. Quelle adresse IP source les serveurs d'Internet voient-ils pour ce `ping`?
## 2- Configurer le serveur DHCP
 
### 1. Le fichier de configuration
 
Sur **dhcp-router**, conservez le fichier fourni avec Kea, qui contient de nombreux exemples commentés :
 
```bash
sudo mv /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.conf.exemple
```
 
Créez un nouveau fichier :
 
```bash
sudo nano /etc/kea/kea-dhcp4.conf
```
 
Contenu :
 
```json
{
"Dhcp4": {
    # Interface(s) sur laquelle le serveur répond aux clients
    "interfaces-config": {
        "interfaces": [ "ens224" ]
    },
 
    # Enregistrement des baux dans un fichier
    "lease-database": {
        "type": "memfile",
        "persist": true,
        "name": "/var/lib/kea/kea-leases4.csv"
    },
 
    # Durées en secondes : bail, T1 (50 %), T2 (87,5 %)
    "valid-lifetime": 3600,
    "renew-timer": 1800,
    "rebind-timer": 3150,
 
    # Étendues
    "subnet4": [
        {
            "id": 1,
            "subnet": "172.16.10.0/24",
            "pools": [
                { "pool": "172.16.10.100 - 172.16.10.199" }
            ],
            "option-data": [
                { "name": "routers", "data": "172.16.10.1" },
                { "name": "domain-name-servers", "data": "172.16.10.2" }
            ]
        }
    ]
}
}
```
 
{{%notice style="info" title="Format du fichier"%}}
Le fichier est au format **JSON**, auquel Kea ajoute les commentaires (`#`). Une virgule manquante ou en trop entre deux éléments suffit à empêcher le démarrage du service.
{{%/notice%}}
 
### 2. Valider et démarrer
 
```bash
sudo kea-dhcp4 -t /etc/kea/kea-dhcp4.conf
sudo systemctl enable --now kea-dhcp4
systemctl status kea-dhcp4
```
 
En cas d'erreur, suivez la démarche de diagnostic de la section 6.0.
 
### 3. Pare-feu et écoute
 
```bash
sudo firewall-cmd --add-service=dhcp --permanent
sudo firewall-cmd --reload
sudo ss -ulnp | grep :67
```
 
### Questions
 
1. Sur quel port et quel protocole Kea écoute-t-il?
2. Pourquoi le serveur DHCP ne doit-il **pas** répondre sur l'interface `ens160`?
3. Pourquoi la plage distribuée commence-t-elle à `172.16.10.100`?
 
## 3- Tester le fonctionnement 

### 1. Vérifier la configuration du client
 
 
Démarrez la VM **client**,ouvrez une session, puis un terminal (`Ctrl+Alt+T`) :
 
```bash
ip a
ip route
nmcli -f DHCP4 device show ens160
```
 
Complétez le tableau :
 
| Élément | Valeur obtenue |
|---------|----------------|
| Adresse IP et préfixe | |
| Passerelle par défaut | |
| Serveur DNS | |
| Durée du bail (`dhcp_lease_time`) | |
| Serveur DHCP (`dhcp_server_identifier`) | |
 
 
### 2. Le bail côté serveur
 
Sur **dhcp-router** :
 
```bash
sudo cat /var/lib/kea/kea-leases4.csv
```
 
Kea ajoute une ligne à chaque attribution ou renouvellement. Repérez la **dernière** ligne correspondant au client : son adresse IP, son adresse MAC et la date d'expiration (`expire`, en secondes depuis le 1<sup>er</sup> janvier 1970).
 
### 3. Tester la connectivité
 
Sur **client** :
 
```bash
ping -c 3 172.16.10.1
ping -c 3 8.8.8.8
ping -c 3 www.google.com
```
 
### Questions
 
1. Le client a-t-il reçu la première adresse de la plage? Comparez l'adresse MAC du fichier des baux avec celle de `ip a`.
2. Pourquoi `ping 8.8.8.8` fonctionne-t-il, mais pas `ping www.google.com`?
3. Ajoutez une entrée `client` à votre `~/.ssh/config` (section 6.1) avec cette adresse. Pourquoi une adresse attribuée dynamiquement est-elle peu pratique pour administrer une machine?

# 6.3. DNS
-------------------
 
À la fin du laboratoire DHCP, le client pouvait joindre `8.8.8.8`, mais pas `www.google.com`. Les machines communiquent avec des **adresses IP**; les humains, eux, retiennent des **noms**.
 
**DNS (*Domain Name System*)** traduit un nom en adresse IP (et inversement). C'est un service indispensable : presque toutes les communications commencent par une requête DNS.

Il a été défini à l'origine dans la [RFC 1035](https://www.rfc-editor.org/rfc/rfc1035.txt).
 
DNS utilise le port **53** :
 
| Transport | Usage |
|-----------|-------|
| **UDP 53** | Requêtes et réponses courantes |
| **TCP 53** | Réponses trop volumineuses pour un datagramme, et copie d'une zone entre serveurs |

 
## Noms de domaine
 
### FQDN
 
Un **FQDN (*Fully Qualified Domain Name*)** est un nom complet, qui identifie une machine sans ambiguïté :
 
```text
www.cegepmv.ca.
```
 
Il se lit **de droite à gauche**, du plus général au plus précis :
 
| Partie | Nom |
|--------|-----|
| `.` (point final, souvent omis) | **Racine** |
| `ca` | **Domaine de premier niveau** (*TLD, Top-Level Domain*) |
| `cegepmv` | Domaine de deuxième niveau |
| `www` | Nom de l'hôte |
 
Chaque partie, appelée **étiquette** (ou *label*), est séparée par un point.
 
### Une base de données hiérarchique
 
Aucun serveur ne connaît tous les noms d'Internet. L'espace des noms est découpé en un **arbre**, et chaque niveau ne connaît que le niveau situé juste en dessous.
 
![Hiérarchie du DNS](/images/06/06-dns-hierarchie.png)
 
| Niveau | Exemples | Ce que ses serveurs connaissent |
|--------|----------|---------------------------------|
| **Racine** | `.` | Les serveurs de chaque TLD |
| **TLD** | `ca`, `com`, `org`, `fr` | Les serveurs de chaque domaine du TLD |
| **Domaine** | `cegepmv.ca`, `wikipedia.org` | Les adresses de ses machines |
 
Il existe 13 noms de serveurs racine (`a.root-servers.net` à `m.root-servers.net`). Chacun correspond en réalité à des centaines de serveurs répartis dans le monde.
 
### Délégation
 
L'administrateur d'un domaine peut **déléguer** une partie de ce domaine à d'autres serveurs. Par exemple :
 
- les serveurs racine délèguent `ca` à l'organisme qui gère les domaines canadiens;
- les serveurs de `ca` délèguent `cegepmv.ca` aux serveurs du cégep.
Pour déléguer, le serveur parent enregistre le **nom** des serveurs responsables du sous-domaine (enregistrement `NS`), et au besoin leur **adresse IP**.
 
### Domaine et zone
 
- Un **domaine** est un ensemble de noms qui partagent le même suffixe : `cegepmv.ca` comprend `www.cegepmv.ca`, `mail.cegepmv.ca`, `info.cegepmv.ca`, etc.
- Une **zone** est la partie d'un domaine gérée par un même serveur. Si le cégep délègue `info.cegepmv.ca` au département d'informatique, la zone `cegepmv.ca` ne contient plus les noms de `info.cegepmv.ca`.

Un serveur est **autoritaire** pour une zone : c'est lui qui détient les informations officielles de cette zone.
 
## Résolution d'un nom
 
### Les acteurs
 
![Résolution récursive et itérative du nom www.cegepmv.ca](/images/06/06-dns-resolution.png)
 
| Acteur | Rôle |
|--------|------|
| **Résolveur du client** (*stub resolver*) | Partie du système d'exploitation qui envoie les requêtes DNS des applications au serveur DNS configuré (souvent obtenu par DHCP). |
| **Serveur récursif** | Reçoit la requête du client et se charge de trouver la réponse complète, en interrogeant d'autres serveurs. Il conserve les réponses dans un **cache**. |
| **Serveurs autoritaires** | Racine, TLD, puis serveur du domaine : chacun répond pour sa zone. |
 
### Requête récursive et requête itérative
 
1. Le client envoie une **requête récursive** au serveur récursif : « Trouve-moi l'adresse de `www.cegepmv.ca` ».
2. Le serveur récursif effectue des **requêtes itératives** :
    - il demande à un serveur **racine**, qui répond : « Je ne sais pas, mais voici les serveurs de `ca` »;
    - il demande à un serveur de **`ca`**, qui répond : « Voici les serveurs de `cegepmv.ca` »;
    - il demande à un serveur de **`cegepmv.ca`**, qui donne la réponse : « `www.cegepmv.ca` a l'adresse `…` ».
3. Le serveur récursif transmet la réponse au client.
| Requête | Le serveur interrogé… |
|---------|------------------------|
| **Récursive** | doit fournir la réponse complète, quitte à interroger d'autres serveurs. |
| **Itérative** | répond avec ce qu'il sait : la réponse, ou l'adresse d'un serveur plus proche de la réponse (un **renvoi**). |

## Types de serveurs
 
| Serveur | Rôle | Exemples |
|---------|------|----------|
| **Récursif** (ou **cache**) | Résout les noms pour ses clients. Ne détient aucune zone. | Serveur DNS d'une entreprise, `8.8.8.8`, `1.1.1.1` |
| **Autoritaire** | Détient les informations officielles d'une ou plusieurs zones. Ne résout pas les autres noms. | Serveurs racine, serveurs des TLD, serveurs d'un domaine |
 
Un même logiciel peut jouer les deux rôles. Dans le laboratoire, votre serveur sera **récursif** pour les machines du LAN et **autoritaire** pour la zone `votrenom.lan`.
 
{{%notice style="warning" title="Résolveur ouvert"%}}
Un serveur récursif doit répondre **seulement à ses propres clients**. Un serveur qui accepte les requêtes récursives de n'importe qui sur Internet (*résolveur ouvert*) peut être utilisé pour des attaques par amplification : l'attaquant envoie de petites requêtes en usurpant l'adresse de sa victime, et le serveur inonde la victime de grosses réponses.
{{%/notice%}}
 
## Enregistrements
 
Une zone contient des **enregistrements** (*Resource Records*). Chacun associe un **nom**, un **type** et une **valeur** :
 
| Type | Associe | Exemple |
|------|---------|---------|
| **A** | Un nom à une adresse IPv4 | `web` → `172.16.10.3` |
| **AAAA** | Un nom à une adresse IPv6 | `web` → `2001:db8::3` |
| **CNAME** | Un nom (alias) à un autre nom | `www` → `web.votrenom.lan.` |
| **MX** | Un domaine à son serveur de courriel | `votrenom.lan` → `mail.votrenom.lan.` |
| **NS** | Une zone à ses serveurs autoritaires | `votrenom.lan` → `ns1.votrenom.lan.` |
| **SOA** | Une zone à ses paramètres généraux | Un seul par zone, au début du fichier |
| **PTR** | Une adresse IP à un nom (résolution **inverse**) | `172.16.10.3` → `web.votrenom.lan.` |
| **TXT** | Un nom à un texte libre | Souvent utilisé pour prouver la propriété d'un domaine |

## Le fichier de zone
 
Avec BIND, chaque zone est décrite dans un **fichier de zone** :
 
```text
$TTL 1h
@       IN  SOA  ns1.votrenom.lan. admin.votrenom.lan. (
                 2026111101  ; serial
                 1d          ; refresh
                 1h          ; retry
                 1w          ; expire
                 1h )        ; TTL des réponses négatives
 
@       IN  NS     ns1.votrenom.lan.
ns1     IN  A      172.16.10.2
dhcp    IN  A      172.16.10.1
web     IN  A      172.16.10.3
www     IN  CNAME  web
```
 
| Élément | Rôle |
|---------|------|
| `@` | Le nom de la zone elle-même (`votrenom.lan.`) |
| `IN` | Classe *Internet* (toujours la même) |
| `SOA` | Serveur principal de la zone (`ns1.votrenom.lan.`), puis adresse du responsable, où le premier `.` remplace le `@` (`admin.votrenom.lan.` = `admin@votrenom.lan`) |
| **serial** | **Numéro de version** de la zone, à **incrémenter à chaque modification**. Convention : `AAAAMMJJnn` |
 
### Le point final
 
Dans un fichier de zone, un nom **sans point final** est **relatif** : le nom de la zone lui est ajouté.
 
| Écrit dans la zone `votrenom.lan` | Nom compris |
|-----------------------------------|-------------|
| `web` | `web.votrenom.lan.` |
| `web.votrenom.lan.` | `web.votrenom.lan.` |
| `web.votrenom.lan` (point oublié) | `web.votrenom.lan.votrenom.lan.` |
 
{{%notice style="warning" title="Erreur fréquente"%}}
L'oubli du point final est l'erreur la plus courante dans un fichier de zone. Elle ne provoque souvent aucun message d'erreur : le nom est simplement faux.
{{%/notice%}}
 
## Côté client
 
### Ordre de résolution
 
Avant d'interroger un serveur DNS, le résolveur du client consulte le fichier **`hosts`**, qui associe manuellement des noms à des adresses :
 
- Linux : `/etc/hosts`;
- Windows : `C:\Windows\System32\drivers\etc\hosts`.
```text
127.0.0.1     localhost
172.16.10.3   web.votrenom.lan web
```
 
### Linux (Ubuntu)
 
Sur Ubuntu, le service **systemd-resolved** joue le rôle de résolveur. Les applications l'interrogent à l'adresse locale `127.0.0.53`, et il transmet les requêtes au serveur DNS reçu par DHCP.
 
```bash
resolvectl status          # serveur(s) DNS utilisé(s), par interface
resolvectl query web.votrenom.lan
resolvectl flush-caches    # vider le cache local
```
 
{{%notice style="info" title="127.0.0.53"%}}
Sur Ubuntu, `/etc/resolv.conf` contient `nameserver 127.0.0.53`. Ce n'est pas une erreur : c'est l'adresse de systemd-resolved, qui relaie ensuite vers le vrai serveur DNS.
{{%/notice%}}
 
### Windows
 
```powershell
nslookup www.cegepmv.ca
nslookup www.cegepmv.ca 8.8.8.8     # en interrogeant un serveur précis
ipconfig /displaydns                # afficher le cache local
ipconfig /flushdns                  # vider le cache local
```
 
## La commande dig
 
`dig` interroge un serveur DNS et affiche sa réponse en détail.
 
```bash
dig www.votrenom.lan                  # enregistrement A, serveur par défaut
dig @172.16.10.2 www.votrenom.lan     # en interrogeant un serveur précis
dig votrenom.lan NS                   # un autre type d'enregistrement
dig -x 172.16.10.3                    # résolution inverse
dig +short www.votrenom.lan           # réponse seulement
dig +norecurse @198.41.0.4 cegepmv.ca # requête itérative
```
 
### Lire une réponse
 
```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31337
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
 
;; QUESTION SECTION:
;www.votrenom.lan.          IN   A
 
;; ANSWER SECTION:
www.votrenom.lan.   3600    IN   CNAME  web.votrenom.lan.
web.votrenom.lan.   3600    IN   A      172.16.10.3
 
;; Query time: 1 msec
;; SERVER: 172.16.10.2#53(172.16.10.2) (UDP)
```
 
| Élément | Signification |
|---------|---------------|
| `status` | `NOERROR` : réponse trouvée · `NXDOMAIN` : le nom n'existe pas · `SERVFAIL` : le serveur n'a pas pu résoudre · `REFUSED` : le serveur refuse de répondre |
| `aa` | *Authoritative Answer* : la réponse vient d'un serveur **autoritaire** pour la zone |
| `rd` | *Recursion Desired* : le client demande une requête récursive |
| `ra` | *Recursion Available* : le serveur accepte les requêtes récursives |
| `QUESTION` | La question posée |
| `ANSWER` | La réponse |
| `AUTHORITY` | Dans un renvoi : les serveurs `NS` à interroger ensuite |
| `ADDITIONAL` | Informations utiles, par exemple l'adresse IP des serveurs de la section `AUTHORITY` |
| `Query time`, `SERVER` | Durée de la requête et serveur qui a répondu |
 
{{%notice style="tip" title="À retenir"%}}
+ DNS utilise **UDP 53** (et TCP 53). 
+ Le client envoie une **requête récursive** à son serveur récursif, qui effectue des **requêtes itératives** auprès des serveurs racine, du TLD, puis du serveur **autoritaire** du domaine. 
+ Les réponses sont conservées en **cache** <!-- pendant leur **TTL**-->. 
+ Une zone est décrite par un **fichier de zone** : un `SOA`, des `NS`, puis des enregistrements `A`, `CNAME`, etc.
{{%/notice%}}


# 6.3.1. Exercices
----------------
 
### 1. Ordonner une résolution
 
Le cache du serveur récursif est vide. Un poste demande l'adresse de `www.info.cegepmv.ca`; le domaine `info.cegepmv.ca` a été délégué au département d'informatique.
 
Remettez les messages dans l'ordre, puis indiquez pour chacun s'il s'agit d'une requête **récursive**, d'une requête **itérative**, d'un **renvoi** ou d'une **réponse finale**.
 
1. Le serveur de `cegepmv.ca` répond : « voici les serveurs de `info.cegepmv.ca` ».
2. Le poste demande l'adresse de `www.info.cegepmv.ca` à son serveur récursif.
3. Un serveur racine répond : « voici les serveurs de `ca` ».
4. Le serveur récursif interroge un serveur de `info.cegepmv.ca`.
5. Le serveur récursif interroge un serveur racine.
6. Le serveur récursif transmet l'adresse au poste.
7. Un serveur de `ca` répond : « voici les serveurs de `cegepmv.ca` ».
8. Le serveur récursif interroge un serveur de `ca`.
9. Le serveur de `info.cegepmv.ca` répond avec l'adresse.
10. Le serveur récursif interroge un serveur de `cegepmv.ca`.

Combien de messages auraient été nécessaires si le serveur récursif avait déjà eu les serveurs de `cegepmv.ca` en cache?
 
{{% expand title="Solution" %}}
| Ordre | Message | Type |
|-------|---------|------|
| 1 | Le poste interroge son serveur récursif (2) | Requête récursive |
| 2 | Le récursif interroge un serveur racine (5) | Requête itérative |
| 3 | La racine renvoie vers `ca` (3) | Renvoi |
| 4 | Le récursif interroge `ca` (8) | Requête itérative |
| 5 | `ca` renvoie vers `cegepmv.ca` (7) | Renvoi |
| 6 | Le récursif interroge `cegepmv.ca` (10) | Requête itérative |
| 7 | `cegepmv.ca` renvoie vers `info.cegepmv.ca` (1) | Renvoi |
| 8 | Le récursif interroge `info.cegepmv.ca` (4) | Requête itérative |
| 9 | `info.cegepmv.ca` donne l'adresse (9) | Réponse finale |
| 10 | Le récursif transmet l'adresse au poste (6) | Réponse finale |
 
Avec les serveurs de `cegepmv.ca` en cache, le récursif commence directement à l'étape 6 : **6 messages** au lieu de 10.
{{% /expand %}}

### 2. Choisir l'enregistrement
 
Pour chaque besoin, indiquez le type d'enregistrement à créer.
 
1. `intranet.exemple.lan` doit mener au même serveur que `web.exemple.lan`, et suivre automatiquement ses changements d'adresse.
2. Les courriels destinés à `@exemple.lan` doivent être livrés au serveur `mail.exemple.lan`.
3. Le nouveau serveur `ns2.exemple.lan` doit aussi répondre pour la zone `exemple.lan`.
4. Le serveur `web` doit être joignable en IPv6.

{{% expand title="Solution" %}}
1. **CNAME** : `intranet` → `web.exemple.lan.`
2. **MX**
3. **NS** (en plus d'un `A` pour `ns2`)
4. **AAAA**
{{% /expand %}}

### 3. Raisonner
 
1. Pourquoi un serveur racine ne peut-il pas répondre directement à la question « quelle est l'adresse de `www.cegepmv.ca`? »
2. Le serveur DNS d'une entreprise accepte les requêtes récursives de n'importe quelle adresse. Quel est le risque? Que faut-il configurer?
3. Un poste reçoit par DHCP le serveur DNS `172.16.10.2`, mais ce serveur est éteint. L'utilisateur peut-il naviguer sur `www.cegepmv.ca`? Et sur `http://<adresse IP du site>`? Justifiez.

{{% expand title="Solution" %}}
1. Il ne connaît que les serveurs des TLD. Il renvoie donc vers les serveurs de `ca`.
2. C'est un **résolveur ouvert**, utilisable pour des attaques par amplification. Il faut limiter les requêtes récursives aux réseaux de l'entreprise.
3. Pas par le nom : la résolution échoue. Par l'adresse IP, oui : aucune résolution n'est nécessaire, et le réseau (IP, routage) fonctionne.
{{% /expand %}}

# 6.3.2 Laboratoires
-------------

# 6.3.2.1. - Résolution itérative
 
## Contexte
 
Dans ce laboratoire, vous allez jouer le rôle d'un **serveur récursif** : en partant d'un serveur racine, vous allez interroger un à un les serveurs de la hiérarchie jusqu'à obtenir l'adresse de :
 
```text
www.wikipedia.org
```
 
Vous utiliserez l'option `+norecurse` de `dig`, qui envoie des **requêtes itératives** : chaque serveur répond avec ce qu'il sait, sans interroger personne d'autre.
 
## Préparation
 
- La machine **client** obtient sa configuration par DHCP et accède à Internet par `dhcp-router` (laboratoire 6.2.1).
- Le serveur DNS `172.16.10.2` n'est pas encore configuré : dans ce laboratoire, chaque commande précise le serveur interrogé avec `@`.
- Toutes les commandes s'exécutent dans un terminal de **client**.

## 1- Trouver les serveurs racine
 
```bash
dig @8.8.8.8 . NS
```
 
### Questions
 
1. Combien de serveurs racine la réponse contient-elle? Quel est leur nom?

Pour la suite, vous utiliserez `a.root-servers.net`, dont l'adresse est `198.41.0.4`.

Vous pouvez le vérifier en exécutant la commande `dig` suivante : 
```bash
dig @8.8.8.8 a.root-servers.net. A
```
 
## 2- Interroger la racine
 
```bash
dig +norecurse @198.41.0.4 www.wikipedia.org
```
ou
```bash
dig +norecurse @a.root-servers.net. www.wikipedia.org
```
 
### Questions
 
1. La section `ANSWER` contient-elle une réponse? Quel est le `status`?
2. Que contient la section `AUTHORITY`? Pour quelle zone?
3. Que contient la section `ADDITIONAL`? 

Relevez l'adresse IPv4 d'un des serveurs.

## 3- Interroger le TLD
 
Interrogez l'un des serveurs relevés à l'étape précédente :
 
```bash
dig +norecurse @<ADRESSE_OU_NOM_DU_SERVEUR_TLD> www.wikipedia.org
```
 
Relevez le nom et l'adresse IPv4 d'un des serveurs de `wikipedia.org`.
 
## 4- Interroger le serveur autoritaire
 
```bash
dig +norecurse @<ADRESSE_DU_SERVEUR_WIKIPEDIA> www.wikipedia.org
```
 
### Questions
 
1. Quel drapeau (*flag*) indique que ce serveur est autoritaire?
2. La réponse est-elle une adresse IP? Quel type d'enregistrement obtenez-vous?
3. Que devez-vous faire pour obtenir l'adresse IP finale? Faites-le.
## 5- Comparer avec dig +trace
 
L'option `+trace` effectue automatiquement la même démarche :
 
```bash
dig +trace @8.8.8.8 www.wikipedia.org
```
 
Complétez le tableau à partir de vos résultats :
 
| Étape | Serveur interrogé (nom et adresse) | Zone dont il est responsable | Ce qu'il répond |
|-------|------------------------------------|------------------------------|-----------------|
| 1 | | Racine | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
 
## Questions de synthèse
 
1. Combien de serveurs avez-vous interrogés pour obtenir l'adresse? Lesquels auraient été évités si un serveur récursif avait déjà eu les serveurs de `org` en cache?
2. Dans ce laboratoire, qui a joué le rôle du serveur récursif? Dans une situation normale, quelle machine fait ce travail pour le client?


# 6.3.2.2. Serveur DNS BIND
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez configurer **BIND** sur `dns-server` pour qu'il soit :
 
1. **récursif** pour les machines du LAN, et seulement pour elles;
2. **autoritaire** pour la zone `votrenom.lan`.
Le client reçoit déjà l'adresse `172.16.10.2` comme serveur DNS par DHCP : dès que BIND fonctionnera, il pourra résoudre les noms.
 
| Nom | Type | Valeur |
|-----|------|--------|
| `ns1.votrenom.lan` | A | `172.16.10.2` |
| `dhcp.votrenom.lan` | A | `172.16.10.1` |
| `web.votrenom.lan` | A | `172.16.10.3` |
| `www.votrenom.lan` | CNAME | `web.votrenom.lan` |
 
{{%notice style="note" title="Important"%}}
Remplacez `votrenom` par votre nom (par exemple `tremblay.lan`) partout dans ce laboratoire.
{{%/notice%}}
 
{{%notice style="info" title="Le domaine .lan"%}}
`.lan` n'est pas un vrai TLD : il n'existe pas sur Internet, et seul votre serveur connaît les noms qui s'y terminent. En entreprise, on utilise plutôt un sous-domaine du domaine de l'organisation (`interne.cegepmv.ca`) ou le TLD `.internal`, réservé pour les réseaux privés.
{{%/notice%}}
 
## Préparation
 
- `dns-server` est configuré selon le laboratoire 6.1.1 et `bind` est installé (section 6.0).
- `client` a obtenu sa configuration par DHCP (laboratoire 6.2.1).
- Ouvrez une session `ssh dns` depuis le poste Windows.
## 1- Serveur récursif
 
### 1. Lire la configuration par défaut
 
Sur **dns-server** :
 
```bash
sudo less /etc/named.conf
```
 
Repérez les directives `listen-on`, `allow-query` et `recursion` dans le bloc `options`.
 
### Questions
 
1. Sur quelle(s) adresse(s) BIND écoute-t-il par défaut?
2. Quelles machines ont le droit de l'interroger?
3. Pourquoi cette configuration par défaut empêche-t-elle le client d'utiliser le serveur?

### 2. Modifier la configuration
 
Faites une copie de sauvegarde, puis modifiez le bloc `options` :
 
```bash
sudo cp /etc/named.conf /etc/named.conf.orig
sudo nano /etc/named.conf
```
 
```text
options {
        listen-on port 53 { 127.0.0.1; 172.16.10.2; };
        ...
        allow-query     { localhost; 172.16.10.0/24; };
        recursion yes;
        ...
};
```
 
Ne modifiez pas les autres lignes.
 
### 3. Valider, démarrer et ouvrir le pare-feu
 
```bash
sudo named-checkconf
sudo systemctl enable --now named
systemctl status named
sudo ss -tulnp | grep :53
sudo firewall-cmd --add-service=dns --permanent
sudo firewall-cmd --reload
```
 
### 4. Tester depuis le client
 
Dans un terminal de **client** :
 
```bash
dig @172.16.10.2 www.wikipedia.org
```
 
### Questions
 
1. Sur quelles adresses, quels ports et quels protocoles BIND écoute-t-il maintenant?
2. Quels drapeaux de la réponse indiquent que le serveur est récursif et qu'il n'est pas autoritaire pour `wikipedia.org`?
3. Pourquoi `allow-query` limite-t-il les requêtes à `172.16.10.0/24`?

## 2- Zone autoritaire
 
### 1. Créer le fichier de zone
 
Sur **dns-server** :
 
```bash
sudo nano /var/named/votrenom.lan.zone
```
 
Écrivez le fichier de zone de `votrenom.lan` à partir du tableau du contexte et de l'exemple de la section 6.3. Utilisez comme serial la date du jour suivie de `01` (par exemple `2026111201`).
 
Le fichier doit appartenir au groupe `named`, sinon BIND ne pourra pas le lire :
 
```bash
sudo chgrp named /var/named/votrenom.lan.zone
```
 
### 2. Valider le fichier de zone
 
```bash
sudo named-checkzone votrenom.lan /var/named/votrenom.lan.zone
```
 
Le résultat doit se terminer par `OK`.
 
### 3. Déclarer la zone
 
À la fin de `/etc/named.conf`, ajoutez :
 
```text
zone "votrenom.lan" IN {
        type primary;
        file "votrenom.lan.zone";
};
```
 
Le chemin du fichier est relatif au répertoire `/var/named` (directive `directory` du bloc `options`).
 
### 4. Appliquer
 
```bash
sudo named-checkconf
sudo systemctl restart named
systemctl status named
```
 
### 5. Tester
 
Dans un terminal de **client** :
 
```bash
dig @172.16.10.2 ns1.votrenom.lan
dig @172.16.10.2 www.votrenom.lan
dig @172.16.10.2 votrenom.lan NS
dig @172.16.10.2 inconnu.votrenom.lan
ping -c 3 dhcp.votrenom.lan
```
 
### Questions
 
1. Quel drapeau apparaît maintenant dans les réponses? Que signifie-t-il?
2. Que contient la réponse pour `www.votrenom.lan`? Pourquoi deux enregistrements?
3. Quel `status` obtenez-vous pour `inconnu.votrenom.lan`? Que contient la section `AUTHORITY`?

# 6.4. Web : HTTP et HTTPS
-------------------
 
Le **Web** repose sur le protocole **HTTP (*HyperText Transfer Protocol*)** : un navigateur (le client) demande une ressource à un serveur web, qui la lui envoie. Une page, une image, une feuille de style ou une réponse JSON d'une API sont toutes des ressources obtenues par HTTP.
 
| Protocole | Transport et port | Sécurité |
|-----------|-------------------|----------|
| **HTTP** | TCP `80` | Aucune : tout circule en clair |
| **HTTPS** | TCP `443` | HTTP transporté dans une connexion chiffrée **TLS** |
 
## L'URL
 
Une **URL (*Uniform Resource Locator*)** indique au client **comment** et **où** obtenir une ressource :

![Structure d'une URL](/images/06/06-url-structure.png)
 
| Partie | Rôle | Lien avec le réseau |
|--------|------|---------------------|
| **Schéma** | Protocole à utiliser | `http` ou `https` |
| **Hôte** | Serveur à contacter | Traduit en adresse IP par **DNS** (section 6.3) |
| **Port** | Port du serveur | Facultatif : `80` pour `http`, `443` pour `https` par défaut |
| **Chemin** | Ressource demandée sur le serveur | Transmis dans la requête HTTP |
| **Requête** (*query string*) | Paramètres envoyés au serveur | Transmis dans la requête HTTP |
| **Fragment** | Position dans la page | **Jamais** envoyé au serveur : utilisé seulement par le navigateur |
 
## Le protocole HTTP
 
HTTP est un protocole :
 
- **client/serveur** : le client envoie une **requête**, le serveur renvoie une **réponse**;
- **texte** (jusqu'à HTTP/1.1) : les messages sont lisibles, comme vous le verrez avec `nc`;
- **sans état** (*stateless*) : chaque requête est indépendante, le serveur ne se souvient pas des requêtes précédentes. Pour reconnaître un utilisateur d'une page à l'autre (connexion, panier), le serveur lui envoie un **cookie**, que le client renvoie ensuite dans chaque requête.
![Structure d'une requête et d'une réponse HTTP](/images/06/06-http-message.png)
 
### La requête

![Structure d'une requête HTTP](/images/06/06-http-requete.png)
 
| Élément | Rôle |
|---------|------|
| **Ligne de requête** | **Méthode**, chemin de la ressource, version de HTTP |
| **En-têtes** | Informations sur la requête, sous la forme `Nom: valeur` |
| **Ligne vide** | Marque la fin des en-têtes |
| **Corps** (facultatif) | Données envoyées au serveur (formulaire, JSON), surtout avec `POST` et `PUT` |
 
L'en-tête **`Host`** est **obligatoire** en HTTP/1.1. Il indique le nom du site demandé : c'est lui qui permet à un même serveur d'héberger plusieurs sites sur une seule adresse IP (hôtes virtuels, étudiés dans la sous-section sur *nginx*).
 
### Les méthodes
 
La méthode indique l'action demandée sur la ressource.
 
| Méthode | Action | Corps dans la requête? | Sûre | Idempotente |
|---------|--------|------------------------|------|-------------|
| **GET** | Obtenir une ressource | Non | Oui | Oui |
| **POST** | Envoyer des données à traiter (formulaire, création d'une ressource) | Oui | Non | Non |
| **PUT** | Créer ou remplacer une ressource | Oui | Non | Oui |
| **DELETE** | Supprimer une ressource | Non | Non | Oui |
 
- Une méthode **sûre** ne modifie rien sur le serveur : le navigateur peut la répéter, la mettre en cache ou la précharger sans risque.
- Une méthode **idempotente** produit le même résultat qu'on l'envoie une fois ou dix fois. Supprimer deux fois le produit `#7` laisse le serveur dans le même état; envoyer deux fois un formulaire de commande avec `POST` crée deux commandes.

Lorsqu'une requête contient un corps, l'en-tête **`Content-Type`** indique son format :

![Requête HTTP POST avec un corps JSON](/images/06/06-http-post.png)
 
### La réponse

![Structure d'une réponse HTTP](/images/06/06-http-reponse.png)

| Élément | Rôle |
|---------|------|
| **Ligne de statut** | Version, **code de statut** et sa description |
| **En-têtes** | Informations sur la réponse |
| **Corps** | La ressource (HTML, image, JSON…) |
 
### Les codes de statut
 
Le premier chiffre du code indique sa catégorie :
 
| Catégorie | Signification | Codes courants |
|-----------|---------------|----------------|
| **2xx** | Succès | `200 OK` · `201 Created` · `204 No Content` |
| **3xx** | Redirection : la ressource est ailleurs | `301 Moved Permanently` · `302 Found` · `304 Not Modified` |
| **4xx** | Erreur du **client** | `400 Bad Request` · `401 Unauthorized` · `403 Forbidden` · `404 Not Found` · `405 Method Not Allowed` |
| **5xx** | Erreur du **serveur** | `500 Internal Server Error` · `502 Bad Gateway` · `503 Service Unavailable` |
 
{{%notice style="tip" title="Pour le diagnostic"%}}
Recevoir un code de statut, même `404` ou `500`, prouve que tout le réseau fonctionne : DNS, routage, connexion TCP et serveur web ont répondu. Le problème se situe dans l'application ou dans la requête.
{{%/notice%}}
 
### Les en-têtes courants
 
| En-tête | Dans | Rôle |
|---------|------|------|
| `Host` | Requête | Nom du site demandé |
| `User-Agent` | Requête | Logiciel du client |
| `Content-Type` | Les deux | Format du corps (`text/html`, `application/json`, `image/png`) |
| `Content-Length` | Les deux | Taille du corps, en octets |
| `Server` | Réponse | Logiciel du serveur |
| `Location` | Réponse | Nouvelle adresse, avec un code `3xx` |
| `Set-Cookie` / `Cookie` | Réponse / requête | Envoi d'un cookie au client / renvoi du cookie au serveur |
 

## HTTPS 
**HTTPS** transporte HTTP dans une connexion chiffrée par **TLS (*Transport Layer Security*)**. TLS assure :
 
- la **confidentialité** : un observateur ne peut pas lire les échanges;
- l'**intégrité** : un message modifié en route est détecté;
- l'**authentification du serveur** : grâce à son **certificat**, le client vérifie qu'il parle au bon site.

{{%notice style="note" title="Rappel"%}}
Les certificats, les autorités de certification et la configuration HTTPS de nginx ont été étudiés dans le cours **Sécurité informatique**. Ici, on s'intéresse à ce qui circule sur le réseau.
{{%/notice%}}

# 6.4.1. Nginx
--------------

## Le serveur web nginx
 
Un **serveur web** reçoit les requêtes HTTP et y répond. Dans ce cours, on utilise **nginx**, l'un des serveurs web les plus utilisés au monde. Il peut :
 
- servir des **fichiers statiques** (HTML, CSS, images, application React compilée);
- servir de **mandataire inverse** (*reverse proxy*) : il reçoit les requêtes des clients et les transmet à une application (*backend*) qui s'exécute sur le même serveur ou sur un autre;
- gérer HTTPS pour le compte de ces applications.

### Place du serveur web dans une application
 
Une application web est souvent organisée en **trois tiers** :
 
![Architecture web en trois tiers](/images/06/06-web-3tiers.png)
 
| Tiers | Rôle | Exemples |
|-------|------|----------|
| **Présentation** | Affiche l'interface et envoie les requêtes HTTP | Navigateur, application React |
| **Logique** | Traite les requêtes, applique les règles de l'application | *Backend* : Node.js, Python, PHP, Java |
| **Données** | Stocke les données | PostgreSQL, MySQL, MongoDB |
 
nginx se place devant le tiers logique, comme porte d'entrée. Pour une page statique, il répond directement. Pour une requête dynamique (`/api/...`), il la transmet au *backend*, qui interroge la base de données et renvoie le résultat.
 
### Fichiers de configuration (Ubuntu)
 
| Fichier ou répertoire | Rôle |
|-----------------------|------|
| `/etc/nginx/nginx.conf` | Configuration générale |
| `/etc/nginx/sites-available/` | Un fichier de configuration par site |
| `/etc/nginx/sites-enabled/` | Liens vers les sites **activés** |
| `/var/www/html/` | Contenu du site par défaut |
| `/var/log/nginx/access.log` | Une ligne par requête reçue |
| `/var/log/nginx/error.log` | Erreurs du serveur |
 
{{%notice style="note" title="Rappel"%}}
`sudo nginx -t` valide la configuration (section 6.0). Après une modification, `sudo systemctl reload nginx` applique la nouvelle configuration sans interrompre les connexions en cours.
{{%/notice%}}
 
### Le bloc server
 
Chaque site est décrit par un bloc **`server`** :
 
```nginx
server {
    listen 80;
    server_name www.votrenom.lan;
 
    root /var/www/votrenom;
    index index.html;
 
    location / {
        try_files $uri $uri/ =404;
    }
}
```
 
| Directive | Rôle |
|-----------|------|
| `listen` | Port (et adresse) d'écoute |
| `server_name` | Nom(s) du site, comparé(s) à l'en-tête `Host` de la requête |
| `root` | Répertoire qui contient les fichiers du site |
| `index` | Fichier servi lorsque le chemin est un répertoire (`/`) |
| `location` | Règles pour les chemins qui correspondent |
| `try_files` | Cherche le fichier demandé, puis un répertoire, sinon répond `404` |
 
### Hôtes virtuels
 
Un même serveur, avec **une seule adresse IP** et **un seul port**, peut héberger plusieurs sites : ce sont des **hôtes virtuels** (*virtual hosts*).
 
![Hôtes virtuels : nginx choisit le site selon l'en-tête Host](/images/06/06-nginx-hotes-virtuels.png)
 
Pour chaque requête, nginx compare l'en-tête **`Host`** aux `server_name` de ses blocs `server` :
 
| Requête reçue | Bloc choisi |
|---------------|-------------|
| `Host: www.votrenom.lan` | Celui dont `server_name` est `www.votrenom.lan` |
| `Host: intranet.votrenom.lan` | Celui dont `server_name` est `intranet.votrenom.lan` |
| `Host` inconnu, ou adresse IP (`Host: 172.16.10.3`) | Le bloc marqué **`default_server`** dans sa directive `listen` |
 
Les noms des sites doivent tous être traduits par DNS vers l'adresse du serveur, souvent avec des `CNAME`.
 
### Le journal des accès
 
Chaque requête ajoute une ligne à `/var/log/nginx/access.log` :
 
```text
172.16.10.100 - - [10/Nov/2026:14:03:12 -0500] "GET / HTTP/1.1" 200 615 "-" "curl/8.14.1"
```
 
| Partie | Signification |
|--------|---------------|
| `172.16.10.100` | Adresse IP du client |
| `[10/Nov/2026:14:03:12 -0500]` | Date et heure |
| `"GET / HTTP/1.1"` | Ligne de requête |
| `200` | Code de statut renvoyé |
| `615` | Taille du corps de la réponse, en octets |
| `"curl/8.14.1"` | En-tête `User-Agent` du client |
 
{{%notice style="tip" title="À retenir"%}}
HTTP utilise **TCP 80**, HTTPS **TCP 443**. Une requête contient une **méthode**, un chemin, des en-têtes (dont **`Host`**) et parfois un corps; la réponse contient un **code de statut**, des en-têtes et la ressource. HTTPS chiffre tout le dialogue HTTP, mais le **nom du site** reste visible (DNS, SNI). nginx choisit le site à servir selon l'en-tête **`Host`** : c'est le principe des **hôtes virtuels**.
{{%/notice%}}

# 6.4.2. Exercices
----------------
 
### 1. Décomposer des URL
 
Pour chaque URL, indiquez le schéma, le nom d'hôte, le port utilisé (même s'il n'est pas écrit), le chemin, et ce qui est envoyé au serveur dans la ligne de requête.
 
1. `http://www.votrenom.lan/`
2. `https://www.cegepmv.ca/programmes/informatique?session=automne#admission`
3. `http://172.16.10.3:8080/api/produits/7`

{{% expand title="Solution" %}}
| URL | Schéma | Hôte | Port | Chemin | Ligne de requête |
|-----|--------|------|------|--------|------------------|
| 1 | `http` | `www.votrenom.lan` | `80` | `/` | `GET / HTTP/1.1` |
| 2 | `https` | `www.cegepmv.ca` | `443` | `/programmes/informatique` | `GET /programmes/informatique?session=automne HTTP/1.1` |
| 3 | `http` | `172.16.10.3` (pas de DNS nécessaire) | `8080` | `/api/produits/7` | `GET /api/produits/7 HTTP/1.1` |
 
Le fragment `#admission` n'est jamais envoyé au serveur.
{{% /expand %}}
 
### 2. Lire un échange
 
```text
POST /connexion HTTP/1.1
Host: intranet.votrenom.lan
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
 
utilisateur=student&motdepasse=Secret
```
 
```text
HTTP/1.1 302 Found
Server: nginx/1.28.0
Location: /accueil
Set-Cookie: session=8f3a2c91; HttpOnly
Content-Length: 0
```
 
1. Quelle méthode le client utilise-t-il? Pourquoi celle-ci plutôt que `GET`?
2. Quel site le client demande-t-il? À quoi sert cette information pour le serveur?
3. Que signifie la réponse? Que fera le navigateur ensuite?
4. À quoi servira l'en-tête `Set-Cookie`?
5. Cet échange a lieu en HTTP. Qu'est-ce qu'un observateur sur le réseau peut apprendre? Que changerait HTTPS?

{{% expand title="Solution" %}}
1. `POST` : le client envoie des données (le formulaire de connexion) dans le corps de la requête. Avec `GET`, les données apparaîtraient dans l'URL, donc dans les journaux et l'historique du navigateur.
2. `intranet.votrenom.lan` (en-tête `Host`). Le serveur l'utilise pour choisir le site (hôte virtuel) qui traitera la requête.
3. Redirection (`302`) : la ressource se trouve à `/accueil` (en-tête `Location`). Le navigateur envoie automatiquement une nouvelle requête `GET /accueil`.
4. Le navigateur renverra ce cookie (`Cookie: session=8f3a2c91`) dans ses prochaines requêtes : le serveur saura que l'utilisateur est connecté, malgré le caractère sans état de HTTP.
5. Tout : l'utilisateur, le mot de passe et le cookie de session (qui permettrait d'usurper la session). Avec HTTPS, l'observateur ne verrait que les adresses IP, les ports et le nom `intranet.votrenom.lan` (SNI).
{{% /expand %}}

### 3. Méthodes et codes
 
Pour chaque situation, indiquez la méthode HTTP utilisée par le client et le code de statut le plus probable.
 
1. Un navigateur affiche la page d'accueil d'un site, qui existe.
2. Une application crée un nouveau produit avec l'API `/api/produits`.
3. Un utilisateur demande une page qui a été supprimée.

{{% expand title="Solution" %}}
1. `GET` → `200 OK`
2. `POST` → `201 Created`
3. `GET` → `404 Not Found`
{{% /expand %}}

### 5. Synthèse : de l'URL à la page
 
Le poste `client` (`172.16.10.100`) vient d'obtenir son adresse par DHCP. Ses caches ARP et DNS sont vides. L'utilisateur ouvre `http://www.votrenom.lan/`.
 
1. Énumérez, dans l'ordre, tous les échanges réseau nécessaires jusqu'à l'affichage de la page. Pour chacun, indiquez le protocole, la couche, la machine contactée et le port s'il y a lieu.
2. Lesquels de ces échanges passent par `dhcp-router`?

{{% expand title="Solution" %}}
1. Échanges :
    | # | Échange | Protocole | Couche | Machine | Port |
    |---|---------|-----------|--------|---------|------|
    | 1 | Adresse MAC du serveur DNS | ARP | Accès réseau | `dns-server` (`172.16.10.2`) | — |
    | 2 | Adresse de `www.votrenom.lan` | DNS | Application (UDP) | `dns-server` | `53` |
    | 3 | Réponse : `CNAME` `web.votrenom.lan`, `A` `172.16.10.3` | DNS | Application (UDP) | `dns-server` | `53` |
    | 4 | Adresse MAC du serveur web | ARP | Accès réseau | `web-server` (`172.16.10.3`) | — |
    | 5 | Établissement de la connexion (SYN, SYN-ACK, ACK) | TCP | Transport | `web-server` | `80` |
    | 6 | `GET / HTTP/1.1` avec `Host: www.votrenom.lan` | HTTP | Application (TCP) | `web-server` | `80` |
    | 7 | `HTTP/1.1 200 OK` et la page | HTTP | Application (TCP) | `web-server` | `80` |
2. Aucun : `dns-server` et `web-server` sont dans le même réseau que le client, qui les joint directement, sans passer par sa passerelle.
{{% /expand %}}


# 6.4.3. Laboratoire
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez :
 
1. mettre en service `web-server`, la dernière machine du réseau du chapitre;
2. héberger **deux sites** sur le même serveur, grâce aux hôtes virtuels;
3. valider le fonctionnement de **tout le réseau du chapitre**, de bout en bout.

| Machine | Interface | Configuration |
|---------|-----------|---------------|
| **web-server** | `ens160` (LAN-SERVICES) | `172.16.10.3/24`, passerelle `172.16.10.1`, DNS `172.16.10.2` |
 
{{%notice style="note" title="Important"%}}
Remplacez `votrenom` par le nom de domaine choisi au laboratoire DNS.
{{%/notice%}}
 
## Préparation
 
### 1. Configurer web-server
 
La VM `web-server` (`ubuntu-server-26.04.ova`) est créée selon la section 6.0, avec nginx installé et sa carte réseau dans **LAN-SERVICES**.
 
Sur la console de **web-server**, configurez son interface avec Netplan (comme au chapitre 4), dans un fichier de `/etc/netplan/` :
 
```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 172.16.10.3/24
      routes:
        - to: default
          via: 172.16.10.1
      nameservers:
        addresses:
          - 172.16.10.2
```
 
```bash
sudo netplan apply
ping -c 3 dhcp.votrenom.lan
```
 
## 1- Le serveur web
 
### 1. Vérifier le service
 
Sur **web-server** :
 
```bash
systemctl status nginx
sudo ss -tlnp | grep nginx
cat /etc/nginx/sites-enabled/default
```

### 2. Consulter le site par défaut
 
Dans un terminal de **client** :
 
```bash
curl -v http://www.votrenom.lan/
```
 
Ouvrez aussi `http://www.votrenom.lan` dans **Firefox**.
 
### 3. Observer le journal
 
Sur **web-server**, dans une deuxième session `ssh web`, affichez le journal des accès en continu :
 
```bash
sudo tail -f /var/log/nginx/access.log
```
 
Rechargez la page dans Firefox, puis relancez la commande `curl`. Gardez cette session ouverte pour la suite du laboratoire.
 
### Questions
 
1. Sur quel port nginx écoute-t-il? Quel répertoire contient le site par défaut?
2. Dans la sortie de `curl -v`, repérez la ligne de requête, l'en-tête `Host`, la ligne de statut et l'en-tête `Content-Type`. Les lignes de la requête commencent par `>`, celles de la réponse par `<`.
3. Avant d'envoyer la requête HTTP, `curl` a affiché l'adresse IP du serveur. Quel service lui a fourni cette adresse? Quel enregistrement a été utilisé?
4. Dans le journal, quelle différence voyez-vous entre la requête de Firefox et celle de `curl`?

## 2- Deux sites sur le même serveur
 
Vous allez créer deux sites sur `web-server` :
 
| Site | Répertoire |
|------|------------|
| `www.votrenom.lan` | `/var/www/votrenom` |
| `pokedex.votrenom.lan` | `/var/www/pokedex` |
 
Le site par défaut (`/var/www/html`) reste en place : il répondra aux requêtes qui ne correspondent à aucun des deux sites.
 
### 1. Le contenu des sites

#### Le site public
Sur le poste Windows, créez un fichier `index.html` pour votre site public, avec le contenu de votre choix, par exemple :
 
```html
<!DOCTYPE html>
<html lang="fr">
  <head><meta charset="utf-8"><title>votrenom.lan</title></head>
  <body><h1>Site public de votrenom.lan</h1></body>
</html>
```

Copiez-le sur `web-server` avec `scp` (section 6.1) :
 
```powershell
scp index.html web:/tmp/
```
 
Sur **web-server** :
 
```bash
sudo mkdir -p /var/www/votrenom
sudo mv /tmp/index.html /var/www/votrenom/
```

#### Le pokédex
Pokédex est une application développée en React. Elle a déjà été utilisée dans des laboratoires du cours de *Sécurité Informatique*. 

Avant de la déployer sur le serveur web, il faut la construire (*build*) à partir du code source. 

Sur votre poste Windows : 
```bash
git clone https://github.com/gbenachour/pokedex.git # Télécharger le code de GitHub
cd pokedex
npm install # installer les dépendances de l'application
npm run build # construire l'application (répertoire dist)
```
L'application à copier sur `web-server` se trouve dans le répertoire `dist`.

Copiez son contenu sur `web-server` avec `scp` (section 6.1) :
 
```powershell
scp -r dist web:/tmp/
``` 
Sur **web-server** :
 
```bash
sudo mkdir -p  /var/www/pokedex
sudo mv /tmp/dist/* /var/www/pokedex/
```

{{%notice style="tip" title="Même démarche pour votre application React"%}}
Si vous désirez déployer votre propre application React (par exemple, votre projet final du cours d'*Application Web*), les mêmes étapes sont à suivre.
{{%/notice%}}

### 2. La configuration
 
Créez le fichier `/etc/nginx/sites-available/votrenom` avec **deux** blocs `server` : un pour `www.votrenom.lan`, un pour `pokedex.votrenom.lan` (modèle dans la section *Nginx*).
 
Activez les sites, validez la configuration, puis appliquez-la :
 
```bash
sudo ln -s /etc/nginx/sites-available/votrenom /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
 
### 3. Ajouter le nom dans le DNS
 
Sur **dns-server**, ajoutez à la zone `votrenom.lan` :
 
```text
pokedex    IN  CNAME  web
```
 
N'oubliez pas d'**incrémenter le serial**. Validez la zone, puis rechargez BIND :
 
```bash
sudo named-checkzone votrenom.lan /var/named/votrenom.lan.zone
sudo systemctl reload named
```
 
Sur **client**, testez `curl http://pokedex.votrenom.lan/`, puis ouvrez les deux sites dans Firefox.
 
### Questions
 
1. Firefox se connecte aux deux sites en utilisant la même adresse IP et le même port. Pourquoi obtient-on des pages différentes?
2. Pourquoi utiliser un `CNAME` plutôt qu'un enregistrement `A` pour `pokedex`?
3. Quelle page obtenez-vous en visitant `http://172.16.10.3/`? Pourquoi?


## 3- Validation de bout en bout
 
Tous les services du chapitre sont maintenant en place. Vous allez vérifier qu'un poste qui se connecte au réseau obtient tout ce dont il a besoin. 
 
### Liste de vérification
 
Vérifiez chaque élément depuis **client**, et indiquez la commande utilisée :
 
| Vérification | Commande | Résultat |
|--------------|----------|----------|
| Le client a une adresse dans la plage `172.16.10.100` à `.199` | | |
| Sa passerelle est `172.16.10.1` | | |
| Son serveur DNS est `172.16.10.2` | | |
| `www.votrenom.lan` est résolu par un serveur **autoritaire** | | |
| `www.wikipedia.org` est résolu par le serveur récursif | | |
| Le site `www.votrenom.lan` s'affiche | | |
| Le site `pokedex.votrenom.lan` s'affiche | | |
| Le client accède à Internet | | |
| Tous les serveurs sont administrables par SSH depuis le poste Windows | | |
 
## Questions de synthèse
 
1. Expliquez pourquoi chaque service du chapitre dépend du précédent : DHCP, DNS, puis HTTP.
2. Un utilisateur se plaint que `http://intranet.votrenom.lan` ne fonctionne pas, alors que `http://www.votrenom.lan` fonctionne. Proposez une démarche de diagnostic, en indiquant pour chaque étape la commande utilisée et la machine sur laquelle vous l'exécutez.
