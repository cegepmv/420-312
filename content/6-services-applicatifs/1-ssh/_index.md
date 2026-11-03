+++
pre = '<b>1. </b>'
title = "SSH"
weight = "610"
draft = false
+++
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