+++
pre = '<b>3. </b>'
title = "DNS"
weight = "630"
draft = true
+++
-------------------
 
À la fin du laboratoire DHCP, le client pouvait joindre `8.8.8.8`, mais pas `www.google.com`. Les machines communiquent avec des **adresses IP**; les humains, eux, retiennent des **noms**.
 
**DNS (*Domain Name System*)** traduit un nom en adresse IP (et inversement). C'est un service indispensable : presque toutes les communications commencent par une requête DNS.
 
DNS utilise le port **53** :
 
| Transport | Usage |
|-----------|-------|
| **UDP 53** | Requêtes et réponses courantes |
| **TCP 53** | Réponses trop volumineuses pour un datagramme, et copie d'une zone entre serveurs |
 
{{%notice style="note" title="Rappel"%}}
DNS est l'exemple d'un service qui utilise les deux protocoles de transport, selon le type d'échange (chapitre 5).
{{%/notice%}}
 
## Noms de domaine
 
### FQDN
 
Un **FQDN (*Fully Qualified Domain Name*)** est un nom complet, qui identifie une machine sans ambiguïté :
 
```text
www.cegepmv.ca.
```
 
Il se lit **de droite à gauche**, du plus général au plus précis :
 
| Partie | Nom |
|--------|-----|
| `.` (point final, souvent omis) | **Racine** |
| `ca` | **Domaine de premier niveau** (*TLD, Top-Level Domain*) |
| `cegepmv` | Domaine de deuxième niveau |
| `www` | Nom de l'hôte |
 
Chaque partie, appelée **étiquette**, est séparée par un point.
 
### Une base de données hiérarchique
 
Aucun serveur ne connaît tous les noms d'Internet. L'espace des noms est découpé en un **arbre**, et chaque niveau ne connaît que le niveau situé juste en dessous.
 
![Hiérarchie du DNS](/images/06/06-dns-hierarchie.png)
 
| Niveau | Exemples | Ce que ses serveurs connaissent |
|--------|----------|---------------------------------|
| **Racine** | `.` | Les serveurs de chaque TLD |
| **TLD** | `ca`, `com`, `org`, `fr` | Les serveurs de chaque domaine du TLD |
| **Domaine** | `cegepmv.ca`, `wikipedia.org` | Les adresses de ses machines |
 
Il existe 13 noms de serveurs racine (`a.root-servers.net` à `m.root-servers.net`). Chacun correspond en réalité à des centaines de serveurs répartis dans le monde.
 
### Délégation
 
L'administrateur d'un domaine peut **déléguer** une partie de ce domaine à d'autres serveurs. Par exemple :
 
- les serveurs racine délèguent `ca` à l'organisme qui gère les domaines canadiens;
- les serveurs de `ca` délèguent `cegepmv.ca` aux serveurs du cégep.
Pour déléguer, le serveur parent enregistre le **nom** des serveurs responsables du sous-domaine (enregistrement `NS`), et au besoin leur **adresse IP**.
 
### Domaine et zone
 
- Un **domaine** est un ensemble de noms qui partagent le même suffixe : `cegepmv.ca` comprend `www.cegepmv.ca`, `mail.cegepmv.ca`, `info.cegepmv.ca`, etc.
- Une **zone** est la partie d'un domaine gérée par un même serveur. Si le cégep délègue `info.cegepmv.ca` au département d'informatique, la zone `cegepmv.ca` ne contient plus les noms de `info.cegepmv.ca`.

Un serveur est **autoritaire** pour une zone : c'est lui qui détient les informations officielles de cette zone.
 
## Résolution d'un nom
 
### Les acteurs
 
![Résolution récursive et itérative du nom www.cegepmv.ca](/images/06/06-dns-resolution.png)
 
| Acteur | Rôle |
|--------|------|
| **Résolveur du client** (*stub resolver*) | Partie du système d'exploitation qui envoie les requêtes DNS des applications au serveur DNS configuré (souvent obtenu par DHCP). |
| **Serveur récursif** | Reçoit la requête du client et se charge de trouver la réponse complète, en interrogeant d'autres serveurs. Il conserve les réponses dans un **cache**. |
| **Serveurs autoritaires** | Racine, TLD, puis serveur du domaine : chacun répond pour sa zone. |
 
### Requête récursive et requête itérative
 
