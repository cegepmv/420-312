# 6. Services applicatifs

# 6.1. DHCP
-------------------

**DHCP** (*Dynamic Host Configuration Protocol*) est un service réseau qui automatise l’attribution des paramètres IP aux appareils. Sans lui, chaque poste devrait être configuré manuellement (de façon *statique*, comme vu jusqu’à présent) : une tâche longue, répétitive et source d’erreurs.

Avec DHCP, un ordinateur qui se connecte au réseau peut "demander" une configuration, et un serveur lui "répond" avec tout ce dont il a besoin pour communiquer :
- une adresse IP valide,
- un masque de sous-réseau,
- une passerelle par défaut,
- les serveurs DNS,
- et de nombreuses autres options.

Ce mécanisme permet non seulement de gagner du temps, mais aussi de garder un réseau cohérent, organisé et facile à administrer, que ce soit dans un petit laboratoire informatique ou dans une grande entreprise.

{{%notice style="info" title="Note"%}}
- DHCP existe pour IPv4 et IPv6, avec des fonctionnalités légèrement différentes.
- Il a d’abord été conçu comme extension du protocole **BootP**, qui permettait à une station de travail sans disque dur de démarrer par le réseau via **TFTP**.
- Il est désormais indispensable dans la majorité des entreprises, et essentiel pour les fournisseurs d’accès Internet.
- Le DHCP permet une gestion optimisée des adresses IP, en utilisant moins d’adresses que de clients (voir la section *Limitation IPv4* du cours).
{{%/notice%}}


## Le protocole

![DORA](../images/05-01-01.png?width=300px)

- **DHCP** utilise les ports `UDP 67` (serveur) et `UDP 68` (client).
- `UDP 68` n’est utilisé par le client que pendant la négociation initiale.

Pour fournir une adresse IP à un hôte, quatre messages sont échangés :

1. **DHCPDISCOVER** — client → serveur  
2. **DHCPOFFER** — serveur → client  
3. **DHCPREQUEST** — client → serveur  
4. **DHCPACK** — serveur → client  

{{%notice style="tip" title=" "%}}
Cet échange est communément appelé **DORA** ( **D**iscover-**O**ffer-**R**equest-**A**CK).
{{% /notice%}}
### DHCPDISCOVER
![DHCPDISCOVER](../images/05-01-02.png?width=550px)


- Envoyé par le client.  
- Adresse IP source : `0.0.0.0` (le client n’a pas d’adresse IP).  
- Adresse IP de destination : `255.255.255.255` (le serveur est inconnu).  
- Identifié comme un message `DHCPDISCOVER`.  
- Le paquet contient l’adresse MAC du client.  
- Transmission en **broadcast**.

### DHCPOFFER
![DHCPOFFER](../images/05-01-03.png?width=550px)

- Envoyé par le serveur DHCP.  
- Adresse IP source : IP du serveur.  
- Adresse IP de destination : `255.255.255.255`.  
- Identifié comme `DHCPOFFER`.  
- Contient l’adresse IP proposée au client, son masque, la passerelle, les DNS et autres options.  
- Contient aussi l’adresse MAC du client.  
- Transmission en **unicast** destiné spécifiquement au client.

### DHCPREQUEST
![DHCPREQUEST](../images/05-01-04.png?width=550px)

- Envoyé par le client.  
- Adresse IP source : `0.0.0.0`.  
- Adresse IP de destination : `255.255.255.255` (**broadcast**), car plusieurs serveurs ont peut-être répondu.  
- Identifié comme `DHCPREQUEST`.  
- Le client y indique l’offre (le serveur) qu’il accepte.

### DHCPACK
![DHCPACK](../images/05-01-05.png?width=550px)

- Envoyé par le serveur choisi.  
- Adresse IP source : IP du serveur.  
- Adresse IP de destination : `255.255.255.255`.  
- Identifié comme `DHCPACK`.  
- Le client peut maintenant utiliser l’adresse IP.  
- Contient les options DHCP applicables.  
- Contient l’adresse MAC du client.


## Les baux d’adresses

Une adresse IP est attribuée à un client pour une **durée limitée**, appelée *bail*.

Si le client ne renouvelle pas son bail (machine éteinte, partition du réseau…), l’adresse redevient disponible après un délai supplémentaire appelé *période de grâce*.

{{%notice style="tip" title="Notes"%}}
- Un bail trop long immobilise inutilement des adresses.
- Un bail trop court génère du trafic réseau et de la charge sur les serveurs DHCP.
- Les durées courantes vont de **24 h à 7 jours**, mais elles sont configurables.
{{%/notice%}}

### Renouvellement du bail

Un client essaie de garder son adresse IP. Le processus :

- À **50 %** du bail : premier renouvellement (*T1*).  
- À **75 %** : deuxième tentative (*T2*).  
- À **87,5 %** : troisième tentative.  
- Si aucune réponse : le client relance un processus complet **DORA**.

Le renouvellement consiste simplement à envoyer un **DHCPREQUEST** ; le serveur répond par **DHCPACK** (succès) ou **DHCPNACK** (refus).

<!-- ## Commandes côté client

Renouveler une adresse IP :

```bash
dhclient [interface]
```

Libérer l’adresse IP :

```bash
dhclient -r [interface]
```

--- -->

## Étendue DHCP (*scope*)

Une **étendue DHCP** est une plage d’adresses IP qu’un serveur peut distribuer, accompagnée de ses options.

