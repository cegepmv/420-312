+++
title = "Exercices"
weight = "631"
draft = true
+++
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
 
### 2. Lire une sortie de dig
 
Un poste exécute deux fois la même commande, à 30 secondes d'intervalle :
 
```text
$ dig @172.16.10.2 www.wikipedia.org
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5120
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
 
;; ANSWER SECTION:
www.wikipedia.org.    86400   IN   CNAME  dyna.wikimedia.org.
dyna.wikimedia.org.   600     IN   A      208.80.154.224
 
;; Query time: 184 msec
;; SERVER: 172.16.10.2#53(172.16.10.2) (UDP)
```
 
```text
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
 
;; ANSWER SECTION:
www.wikipedia.org.    86370   IN   CNAME  dyna.wikimedia.org.
dyna.wikimedia.org.   570     IN   A      208.80.154.224
 
;; Query time: 1 msec
```
 
1. Le serveur `172.16.10.2` est-il autoritaire pour `wikipedia.org`? Comment le savez-vous?
2. Quelle est l'adresse IPv4 de `www.wikipedia.org`? Pourquoi la réponse contient-elle deux enregistrements?
3. Expliquez la différence de `Query time` entre les deux requêtes.
4. Expliquez l'évolution des valeurs `86400` → `86370` et `600` → `570`.
5. Dans combien de temps, au plus tard, le serveur interrogera-t-il de nouveau Internet pour obtenir l'adresse de `dyna.wikimedia.org`?
{{% expand title="Solution" %}}
1. Non : le drapeau `aa` est absent. La réponse vient du serveur récursif (drapeau `ra`).
2. `208.80.154.224`. `www.wikipedia.org` est un **alias** (`CNAME`) de `dyna.wikimedia.org`; le serveur a donc aussi résolu le nom cible.
3. La première fois, le serveur récursif a dû interroger les serveurs d'Internet (184 ms). La deuxième fois, il a répondu depuis son **cache** (1 ms).
4. Ce sont les TTL restants : 30 secondes se sont écoulées depuis la mise en cache.
5. Dans **570 secondes** (9 min 30 s), à l'expiration du TTL de l'enregistrement `A`.
{{% /expand %}}

### 3. Trouver les erreurs
 
L'administrateur de la zone `exemple.lan` a modifié son fichier de zone pour ajouter le serveur `mail`. Le serial était `2026110301` avant la modification. Le serveur DNS a l'adresse `10.0.0.2`.
 
```text
$TTL 1h
@       IN  SOA  ns1.exemple.lan. admin@exemple.lan. (
                 2026110301 ; serial
                 1d 1h 1w 1h )
 
@       IN  NS     ns1.exemple.lan
ns1     IN  A      10.0.0.2
web     IN  A      10.0.0.300
www     IN  CNAME  web
www     IN  A      10.0.0.3
mail    IN  A      10.0.0.4
```
 
Trouvez les **cinq** erreurs.
 
{{% expand title="Solution" %}}
1. **Adresse du responsable** : `admin@exemple.lan.` doit s'écrire `admin.exemple.lan.` (le `@` a un sens particulier dans un fichier de zone).
2. **Serial non incrémenté** : la zone a été modifiée, le serial doit devenir `2026110302` (ou plus).
3. **Point final oublié** dans l'enregistrement `NS` : `ns1.exemple.lan` devient `ns1.exemple.lan.exemple.lan.`.
4. **Adresse invalide** : `10.0.0.300` (un octet ne dépasse pas `255`).
5. **`CNAME` et `A` pour le même nom** : `www` ne peut pas avoir d'autre enregistrement que son `CNAME`.
{{% /expand %}}

### 4. Choisir l'enregistrement
 
Pour chaque besoin, indiquez le type d'enregistrement à créer.
 
1. `intranet.exemple.lan` doit mener au même serveur que `web.exemple.lan`, et suivre automatiquement ses changements d'adresse.
2. Les courriels destinés à `@exemple.lan` doivent être livrés au serveur `mail.exemple.lan`.
3. `traceroute` doit afficher `routeur.exemple.lan` plutôt que `10.0.0.1`.
4. Le nouveau serveur `ns2.exemple.lan` doit aussi répondre pour la zone `exemple.lan`.
5. Le serveur `web` doit être joignable en IPv6.
{{% expand title="Solution" %}}
1. **CNAME** : `intranet` → `web.exemple.lan.`
2. **MX**
3. **PTR**, dans la zone inverse
4. **NS** (en plus d'un `A` pour `ns2`)
5. **AAAA**
{{% /expand %}}

### 5. Raisonner
 
1. Pourquoi un serveur racine ne peut-il pas répondre directement à la question « quelle est l'adresse de `www.cegepmv.ca`? »
2. Une entreprise déménage son site web vers une nouvelle adresse IP. Le TTL de l'enregistrement est de 2 jours. Pendant combien de temps certains clients risquent-ils d'utiliser l'ancienne adresse? Que faudrait-il faire avant le déménagement?
3. Le serveur DNS d'une entreprise accepte les requêtes récursives de n'importe quelle adresse. Quel est le risque? Que faut-il configurer?
4. Un poste reçoit par DHCP le serveur DNS `172.16.10.2`, mais ce serveur est éteint. L'utilisateur peut-il naviguer sur `www.cegepmv.ca`? Et sur `http://<adresse IP du site>`? Justifiez.
{{% expand title="Solution" %}}
1. Il ne connaît que les serveurs des TLD. Il renvoie donc vers les serveurs de `ca`.
2. Jusqu'à **2 jours** : c'est la durée pendant laquelle les serveurs récursifs peuvent garder l'ancienne réponse en cache. Quelques jours avant, il faut réduire le TTL (par exemple à 5 minutes), attendre l'expiration de l'ancien TTL, effectuer le changement, puis rétablir un TTL normal.
3. C'est un **résolveur ouvert**, utilisable pour des attaques par amplification. Il faut limiter les requêtes récursives aux réseaux de l'entreprise.
4. Pas par le nom : la résolution échoue. Par l'adresse IP, oui : aucune résolution n'est nécessaire, et le réseau (IP, routage) fonctionne.
{{% /expand %}}