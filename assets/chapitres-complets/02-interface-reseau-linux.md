# 2. Interface réseau Linux

Nous avons vu que les communications réseau utilisent notamment deux types d'adresses :

+ **Adresse MAC (couche 2) :** utilisée sur le réseau local par les switches pour acheminer les trames.
+ **Adresse IP (couche 3) :** utilisée par les hôtes et les routeurs pour acheminer les paquets entre les réseaux.
+ **ARP :** permet de trouver l'adresse MAC associée à une adresse IPv4 sur le réseau local.

Nous allons maintenant voir comment retrouver et configurer ces informations sur une machine Linux.

## Interface réseau

Une **interface réseau** est un point de connexion qui permet à une machine de communiquer avec un réseau.

Une interface peut être :

+ **physique :** une carte réseau Ethernet, une carte Wi-Fi, etc. ;
+ **virtuelle :** une interface créée par le système d'exploitation ou par une technologie de virtualisation ou de réseau.

Une interface réseau possède généralement :

+ une **adresse MAC** ;
+ une ou plusieurs **adresses IP** lorsqu'elle est configurée ;
+ un état : **UP** ou **DOWN** ;
+ éventuellement une configuration de routage associée.

Par exemple, une machine virtuelle Linux peut posséder les interfaces suivantes :
```bash
ens160
ens224
lo
```
`ens160` et `ens224` peuvent représenter des cartes réseau Ethernet virtuelles, tandis que `lo` est l'interface **loopback**.

### L'interface loopback

L'interface `lo` est une interface virtuelle particulière qui permet à une machine de communiquer avec elle-même.

Son adresse IPv4 est généralement :
```bash
127.0.0.1
```
On peut donc utiliser :
```bash
ping 127.0.0.1
```
ou :
```bash
ping localhost
```
Cette communication ne quitte jamais la machine et ne passe donc ni par un switch ni par un routeur.

### Lire la configuration d'une interface

La commande moderne utilisée pour consulter les interfaces réseau sous Linux est :
```bash
ip address
```
ou, plus simplement :
```bash
ip a
```
**Exemple :**
```bash
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP>
    link/ether 00:0c:29:12:34:56
    inet 192.168.230.10/24
```

On peut y retrouver plusieurs informations importantes.

+ **Nom de l'interface :** `ens160`
+ **Adresse MAC :** la ligne `link/ether 00:0c:29:12:34:56` correspond à l'adresse utilisée à la couche 2.
+ **Adresse IP :** `inet 192.168.230.10/24` indique que l'interface possède l'adresse IPv4, soit `192.168.230.10` avec un préfixe `/24`.
{{%notice style="info" title=""%}}
Le `/24` correspond au masque :`255.255.255.0`. Cette information permet notamment au système de déterminer quelles adresses IP appartiennent au même réseau local (nous verrons cela plus en détail dans un chapitre ultérieur).
{{%/notice%}}
### État d'une interface

Une interface peut être **activée** ou **désactivée**.

Pour afficher uniquement les interfaces et leur état :
```bash
ip link
```
Exemple :
```bash
2: ens160: <BROADCAST,MULTICAST,UP,LOWER_UP>
```
La présence de **UP** indique que l'interface est activée.

On peut également utiliser la commande suivante pour examiner une interface particulière :
```bash
ip link show ens160
```

Pour activer une interface :
```bash
ip link set ens160 up
```
Pour la désactiver :
```bash
ip link set ens160 down
```
{{%notice style="warning" title="Attention"%}}
Ces commandes modifient l'état courant de l'interface, mais ne constituent pas nécessairement une configuration permanente.
{{%/notice%}}

## Configuration réseau

Une machine Linux doit connaître différentes informations pour communiquer correctement :

+ Adresse IP → 192.168.230.10/24   
+ Passerelle → 192.168.230.2       
+ Serveur DNS → 8.8.8.8             

Ces informations ont des rôles différents.

