+++
title = 'Netplan'
draft = false
weight = "153"
+++
---------------------

Sur **Ubuntu**, une autre méthode courante de configuration réseau est **Netplan**.

Netplan permet de définir la configuration réseau dans des **fichiers YAML**. Cette configuration est ensuite appliquée par le système de gestion réseau utilisé par Ubuntu.

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
`network:` - Indique que nous configurons le réseau.
`version: 2` - Indique la version de la syntaxe Netplan.
`ethernets:` - Indique que nous configurons une interface Ethernet.
`ens160:` - Correspond au nom de l'interface réseau.

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

