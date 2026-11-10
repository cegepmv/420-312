+++
title = "Exercices"
weight = "642"
draft = false
+++
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
4. Un client envoie un `DELETE` à un serveur nginx qui ne sert que des fichiers statiques.
5. nginx transmet une requête à un *backend* Node.js qui est arrêté.
6. Un site a déménagé définitivement de `http://` vers `https://`; un client demande l'ancienne adresse.

{{% expand title="Solution" %}}
1. `GET` → `200 OK`
2. `POST` → `201 Created`
3. `GET` → `404 Not Found`
4. `DELETE` → `405 Method Not Allowed`
5. N'importe quelle méthode → `502 Bad Gateway` (le mandataire n'obtient pas de réponse valide du *backend*)
6. `GET` → `301 Moved Permanently`, avec l'en-tête `Location: https://...`
{{% /expand %}}
<!-- 
### 4. Hôtes virtuels
 
Un serveur nginx (`172.16.10.3`) contient les deux blocs suivants :
 
```nginx
server {
    listen 80 default_server;
    server_name _;
    root /var/www/html;
}
 
server {
    listen 80;
    server_name www.votrenom.lan;
    root /var/www/votrenom;
}
```
 
Le DNS associe `www.votrenom.lan` et `intranet.votrenom.lan` à `172.16.10.3`. Quel répertoire est utilisé pour chaque requête?
 
1. `curl http://www.votrenom.lan/`
2. `curl http://172.16.10.3/`
3. `curl http://intranet.votrenom.lan/`
4. `curl -H "Host: www.votrenom.lan" http://172.16.10.3/`
{{% expand title="Solution" %}}
1. `/var/www/votrenom` : `Host: www.votrenom.lan` correspond au deuxième bloc.
2. `/var/www/html` : `Host: 172.16.10.3` ne correspond à aucun `server_name`, le bloc `default_server` est utilisé.
3. `/var/www/html` : le DNS fonctionne, mais aucun bloc n'a `intranet.votrenom.lan` comme `server_name`.
4. `/var/www/votrenom` : c'est l'en-tête `Host`, et non l'adresse utilisée pour se connecter, qui détermine le site.
{{% /expand %}} -->
### 5. Synthèse : de l'URL à la page
 
Le poste `client` (`172.16.10.100`) vient d'obtenir son adresse par DHCP. Ses caches ARP et DNS sont vides. L'utilisateur ouvre `http://www.votrenom.lan/`.
 
1. Énumérez, dans l'ordre, tous les échanges réseau nécessaires jusqu'à l'affichage de la page. Pour chacun, indiquez le protocole, la couche, la machine contactée et le port s'il y a lieu.
2. Lesquels de ces échanges passent par `dhcp-router`?
<!-- 3. Qu'est-ce qui change si l'utilisateur ouvre plutôt `https://www.wikipedia.org/`? -->

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
<!-- 3. Le client contacte toujours `dns-server`, mais celui-ci doit résoudre le nom sur Internet (requêtes itératives, à travers `dhcp-router`). Le client fait aussi une requête ARP pour la **passerelle** `172.16.10.1`, puisque le serveur web est sur un réseau distant. La connexion TCP vise le port **443**, suivie de la négociation **TLS** (ClientHello avec le SNI `www.wikipedia.org`), puis de l'échange HTTP **chiffré**. Tout ce trafic traverse `dhcp-router`, qui fait du NAT. -->
{{% /expand %}}