+ **Adresse IP :** Identifie la machine sur le réseau IP.
+ **Préfixe / masque :** Permet de déterminer quelle partie de l'adresse représente le réseau et quelle partie représente l'hôte.
+ **Passerelle par défaut :** La passerelle est la "porte de sortie" du réseau local (généralement l'adresse du routeur), utilisée lorsqu'une destination n'est pas située sur le réseau local.
+ **Serveur DNS :** Un serveur DNS permet notamment de transformer un nom de domaine en adresse IP. Par exemple : `www.google.com` -> `142.250.x.x`

### Afficher les routes

La configuration IP ne suffit pas : la machine doit également savoir **où envoyer les paquets**.

Pour afficher la table de routage :
```bash
ip route
```
Exemple :
```bash
default via 192.168.230.2 dev ens160
192.168.230.0/24 dev ens160 proto kernel scope link src 192.168.230.10
```
La ligne :
```bash
default via 192.168.230.2 dev ens160
```
indique que la passerelle par défaut est `192.168.230.2`

La ligne :
```bash
192.168.230.0/24 dev ens160
```
indique que le réseau `192.168.230.0/24` est directement accessible par l'interface `ens160`.

{{%notice style="tip" title="À retenir"%}}
L'adresse IP indique qui je suis sur le réseau, tandis que la table de routage indique où envoyer les paquets.
{{%/notice%}}

## ARP sous Linux

Dans le chapitre précédent, nous avons vu qu'ARP permet d'associer une adresse IPv4 à une adresse MAC.

Sous Linux, on peut consulter le cache ARP avec :
```bash
ip neigh
```
Exemple :
```bash
192.168.230.2 dev ens160 lladdr 00:50:56:AA:BB:CC REACHABLE
```
On peut y lire :

`192.168.230.2` -> `00:50:56:AA:BB:CC`

Linux sait donc que l'adresse IP `192.168.230.2` correspond à cette adresse MAC.

# 2.1. La commande ip

La commande `ip` est l'outil moderne utilisé sous Linux pour consulter et manipuler la configuration réseau.

Elle remplace progressivement les anciens outils comme `ifconfig` et `route`.

Quelques commandes importantes :

| Commande | Utilité |
|----------|---------|
|`ip a`|	 Afficher les adresses des interfaces|
|`ip link`|	Afficher les interfaces et leur état|
|`ip route`|	Afficher la table de routage|
|`ip neigh`| Afficher le cache ARP|
|`ip a show ens160`|	Afficher une interface précise|
|`ip link set ens160 up`| Activer une interface|
|`ip link set ens160 down`|	 Désactiver une interface|


### Modifier temporairement une adresse IP

Il est possible d'ajouter une adresse IP à une interface avec :
```bash
ip addr add 192.168.230.132/24 dev ens160
```
On peut vérifier le résultat avec :
```bash
ip a show ens160
```
Il est possible d'avoir plusieurs adresses IP sur une même interface :
```bash
ens160
 ├── 192.168.230.10/24
 └── 192.168.230.132/24
```
Pour supprimer une adresse :
```bash
ip addr del 192.168.230.132/24 dev ens160
```
{{%notice style="note" title="Important"%}}
Les modifications effectuées directement avec `ip` sont généralement **temporaires**. Elles peuvent disparaître lors du redémarrage de la machine ou lorsqu'un gestionnaire réseau réapplique sa configuration.
{{%/notice%}}

La commande `ip` est donc particulièrement utile pour :
+ observer la configuration ;
+ effectuer des tests ;
+ modifier temporairement une configuration ;
+ diagnostiquer des problèmes réseau.

Pour une configuration permanente, on utilise le système de configuration réseau de la distribution.

### Ajouter une passerelle temporairement

La passerelle par défaut peut également être configurée avec `ip` :
```bash
ip route add default via 192.168.230.2
```
On peut ensuite vérifier la configuration :
```bash
ip route
```
On devrait retrouver :
```bash
default via 192.168.230.2
```
Comme pour l'adresse IP, cette modification est temporaire.

## ifconfig : l'ancien outil

`ifconfig` est un ancien outil permettant notamment d'afficher et de modifier la configuration des interfaces réseau.

Pour afficher les interfaces :
```bash
ifconfig
```
Pour afficher une interface précise :
```bash
ifconfig ens160
```
Pour désactiver une interface :
```bash
ifconfig ens160 down
```
Pour l'activer :
```bash
ifconfig ens160 up
```
Cependant, `ifconfig` est aujourd'hui considéré comme un outil ancien et n'est généralement plus installé par défaut sur les distributions Linux modernes.

On privilégiera :
```bash
ip a
```
et :
```bash
ip link
```

# 2.2. NetworkManager et nmcli

De nombreuses distributions Linux utilisent **NetworkManager** pour gérer les connexions réseau.

`nmcli` est l'outil en ligne de commande permettant d'interagir avec **NetworkManager**.

Pour afficher les interfaces :
```bash
nmcli device
```
Exemple :
```bash
DEVICE   TYPE      STATE        CONNECTION
ens160   ethernet  connected    ens160
ens224   ethernet  disconnected  --
lo       loopback  connected     lo
```

Il faut faire attention à une distinction importante :

+ **interface / device :** l'interface réseau réelle, par exemple `ens160` ;
+ **connection :** la configuration que *NetworkManager* applique à cette interface.

Dans l'exemple ci-dessus : 
+ `ens160` possède une connection appelée `ens160` ;
+ `ens224` existe bien comme **device**, mais la colonne **CONNECTION** contient `--` : aucune connection *NetworkManager* ne lui est actuellement associée.

Pour afficher les connexions :
```bash
nmcli connection show # ou nmcli con show
```
Pour afficher une connexion particulière :
```bash
nmcli connection show ens160
```

### Créer une connection pour un device

Si une interface existe mais qu'elle n'est pas encore gérée par une connection *NetworkManager*, il faut d'abord créer cette connection.

La commande générale est :
```bash
sudo nmcli connection add con-name <nom-connection> ifname <device> type ethernet
```

Par exemple :
```bash
sudo nmcli connection add con-name my-conn ifname eth0 type ethernet
```

Cette commande crée une connection appelée `my-conn` et l'associe au device `eth0`.

On peut ensuite vérifier :
```bash
nmcli con show
```
et :
```bash
nmcli device
```
On devrait maintenant voir la connection associée au device.

### Activer une connection
Après avoir créé une connection, on peut l'activer avec :

```bash
sudo nmcli connection up my-conn
```

On peut ensuite vérifier l'état :

```bash
nmcli device
```

ou :

```bash
nmcli connection show
```

### Configurer une adresse IP statique avec nmcli

Supposons que nous souhaitons configurer :

+ **Adresse IP :** `192.168.230.10/24`
+ **Passerelle :** `192.168.230.2`
+ **DNS        :** `8.8.8.8`
+ **Interface  :** `ens160`

On peut modifier la connexion avec :
```bash
nmcli con mod ens160 ipv4.addresses 192.168.230.10/24
nmcli con mod ens160 ipv4.gateway 192.168.230.2
nmcli con mod ens160 ipv4.dns 8.8.8.8
nmcli con mod ens160 ipv4.method manual
```
Puis réactiver la connexion :
```bash
nmcli con down ens160
nmcli con up ens160
```
On peut ensuite vérifier :
```bash
ip a
ip route
```
et :
```bash
nmcli con show ens160
```
### Revenir à DHCP avec nmcli

Pour demander à **NetworkManager** de récupérer automatiquement l'adresse IP :

```bash
nmcli con mod ens160 ipv4.method auto
```

On peut également supprimer les paramètres statiques :
```bash
nmcli con mod ens160 ipv4.addresses ""
nmcli con mod ens160 ipv4.gateway ""
nmcli con mod ens160 ipv4.dns ""
```
Puis :
```bash
nmcli con down ens160
nmcli con up ens160
```
L'interface devrait maintenant obtenir sa configuration automatiquement auprès d'un serveur **DHCP**.

# 2.3. Netplan

Sur **Ubuntu**, une autre méthode courante de configuration réseau est **Netplan**.

**Netplan** permet de définir la configuration réseau dans des **fichiers YAML**. Cette configuration est ensuite appliquée par le système de gestion réseau utilisé par Ubuntu.

Les fichiers de configuration Netplan se trouvent généralement dans :
```bash
/etc/netplan/
```
On peut par exemple retrouver :
```bash
/etc/netplan/00-installer-config.yaml
```
ou :
```bash
/etc/netplan/50-cloud-init.yaml
```
Le nom exact du fichier peut varier selon l'installation.

### Configuration statique avec Netplan

Supposons que nous voulons configurer l'interface `ens160` avec :
+ **Adresse IP :** `192.168.230.10/24`
+ **Passerelle :** `192.168.230.2`
+ **DNS        :** `8.8.8.8`

Un fichier Netplan pourrait être :
```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 192.168.230.10/24
      routes:
        - to: default
          via: 192.168.230.2
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
**Explication**
+ `network:` Indique que nous configurons le réseau.
+ `version: 2` Indique la version de la syntaxe *Netplan*.
+ `ethernets:` Indique que nous configurons une interface **Ethernet**.
+ `ens160:` Correspond au nom de l'interface réseau.

```yaml
addresses:
  - 192.168.230.10/24
```
Définit l'adresse IP et le préfixe.

```yaml
routes:
  - to: default
    via: 192.168.230.2
```
Définit la route par défaut et donc la passerelle.

```yaml
nameservers:
  addresses:
    - 8.8.8.8
    - 8.8.4.4
```
Définit les serveurs DNS utilisés par la machine.

### Appliquer une configuration Netplan

Après avoir modifié le fichier YAML, on peut tester et appliquer la configuration avec :
```bash
sudo netplan try
```
Cette commande applique temporairement la configuration et permet de la confirmer.

On peut également appliquer directement la configuration avec :
```bash
sudo netplan apply
```
Puis vérifier le résultat :
```bash
ip a
ip route
ip neigh
```
et tester la connectivité :
```bash
ping 192.168.230.2
```
### Netplan avec DHCP

Netplan peut également configurer une interface pour utiliser DHCP.

Exemple :
```yaml
network:
  version: 2
  ethernets:
    ens160:
      dhcp4: true
```
Dans ce cas, l'adresse IP, la passerelle et généralement les serveurs DNS sont obtenus automatiquement auprès du serveur DHCP.


# 2.4. Fichiers réseau Linux

## Le fichier /etc/resolv.conf

Le fichier `/etc/resolv.conf` contient des informations utilisées pour la résolution DNS.

On peut notamment y retrouver :
```bash
nameserver 8.8.8.8
nameserver 8.8.4.4
```
Chaque directive `nameserver` indique un serveur DNS à utiliser.

On peut également retrouver :
```bash
search linux.local
```
qui définit un ou plusieurs domaines de recherche.

{{%notice style="warning" title="Attention"%}}
Sur les distributions Linux modernes, `/etc/resolv.conf` peut être généré automatiquement par **NetworkManager**, **systemd-resolved** ou un autre gestionnaire. Il ne faut donc pas nécessairement modifier ce fichier directement.

Il est généralement préférable de configurer les DNS dans le système de gestion réseau utilisé par la distribution, par exemple **Netplan** ou **NetworkManager**.
{{%/notice%}}


## Le fichier /etc/hosts

Le fichier `/etc/hosts` permet d'associer manuellement des noms d'hôtes à des adresses IP.

Sa syntaxe est : `<adresse IP> <nom>`

Par exemple :
```bash
192.168.230.122 www.example.local
192.168.230.123 serveur1
```
La machine pourra alors résoudre `serveur1` vers `192.168.230.123`

Ce fichier est particulièrement pratique dans un laboratoire ou un petit réseau lorsque l'on ne dispose pas encore d'un serveur DNS.
