+++
title = "Nginx"
weight = "641"
draft = false
+++
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