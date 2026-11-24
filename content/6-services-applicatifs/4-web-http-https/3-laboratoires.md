+++
title = "Laboratoire"
weight = "644"
draft = false
+++
-------------
 
## Contexte
 
Dans ce laboratoire, vous allez :
 
1. mettre en service `web-server`, la dernière machine du réseau du chapitre;
2. héberger **deux sites** sur le même serveur, grâce aux hôtes virtuels;
3. valider le fonctionnement de **tout le réseau du chapitre**, de bout en bout.
<!-- 2. dialoguer avec nginx **à la main**, avec `nc` et `curl`, pour observer les méthodes, les en-têtes et les codes de statut; -->
<!-- 4. comparer HTTP et HTTPS dans Wireshark; -->

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
 
<!-- ### 2. Accès SSH
 
Ajoutez l'alias `web` à votre `~/.ssh/config` (section 6.1), puis testez depuis le poste Windows :
 
```powershell
ssh web
``` -->
 
## 1- Le serveur web
 
### 1. Vérifier le service
 
Sur **web-server** :
 
```bash
systemctl status nginx
sudo ss -tlnp | grep nginx
cat /etc/nginx/sites-enabled/default

```
<!--  
cat /etc/nginx/sites-enabled/default | grep -v '^\s*#' | grep -v '^\s*$' 
-->
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

<!-- ## 2- HTTP à la main
 
### 1. Une requête avec nc
 
Dans un terminal de **client**, ouvrez une connexion TCP avec le serveur web :
 
```bash
nc -C 172.16.10.3 80
```
 
Tapez la requête suivante, puis appuyez **deux fois** sur `Entrée` (la ligne vide termine la requête) :
 
```text
GET / HTTP/1.1
Host: www.votrenom.lan
```
 
{{%notice style="info" title="Remarque"%}}
L'option `-C` envoie des fins de ligne `\r\n`, comme l'exige HTTP. Après la réponse, la connexion reste ouverte quelques secondes (connexion persistante) : vous pouvez envoyer une autre requête, ou fermer avec `Ctrl+C`.
{{%/notice%}}
 
### 2. Autres requêtes
 
Avec `nc`, envoyez successivement :
 
1. une requête `GET` pour la page `/inexistante.html`;
2. une requête `HEAD` pour `/`;
3. une requête `GET` pour `/` **sans** en-tête `Host`.
Pour chacune, relevez le code de statut et observez la ligne correspondante dans le journal.
 
### 3. Les méthodes avec curl
 
`curl -X` permet de choisir la méthode, `curl -I` envoie une requête `HEAD` :
 
```bash
curl -I http://www.votrenom.lan/
curl -X POST -d "nom=test" http://www.votrenom.lan/
curl -X DELETE http://www.votrenom.lan/index.nginx-debian.html
```
 
### Questions
 
Complétez le tableau :
 
| Requête | Code de statut | Corps dans la réponse? |
|---------|----------------|------------------------|
| `GET /` | | |
| `GET /inexistante.html` | | |
| `HEAD /` | | |
| `GET /` sans `Host` | | |
| `POST /` | | |
| `DELETE /index.nginx-debian.html` | | |
 
1. Pourquoi nginx refuse-t-il une requête sans en-tête `Host`?
2. Pourquoi nginx refuse-t-il les méthodes `POST` et `DELETE` sur ce site? Quel type d'application pourrait les accepter?
3. La requête `DELETE` a-t-elle supprimé le fichier? Vérifiez sur web-server.
4. Quel est l'avantage de `HEAD` par rapport à `GET` pour vérifier qu'une page existe? -->

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
<!-- 
echo '<h1>Intranet de votrenom.lan</h1>' | sudo tee /var/www/pokedex/index.html
``` -->

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
 
<!-- ### 3. Tester sans DNS
 
Le nom `intranet.votrenom.lan` n'existe pas encore dans le DNS. On peut tout de même tester le site en fournissant l'en-tête `Host` à la main. Dans un terminal de **client** :
 
```bash
curl http://172.16.10.3/
curl -H "Host: www.votrenom.lan" http://172.16.10.3/
curl -H "Host: intranet.votrenom.lan" http://172.16.10.3/
curl http://intranet.votrenom.lan/
``` -->
 
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
<!-- 2. Pourquoi la quatrième commande échoue-t-elle à l'étape 3? À quelle étape de la communication l'échec se produit-il? -->
<!-- ## 4- HTTP et HTTPS dans Wireshark
 
Cette partie se fait sur le **poste Windows**, avec Wireshark, comme au chapitre 5.
 
### 1. Capturer
 
1. Démarrez une capture sur l'interface **Ethernet** du poste.
2. Dans PowerShell :
```powershell
    curl.exe http://ftp.lan/
    curl.exe -s -o NUL https://www.wikipedia.org/
