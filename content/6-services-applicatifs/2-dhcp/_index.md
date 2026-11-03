+++
pre = '<b>2. </b>'
title = "DHCP"
weight = "620"
draft = true
+++
-------------------
 
Jusqu'ici, toutes les interfaces ont été configurées **manuellement** : adresse IP, préfixe, passerelle et serveur DNS. Cette méthode convient pour quelques serveurs, mais pas pour les centaines de postes, portables et téléphones d'une organisation : elle est longue et source d'erreurs (adresse en double, mauvaise passerelle).
 
**DHCP (*Dynamic Host Configuration Protocol*)** automatise cette configuration. Un poste qui se connecte au réseau demande sa configuration; un **serveur DHCP** lui fournit :
 
- une **adresse IP** et son **masque**;
- la **passerelle par défaut**;
- l'adresse des **serveurs DNS**;
- la **durée** pendant laquelle il peut utiliser cette adresse (le **bail**);
- d'autres options au besoin (nom de domaine, serveur NTP, etc.).

DHCP utilise **UDP** :
 
| Rôle | Port |
|------|------|
| Serveur | `67` |
| Client | `68` |
 
{{%notice style="note" title="Rappel"%}}
DHCP est un bon exemple du choix d'UDP (chapitre 5) : les échanges sont courts, et le client ne pourrait de toute façon pas établir de connexion TCP, puisqu'il n'a pas encore d'adresse IP.
{{%/notice%}}
 
## L'échange DORA
 
Pour obtenir une adresse, le client et le serveur échangent quatre messages. On appelle cet échange **DORA**, d'après la première lettre de chaque message.
 
![Échange DORA entre un client et un serveur DHCP](/images/06/06-dhcp-dora.png)
 
| # | Message | Sens | Rôle |
|---|---------|------|------|
| 1 | **DISCOVER** | Client → serveur | « Y a-t-il un serveur DHCP sur ce réseau? » |
| 2 | **OFFER** | Serveur → client | « Je te propose l'adresse `172.16.10.100`, avec ces options. » |
| 3 | **REQUEST** | Client → serveur | « J'accepte l'offre de tel serveur. » |
| 4 | **ACK** | Serveur → client | « C'est confirmé : l'adresse est à toi pour la durée du bail. » |
 
Le client configure son interface seulement après avoir reçu l'**ACK**.
 
### Un échange en broadcast
 
Au départ, le client n'a **pas d'adresse IP** et ne connaît **pas l'adresse du serveur**. Il utilise donc :
 
- l'adresse IP source `0.0.0.0`;
- l'adresse IP de destination `255.255.255.255` (broadcast);
- l'adresse MAC de destination `ff:ff:ff:ff:ff:ff` (broadcast Ethernet, chapitre 3).
Le serveur, lui, a une adresse IP **statique** et répond depuis cette adresse.
 
| Message | IP source | IP destination | Ports |
|---------|-----------|----------------|-------|
| **DISCOVER** | `0.0.0.0` | `255.255.255.255` | `68` → `67` |
| **OFFER** | IP du serveur | `255.255.255.255` ou adresse proposée | `67` → `68` |
| **REQUEST** | `0.0.0.0` | `255.255.255.255` | `68` → `67` |
| **ACK** | IP du serveur | `255.255.255.255` ou adresse proposée | `67` → `68` |
 
{{%notice style="info" title="OFFER et ACK : broadcast ou unicast?"%}}
Selon le client, le serveur envoie l'OFFER et l'ACK en **broadcast**, ou en **unicast** directement vers l'adresse MAC du client et l'adresse proposée. Le client l'indique dans son DISCOVER. Vous observerez le choix de votre client dans le laboratoire.
{{%/notice%}}
 
### Comment le client est-il reconnu?
 
Puisque le client n'a pas d'adresse IP, chaque message DHCP contient :
 
- l'**adresse MAC du client**, dans le message DHCP lui-même (et pas seulement dans l'en-tête Ethernet);
- un **identifiant de transaction** (*Transaction ID*), choisi au hasard par le client, qui permet d'associer chaque réponse à la bonne demande.
C'est grâce à l'adresse MAC que le serveur reconnaît un client d'une fois à l'autre.
 
### Pourquoi un REQUEST en broadcast?
 
Plusieurs serveurs DHCP peuvent répondre au même DISCOVER. Le client choisit une offre, puis envoie son REQUEST **en broadcast**, avec l'identifiant du serveur choisi. Ainsi :
 
