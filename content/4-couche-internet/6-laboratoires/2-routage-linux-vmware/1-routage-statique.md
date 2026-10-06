+++
slug = '1-routes-statiques'
title = "1- Routage statique"
weight = "463"
draft = false
+++
-------------

### 1. Configurer CLIENT1 avec nmcli
Configurez l'interface de **CLIENT1** avec `nmcli`

{{%notice style="note" title="Adresse IP et CIDR seulement !"%}}
Ne configurez pas sa passerelle par défaut et son DNS, seulement son adresse IP/préfixe :
```bash
sudo nmcli con mod "CONNECTION" \
    ipv4.method manual \
    ipv4.addresses 10.20.10.10/24
```
Remplacez `CONNECTION` par le nom réel de votre connexion. 

Si aucune connection n'est assignée à l'interface, créez-en une.

{{%/notice%}}

Vérifiez :
```bash
ip a
```

<!-- 
Commencez par identifier la connexion :
```bash
nmcli con show
```
Vous devriez obtenir une connexion associée à l'interface réseau. 
-->

### 2. Configurer CLIENT2 avec Netplan
Configurez l'interface de **CLIENT2** avec `Netplan`.

{{%notice style="note" title="Adresse IP et CIDR seulement !"%}}
Ne configurez pas sa passerelle par défaut et son DNS, seulement son adresse IP/préfixe :
```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 10.20.20.10/24
```
Adaptez `ens160` au nom réel de votre interface
{{%/notice%}}

Vérifiez :
```bash
ip a
```

### 3. Configurer le routeur Linux

Sur **ROUTEUR** configurez les interfaces avec `nmcli` :
1. Interface du *LAN-1* : `10.20.10.1/24` 
2. Interface du *LAN-2* : `10.20.20.1/24`

Vérifiez :
```bash
ip a
```
Puis :
```bash
ip route
```

### 4. Premiers tests

Depuis **CLIENT1** :
```bash
ping 10.20.10.1
```
Depuis **CLIENT2** :
```bash
ping 10.20.20.1
```
Ces tests doivent fonctionner.

Testez ensuite à partir de **CLIENT1** :
```bash
ping 10.20.20.10
```
Le test devrait échouer.

{{%notice style="info" title="Pourquoi ?"%}}
Les deux machines sont sur des réseaux différents et, pour le moment, aucune route permettant à **CLIENT1** d'atteindre `10.20.20.0/24` n'a été configurée sur **CLIENT1**.
{{%/notice%}}

### 5. Ajouter une route statique avec ip
En utilisant la commande `ip`:
1. Sur **CLIENT1**, ajoutez une route statique pour le réseau *LAN-2*. 
2. Sur **CLIENT2**, ajoutez une route statique pour le réseau *LAN-1*. 
3. Sur les deux machines, vérifiez avec `ip route`.
4. À partir de **CLIENT1** testez :
```bash
ping 10.20.20.10
```
Le test peut toutefois encore échouer.
{{%notice style="info" title="Pourquoi ?"%}}
La route permet maintenant à **CLIENT1** d'envoyer le paquet au routeur, mais le routeur doit également être autorisé à transférer les paquets IPv4 entre ses interfaces.
{{%/notice%}}

### 6. Activer le transfert (*forwarding*) IPv4

Sur **ROUTEUR** :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Si la valeur retournée est `0`, le transfert IPv4 est désactivé.

Activez-le temporairement :
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Vérifiez :
```bash
cat /proc/sys/net/ipv4/ip_forward
```
Vous devriez obtenir : `1`

### 7. Tester le routage
```text
CLIENT1 : ping 10.20.20.10
CLIENT2 : ping 10.20.10.10
```
Les deux communications devraient maintenant fonctionner.
<!-- 
{{%notice style="info" title="Chemin du paquet"%}}
Le chemin d'un paquet de **CLIENT1** vers **CLIENT2** est :
```text
CLIENT1
   |
   | 192.168.10.10
   v
ROUTEUR
192.168.10.1
   |
   | 192.168.20.1
   v
CLIENT2
```
Le routeur reçoit le paquet sur `ens160` et le transmet sur `ens224`.
{{%/notice%}} -->

### 8. Observer le chemin avec traceroute

Depuis **CLIENT1** :
```bash
traceroute 10.20.20.10
```
Le résultat devrait montrer au minimum le routeur comme intermédiaire.

{{%notice style="tip" title="traceroute non installé ?"%}}
Si `traceroute` n'est pas installé, ajoutez temporairement un adaptateur réseau en **NAT** ou **Bridged** (pour avoir une connexion internet), puis installez :
```bash
sudo apt install traceroute # Debian/Ubuntu
sudo dnf install traceroute # Rocky/CentOS/RHEL
```
{{%/notice%}}

### 9. Configuration permanente 
La commande `ip route add` modifie la configuration courante du noyau. Elle est donc utile pour expérimenter, mais la route peut disparaître après un redémarrage.

De même lorsqu'on active le transfert (*forwarding*) des paquets IPv4 sur le routeur avec la commande : 
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Il ne sera actif que temporairement.

#### Ajouter les routes avec les outils de configuration

1. Supprimez les routes configurées avec `ip`
2. Configurez les routes de manière persistante en utilisant :
    + `nmcli` sur **CLIENT1** 
    + `Netplan` sur **CLIENT2**.
3. Vérifiez votre configuration avec `ip route`.

#### Activer le routage IPv4

Pour activer le *forwarding* IPv4 de façon permanente, ajoutez au fichier `/etc/sysctl.conf` du routeur la ligne : 
```text
net.ipv4.ip_forward=1
```
Ensuite, pour activer les changements : 
```bash
sudo sysctl -p /etc/sysctl.conf
```

### 10. Tests finaux
Depuis **CLIENT1** :
```bash
ping 10.20.10.1
ping 10.20.20.1
ping 10.20.20.10
```

Depuis **CLIENT2** :
```bash
ping 10.20.20.1
ping 10.20.10.1
ping 10.20.10.10
```