1. Le client envoie une **requête récursive** au serveur récursif : « Trouve-moi l'adresse de `www.cegepmv.ca` ».
2. Le serveur récursif effectue des **requêtes itératives** :
    - il demande à un serveur **racine**, qui répond : « Je ne sais pas, mais voici les serveurs de `ca` »;
    - il demande à un serveur de **`ca`**, qui répond : « Voici les serveurs de `cegepmv.ca` »;
    - il demande à un serveur de **`cegepmv.ca`**, qui donne la réponse : « `www.cegepmv.ca` a l'adresse `…` ».
3. Le serveur récursif transmet la réponse au client.
| Requête | Le serveur interrogé… |
|---------|------------------------|
| **Récursive** | doit fournir la réponse complète, quitte à interroger d'autres serveurs. |
| **Itérative** | répond avec ce qu'il sait : la réponse, ou l'adresse d'un serveur plus proche de la réponse (un **renvoi**). |
 
### Le cache et le TTL
 
Chaque réponse est accompagnée d'un **TTL (*Time To Live*)**, en secondes, fixé par le serveur autoritaire. Le serveur récursif conserve la réponse dans son **cache** pendant cette durée.
 
Si un autre client demande le même nom avant l'expiration du TTL, le serveur récursif répond **immédiatement**, sans interroger les autres serveurs. Il connaît aussi, déjà, les serveurs de `ca`, et n'a donc plus besoin de passer par la racine pour un autre nom en `.ca`.
 
{{%notice style="tip" title="Conséquence pratique"%}}
Lorsqu'on change l'adresse d'un nom, les serveurs récursifs qui l'ont en cache continuent de donner l'**ancienne** adresse jusqu'à l'expiration du TTL. Avant une migration, on réduit donc le TTL quelques jours à l'avance.
{{%/notice%}}
 
{{%notice style="info" title="Ne pas confondre"%}}
Le TTL d'un enregistrement DNS (une durée en secondes) n'a aucun lien avec le TTL de l'en-tête IP (un nombre de routeurs, chapitre 4).
{{%/notice%}}
 
## Types de serveurs
 
| Serveur | Rôle | Exemples |
|---------|------|----------|
| **Récursif** (ou **cache**) | Résout les noms pour ses clients. Ne détient aucune zone. | Serveur DNS d'une entreprise, `8.8.8.8`, `1.1.1.1` |
| **Autoritaire** | Détient les informations officielles d'une ou plusieurs zones. Ne résout pas les autres noms. | Serveurs racine, serveurs des TLD, serveurs d'un domaine |
 
Un même logiciel peut jouer les deux rôles. Dans le laboratoire, votre serveur sera **récursif** pour les machines du LAN et **autoritaire** pour la zone `votrenom.lan`.
 
{{%notice style="warning" title="Résolveur ouvert"%}}
Un serveur récursif doit répondre **seulement à ses propres clients**. Un serveur qui accepte les requêtes récursives de n'importe qui sur Internet (*résolveur ouvert*) peut être utilisé pour des attaques par amplification : l'attaquant envoie de petites requêtes en usurpant l'adresse de sa victime, et le serveur inonde la victime de grosses réponses.
{{%/notice%}}
 
## Enregistrements
 
Une zone contient des **enregistrements** (*Resource Records*). Chacun associe un **nom**, un **type** et une **valeur** :
 
| Type | Associe | Exemple |
|------|---------|---------|
| **A** | Un nom à une adresse IPv4 | `web` → `172.16.10.3` |
| **AAAA** | Un nom à une adresse IPv6 | `web` → `2001:db8::3` |
| **CNAME** | Un nom (alias) à un autre nom | `www` → `web.votrenom.lan.` |
| **MX** | Un domaine à son serveur de courriel | `votrenom.lan` → `mail.votrenom.lan.` |
| **NS** | Une zone à ses serveurs autoritaires | `votrenom.lan` → `ns1.votrenom.lan.` |
| **SOA** | Une zone à ses paramètres généraux | Un seul par zone, au début du fichier |
| **PTR** | Une adresse IP à un nom (résolution **inverse**) | `172.16.10.3` → `web.votrenom.lan.` |
| **TXT** | Un nom à un texte libre | Souvent utilisé pour prouver la propriété d'un domaine |
 
