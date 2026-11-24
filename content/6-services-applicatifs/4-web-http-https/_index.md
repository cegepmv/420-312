+++
pre = '<b>4. </b>'
title = "Web : HTTP et HTTPS"
weight = "640"
draft = false
+++
-------------------
 
Le **Web** repose sur le protocole **HTTP (*HyperText Transfer Protocol*)** : un navigateur (le client) demande une ressource à un serveur web, qui la lui envoie. Une page, une image, une feuille de style ou une réponse JSON d'une API sont toutes des ressources obtenues par HTTP.
 
| Protocole | Transport et port | Sécurité |
|-----------|-------------------|----------|
| **HTTP** | TCP `80` | Aucune : tout circule en clair |
| **HTTPS** | TCP `443` | HTTP transporté dans une connexion chiffrée **TLS** |
 
## L'URL
 
Une **URL (*Uniform Resource Locator*)** indique au client **comment** et **où** obtenir une ressource :

![Structure d'une URL](/images/06/06-url-structure.png)

<!-- ```text
https://www.votrenom.lan:8443/produits/liste.html?tri=prix#avis
└─┬─┘   └──────┬───────┘ └┬─┘└─────────┬────────┘└──┬───┘└┬─┘
schéma        hôte      port        chemin      requête fragment
``` -->
 
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
- **texte** (jusqu'à HTTP/1.1) : les messages sont lisibles;
- **sans état** (*stateless*) : chaque requête est indépendante, le serveur ne se souvient pas des requêtes précédentes. Pour reconnaître un utilisateur d'une page à l'autre (connexion, panier), le serveur lui envoie un **cookie**, que le client renvoie ensuite dans chaque requête.
![Structure d'une requête et d'une réponse HTTP](/images/06/06-http-message.png)
 
### La requête

![Structure d'une requête HTTP](/images/06/06-http-requete.png)

<!-- 
```text
GET /produits/liste.html?tri=prix HTTP/1.1      ← ligne de requête
Host: www.votrenom.lan                          ┐
User-Agent: curl/8.14.1                         │ en-têtes
Accept: */*                                     ┘
                                                ← ligne vide : fin des en-têtes
``` 
-->
 
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
<!-- | **HEAD** | Comme `GET`, mais le serveur ne renvoie que les en-têtes | Non | Oui | Oui | -->
 
- Une méthode **sûre** ne modifie rien sur le serveur : le navigateur peut la répéter, la mettre en cache ou la précharger sans risque.
- Une méthode **idempotente** produit le même résultat qu'on l'envoie une fois ou dix fois. Supprimer deux fois le produit `#7` laisse le serveur dans le même état; envoyer deux fois un formulaire de commande avec `POST` crée deux commandes.

Lorsqu'une requête contient un corps, l'en-tête **`Content-Type`** indique son format :

![Requête HTTP POST avec un corps JSON](/images/06/06-http-post.png)

<!-- 
```text
POST /api/produits HTTP/1.1
Host: www.votrenom.lan
Content-Type: application/json
Content-Length: 32
 
{"nom": "Câble RJ45", "prix": 5}
```
-->

<!-- {{%notice style="note" title="Rappel"%}}
Les méthodes HTTP ont été introduites dans le cours **Applications Web**. Ici, on s'intéresse à ce qui circule sur le réseau.
{{%/notice%}} -->
 
### La réponse

![Structure d'une réponse HTTP](/images/06/06-http-reponse.png)


<!-- ```text
HTTP/1.1 200 OK                                 ← ligne de statut
Server: nginx/1.28.0                            ┐
Content-Type: text/html                         │ en-têtes
Content-Length: 615                             ┘
                                                ← ligne vide
<!DOCTYPE html>                                 ┐
<html>                                          │ corps : la ressource demandée
...                                             ┘
``` 
-->


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
 
<!-- ### Versions de HTTP
 
| Version | Particularité |
|---------|---------------|
| **HTTP/1.1** | Messages texte. Une connexion TCP peut servir à **plusieurs requêtes** successives (connexion persistante). |
| **HTTP/2** | Messages binaires; plusieurs requêtes en parallèle dans une seule connexion TCP. |
| **HTTP/3** | Utilise **QUIC**, un protocole construit sur **UDP** (port `443`), pour éviter les délais de TCP. |
 
Les méthodes, les codes et les en-têtes restent les mêmes dans toutes les versions. Dans ce cours, on utilise HTTP/1.1, dont les messages sont lisibles. -->
 
<!-- ## HTTPS vu du réseau -->
## HTTPS 
**HTTPS** transporte HTTP dans une connexion chiffrée par **TLS (*Transport Layer Security*)**. TLS assure :
 
- la **confidentialité** : un observateur ne peut pas lire les échanges;
- l'**intégrité** : un message modifié en route est détecté;
- l'**authentification du serveur** : grâce à son **certificat**, le client vérifie qu'il parle au bon site.

{{%notice style="note" title="Rappel"%}}
Les certificats, les autorités de certification et la configuration HTTPS de nginx ont été étudiés dans le cours **Sécurité informatique**. Ici, on s'intéresse à ce qui circule sur le réseau.
{{%/notice%}}
<!--  
### Déroulement d'une connexion HTTPS
 
![Déroulement d'une connexion HTTPS](/images/06/06-https-tls.png)
 
1. **Connexion TCP** vers le port `443` : le *3-way handshake* (chapitre 5).
2. **ClientHello** : le client propose ses versions de TLS et ses algorithmes, et indique le **nom du site** demandé (extension **SNI**, *Server Name Indication*). Ce message est **en clair**.
3. **ServerHello** : le serveur choisit les algorithmes. Les deux côtés calculent une clé de session. En TLS 1.3, la suite de l'échange, y compris le **certificat** du serveur, est déjà chiffrée.
4. **Échange HTTP chiffré** : les requêtes et réponses HTTP circulent dans des messages TLS **Application Data**.
### Ce qu'un observateur peut voir
 
| Information | HTTP | HTTPS |
|-------------|------|-------|
| Adresses IP et ports | Visible | Visible |
| Nom du site (`Host` en HTTP, **SNI** en HTTPS) | Visible | **Visible** |
| Méthode, chemin et paramètres de l'URL | Visible | Chiffré |
| En-têtes, cookies, mots de passe | Visible | Chiffré |
| Contenu des pages | Visible | Chiffré |
| Taille et moment des échanges | Visible | Visible |
 
{{%notice style="info" title="Le nom du site reste visible"%}}
HTTPS ne cache pas **quel site** est consulté : le nom apparaît en clair dans la requête DNS et dans le SNI du ClientHello. Il cache **ce qui** est consulté et échangé sur ce site.
{{%/notice%}}
 
{{%notice style="tip" title="Comparaison avec SSH"%}}
HTTPS suit la même logique que SSH (section 6.1) : connexion TCP, négociation en clair, échange de clés, puis tout est chiffré. Seuls le port et les détails du protocole changent.
{{%/notice%}}
 
  -->