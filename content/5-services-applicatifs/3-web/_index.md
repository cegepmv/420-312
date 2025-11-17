+++
pre = '<b>3. </b>'
title = "Web"
weight = "530"
+++
-------------------

Le Web repose sur un ensemble de protocoles permettant à des clients (navigateurs, applications) de communiquer avec des serveurs.

Le protocole central s’appelle **HTTP** (*HyperText Transfer Protocol*), utilisé pour envoyer des requêtes et recevoir des réponses.

Son équivalent sécurisé s’appelle **HTTPS**, utilisé dans la quasi-totalité des sites modernes.

Ce cours vise à comprendre :

+ comment fonctionne HTTP,
+ comment se déroule une communication client/serveur,
+ comment la sécurité est assurée avec HTTPS,
+ comment les serveurs Web tels que nginx servent des pages web,
+ comment s’inscrit le tout dans une architecture 3-tiers.

## Le protocole HTTP

+ HTTP est un protocole **texte**, simple et stateless (sans mémoire).
+ Chaque requête est indépendante et contient toutes les informations nécessaires.

### Structure d’une requête HTTP

![Requête HTTP](../images/05-03-02.svg?width=600px)

Une requête comporte :

1. **Une ligne de requête -** Exemple : `GET /index.html HTTP/1.1`
2. **Des entêtes (headers) -** informations supplémentaires (Agent, Cookies, Type d’acception…).
3. **Un corps (*body*)** (optionnel) **-** utilisé surtout dans les requêtes `POST`/`PUT`.

### Méthodes HTTP
|Méthode|	Rôle|
|-------|------|
|**GET**|	Récupère une ressource. Pas de modification.|
|**POST**|	Envoie des données (formulaires, JSON…). Souvent pour créer une ressource.|
|**PUT**|	Modifie ou remplace une ressource existante.|
|**DELETE**|	Supprime une ressource.|

Exemples :

+ `GET /produits` → récupère la liste des produits
+ `POST /produits` → ajoute un nouveau produit
+ `PUT /produits/7` → met à jour le produit #7
+ `DELETE /produits/7` → supprime le produit #7

### Les réponses HTTP : codes & entêtes
#### Structure d’une réponse
![Réponse HTTP](../images/05-03-03.svg?width=600px)

1. **Ligne de statut -** Exemple : `HTTP/1.1 200 OK`
2. **Entêtes (headers) -** date, type de contenu, cache, cookies…
3. **Corps (*body*) -** la ressource demandée (HTML, JSON, image…).

#### Catégories de codes de statut
|Catégorie|	Signification|	Exemples|
|---------|--------------|----------|
|**1xx**|Information|100 Continue|
|**2xx**|Succès|200 OK, 201 Created|
|**3xx**|Redirection|301 Moved Permanently, 302 Found|
|**4xx**|Erreurs client|400 Bad Request, 404 Not Found|
|**5xx**|Erreurs serveur|500 Internal Server Error, 503 Service Unavailable|

#### Les entêtes courants

+ `Content-Type` → type du contenu (html, json, jpeg…)
+ `Content-Length` → taille en octets
+ `Server` → nom du serveur (nginx, Apache…)
+ `Set-Cookie` → envoie un cookie au client
+ `Authorization` → authentification
+ `Cache-Control` → gestion du cache

## HTTPS : HTTP + Sécurité
### Pourquoi HTTPS ?
**HTTPS** (*HTTP Secure*) ajoute une couche de chiffrement grâce au protocole TLS.

Cela permet :
+ de chiffrer les données (personne ne peut lire les données interceptées),
+ de garantir l’intégrité (données non modifiables en transit),
+ de vérifier l’identité du serveur grâce au certificat.

### Fonctionnement de HTTPS

1. Le client contacte le serveur en HTTPS (port 443).
2. Le serveur envoie son certificat SSL/TLS.
3. Le client vérifie la validité du certificat.
4. Un échange de clés (handshake) crée un canal chiffré.
5. Toute communication suivante se fait de manière sécurisée.

### Particularités techniques

+ Utilise TLS (Transport Layer Security).
+ Fonctionne comme HTTP… mais dans un tunnel chiffré.
+ Plus coûteux en calcul mais indispensable aujourd’hui.

## Architecture Web : le modèle 3-tiers
![3-tiers](../images/05-03-01.jpg?width=600px)

Une architecture Web moderne repose souvent sur 3 couches :

1. **Couche présentation/Frontend (client)**
    + Navigateurs : Chrome, Firefox...
    + Envoie des requêtes HTTP/HTTPS.
    + Technologies : HTML/CSS/JS, frameworks (React, Vue, etc.).

2. **Couche logique/Backend (serveur)**
    + Reçoit les requêtes du frontend via HTTP.
    + Communique avec la base de données.
    + Exemple : *Python* (*Flask*, *Django*), *Node.js*, *PHP*, *Java* (*SpringBoot*), etc.

3. **Couche données (Base de données)**
    + Stocke les données persistantes.
    + Types : SQL (*PostgreSQL*, *MySQL*), NoSQL (*MongoDB*).

### Circulation des requêtes
1. Le client envoie une requête au serveur web.
2. Si la requête est une page statique, le serveur web passe directement au point 5, sinon il transmet la requête au backend.
3. Le backend interroge la base de données.
4. Le backend renvoie le résultat au serveur web.
5. Le serveur web renvoie la réponse au client.
6. Le client (navigateur) affiche la réponse.

{{%notice style="info" title="Remarque"%}}
**Nginx** intervient souvent au début du flux comme "porte d'entrée" du serveur.
{{%/notice%}}