{{%notice style="info" title="CNAME"%}}
Un nom qui possède un `CNAME` ne peut avoir **aucun autre** enregistrement. Le client qui reçoit un `CNAME` doit ensuite résoudre le nom cible.
{{%/notice%}}
 
## Le fichier de zone
 
Avec BIND, chaque zone est décrite dans un **fichier de zone** :
 
```text
$TTL 1h
@       IN  SOA  ns1.votrenom.lan. admin.votrenom.lan. (
                 2026111101  ; serial
                 1d          ; refresh
                 1h          ; retry
                 1w          ; expire
                 1h )        ; TTL des réponses négatives
 
@       IN  NS     ns1.votrenom.lan.
ns1     IN  A      172.16.10.2
dhcp    IN  A      172.16.10.1
web     IN  A      172.16.10.3
www     IN  CNAME  web
```
 
| Élément | Rôle |
|---------|------|
| `$TTL` | TTL par défaut des enregistrements de la zone |
| `@` | Le nom de la zone elle-même (`votrenom.lan.`) |
| `IN` | Classe *Internet* (toujours la même) |
| `SOA` | Serveur principal de la zone (`ns1.votrenom.lan.`), puis adresse du responsable, où le premier `.` remplace le `@` (`admin.votrenom.lan.` = `admin@votrenom.lan`) |
| **serial** | **Numéro de version** de la zone, à **incrémenter à chaque modification**. Convention : `AAAAMMJJnn` |
| refresh, retry, expire | Utilisés par les serveurs secondaires (non étudiés dans ce cours) |
| Dernière valeur | Durée pendant laquelle un serveur récursif garde en cache une réponse « ce nom n'existe pas » |
 
### Le point final
 
Dans un fichier de zone, un nom **sans point final** est **relatif** : le nom de la zone lui est ajouté.
 
| Écrit dans la zone `votrenom.lan` | Nom compris |
|-----------------------------------|-------------|
| `web` | `web.votrenom.lan.` |
| `web.votrenom.lan.` | `web.votrenom.lan.` |
| `web.votrenom.lan` (point oublié) | `web.votrenom.lan.votrenom.lan.` |
 
{{%notice style="warning" title="Erreur fréquente"%}}
L'oubli du point final est l'erreur la plus courante dans un fichier de zone. Elle ne provoque souvent aucun message d'erreur : le nom est simplement faux.
{{%/notice%}}
 
<!-- ### Résolution inverse
 
La résolution inverse retrouve le nom à partir d'une adresse IP (enregistrements `PTR`). Elle utilise une zone spéciale, dont le nom est formé des octets du réseau **à l'envers**, suivis de `in-addr.arpa` :
 
```text
Réseau 172.16.10.0/24   →   zone 10.16.172.in-addr.arpa
```
 
Elle sert surtout au diagnostic (noms affichés par `traceroute`, journaux) et aux serveurs de courriel. -->
 
## Côté client
 
### Ordre de résolution
 
Avant d'interroger un serveur DNS, le résolveur du client consulte le fichier **`hosts`**, qui associe manuellement des noms à des adresses :
 
- Linux : `/etc/hosts`;
- Windows : `C:\Windows\System32\drivers\etc\hosts`.
```text
127.0.0.1     localhost
172.16.10.3   web.votrenom.lan web
```
 
### Linux (Ubuntu)
 
Sur Ubuntu, le service **systemd-resolved** joue le rôle de résolveur. Les applications l'interrogent à l'adresse locale `127.0.0.53`, et il transmet les requêtes au serveur DNS reçu par DHCP.
 
```bash
resolvectl status          # serveur(s) DNS utilisé(s), par interface
resolvectl query web.votrenom.lan
resolvectl flush-caches    # vider le cache local
```
 
{{%notice style="info" title="127.0.0.53"%}}
Sur Ubuntu, `/etc/resolv.conf` contient `nameserver 127.0.0.53`. Ce n'est pas une erreur : c'est l'adresse de systemd-resolved, qui relaie ensuite vers le vrai serveur DNS.
{{%/notice%}}
 
### Windows
 
```powershell
nslookup www.cegepmv.ca
nslookup www.cegepmv.ca 8.8.8.8     # en interrogeant un serveur précis
ipconfig /displaydns                # afficher le cache local
ipconfig /flushdns                  # vider le cache local
```
 