Elle contient :
- une plage d’adresses à distribuer,
- un masque,
- la passerelle,
- les DNS,
- d’autres paramètres (WINS, NTP, nom de domaine, etc.).

Exemple de plage : `10.2.2.1` à `10.2.2.35`.

{{%notice style="note" title="Important"%}}
- L’adresse IP du serveur DHCP doit être **statique**.  
- Elle ne doit pas appartenir à la plage distribuée.  
- Le serveur doit se trouver **dans le même réseau**, car DHCP utilise `255.255.255.255` qui ne traverse pas les routeurs.
{{%/notice%}}


### Options courantes

| Option | Désignation |
|--------|-------------|
| `routers` | Routeur(s) par défaut du réseau |
| `range` | Plage d’adresses distribuées |
| `domain-name-servers` | Liste des DNS |
| `domain-name` | Nom de domaine utilisé par les clients |
| `broadcast-address` | Adresse de diffusion du réseau |
| `default-lease-time` | Durée par défaut du bail (secondes) |
| `max-lease-time` | Durée maximale du bail |

## Les exclusions

Une **plage d’exclusion** permet d’indiquer quelles adresses ne doivent pas être distribuées par DHCP.

Exemples :
- Adresses réservées pour des serveurs
- Adresses utilisées statiquement dans le réseau
- Plages réservées pour un autre usage

Avec ISC DHCP classique, on peut définir explicitement des plages d’exclusion.  
Avec **DHCP Kea**, on crée plutôt plusieurs plages distinctes qui évitent les adresses à exclure.

Exemple : exclure `192.168.10.100` à `192.168.10.150` :

```json
{
"Dhcp4": {
    "subnet4": [
        {
            "subnet": "192.168.10.0/24",
            "pools": [
                {"pool": "192.168.10.1 - 192.168.10.99"},
                {"pool": "192.168.10.151 - 192.168.10.254"}
            ],
        }
    ]
}
}
```

---

## Réservations d’adresses IP

Une **réservation** permet de garantir qu’un appareil obtiendra toujours la même adresse IP *via DHCP*.

Utile pour :
- des serveurs,
- des imprimantes,
- des appareils nécessitant des règles particulières,
- des portables qui circulent entre plusieurs réseaux.

Basée sur l’adresse **MAC** du client.

Éléments requis :
- un nom de réservation,
- l’adresse MAC,
- l’adresse IP réservée.
- (optionnel) sont nom d'hôte (*hostname*)

Exemple :

```json
{
  "subnet4": [
    {
      "reservations": [
        {
          "hw-address": "aa:bb:cc:dd:ee:ff",
          "ip-address": "192.0.10.150",
          "hostname": "server.example.org"
        }
      ],
    }
  ]
}
```


# 6.1.2 Laboratoire
-------------------

Ce lab consiste à installer un serveur DHCP ayant trois interfaces réseau:

+ Une en **NAT**
+ Une dans le `192.168.10.0/24` (LAN SEGMENT)
+ Une dans le `10.10.10.0/24` (LAN SEGMENT)

Pensez à donner une adresse IP fixe aux deux interfaces du serveur DHCP qui ne sont pas en NAT.

```bash
$ sudo nmcli con mod ens160 ipv4.addresses a.b.c.d/24
$ sudo nmcli con mod ens160 ipv4.method manual
$ sudo nmcli con down ens160
$ sudo nmcli con up ens160
```

Vous placerez deux machines Linux :
+ Une dans le réseau `192.168.10.0/24`
+ L'autre dans le réseau `10.10.10.0/24`.

+ Ces deux machines seront les clients DHCP.

### Installation du serveur DHCP:
```bash
$ sudo dnf -y install kea
```

Le fichier de configuration est `/etc/kea/kea-dhcp4.conf`.

Un gabarit est déjà fourni (avec la configuration commentée). Renommez le gabarit puis créez un nouveau fichier de configuration : 

```bash
$ sudo mv /etc/kea/kea-dhcp4.conf /etc/kea/kea-dhcp4.template.conf
$ sudo nano /etc/kea/kea-dhcp4.conf
```

+ Créez votre subnet pour le réseau `192.168.10.0/24`:
```json
{
# La configuration DHCP (v4) commence
"Dhcp4": {

# Variables globales
  # Durée du bail (s)
  "valid-lifetime": 4000,
  # Durée avant demande de renouvellement (s)
  "renew-timer": 1000,
  # Durée avant demande de resynchronisation
  "rebind-timer": 2000,

  # Configuration des interfaces utilisées par le serveur
  "interfaces-config": {
      "interfaces": [ "ens160" ]
  },

  # Définition de la BD des bails
  "lease-database": {
    "type": "memfile",
    "persist": true,
    "name": "/var/lib/kea/dhcp4.leases"
  },

  # Configuration de la liste des réseaux 
  "subnet4": [
    {
      "id": 1,
      "subnet": "192.168.10.0/24",
      "pools": [
        {
          "pool": "172.16.10.2 - 192.168.10.200"
        }
      ],
	  "option-data": [
	    {
		  "name": "domain-name-servers",
          "data": "8.8.8.8, 8.8.4.4",
		  "always-send": true
        },
	    {
          "name": "routers",
          "data": "172.16.0.1",
		  "always-send": true
        },
	  ]
    }
  ]
}
}


```

Pour démarrer le service DHCP :
```bash
$ sudo systemctl start kea-dhcp4
```

Pour activer le service DHCP (démarre à chaque *reboot*) :
```bash
$ sudo systemctl enable kea-dhcp4
```


