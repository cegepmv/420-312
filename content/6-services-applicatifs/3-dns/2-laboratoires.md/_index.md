# 6.3.1. Laboratoire : résolution itérative
-------------
 
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
2. Quel est le TTL de ces enregistrements? Pourquoi est-il si long?
Pour la suite, vous utiliserez `a.root-servers.net`, dont l'adresse est `198.41.0.4`.
 
## 2- Interroger la racine
 
```bash
dig +norecurse @198.41.0.4 www.wikipedia.org
```
 
### Questions
 
1. La section `ANSWER` contient-elle une réponse? Quel est le `status`?
2. Que contient la section `AUTHORITY`? Pour quelle zone?
3. Que contient la section `ADDITIONAL`? Relevez l'adresse IPv4 d'un des serveurs.
## 3- Interroger le TLD
 
Interrogez l'un des serveurs relevés à l'étape précédente :
 
```bash
dig +norecurse @<ADRESSE_DU_SERVEUR_TLD> www.wikipedia.org
```
 
Relevez le nom et l'adresse IPv4 d'un des serveurs de `wikipedia.org`.
 
{{%notice style="tip" title="Pas d'adresse dans ADDITIONAL?"%}}
Si la section `ADDITIONAL` ne donne pas l'adresse d'un serveur, résolvez son nom avec un serveur récursif :
```bash
dig @8.8.8.8 +short <NOM_DU_SERVEUR>
```
{{%/notice%}}
 
## 4- Interroger le serveur autoritaire
 
```bash
dig +norecurse @<ADRESSE_DU_SERVEUR_WIKIPEDIA> www.wikipedia.org
```
 
### Questions
 
1. Quel drapeau indique que ce serveur est autoritaire?
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
2. Pourquoi les serveurs racine et TLD donnent-ils l'**adresse** des serveurs suivants dans la section `ADDITIONAL`, et pas seulement leur nom?
3. Dans ce laboratoire, qui a joué le rôle du serveur récursif? Dans une situation normale, quelle machine fait ce travail pour le client?
# 6.3.2. Laboratoire : serveur DNS BIND
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
## 2- Le cache
 
### 1. Observer les requêtes du serveur
 
Dans une deuxième session `ssh dns`, observez le trafic DNS de `dns-server` en direct :
 
```bash
sudo tcpdump -i ens160 -n port 53
```
 
### 2. Interroger un nom jamais demandé
 
Dans un terminal de **client**, choisissez un nom qui n'a probablement jamais été demandé au serveur (par exemple, le site d'un cégep d'une autre région), et interrogez-le **deux fois de suite** :
 
```bash
dig @172.16.10.2 www.cegepgranby.ca
dig @172.16.10.2 www.cegepgranby.ca
```
 
Relevez, pour chaque requête :
 
| Requête | Query time | TTL de l'enregistrement A | Nombre de paquets observés par tcpdump |
|---------|------------|---------------------------|----------------------------------------|
| 1 | | | |
| 2 | | | |
 
Arrêtez `tcpdump` (`Ctrl+C`).
 
### Questions
 
1. Lors de la première requête, à quelles adresses `dns-server` a-t-il envoyé des requêtes? À quoi correspondent-elles?
2. Pourquoi la deuxième requête ne génère-t-elle que deux paquets?
3. Expliquez la différence de TTL entre les deux réponses.
## 3- Le client utilise le serveur
 
Dans un terminal de **client** :
 
```bash
resolvectl status
ping -c 3 www.google.com
```
 
Ouvrez ensuite **Firefox** sur le client et consultez un site de votre choix.
 
### Questions
 
1. Quel serveur DNS systemd-resolved utilise-t-il? D'où vient cette information?
2. Pourquoi `ping www.google.com` fonctionne-t-il maintenant, alors qu'il échouait à la fin du laboratoire DHCP?
## 4- Zone autoritaire
 
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
4. `ping web.votrenom.lan` échoue pour le moment. Est-ce un problème de DNS? Comment le vérifier?
## 5- Erreurs courantes
 
Vous allez introduire, **une à la fois**, trois erreurs fréquentes, et observer comment les détecter. Après chaque erreur, corrigez-la avant de passer à la suivante.
 
### 1. Le point final oublié
 
Dans le fichier de zone, retirez le point final du nom du serveur de l'enregistrement `NS` (`ns1.votrenom.lan.` → `ns1.votrenom.lan`). Validez la zone avec `named-checkzone`.
 
### 2. Une adresse invalide
 
Remplacez l'adresse de `web` par `172.16.10.300`. Validez la zone avec `named-checkzone`.
 
### 3. Un fichier illisible
 
Changez le groupe du fichier de zone :
 
```bash
sudo chgrp root /var/named/votrenom.lan.zone
sudo chmod 640 /var/named/votrenom.lan.zone
sudo systemctl restart named
```
 
Testez `dig @172.16.10.2 www.votrenom.lan` depuis le client, puis consultez le journal de BIND :
 
```bash
sudo journalctl -u named -n 30
```
 
Rétablissez le groupe `named` et redémarrez BIND.
 
### Questions
 
Complétez le tableau :
 
| Erreur | Détectée par `named-checkzone`? | Message ou symptôme observé |
|--------|----------------------------------|-----------------------------|
| Point final oublié | | |
| Adresse invalide | | |
| Fichier illisible | | |
 
1. Pourquoi faut-il **toujours** valider avec `named-checkzone` et `named-checkconf` avant de redémarrer BIND?
2. Pour l'erreur 3, quel `status` le client a-t-il obtenu? Pourquoi le journal est-il indispensable pour en trouver la cause?
## Questions de synthèse
 
1. Dans ce laboratoire, `dns-server` joue deux rôles. Pour chacun, donnez une requête qui l'illustre et le drapeau qui le prouve.
2. Décrivez le trajet complet de la requête `ping www.google.com` exécutée sur le client : quelles machines interviennent, dans quel ordre, et avec quels protocoles?
3. Vous ajoutez l'enregistrement `mail IN A 172.16.10.4` à la zone. Quelles sont les trois étapes à faire avant que le client puisse résoudre ce nom?