- le serveur choisi confirme l'adresse (ACK);
- les autres serveurs savent que leur offre est refusée et libèrent l'adresse qu'ils avaient proposée.
### Autres messages
 
| Message | Sens | Rôle |
|---------|------|------|
| **NAK** | Serveur → client | Refus : l'adresse demandée n'est pas valide. Le client doit recommencer un DORA. |
| **RELEASE** | Client → serveur | Le client libère son adresse avant la fin du bail. |
| **DECLINE** | Client → serveur | Le client refuse l'adresse, car il a détecté qu'une autre machine l'utilise déjà. |
 
## Le bail
 
Une adresse est attribuée pour une **durée limitée** : le **bail** (*lease*). Le serveur enregistre chaque bail : adresse, adresse MAC du client, date d'expiration.
 
Si le client ne renouvelle pas son bail (machine éteinte, retirée du réseau), l'adresse redevient disponible pour un autre client à l'expiration du bail. C'est ce qui permet à un réseau de servir **plus d'appareils qu'il n'a d'adresses**, tant qu'ils ne sont pas tous connectés en même temps.
 
### Renouvellement
 
Un client essaie de conserver son adresse. Il n'attend pas l'expiration du bail :
 
![Renouvellement et expiration d'un bail DHCP](/images/06/06-dhcp-bail.png)
 
| Moment | Nom | Action du client |
|--------|-----|------------------|
| **50 %** du bail | **T1** (*renew*) | Envoie un REQUEST en **unicast** au serveur qui lui a attribué l'adresse. |
| **87,5 %** du bail | **T2** (*rebind*) | Si le serveur n'a pas répondu, envoie un REQUEST en **broadcast**, à n'importe quel serveur DHCP. |
| **100 %** du bail | Expiration | Sans réponse, le client cesse d'utiliser l'adresse et recommence un DORA. |
 
Le renouvellement ne comporte que **deux messages** (REQUEST, ACK) : le client a déjà une adresse IP et connaît le serveur, il peut donc lui écrire directement.
 
{{%notice style="tip" title="Au redémarrage"%}}
Un client qui redémarre et se souvient de son ancienne adresse envoie directement un **REQUEST** pour cette adresse, sans DISCOVER. Si le serveur refuse (NAK), le client recommence un DORA complet.
{{%/notice%}}
 
### Choisir la durée du bail
 
- Un bail **trop long** immobilise des adresses attribuées à des appareils qui ne sont plus là.
- Un bail **trop court** multiplie les renouvellements et la charge du serveur.
Des durées de **quelques heures** conviennent à un réseau Wi-Fi public; de **un à huit jours** à un réseau d'entreprise.
 
## L'étendue
 
Une **étendue** (*scope*) regroupe tout ce que le serveur sait d'un réseau :
 
| Élément | Exemple |
|---------|---------|
| **Réseau** | `172.16.10.0/24` |
| **Plage(s) d'adresses à distribuer** (*pool*) | `172.16.10.100` à `172.16.10.199` |
| **Options** | Passerelle `172.16.10.1`, DNS `172.16.10.2` |
| **Durée du bail** | `3600` secondes |
 
Le masque n'est pas une option à configurer : il découle du réseau de l'étendue.
 
### Options courantes
 
| Option | Code | Rôle |
|--------|------|------|
| Masque de sous-réseau | `1` | Masque du réseau (déduit de l'étendue) |
| Routeur | `3` | Passerelle par défaut |
| Serveurs DNS | `6` | Adresses des serveurs DNS |
| Nom de domaine | `15` | Domaine ajouté aux noms courts (`www` → `www.votrenom.lan`) |
| Durée du bail | `51` | Durée du bail, en secondes |
| Identifiant du serveur | `54` | Adresse IP du serveur DHCP qui répond |
 
Le **code** est le numéro de l'option dans le message DHCP. C'est lui que Wireshark affiche, par exemple *Option: (3) Router*.
 
### Plan d'adressage et étendue
 
Toutes les adresses d'un réseau ne doivent pas être distribuées. Les équipements dont l'adresse doit être connue à l'avance (routeur, serveurs) reçoivent une **adresse statique**, en dehors de la plage distribuée.
 
Exemple pour le réseau du chapitre :
 
| Adresses | Usage |
|----------|-------|
| `172.16.10.1` à `172.16.10.9` | Routeur et serveurs (statiques) |
| `172.16.10.10` à `172.16.10.99` | Réservations |
| `172.16.10.100` à `172.16.10.199` | Plage distribuée |
| `172.16.10.200` à `172.16.10.254` | Libres (croissance future) |
 
{{%notice style="warning" title="Attention"%}}
Une adresse attribuée statiquement ne doit **jamais** se trouver dans la plage distribuée : le serveur pourrait l'attribuer à un client, et deux machines auraient la même adresse.
{{%/notice%}}
 
### Exclusions
 
Une **exclusion** retire des adresses de la plage distribuée, par exemple pour des imprimantes configurées statiquement.
 
Avec Kea, le serveur utilisé dans ce cours, il n'existe pas de directive d'exclusion : on découpe la plage en **plusieurs plages** qui contournent les adresses exclues.
 
Exemple : exclure `172.16.10.150` à `172.16.10.159` :
 
```json
"pools": [
    { "pool": "172.16.10.100 - 172.16.10.149" },
    { "pool": "172.16.10.160 - 172.16.10.199" }
]
```
 
### Réservations
 
Une **réservation** garantit qu'un client obtient **toujours la même adresse** par DHCP. Elle associe une **adresse MAC** à une adresse IP :
 
```json
"reservations": [
    {
        "hw-address": "00:0c:29:3a:5b:7c",
        "ip-address": "172.16.10.50",
        "hostname": "imprimante-01"
    }
]
```
 
Une réservation est utile pour un appareil dont l'adresse doit être connue (imprimante, caméra), mais qu'on souhaite gérer de façon centralisée plutôt que de le configurer à la main.
 
Avec Kea, l'adresse réservée peut se trouver **en dehors** de la plage distribuée. C'est même recommandé : on évite ainsi qu'elle soit attribuée à un autre client.
 
## Où placer le serveur DHCP?
 
Les messages DISCOVER et REQUEST sont envoyés en **broadcast**. Or un routeur **ne transmet pas** les broadcasts d'un réseau à un autre (chapitre 4).
 
Un serveur DHCP ne reçoit donc que les demandes de son propre réseau.
 
### Le relais DHCP
 
Dans une organisation comptant plusieurs réseaux, on n'installe pas un serveur DHCP par réseau. On configure plutôt, sur chaque routeur, un **relais DHCP** (*DHCP relay*) :
 
![Relais DHCP](/images/06/06-dhcp-relais.png)
 
1. Le relais reçoit le DISCOVER diffusé par le client.
2. Il le retransmet en **unicast** au serveur DHCP central, en y inscrivant l'adresse de son interface (champ *Relay agent IP address*).
3. Grâce à cette adresse, le serveur sait de quel réseau provient la demande et choisit la bonne étendue.
{{%notice style="info" title="Remarque"%}}
Le relais DHCP n'est pas mis en œuvre dans les laboratoires de ce cours. Dans notre réseau, le serveur DHCP est installé directement sur le routeur, qui possède une interface dans le réseau des clients.
{{%/notice%}}
 
## Côté client
 
| Action | Linux (NetworkManager) | Windows |
|--------|------------------------|---------|
| Afficher la configuration obtenue | `nmcli -f DHCP4 device show ens160` | `ipconfig /all` |
| Libérer l'adresse | — | `ipconfig /release` |
| Redemander une adresse | `sudo nmcli con down ens160`<br>`sudo nmcli con up ens160` | `ipconfig /renew` |
 
{{%notice style="tip" title="Adresse 169.254.x.x"%}}
Un poste Windows qui ne reçoit aucune réponse DHCP s'attribue lui-même une adresse dans `169.254.0.0/16` (**APIPA**). Cette adresse lui permet seulement de communiquer avec les machines voisines dans le même cas. Une adresse `169.254.x.x` indique donc presque toujours un **problème de DHCP**.
{{%/notice%}}
 
{{%notice style="tip" title="À retenir"%}}
DHCP utilise **UDP 67/68**. Le client obtient son adresse par l'échange **DORA**, en broadcast, car il n'a pas encore d'adresse. L'adresse est attribuée pour la durée d'un **bail**, renouvelé à **T1** (unicast) puis à **T2** (broadcast). Le serveur ne reçoit que les demandes de son réseau, à moins d'utiliser un **relais**.
{{%/notice%}}
 
 