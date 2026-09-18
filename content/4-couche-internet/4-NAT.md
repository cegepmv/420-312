+++
pre = '<b>4. </b>'
title = "NAT"
weight = "440"
draft=true
+++
# 3.21 - Limitation d'IPv4

IPv4 utilise des adresses de **32 bits**.

Cela représente :

$$2^{32} = 4\,294\,967\,296$$

valeurs d'adresses possibles, soit environ **4,3 milliards**.

Toutes ces adresses ne sont cependant pas disponibles comme adresses publiques attribuables à des appareils sur Internet.

Avec l'augmentation du nombre d'ordinateurs, de téléphones, de serveurs et d'autres appareils connectés, les adresses IPv4 publiques sont devenues une ressource limitée.

Plusieurs mécanismes permettent de conserver l'utilisation d'IPv4, notamment le **NAT**.

À long terme, **IPv6** fournit un espace d'adressage beaucoup plus vaste.

---

# 3.22 - NAT

Le **NAT (*Network Address Translation*)** permet de traduire des adresses IP lorsqu'un paquet traverse un routeur ou un dispositif de traduction.

Dans les réseaux résidentiels et de nombreuses organisations, le NAT permet notamment à plusieurs appareils utilisant des adresses IPv4 privées de partager une ou plusieurs adresses IPv4 publiques.

Exemple :

```text
Réseau privé

192.168.1.10 ─┐
192.168.1.11 ─┼──► Routeur NAT ───► Internet
192.168.1.12 ─┘       │
                       │
                 IP publique
                 203.0.113.10
```

Les appareils internes utilisent des adresses privées :

```text
192.168.1.10
192.168.1.11
192.168.1.12
```

Le routeur possède une adresse publique :

```text
203.0.113.10
```

Lorsqu'un appareil interne communique avec Internet, le routeur peut traduire l'adresse IP source du paquet.

Le NAT n'implique pas nécessairement une traduction de ports. Plusieurs formes de NAT existent.

---

## PAT

Dans les réseaux domestiques et de nombreuses infrastructures, le NAT est généralement associé au **PAT (*Port Address Translation*)**.

Le PAT utilise également les numéros de port afin de permettre à plusieurs connexions internes de partager une même adresse IPv4 publique.

Exemple simplifié :

```text
192.168.1.10:51500 ──┐
                      │
192.168.1.11:51501 ──┼──► Routeur NAT/PAT
                      │       │
192.168.1.12:51502 ──┘        │
                               ▼
                        203.0.113.10
```

Le routeur conserve une table de traduction permettant d'associer les connexions externes aux connexions internes correspondantes.

---

## Connexions entrantes et redirection de port

Une connexion provenant d'Internet vers une machine privée n'est pas automatiquement dirigée vers cette machine.

Le routeur peut être configuré pour effectuer une **redirection de port (*port forwarding*)**.

Exemple :

```text
Internet
    │
    │ TCP 443
    ▼
Routeur
203.0.113.10:443
    │
    │ redirection
    ▼
Serveur
192.168.1.50:443
```

Le routeur transmet alors les connexions reçues sur son port `443` vers le serveur interne.

> Le NAT peut avoir des effets sur l'accessibilité depuis Internet, mais il ne doit pas être considéré comme un mécanisme de sécurité à part entière. La sécurité repose notamment sur les pare-feu, les contrôles d'accès et la configuration des services.

---

# 3.23 - Adresses IPv4 privées

Les principales plages privées IPv4 définies par RFC 1918 sont :

| Plage                                | Préfixe           |
| ------------------------------------- | ------------------ |
| `10.0.0.0` à `10.255.255.255`         | `10.0.0.0/8`        |
| `172.16.0.0` à `172.31.255.255`       | `172.16.0.0/12`     |
| `192.168.0.0` à `192.168.255.255`     | `192.168.0.0/16`    |

Ces adresses sont destinées aux réseaux privés et ne sont pas annoncées comme des adresses IPv4 publiques sur Internet.

---

# 3.24 - IPv4, routage et couches inférieures

Lorsqu'une application communique avec un serveur distant, les différentes couches travaillent ensemble.

Exemple :

```text
Application
    │
    │ données
    ▼
Transport
    │
    │ segment TCP/UDP
    ▼
Internet
    │
    │ paquet IP
    │ source = 192.168.1.10
    │ destination = 8.8.8.8
    ▼
Liaison de données
    │
    │ trame Ethernet
    │ MAC destination = passerelle
    ▼
Physique
    │
    │ bits
    ▼
Support réseau
```

Le paquet IP peut traverser plusieurs réseaux et plusieurs routeurs.

Les informations de couche 2 sont généralement **recréées à chaque liaison**.

Par exemple :

```text
Hôte A             Routeur              Routeur             Serveur
   │                   │                   │                   │
   │ Trame Ethernet    │                   │                   │
   ├──────────────────►│                   │                   │
   │                   │ Nouvelle trame    │                   │
   │                   ├──────────────────►│                   │
   │                   │                   │ Nouvelle trame    │
   │                   │                   ├──────────────────►│
```

Le paquet IP, lui, continue son chemin à travers les différents routeurs.

> Le chapitre précédent a détaillé Ethernet et ARP. Ici, l'idée essentielle est de comprendre que la couche Internet fournit l'adressage logique et l'acheminement, tandis que la couche de liaison assure la transmission sur chaque liaison.

---