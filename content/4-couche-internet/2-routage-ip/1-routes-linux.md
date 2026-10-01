+++
title = "Configurer des routes sur Linux"
weight = "421"
draft = false
+++
-----------

## Configurer une route avec `ip`

La commande `ip` permet de consulter et de modifier la configuration réseau sous Linux.

###### Ajouter une route

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254
```

On peut préciser l'interface :

```bash
sudo ip route add 10.10.20.0/24 via 192.168.1.254 dev ens160
```

La passerelle `192.168.1.254` doit elle-même être accessible par l'interface utilisée.

###### Ajouter une route par défaut

```bash
sudo ip route add default via 192.168.1.1
```

###### Supprimer une route

```bash
sudo ip route del 10.10.20.0/24
```

###### Consulter les routes

```bash
ip route
```

###### Déterminer la route utilisée pour une destination

```bash
ip route get 8.8.8.8
```

{{%notice style="note" title="Rappel"%}}
Les modifications effectuées directement avec `ip` modifient l'état réseau courant. Elles ne sont généralement pas persistantes après un redémarrage. Une configuration persistante doit être réalisée avec le gestionnaire réseau utilisé par la distribution (*NetworkManager*, *Netplan* ou autre).
{{%/notice%}}

## Configurer des routes avec nmcli

Sur les distributions utilisant **NetworkManager**, `nmcli` permet de gérer les connexions réseau.

Pour ajouter une route :

```bash
sudo nmcli connection modify ens160 +ipv4.routes "10.10.20.0/24 192.168.1.254"
```

Réactiver la connexion :

```bash
sudo nmcli connection down ens160
sudo nmcli connection up ens160
```

Vérifier la configuration :

```bash
nmcli connection show ens160
ip route
```

Pour supprimer la route :

```bash
sudo nmcli connection modify ens160 -ipv4.routes "10.10.20.0/24 192.168.1.254"
```

## Configurer des routes avec Netplan

Sur Ubuntu, **Netplan** permet de définir la configuration réseau dans des fichiers YAML situés dans :

```text
/etc/netplan/
```

Exemple :

```yaml
network:
  version: 2
  ethernets:
    ens160:
      addresses:
        - 192.168.1.10/24
      routes:
        - to: default
          via: 192.168.1.1
        - to: 10.10.20.0/24
          via: 192.168.1.254
      nameservers:
        addresses:
          - 1.1.1.1
          - 8.8.8.8
```

La route :

```yaml
- to: 10.10.20.0/24
  via: 192.168.1.254
```

indique que le réseau `10.10.20.0/24` est accessible via `192.168.1.254`.

Pour tester temporairement la configuration :

```bash
sudo netplan try
```

Pour appliquer la configuration :

```bash
sudo netplan apply
```
