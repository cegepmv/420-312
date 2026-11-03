+++
pre = "(Bonus) - "
title = "En clair ou chiffré?"
weight = "612"
draft = false
+++
-------------
 
{{%notice style="info" title="Partie facultative"%}}
Cette partie s'adresse aux personnes qui ont terminé le laboratoire et souhaitent approfondir.
{{%/notice%}}
 
Vous allez capturer deux communications entre `dns-server` et `dhcp-router` : l'une avec `nc` (chapitre 5), l'autre avec SSH.
 
{{%notice style="warning" title="Avant de commencer"%}}
Fermez vos sessions `ssh dns`. Votre session d'administration vers `dns-server` passe par l'interface LAN de dhcp-router et serait capturée elle aussi. Dans cette partie, utilisez la **console VMware** de dns-server.
{{%/notice%}}
 
### 1. Préparer dhcp-router
 
Ouvrez **deux** sessions `ssh routeur`. Les sessions vers dhcp-router passent par son interface WAN : elles ne seront pas capturées.
 
Dans la session 1, ouvrez temporairement le port `5000` et démarrez la capture :
 
```bash
sudo firewall-cmd --add-port=5000/tcp
sudo tcpdump -i ens224 -n -w /tmp/ssh-vs-nc.pcap tcp port 22 or tcp port 5000
```
 
Dans la session 2, démarrez un serveur `nc` :
 
```bash
nc -l 5000
```
 
### 2. Générer le trafic
 
Sur la **console** de dns-server :
 
1. Communication en clair :
```bash
    nc 172.16.10.1 5000
```
    Tapez `utilisateur=student motdepasse=Secret123`, puis `Entrée` et `Ctrl+C`.
 
2. Communication chiffrée :
```bash
    ssh student@172.16.10.1
```
    Répondez `yes` à la question de première connexion, saisissez le mot de passe de `student`, puis exécutez :
```bash
    echo "motdepasse=Secret123"
    exit
```
 
### 3. Récupérer la capture
 
Arrêtez `tcpdump` (`Ctrl+C` dans la session 1). Puis, sur le poste Windows :
 
```powershell
scp routeur:/tmp/ssh-vs-nc.pcap .
```
 
Ouvrez le fichier dans Wireshark.
 
### 4. Analyser la communication nc
 
1. Appliquez le filtre `tcp.port == 5000`.
2. Faites **Suivre → TCP Flux**.
### 5. Analyser la communication SSH
 
1. Appliquez le filtre `ssh`.
2. Repérez les paquets **Protocol** : relevez la version annoncée par le client et par le serveur.
3. Repérez les paquets **Key Exchange Init** : développez-en un et relevez deux algorithmes proposés.
4. Observez la colonne *Info* des paquets suivants.
5. Faites **Suivre → TCP Flux** sur la conversation SSH.
### 6. Chercher le mot de passe
 
Dans **Édition → Trouver un paquet**, choisissez **Chaîne de caractères** et **Octets du paquet**, puis cherchez `Secret123`.
 
### Questions
 
1. Dans quelle(s) conversation(s) retrouvez-vous `Secret123`?
2. À partir de quel paquet les données SSH deviennent-elles illisibles?
3. Le mot de passe de `student`, saisi lors de la connexion SSH, apparaît-il dans la capture? Pourquoi?
4. Qu'est-ce qu'un observateur peut tout de même apprendre de la capture SSH?
5. Dressez un tableau de ce qu'un observateur peut voir et ne peut pas voir dans une communication `nc` et dans une session SSH.
Fermez `nc` dans la session 2 (`Ctrl+C`). La règle de pare-feu disparaîtra au prochain redémarrage ou rechargement du pare-feu.