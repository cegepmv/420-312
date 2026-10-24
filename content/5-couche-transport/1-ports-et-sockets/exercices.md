+++
title = "Exercices"
slug = 'exercices'
weight = "511"
draft = false
+++
----------------

### 1. Classer des ports

Pour chaque port, indiquez sa plage (réservé, enregistré ou dynamique) et, s'il y a lieu, le service associé :

```text
22   53   443   3389   51544   8080   67   60123
```

<!-- {{% expand title="Solution" %}}
| Port | Plage | Service |
|------|-------|---------|
| `22` | Réservé | SSH |
| `53` | Réservé | DNS |
| `443` | Réservé | HTTPS |
| `3389` | Enregistré | RDP |
| `51544` | Dynamique | Port éphémère d'un client |
| `8080` | Enregistré | Souvent utilisé comme port HTTP alternatif |
| `67` | Réservé | DHCP (serveur) |
| `60123` | Dynamique | Port éphémère d'un client |
{{% /expand %}} -->

### 2. Lire une sortie de `ss`

La commande `ss -tan` exécutée sur une machine Linux affiche :

```text
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port
LISTEN  0       128            0.0.0.0:22            0.0.0.0:*
LISTEN  0       511            0.0.0.0:80            0.0.0.0:*
ESTAB   0       0             10.0.0.5:22          10.0.0.42:51544
ESTAB   0       0             10.0.0.5:80          10.0.0.42:51560
ESTAB   0       0             10.0.0.5:80          10.0.0.77:49170
ESTAB   0       0             10.0.0.5:39812   93.184.216.34:443
```

1. Quels services cette machine offre-t-elle?
2. Combien de conversations sont établies?
3. Pour chaque conversation, la machine `10.0.0.5` joue-t-elle le rôle de client ou de serveur? Justifiez.
4. Les lignes 4 et 5 utilisent le même socket local `10.0.0.5:80`. Comment la machine distingue-t-elle ces deux conversations?

<!-- {{% expand title="Solution" %}}
1. SSH (port `22`) et HTTP (port `80`) : ce sont les ports à l'état `LISTEN`.
2. Quatre conversations (lignes `ESTAB`).
3. Elle est **serveur** pour les trois premières : le port local est un port réservé (`22`, `80`) et le port distant est éphémère. Elle est **client** pour la dernière : son port local `39812` est éphémère et le port distant `443` est celui d'un serveur HTTPS.
4. Par le socket distant : `10.0.0.42:51560` et `10.0.0.77:49170` sont différents, donc les deux paires de sockets sont différentes.
{{% /expand %}} -->