### Laboratoire 1
Créez maintenant l’étendue pour l’autre interface réseau (10.10.10.0/24).

Assurez-vous que les baux pour cette étendue soit d’une durée d’une semaine.

+ Le serveur DNS distribué doit être celui du département.
+ Démarrez les machines clientes et assurez-vous qu’elles obtiennent les bonnes adresses IP, masque de réseau et passerelle par défaut.
+ Vous pouvez voir les adresses attribuées (les leases) dans le fichier: `/var/lib/kea/dhcp4.leases`

+ Écoutez les requêtes DHCP sur l’une des interfaces et regardez les paquets échangés.
```bash
$ tcpdump -i <interface> -pvn port 67 and port 68
```
Pouvez-vous voir les paquets `DISCOVER`, `OFFER`, `REQUEST` et `ACK`?

### Laboratoire 2
1. Notez l’adresse IP de votre client DHCP Linux.
2. Excluez cette adresse du DHCP.
3. Libérez l’adresse actuelle et demandez en une nouvelle.
4. Assurez-vous que vous obtenez une adresse différente qui n’est pas dans la plage d’exclusion.

Maintenant, créez une réservation d’adresse pour votre client (prenez une adresse IP différente de celle qui lui a été attribuée).

6. Libérez l’adresse actuelle et demandez en une nouvelle.
7. Assurez-vous que le client a obtenu l’adresse IP que vous lui aviez réservé.


### Laboratoire 3
Ce lab consiste à configurer une machine Linux comme routeur et serveur DHCP.

1. Créez une VM Linux avec 2 interfaces : 
    + Une en bridged
    + Une dans le `172.16.10.0/24` (LAN SEGMENT)

2. Configurez l'interface dans le `172.16.10.0/24`, attribuez-lui l'adresse `172.16.10.1`.

