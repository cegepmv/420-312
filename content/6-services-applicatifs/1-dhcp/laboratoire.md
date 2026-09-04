+++
title = "Ateliers"
weight = "511"
+++
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

