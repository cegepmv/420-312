+++
pre = '<b>4. </b>'
title = "Choisir entre TCP et UDP"
slug = '4-tcp-vs-udp'
weight = "540"
draft = false
+++
-------------

Le choix du protocole de transport est fait par les **développeurs de l'application**, selon ses besoins.

| Critère | TCP | UDP |
|---------|-----|-----|
| Fiabilité | Toutes les données arrivent, dans l'ordre | Des données peuvent être perdues ou désordonnées |
| Délai avant le premier envoi | Handshake nécessaire | Aucun |
| Taille de l'en-tête | 20 octets minimum | 8 octets |
| Réaction à une perte | Retransmission (introduit un délai) | Aucune |
| Usage typique | Les données doivent être **complètes et exactes** | Les données doivent être **à temps** |

## Exemples

**TCP** convient lorsqu'il manque une seule donnée et que le résultat est inutilisable :

- une page web (HTTP, HTTPS);
- un courriel (SMTP);
- un transfert de fichier (FTP);
- une session d'administration à distance (SSH).

**UDP** convient lorsqu'une donnée arrivée en retard est aussi inutile qu'une donnée perdue :

- un appel vocal ou vidéo : retransmettre un morceau de son d'il y a une seconde n'aurait aucun sens, il vaut mieux continuer;
- un jeu en ligne : seule la position actuelle des joueurs compte;
- une requête courte (DNS, DHCP, NTP) : établir une connexion coûterait plus cher que l'échange lui-même; en cas de perte, on renvoie simplement la requête.

## DNS : un service qui utilise les deux

Le service **DNS** utilise le port `53` avec les deux protocoles :

- **UDP** pour les requêtes courantes, courtes et nombreuses;
- **TCP** lorsque la réponse est trop volumineuse pour un datagramme, ou pour copier l'ensemble des informations d'un serveur DNS vers un autre.

Le choix dépend donc du **type d'échange**, et pas seulement de l'application.

{{%notice style="tip" title="À retenir"%}}
TCP privilégie la **fiabilité**. UDP privilégie la **rapidité** et la **légèreté**. Aucun n'est meilleur que l'autre : chacun répond à des besoins différents.
{{%/notice%}}