```
3. Arrêtez la capture.
{{%notice style="info" title="Pourquoi curl.exe plutôt qu'un navigateur?"%}}
Un navigateur ouvre de nombreuses connexions et peut utiliser HTTP/3, qui fonctionne sur UDP. `curl.exe` produit une seule connexion TCP, plus facile à analyser.
{{%/notice%}}
 
### 2. La communication HTTP
 
1. Appliquez le filtre `http`.
2. Sur la requête `GET`, faites **Suivre → TCP Flux**.
### 3. La communication HTTPS
 
1. Appliquez le filtre `tls`.
2. Repérez le message **Client Hello**. Développez **Transport Layer Security → Handshake Protocol → Extension: server_name**.
3. Repérez le message **Server Hello**, puis les messages suivants.
4. Faites **Suivre → TCP Flux** sur cette conversation.
### Questions
 
1. Dans la communication HTTP, pouvez-vous lire la requête et la réponse? Relevez l'en-tête `Host` et le code de statut.
2. Dans la communication HTTPS, quel port de destination est utilisé? Quel nom apparaît dans l'extension `server_name`?
3. Après le Server Hello, que contient la colonne *Info* des paquets? Le certificat du serveur apparaît-il en clair?
4. Pouvez-vous trouver le chemin demandé (`/`) ou le code de statut dans la communication HTTPS?
5. Complétez le tableau « Ce qu'un observateur peut voir » de la section 6.4 à partir de vos propres observations. -->

## 3- Validation de bout en bout
 
Tous les services du chapitre sont maintenant en place. Vous allez vérifier qu'un poste qui se connecte au réseau obtient tout ce dont il a besoin. 
<!-- 
en capturant **tout** son trafic. -->
 
<!-- ### 1. Repartir de zéro
 
Sur **client**, ouvrez **deux** terminaux. Dans le premier, démarrez une capture :
 
```bash
sudo tcpdump -i any -n -w /tmp/complet.pcap
```
 
Dans le second, simulez un poste qui arrive sur le réseau :
 
```bash
sudo nmcli con down "CONNEXION"
sudo resolvectl flush-caches
sudo ip neigh flush all
sudo nmcli con up "CONNEXION"
curl -s http://www.votrenom.lan/
```
 
Arrêtez la capture (`Ctrl+C` dans le premier terminal), puis récupérez-la sur le poste Windows :
 
```powershell
scp client:/tmp/complet.pcap .
```
 
{{%notice style="info" title="Pourquoi -i any?"%}}
La commande `nmcli con down` désactive la connexion de `ens160`. En écoutant toutes les interfaces (`any`), `tcpdump` continue de capturer pendant cette coupure.
{{%/notice%}}
 
### 2. Identifier les services
 
Ouvrez la capture dans Wireshark et appliquez le filtre :
 
```text
!(ip.addr == 127.0.0.0/8) && (dhcp || arp || dns || http || tcp.flags.syn == 1)
```
 
La première partie du filtre masque les échanges internes du client avec systemd-resolved (`127.0.0.53`).
 
Complétez le tableau, dans l'ordre chronologique :
 
| # | Protocole | Machine contactée | Rôle de l'échange |
|---|-----------|-------------------|-------------------|
| 1 | DHCP | | |
| 2 | | | |
| … | | | | -->
 
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
<!-- 2. Dans votre capture, le client a-t-il dû faire une requête ARP pour `dhcp-router` avant de joindre le serveur web? Pourquoi?  -->
<!-- 4. Votre site doit maintenant être accessible en HTTPS. Sans le configurer, indiquez ce qu'il faudrait ajouter ou modifier : sur web-server (nginx et pare-feu), et dans le DNS. -->