3. Configurez le serveur DHCP : 
    + Plage d'adresse : 172.16.10.2 à 172.16.10.200,
    + Serveurs DNS : 8.8.8.8, 8.8.4.4 (google)
    + Passerelle : 172.16.10.1 (l'interface du serveur DHCP)

4. Créez un client avec une interface dans le `172.16.10.0/24`. Vérifiez qu'il reçoit bien une adresse IP et les options configurées dans le serveur DHCP.

5. Configurez le serveur DHCP pour qu'il soit aussi un routeur: 
    + Activez les forwarding des paquets : Dans le fichier `/etc/sysctl.conf`, ajouter la ligne `net.ipv4.ip_forward=1` : 
    ```bash
    sudo nano /etc/sysctl.conf 
    # ou
    echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
    ```
    + Appliquez les changement 
    ```bash
    sudo sysctl -p /etc/sysctl.conf
    ```
    + Configurez le pare-feu pour autoriser le transit de trafic entre les interfaces et le masquage NAT (*NAT masquerade*) : 
    ```bash
    sudo firewall-cmd --zone=public --add-masquerade --permanent
    sudo firewall-cmd --zone=public --add-service=dhcp --permanent
    sudo firewall-cmd --reload
    ```

6. Vérifiez que vous pouvez faire des requêtes sur Internet à partir du client (par exemple : `ping www.google.com`)



# 6.2. DNS
-------------------

+ Le DNS, (*Domain Name System*), est un service essentiel du fonctionnement d’Internet. 
+ Son rôle principal est de traduire un nom de machine (par exemple www.google.com) en adresse IP (comme `142.250.65.174`), et inversement. 
+ Cette traduction est nécessaire parce que les utilisateurs retiennent beaucoup plus facilement des noms que des adresses numériques, tandis que les ordinateurs, eux, ne fonctionnent qu’avec des IP.

+ Pour fonctionner correctement, un nom doit être exprimé sous forme de **FQDN** (*Fully Qualified Domain Name*). Un FQDN contient à la fois le nom de l’hôte et le nom du domaine complet, par exemple :
`serveur1.collegemv.qc.ca`.

## Fonctionnement de DNS

Le DNS repose sur un **modèle client/serveur** :

+ **Serveur DNS :** Dans ce cours, nous allons voir comment `bind` fonctionne. Son processus s’appelle **named**.
+ **Client DNS :** appelé **resolver**, il est présent sur toute machine connectée à un réseau. C’est lui qui envoie les requêtes DNS au serveur configuré lors de l’installation du réseau.

Les échanges DNS utilisent principalement :

+ le port `UDP 53` pour les requêtes,
+ le port `TCP 53` pour les transferts de zones (nous n'allons pas aborder les transferts de zone dans ce cours).

## Structure du DNS

![Hiérarchie de DNS](../images/05-02-01.png?width=600px)

+ Le DNS est organisé de façon **hiérarchique** : un peu comme une pyramide inversée où chaque couche connaît des informations sur la couche située juste en dessous.
+ Au sommet, on trouve les **serveurs racine**.
+ Bien qu’il n’existe que 13 noms de serveurs racine (de A à M), chacun d’eux est en réalité répliqué sur des dizaines de serveurs répartis dans le monde (*anycast*), ce qui permet un accès rapide et fiable.

+ Les serveurs racine connaissent les serveurs responsables de chacun des **TLD** (*Top Level Domain*), comme `.com`, `.ca`, `.org`, etc.
+ Chaque TLD gère à son tour les domaines qui lui appartiennent, et connaît les serveurs DNS autoritaires pour chacun de ces domaines.
+ Ce modèle hiérarchique permet à Internet d’être extrêmement scalable, robuste et réparti sur toute la planète.


## Domaine vs Zone

Cette distinction est fondamentale pour comprendre la configuration d'un serveur DNS.

+ Un **domaine** : représente un ensemble logique de noms qui partagent un même suffixe.
Par exemple, le domaine `collegemv.qc.ca` inclut tous les noms terminant par cette chaîne, comme `www.collegemv.qc.ca`, `mail.collegemv.qc.ca`, etc.

+ Une **zone** (en revanche) : représente la partie du domaine qui est réellement gérée par un serveur DNS.
+ La zone peut être plus petite que le domaine si certaines parties ont été **déléguées** à d'autres serveurs.

### La délégation

La délégation est un mécanisme permettant à l’administrateur d’un domaine de confier la gestion de l’un de ses sous-domaines à un autre serveur DNS autoritaire.

Par exemple :

+ le TLD `.ca` délègue `videotron.ca` à Vidéotron,
+ le domaine `.qc.ca` délègue `collegemv.qc.ca` au Collège Marie-Victorin.

Pour effectuer une délégation, l’autorité qui gère une zone doit placer dans son DNS le nom et l’adresse IP des serveurs DNS autoritaire de la zone déléguée. Ces enregistrements se nomment des “glue record”.

Par exemple : on trouve sur le DNS de `.ca` le nom et l’adresse IP des serveurs DNS autoritaires de `videotron.ca`


## Types de requêtes
![Types de requêtes DNS](../images/05-02-02.png?width=500px)

Il existe deux types de requêtes, et comprendre la différence aide énormément à visualiser le fonctionnement du DNS.

1. 🔁 **La requête récursive :** Une requête récursive oblige le serveur interrogé à fournir une réponse complète, même s’il doit pour cela interroger d’autres serveurs.
C’est typiquement ce que fait votre DNS local ou votre routeur lorsque votre ordinateur lui demande de résoudre un nom de domaine.

2. 🔄 **La requête itérative :** 
    + Ici, le serveur interrogé renvoie simplement la meilleure réponse possible.
    + Souvent, cela consiste à dire : « Je ne connais pas la réponse, mais tu peux demander à ce serveur-là. »
    + C’est la méthode utilisée par les DNS récursifs pour parcourir l’arborescence du DNS, depuis les serveurs racine jusqu’au serveur autoritaire final.

### Types de serveurs DNS
Il existe deux types de requêtes DNS:
+ Un **DNS cache/récursif** accepte les requêtes récursives des clients, interroge d’autres serveurs, et conserve temporairement les réponses en cache.
+ Un **DNS autoritaire** détient les informations officielles d’une zone, et ne fait jamais de récursion.


## Les types d’enregistrements
Il existe de nombreux types d’enregistrement. Voici les plus courant : 

|Type|Description|
|----|-----------|
|**A**|adresse IP
|**AAAA**|adresse IPv6|
|**TXT**|texte ou commentaire|
|**PTR**|DNS inverse, pour obtenir un nom|
|**CNAME**|Canonical Name (alias)|
|**MX**|nom du serveur de courriels d’un domaine|
|**SRV**|permet de définir un serveur comme assurant un service donné|


## Références
+ Documentation de Bind (en français) https://wiki.debian.org/fr/Bind9
+ DNS for rocket scientists http://www.zytrax.com/books/dns/
+ Documentation Bind https://www.isc.org/downloads/bind/doc/



# 6.2.3. Laboratoires
-------------------


### 1 - Reproduction d’une requête récursive

#### La commande dig

La commande `dig` permet d’interroger des serveurs DNS.

Sa syntaxe est :
```nginx
dig [@ip serveur DNS] [-t type de RR] [nom demandé]
```
La commande `dig` passée toute seule renvoie la liste de tous les serveurs racines.

Exemple simple :
```nginx
dig google.com
```

Interroger un serveur donné :
```nginx
dig @8.8.4.4 -t A www.google.com
dig @8.8.4.4 -t ANY www.videotron.com
```

#### Principe du lab

Résoudre le nom www.google.com

1. Vous obtenez la liste des serveurs racine grâce à la commande `dig`.
2. Ensuite, interrogez un des serveurs racines pour obtenir l’adresse IP de `www.google.com`. 
3. Ces serveurs vous donnent la liste des serveurs DNS autoritaires de la zone `.com`. Choisissez en un et interrogez le.
4. Vous obtiendrez la liste des serveurs DNS autoritaires de .qc.ca…
5. Continuez jusqu’à obtenir l’adresse IP recherchée.



----------------------------
{{% notice style="tip" title="Note"%}}
Pour les prochains exercices, vous aurez besoin d'un serveur Rocky/Alma.
{{% /notice %}}


### 2- Exploration de Bind

+ Bind est le serveur DNS le plus utilisé au monde.
+ Il est libre et gratuit.
+ Il est très performant et dispose de toutes les fonctionnalités possibles.
+ Comme le DHCP que nous avons vu (Kea), il est développé par ISC.
+ La documentation : https://www.isc.org/downloads/bind/doc/

Son installation est très simple :
```bash
$ dnf install bind
$ systemctl start named
$ systemctl status named
```

Le fichier de configuration est `/etc/named.conf`.

#### 1- Bind est-il un serveur cache par défaut ? 
Lisez le contenu du fichier `/etc/named.conf`. D'après ce fichier, est-ce que votre serveur Bind agit comme serveur DNS cache? Comment pouvez-vous le tester?

Chercher sur Internet comment changer l’état du DNS cache (s’il est activé par défaut, comment le désactiver et s’il est désactivé par défaut, comment l’activer).

Le fichier à modifier est `/etc/named.conf`. Une seule instruction très simple. Modifiez son état et tester que votre manipulation a fonctionné. À la fin de ce lab, le DNS cache doit être activé sur votre serveur.


#### 2- Exploration des fichiers de zone et enregistrements
Le fichier de configuration de bind est `/etc/named.conf`.

C’est dans ce fichier que seront définis les noms des zones ainsi que l’emplacement des fichiers de zone et si le serveur est récursif ou non.

Pour créer une zone, deux actions sont nécessaires :

+ Définition de la zone dans le fichier de configuration `/etc/named.conf`
+ Création du fichier de la zone dans `/var/named` : ce sont les enregistrements qui sont dans ce fichiers.

Définition d’une zone :

```bash
zone "domaine1.com" {
	type primary;        		
	file "domaine1.com";		
};
```
Contenu d’un fichier de zone:

```powershell
$TTL 3H
@       IN SOA  @ admin.ghaziland.internal. (
                                        2       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum

@	IN	NS	localhost.
@	IN	A	127.0.0.1
@	IN	AAAA	::1
```
Explication du fichier ci-dessus :

|Section|Description|
|---|---------------|
|`$TTL`| durée pendant laquelle un DNS cache peut mettre en cache les informations de cette zone|
|`SOA` (*Start Of Authority*)|Cette section définit les options pour toute la zone:|
|`Serial`| Identifie le numéro de version de la zone. Doit être incrémenté à chaque modification.|
|`Refresh`| Intervalle de temps pour que le secondaire vienne mettre à jour les données sur le primaire.|
|`Retry` | Intervalle de temps pour réessayer en cas d’échec.|
|`Expire`| Si le primaire tombe, durée avant que les secondaires cessent de répondre.|
|`Negative cache TTL`| Durée de vie dans le cache des réponses d’erreur (NXDOMAIN)|
|`@`| Correspond au nom de la zone dans laquelle se trouve le signe.|

Il faut ensuite modifier le groupe propriétaire de ce fichier :

```bash
$ chgrp named domaine1.local
```

### 3- Configuration d’un serveur autoritaire
Nous allons configurer une zone : `domaine1.com`.

Le serveur sera autoritaire pour la zone `domaine1.com`.

Pour configurer une zone, deux étapes sont nécessaires:

1. Créer le fichier de zone contenant les informations de la zone (nom et adresses IP).
2. Déclarer la zone dans le fichier `/etc/named.conf` pour que le fichier de zone soit pris en compte.

#### Création de la zone
Vous pouvez partir d’un modèle de zone pour créer la première zone, utilisez `named.empty` dans le répertoire `/var/named` et copiez le en lui donnant le nom de la zone voulue: `domaine1.com`.

Vous pouvez ensuite modifier son contenu pour avoir :
```bash
$TTL 3H
@       IN SOA  @ admin.ghaziland.internal. (
                                        2       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum


@	IN	NS	ns1.domaine1.com.
ns1	IN	A	192.168.230.254		; vous devez inscrire l'adresse de votre serveur
www	IN	A	182.45.32.67		; adresse IP de votre serveur www (ce que vous voulez)
```
Le fichier de zone est créé. Vous pouvez le tester à l’aide de la commande:

```bash
$ named-checkzone domaine1.com /var/named/domaine1.com
```

Le fichier `domaine1.com` doit avoir `named` comme groupe propriétaire.

Cette commande ne doit pas vous retourner d’erreurs.

Le fichier de zone a été créé mais il faut maintenant l’ajouter à la configuration Bind. Ceci peut se faire dans le fichier `/etc/named.conf` en y ajoutant la déclaration de la zone à la fin du fichier de la façon suivante :

```bash
zone "domaine1.com" {
	type primary;        		
	file "domaine1.com";		
};
```

Vérifiez la configuration:
```bash
$ named-checkconf /etc/named.conf
```
Vous ne devriez pas avoir d’erreur.

Redémarrez ensuite le service Bind pour appliquer les nouveaux changements:
```bash
$ sudo systemctl restart named
$ # puis pour vérifier son état :
$ sudo systemctl status named 
```

+ Interrogez votre serveur DNS pour le tester: demandez lui l’adresse de `ns1.domaine1.com` à l’aide de la commande `dig`.

<!-- Il s’agit de configurer maintenant le DNS autoritaire secondaire pour la même zone. -->
+ Configurez votre serveur pour que `www.domaine1.com` et `domaine1.com` renvoie la même adresse IP.
<!-- 
Introduction à DNSSEC
Le principal problème de DNS est la sécurité.

Comme DNS fonctionne sur UDP, n’importe qui peut répondre à vos requêtes DNS et vous rediriger vers un site malveillant.

Plusieurs solutions aident à résoudre ce problème mais la plus efficace est DNSSEC.

Fonctionnement

Le serveur racine signe à l’aide d’une clé tous ses enregistrements.

Si vous demandez à résoudre www.fraise.com, le serveur racine vous donnera les noms des serveurs DNS autoritaires de .com (signé par la clé du serveur racine) et vous donnera aussi la clé de .com (signée aussi avec la clé du serveur racine).

Vous pourrez alors interroger le serveur DNS autoritaire de .com et vérifier que les informations qu’il vous envoie ont bien été signées par la bonne personne vu que le serveur racine vous a envoyé la clé de .com.

.com vous enverra à son tour les noms des DNS autoritaires de fraise.com ainsi que la clé de fraise.com. Vous pourrez alors demander des informations à ces serveurs et vérifier que ce sont bien eux qui répondent.

Il se bâtit ainsi une chaine de confiance.

Pour que cette chaine existe, vous devez seulement disposer de la clé du serveur racine.

Lors de l’installation de Bind, ces clés se trouvent dans: /etc/named.root.key -->



# 6.3. Web
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


# 6.3.1
# Introduction à nginx
-------------------

**Nginx** est un serveur web léger, rapide et très utilisé.
Il peut servir :

+ des pages statiques (HTML, images…),
+ des applications web via des reverse proxies,
+ du HTTPS (certificats),
+ de la répartition de charge (load balancing).

## Installation et vérification
```bash
sudo dnf install nginx
sudo systemctl status nginx
```

### Fichiers utiles
+ **Fichier de configuration (défaut) -** `/etc/nginx/nginx.conf`
+ /etc/nginx/sites-available/
+ **Contenu Web par défaut -** `/var/www/html/`

### Configuration minimale

Exemple simple de serveur web :
```nginx
server {
    listen 80;
    server_name exemple.com;

    root /var/www/html;
    index index.html;
}
```

## Laboratoires

### 1- Introduction à nginx

1. Installer et configurer `nginx`
2. Créer une page `index.html` personnalisée dans le répertoire `/var/www/html`
3. Tester en tapant l'IP du serveur dans un navgateur
4. Observez les logs :
    + `/var/log/nginx/access.log` 
    + `/var/log/nginx/error.log`

{{%notice style="tip" title="Astuce"%}}
Pour voir l'évolution des logs en temps réel, vous pouvez utiliser la commande `tail -f`.
{{%/notice%}}

### 2- Servir une application React
1. Clonez une application de votre choix (peut-être votre projet final du cours Applications Web?)

2. À l'intérieur du répertoire du projet :
```bash
npm install
npm run build
```
La deuxième commande va créer une version exportable de votre projet avec un `index.html` dans un répertoire `build`. 

3. Copiez le contenu de `build` dans le répertoire `/var/www/html`. 


# 6.4. Atelier synthèse
-------------------

Dans ce laboratoire final, vous mettrez en pratique l’ensemble des notions vues dans le dernier chapitre en construisant un petit réseau local complet. Vous devrez déployer un LAN composé de 4 VMs jouant chacune un rôle précis : 
+ un serveur DHCP (**Kea**) faisant également office de **routeur**, 
+ un serveur DNS Bind récursif et autoritaire pour une zone donnée , 
+ un serveur Web Nginx hébergeant une page statique, et enfin
+ un poste client permettant de valider le bon fonctionnement de l’infrastructure. 

L’objectif de ce laboratoire est de vous amener à intégrer ces services, à comprendre leurs interactions et à diagnostiquer les problèmes potentiels d’un réseau réel. À la fin, vous aurez construit un environnement fonctionnel où chaque service dépend des autres, exactement comme dans un réseau réel.

La topologie/diagramme du réseau est la suivante :

![Topologie](../images/05-04-01.png)

+ **Adresse réseau -** `172.16.10.0/24`
+ **Serveur DHCP** 
    + Interface 1 (LAN):
        + Mode: *LAN SEGMENT*
        + IP: `172.16.10.1/24`
    + Interface 2 (WAN):   
        + Mode - *Bridged* 
+ **Serveur DNS**
    + Interface 1:
        + Mode: *LAN SEGMENT*
        + IP: `172.16.10.2/24`
+ **Serveur Web**
    + Interface 1:
        + Mode: *LAN SEGMENT*
        + IP: `172.16.10.3/24`

## Requis fonctionnels
Le diagramme suivant décrit les interactions entre les différentes machines :

![Interaction](../images/05-04-02.png)

### Exigences

+ **Routeur DHCP**
    + Le serveur DHCP doit faire office de routeur: il aura une interface dans le LAN Segment qui sera la **passerelle par défaut** du réseau et une autre en NAT/Bridged qui servira de "porte de sortie" vers internet (ce sera notre interface WAN).
    + Dans la configuration des plages d'adresses et options gérées par DHCP : 
        + La passerelle par défaut donnée aux client doit être l'adresse de son interface LAN: `172.16.10.1`.
        + L'adresse du serveur DNS donnée au client doit être celle du serveur DNS que l'on va configurer: `172.16.10.2`.
+ **DNS** 
    + Le serveur DNS doit accepter les requêtes récursives et être autoritaire pour la zone `votrenom.lan`.
    + Il doit y avoir au minimum 3 enregistrements pour la zone `votrenom.lan`:
        + `ns1` (A) avec l'adresse IP du serveur DNS: `172.16.10.2`
        + `dhcp` (A) avec l'adresse IP du serveur DHCP: `172.16.10.1`
        + `www` (A) avec l'adresse IP du serveur Web:`172.16.10.3`  
+ **Serveur Web**
    + Le serveur Web doit servir une application React simple de votre choix.

## Étapes d'implémentation

### Pré-requis
Créer les machines virtuelles sur VMWare :
+ `dhcp-router` (Rocky/Alma Linux) avec 2 adaptateurs réseau: un en LAN Segment (LAN), et un en NAT ou Bridged (WAN).
+ `dns-server` (Rocky/Alma Linux) avec un adaptateur réseau en LAN Segment
+ `web-server` (Ubuntu) avec un adaptateur réseau en LAN Segment.
+ `client` (OS de votre choix)- avec un adaptateur réseau en LAN Segment.

{{% notice title="Remarque" style="info"%}}
Afin de pouvoir télécharger et installer les services `bind` et `nginx` dans les machines `dns-server` et `web-server`, il est nécessaire de temporairement mettre leur adaptateur réseau en NAT ou Bridged pour avoir accès à internet lors de la configuration. Ensuite, une fois que tout est configuré, changez le mode en LAN Segment et configurez leur interface de façon statique.

Il n'est donc pas nécessaire de faire cela pour le serveur DHCP car il a déjà accès à internet via son interface WAN. 
{{% /notice %}}

### Configuration du routeur DHCP
#### Partie DHCP
1. Changer son *hostname* (optionnel, seulement pour se repérer) :
```bash
sudo hostnamectl set-hostname dhcp-router 
```
2. Configurer l'interface LAN :
```bash
sudo nmcli con add con-name ens224 type ethernet ifname ens224
sudo nmcli con mod ens224 ipv4.address 172.16.10.1/24
sudo nmcli con mod ens224 ipv4.method manual
sudo nmcli con down ens224
sudo nmcli con up ens224
```
<!-- 3. Désactiver le pare-feu
```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
``` -->
3. Installer *ISC-Kea*
```bash
sudo dnf install kea
```
4. Modifier le fichier de configuration `/etc/kea/kea-dhcp4.conf` :
```json
{
# La configuration DHCP (v4) commence
"Dhcp4": {

# Variables globales
  # Durée du bail (s) - 1 semaine
  "valid-lifetime": 604800,
  # Durée avant demande de renouvellement (s)
  "renew-timer": 604800,
  # Durée avant demande de resynchronisation (1 jour)
  "rebind-timer": 86400,

  # Configuration des interfaces utilisées par le serveur
  "interfaces-config": {
      "interfaces": [ "ens224" ]
  },

  # Définition de la BD des bails
  "lease-database": {
    "type": "memfile",
    "persist": true,
    "name": "/var/lib/kea/dhcp4.leases"
  },

  # Configuration de la liste des réseaux 
  "subnet4": [
    {
      "id": 1,
      "subnet": "172.16.10.0/24",
      "pools": [
        {
          "pool": "172.16.10.4 - 172.16.10.254"
        }
      ],
	  "option-data": [
	    {
		  "name": "domain-name-servers",
          "data": "172.16.10.2",
		  "always-send": true
        },
	    {
          "name": "routers",
          "data": "172.16.10.1",
		  "always-send": true
        },
	  ]
    }
  ]
}
}
``` 
{{% notice title="Remarque" style="info"%}}
Il est aussi possible de créer des réservations pour le serveur DNS et Web. Cependant, je vous déconseille de le faire dans le cadre de ce laboratoire pour les raisons suivantes :
+ Lorsqu'on configure des VMs sur *VMWare Workstation*, à chaque fois qu'on ouvre nos VMs dans un nouveau poste de travail/PC, il est possible que les adresses MAC de nos interfaces changent. 
+ Vous serez donc contraints de changer les réservations dans votre DHCP à chaque fois que vous changez de poste...
{{% /notice %}}

5. Démarrer le service et faire en sorte qu'il soit actif à chaque redémarrage de la machine : 
```bash
sudo systemctl enable kea-dhcp4 # automatiquement actif à chaque reboot
sudo systemctl start kea-dhcp4 # démarrage du service
```
6. Vérifier l'état du service (*active* et *enabled*)
```bash
sudo systemctl status kea-dhcp4
```
6. (bis) Si le démarrage du service échoue : 
    + Vérifier les logs pour détecter les erreurs : 
    ```bash
    sudo journalctl -xeu kea-dhcp4
    ```
    + Corriger le fichier de configuration puis redémarrez le service : 
    ```bash
    sudo systemctl restart kea-dhcp4
    ```

#### Partie routeur
1. Activer le "forwarding" des paquets de façon permanente en ajoutant la ligne `net.ipv4.ip_forward=1` au fichier `/etc/sysctl.conf`:
```bash
sudo nano /etc/sysctl.conf # option 1
# ou
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf # option 2

sudo sysctl -p /etc/sysctl.conf # appliquer les changements
```
2. Mettre en place les règles de pare-feu pour accepter les requêtes DHCP et le masquerade NAT (*NAT masquerading*): 
```bash
sudo firewall-cmd --zone=public --add-masquerade --permanent
sudo firewall-cmd --zone=public --add-service=dhcp --permanent
sudo firewall-cmd --reload
```

#### Test
Démarrer la VM client. Vérifier qu'elle reçoit la bonne configuration IP du DHCP  et qu'elle peut faire des requêtes à internet : 
```bash
ip a # vérifier qu'une adresse IP lui a été assignée automatiquement 
ip route # vérification de la bonne passerelle par défaut
nmcli # vérification de l'adresse du serveur DNS  
ping 8.8.8.8 # test d'accès à internet
```
+ Question : À votre avis, pourquoi `ping www.google.com` ne fonctionne pas à cet étape du laboratoire ?

### Serveur DNS
1. Changer son *hostname* (optionnel, seulement pour se repérer) :
```bash
sudo hostnamectl set-hostname dns-server 
```

<!-- 3. Désactiver le pare-feu
```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
``` -->
3. Installer *bind*
```bash
sudo dnf install bind
```
4. Créer le fichier de zone `/var/named/votrenom.lan` pour la zone `votrenom.lan` :
```bash
$TTL 3H
@	IN SOA	@ admin.votrenom.lan. (
					1	; serial
					1D	; refresh
					1H	; retry
					1W	; expire
					3H )	; minimum
@	IN	NS	ns1.votrenom.lan.
ns1	IN	A	172.16.10.2
www	IN	A	172.16.10.3
```
6. Modifier le groupe du fichier à `named`: 
```bash
sudo chgrp named /var/named/votrenom.lan
```
7. Modifier le fichier de configuration `/etc/named.conf` :
    + Modifier listen on à any
    + Modifier allow-query à any
    + Ajouter la déclaration de la zone `votrenom.lan` à la fin du fichier:
    ```bash
    zone "votrenom.lan" {
        type primary;
        file "votrenom.lan";
    };
    ``` 
6. Démarrer le service et faire en sorte qu'il démarre automatiquement à chaque reboot : 
```bash
sudo systemctl enable named # démarre automatiquement à chaque reboot
sudo systemctl start named # démarrage du service
```
7. Vérifier l'état du service (*active* et *enabled*)
```bash
sudo systemctl status named
```
7. (bis) Si le démarrage du service échoue : 
    + Vérifier les logs pour détecter les erreurs : 
    ```bash
    sudo journalctl -xeu named
    ```
    + Corriger le fichier de configuration puis redémarrez le service : 
    ```bash
    sudo systemctl restart named
    ```

8. Configurez le pare-feu pour autoriser les requêtes DNS :
    + Option 1: désactiver simplement le pare-feu :
    ```bash
    sudo systemctl stop firewalld
    sudo sytemctl disable firewalld
    ```
    + Option 2 (meilleure): ajouter une règle pour autoriser les requêtes DNS :
    ```bash
    sudo firewall-cmd --zone=public --add-service=dns --permanent
    sudo firewall-cmd --reload
    ```

#### Test
1. Vérifiez que vous pouvez faire des requêtes au serveur en `localhost`:
```bash
dig @172.16.10.2 ns1.votrenom.lan
dig @172.16.10.2 dhcp.votrenom.lan
dig @172.16.10.2 www.votrenom.lan
dig @172.16.10.2 www.google.com # test de requête récursive
``` 

2. Si tout fonctionne, modifiez le mode de l'adaptateur réseau de votre serveur DNS (*LAN Segment* `172.16.10.0/24`) puis configurez son interface de façon statique : 
```bash
sudo nmcli con mod ens160 ipv4.address 172.16.10.2/24
sudo nmcli con mod ens160 ipv4.gateway 172.16.10.1
sudo nmcli con mod ens160 ipv4.dns 172.16.10.2
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160
```

3. Sur la machine client, vérifiez qu'il est maintenant possible d'envoyer des requêtes au serveur DNS : Faites un test de requête récursive et des tests de requêtes pour la zone pour laquelle le DNS est autoritaire (`votrenom.lan`) : 
```bash
dig @172.16.10.2 www.google.com # test de requête récursive
dig @172.16.10.2 ns1.votrenom.lan
dig @172.16.10.2 dhcp.votrenom.lan
dig @172.16.10.2 www.votrenom.lan
```

4. Ouvrez un navigateur sur votre machine client et vérifiez que votre machine est maintenant capable de faire la résolution de nom (www.wikipedia.org par exemple).

### Serveur Web
1. Changer son *hostname* (optionnel, seulement pour se repérer) :
```bash
sudo hostnamectl set-hostname web-server 
```
<!-- 2. Configurer son interface de façon statique :
```bash
sudo nmcli con mod ens160 ipv4.address 172.16.10.3/24
sudo nmcli con mod ens160 ipv4.gateway 172.16.10.1
sudo nmcli con mod ens160 ipv4.dns 172.16.10.2
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160
``` -->
3. Installer nginx et vérifier l'état du service :
```bash
sudo apt update && sudo apt install nginx -y
sudo systemctl status nginx
```
4. Créez un répertoire temporaire pour y copier le projet React :
```bash
mkdir -p /tmp/react-project
```
5. Sur votre poste de travail (Windows) clonez un projet React puis créez une version exportable du projet. Exemple
```bash
git clone https://github.com/gbenachour/e-portfolio.git
cd e-portfolio
npm install
npm run build
```
6. Copiez le contenu du répertoire `dist/` dans votre VM Ubuntu :
```bash
sudo scp -r ./dist/* student@<adresse IP du serveur web>:/tmp/react-project
```
7. Déplacez le projet dans le répertoire `/var/www/html` :
```bash
sudo mv /tmp/react-project/* /var/www/html/
```
<!-- 7. (bis) changez les permissions  -->
9. Dans votre poste de travail (Windows), ouvrez un navigateur et tapez l'adresse IP de votre serveur Web pour vérifier qu'il sert bien la page React.

10. Si tout fonctionne, modifiez le mode de l'adaptateur réseau de votre serveur Web (*LAN Segment* `172.16.10.0/24`) puis configurez son interface de façon statique : 
```bash
sudo nmcli con mod ens160 ipv4.address 172.16.10.3/24
sudo nmcli con mod ens160 ipv4.gateway 172.16.10.1
sudo nmcli con mod ens160 ipv4.dns 172.16.10.2
sudo nmcli con mod ens160 ipv4.method manual
sudo nmcli con down ens160
sudo nmcli con up ens160
```

#### Test
1. Dans la VM client, vérifiez que vous pouvez accéder à la page web via un navigateur (en tapant l'adresse IP du serveur Web).
2. Vérifiez que vous pouvez accéder à votre site en tapant `www.votrenom.lan` à la place de l'adresse IP.
