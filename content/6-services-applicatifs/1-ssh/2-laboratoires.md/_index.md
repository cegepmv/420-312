+++
pre = "Laboratoire - "
title = "Accès SSH au réseau du chapitre"
weight = "612"
draft = false
+++
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez mettre en place les deux premières machines du réseau du chapitre (section 6.0) : `dhcp-router` et `dns-server`.
 
Vous allez ensuite :
 
- administrer les deux machines depuis le poste Windows, grâce à un **rebond SSH**;
- en bonus, comparer dans une capture une communication **en clair** (`nc`) et une communication **chiffrée** (SSH).
| Machine | Interface | Adresse |
|---------|-----------|---------|
| **Poste Windows** | Carte réseau du poste | Attribuée automatiquement |
| **dhcp-router** | `ens160` (WAN, NAT) | Attribuée automatiquement (à relever) |
| **dhcp-router** | `ens224` (LAN-SERVICES) | `172.16.10.1/24` |
| **dns-server** | `ens160` (LAN-SERVICES) | `172.16.10.2/24` |
 
Les machines doivent avoir été créées et leurs paquets installés selon la section 6.0.
 
{{%notice style="info" title="Client SSH de Windows"%}}
Le client OpenSSH est installé par défaut sous Windows 10 et 11. Vérifiez dans un terminal PowerShell :
```powershell
ssh -V
```
{{%/notice%}}
 
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
 
Complétez le tableau :
 
| Élément | Valeur |
|---------|--------|
| Adresse WAN de dhcp-router | |
| Adresse IPv4 du poste Windows | |
 
## 1- Le service SSH
 
Sur **dhcp-router** et sur **dns-server** :
 
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
notepad $env:USERPROFILE\.ssh\config
```
 
Ajoutez les alias `routeur` et `dns` (section 6.1), avec l'adresse WAN relevée en préparation. Enregistrez.
 
### 2. Tester
 
```powershell
ssh routeur
ssh dns
```
 
### Questions
 
1. Combien de mots de passe la commande `ssh dns` vous demande-t-elle? Pour quelles machines?
2. Quelle directive du fichier remplace l'option `-J`?
3. À la prochaine séance, l'adresse WAN de dhcp-router a changé. Que devez-vous modifier?
## Questions de synthèse
 
1. Lors de la commande `ssh dns`, combien de connexions TCP et combien de sessions SSH sont établies? Entre quelles machines? Appuyez-vous sur les sorties de `ss` relevées dans la partie 2.
2. Expliquez pourquoi le rebond fonctionne alors que le poste Windows n'a aucune route vers `172.16.10.0/24` et que dhcp-router ne fait pas de *forwarding*.
3. Vous transférez un fichier avec `scp`. Ce transfert est-il protégé de la même façon qu'une session SSH? Justifiez.