## La commande dig
 
`dig` interroge un serveur DNS et affiche sa réponse en détail.
 
```bash
dig www.votrenom.lan                  # enregistrement A, serveur par défaut
dig @172.16.10.2 www.votrenom.lan     # en interrogeant un serveur précis
dig votrenom.lan NS                   # un autre type d'enregistrement
dig -x 172.16.10.3                    # résolution inverse
dig +short www.votrenom.lan           # réponse seulement
dig +norecurse @198.41.0.4 cegepmv.ca # requête itérative
```
 
### Lire une réponse
 
```text
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31337
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
 
;; QUESTION SECTION:
;www.votrenom.lan.          IN   A
 
;; ANSWER SECTION:
www.votrenom.lan.   3600    IN   CNAME  web.votrenom.lan.
web.votrenom.lan.   3600    IN   A      172.16.10.3
 
;; Query time: 1 msec
;; SERVER: 172.16.10.2#53(172.16.10.2) (UDP)
```
 
| Élément | Signification |
|---------|---------------|
| `status` | `NOERROR` : réponse trouvée · `NXDOMAIN` : le nom n'existe pas · `SERVFAIL` : le serveur n'a pas pu résoudre · `REFUSED` : le serveur refuse de répondre |
| `aa` | *Authoritative Answer* : la réponse vient d'un serveur **autoritaire** pour la zone |
| `rd` | *Recursion Desired* : le client demande une requête récursive |
| `ra` | *Recursion Available* : le serveur accepte les requêtes récursives |
| `QUESTION` | La question posée |
| `ANSWER` | La réponse, avec le **TTL** de chaque enregistrement |
| `AUTHORITY` | Dans un renvoi : les serveurs `NS` à interroger ensuite |
| `ADDITIONAL` | Informations utiles, par exemple l'adresse IP des serveurs de la section `AUTHORITY` |
| `Query time`, `SERVER` | Durée de la requête et serveur qui a répondu |
 
{{%notice style="tip" title="À retenir"%}}
DNS utilise **UDP 53** (et TCP 53). Le client envoie une **requête récursive** à son serveur récursif, qui effectue des **requêtes itératives** auprès des serveurs racine, du TLD, puis du serveur **autoritaire** du domaine. Les réponses sont conservées en **cache** pendant leur **TTL**. Une zone est décrite par un **fichier de zone** : un `SOA`, des `NS`, puis des enregistrements `A`, `CNAME`, etc.
{{%/notice%}}
 
<!--  
# 6.3.3. Laboratoire bonus : zone inverse et nom de domaine
-------------
 
{{%notice style="info" title="Laboratoire facultatif"%}}
Ce laboratoire s'adresse aux personnes qui souhaitent approfondir DNS. Il n'est pas nécessaire pour la suite du chapitre.
{{%/notice%}}
 
## 1- Zone inverse
 
1. Créez le fichier `/var/named/10.16.172.in-addr.arpa.zone`, avec le même `SOA` et le même `NS` que la zone `votrenom.lan`, et un enregistrement `PTR` pour chacune des adresses `.1`, `.2` et `.3` :
```text
    1       IN  PTR  dhcp.votrenom.lan.
```
2. Déclarez la zone `10.16.172.in-addr.arpa` dans `/etc/named.conf`.
3. Validez, redémarrez BIND, puis testez depuis le client :
```bash
    dig @172.16.10.2 -x 172.16.10.1
```
 
### Questions
 
1. Pourquoi le nom de la zone inverse s'écrit-il « à l'envers »?
2. Pourquoi le point final est-il indispensable dans la valeur des enregistrements `PTR`?
## 2- Distribuer le nom de domaine par DHCP
 
Sur **dhcp-router**, ajoutez l'option `domain-name` à l'étendue de Kea :
 
```json
{ "name": "domain-name", "data": "votrenom.lan" }
```
 
Validez, redémarrez Kea, puis renouvelez le bail du client (`nmcli con down` puis `up`).
 
Sur le client :
 
```bash
resolvectl status
ping -c 3 dhcp
```
 
### Questions
 
1. Quelle nouvelle information `resolvectl status` affiche-t-il?
2. Pourquoi `ping dhcp` fonctionne-t-il, alors que `dhcp` n'est pas un nom complet? -->