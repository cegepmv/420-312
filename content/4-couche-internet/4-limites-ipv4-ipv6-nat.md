+++
pre = '<b>4. </b>'
title = "Limitations d'IPv4: IPv6 et NAT"
weight = "440"
draft = false
+++
----------

## Limitation d'IPv4

IPv4 utilise des adresses de **32 bits**.

Cela représente : `2^32 = 4,294,967,296`
valeurs d'adresses possibles, soit environ **4,3 milliards**.

Toutes ces adresses ne sont cependant pas disponibles comme adresses publiques attribuables à des appareils sur Internet.

Avec l'augmentation du nombre d'ordinateurs, de téléphones, de serveurs et d'autres appareils connectés, les adresses IPv4 publiques sont devenues une ressource limitée.

<!-- Plusieurs mécanismes permettent de conserver l'utilisation d'IPv4, notamment le **NAT**.

À long terme, **IPv6** fournit un espace d'adressage beaucoup plus vaste. -->

## IPv6 : une réponse aux limites d'IPv4

<!-- La principale limite d'IPv4 est la taille de son espace d'adressage. Avec des adresses de 32 bits, IPv4 offre environ 4,3 milliards de valeurs d'adresses possibles. Or, le nombre d'appareils connectés à Internet a considérablement augmenté. -->

Une solution fondamentale au problème de manque d'adresses publique est **IPv6 (*Internet Protocol version 6*)**.

### Un espace d'adressage beaucoup plus vaste

IPv6 utilise des adresses de **128 bits**, contre 32 bits pour IPv4.

Cela représente :
```text
IPv4 : 2^32 ≈ 4,3 milliards d'adresses

IPv6 : 2^128 ≈ 340 undecillions d'adresses (340 avec 36 zéros !!)
```

L'espace d'adressage d'IPv6 est donc suffisamment vaste pour attribuer des adresses uniques à un très grand nombre d'appareils.

{{%notice style="info" title=" "%}}
IPv6 ne constitue pas seulement une augmentation du nombre d'adresses. Il a également été conçu avec différentes améliorations au niveau du protocole IP, notamment une simplification de l'en-tête et la possibilité d'utiliser la **configuration automatique des adresses**.
{{%/notice%}}

### Pourquoi n'a-t-il pas remplacé IPv4 ?

On pourrait penser qu'il suffirait de remplacer progressivement IPv4 par IPv6. En pratique, la transition est plus complexe.

Internet repose sur un très grand nombre de réseaux, de routeurs, de serveurs et d'appareils qui doivent pouvoir communiquer entre eux. IPv4 et IPv6 ne sont pas directement compatibles : un appareil utilisant uniquement IPv4 ne peut pas simplement communiquer avec un appareil utilisant uniquement IPv6.

La transition vers IPv6 nécessite donc la coexistence des deux protocoles et l'utilisation de différentes techniques de transition.

De plus, certaines solutions ont permis de prolonger l'utilisation d'IPv4, notamment le **NAT**.

<!-- Limitation d'IPv4
       │
       ├──────────────► IPv6
       │                 │
       │                 └─ espace d'adressage beaucoup plus vaste
       │
       └──────────────► NAT / PAT
                         │
                         └─ permettent de partager des adresses IPv4 publiques

Le NAT ne résout donc pas la limitation fondamentale du nombre d'adresses IPv4. Il permet surtout de réduire le nombre d'adresses publiques nécessaires en permettant à plusieurs appareils utilisant des adresses privées de partager une même adresse publique.

IPv6 représente la solution à long terme au problème d'épuisement de l'espace d'adressage IPv4, tandis que le NAT et le PAT ont contribué à prolonger l'utilisation d'IPv4.

À retenir : IPv6 apporte un espace d'adressage immense, mais la transition depuis IPv4 est progressive. Les mécanismes comme le NAT et le PAT ont permis de continuer à utiliser efficacement IPv4 malgré la limitation de son espace d'adressage. -->

## NAT

Le **NAT (*Network Address Translation*)** permet de traduire des adresses IP lorsqu'un paquet traverse un routeur ou un dispositif de traduction.

Dans les réseaux résidentiels et de nombreuses organisations, le NAT permet notamment à plusieurs appareils utilisant des adresses IPv4 privées de partager une ou plusieurs adresses IPv4 publiques.

Exemple :

![Exemple de fonctionnement d'un routeur NAT](/images/04-NAT.png)
{{%center%}}
*Fonctionnement d'un routeur NAT (traduction de l'adresse privée vers son adresse publique*
{{%/center%}}

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
<!-- 
Le NAT n'implique pas nécessairement une traduction de ports. Plusieurs formes de NAT existent.


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

{{%notice style="note" title=" "%}}
Le NAT peut avoir des effets sur l'accessibilité depuis Internet, mais il ne doit pas être considéré comme un mécanisme de sécurité à part entière. La sécurité repose notamment sur les pare-feu, les contrôles d'accès et la configuration des services.
{{%/notice%}} -->

## Adresses IPv4 privées

Les principales plages privées IPv4 définies par RFC 1918 sont :

| Plage                                | Préfixe           |
| ------------------------------------- | ------------------ |
| `10.0.0.0` à `10.255.255.255`         | `10.0.0.0/8`        |
| `172.16.0.0` à `172.31.255.255`       | `172.16.0.0/12`     |
| `192.168.0.0` à `192.168.255.255`     | `192.168.0.0/16`    |

Ces adresses sont destinées aux réseaux privés et ne sont pas annoncées comme des adresses IPv4 publiques sur Internet.
