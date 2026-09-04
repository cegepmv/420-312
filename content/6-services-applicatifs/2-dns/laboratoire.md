+++
title = "Ateliers"
weight = "521"
+++
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