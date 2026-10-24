+++
pre = '1- '
title = "Introduction à Wireshark"
slug = '1-wireshark'
weight = "561"
draft = true
+++
-------------

## Contexte

**Wireshark** est un analyseur de trafic réseau. Il capture les trames qui passent par une interface et décode chaque couche : Ethernet, IP, TCP ou UDP, puis le protocole d'application.

Dans ce laboratoire, vous allez capturer et analyser deux échanges entre votre poste et le serveur web du département :

1. une **requête DNS** (UDP), pour obtenir l'adresse IP du serveur;
2. une **requête HTTP** (TCP), pour obtenir une page web.

Vous observerez ainsi, dans du vrai trafic, les notions vues dans ce chapitre : ports, en-tête UDP, établissement de connexion, numéros d'ordre et d'accusé de réception, fermeture de connexion.

<!-- Remplacer SERVEUR_WEB par le nom du serveur HTTP du département. -->

| Élément | Valeur |
|---------|--------|
| **Serveur web** | `SERVEUR_WEB` |
| **Protocole** | HTTP (port `80`) |
| **Poste** | Poste Windows du laboratoire, avec Wireshark |

{{%notice style="note" title="Important"%}}
Utilisez **`curl.exe`** et non `curl` dans PowerShell : dans certaines versions de PowerShell, `curl` correspond à une autre commande.
{{%/notice%}}

## 1- Préparation

### 1. Relever la configuration du poste

Dans une invite de commandes :

```powershell
ipconfig /all
```

Complétez le tableau :

| Élément | Valeur |
|---------|--------|
| **Adresse IPv4 du poste** | |
| **Adresse MAC du poste** | |
| **Passerelle par défaut** | |
| **Serveur DNS** | |

### 2. Prédire avant d'observer

Avant toute capture, complétez le tableau à partir de ce que vous savez du chapitre.

| Question | Requête DNS | Requête HTTP |
|----------|-------------|--------------|
| Protocole de transport utilisé | | |
| Port de destination | | |
| Plage du port source | | |
| Y aura-t-il un établissement de connexion? | | |

Vous comparerez vos prédictions aux résultats à la fin du laboratoire.

### 3. Démarrer la capture

1. Videz le cache DNS du poste :
    ```powershell
    ipconfig /flushdns
    ```
2. Ouvrez **Wireshark**.
3. Sélectionnez l'interface correspondant à l'adresse IP relevée à l'étape 1 (le petit graphique d'activité à côté de chaque interface peut vous aider).
4. Démarrez la capture.
5. Dans l'invite de commandes, exécutez :
    ```powershell
    nslookup SERVEUR_WEB
    curl.exe -4 http://SERVEUR_WEB/
    ```
6. Arrêtez la capture.

{{%notice style="tip" title="Conseil"%}}
Enregistrez votre capture (**File → Save As**). Vous pourrez la rouvrir si vous devez poursuivre l'analyse plus tard.
{{%/notice%}}

## 2- Requête DNS (UDP)

### 1. Trouver la requête

Dans la barre de filtre, saisissez :

```text
dns
```

Repérez la requête (*Standard query*) de type **A** pour `SERVEUR_WEB`, ainsi que sa réponse (*Standard query response*).

{{%notice style="info" title="Remarque"%}}
Vous pourriez voir plusieurs requêtes : par exemple une de type **A** (adresse IPv4) et une de type **AAAA** (adresse IPv6), ou des requêtes avec le suffixe DNS du réseau ajouté au nom. Concentrez-vous sur la requête de type **A** qui a reçu une réponse contenant une adresse.
{{%/notice%}}

### 2. Analyser la requête

Sélectionnez la requête et développez chaque couche dans le volet de détails.

Complétez le tableau :

| Couche | Champ | Valeur |
|--------|-------|--------|
| **Ethernet II** | MAC source | |
| | MAC destination | |
| **IPv4** | IP source | |
| | IP destination | |
| | Protocole | |
| **UDP** | Port source | |
| | Port destination | |
| | Longueur | |

**Faites une capture d'écran** du volet de détails montrant l'en-tête UDP développé.

### 3. Analyser la réponse

Sélectionnez la réponse DNS.

1. Comparez les ports source et destination avec ceux de la requête. Que constatez-vous?
2. Dans la section **Answers** de la réponse DNS, relevez l'adresse IP du serveur web.

### Questions

1. Combien de champs l'en-tête UDP contient-il? Lesquels?
2. Le champ **Longueur** d'UDP inclut-il l'en-tête? Vérifiez en soustrayant 8 octets : combien d'octets occupe la requête DNS elle-même?
3. Avant la requête DNS, y a-t-il un échange préalable entre le poste et le serveur DNS? Qu'est-ce que cela confirme au sujet d'UDP?
4. À quel équipement correspond l'adresse MAC de destination de la requête? Comparez avec l'adresse IP de destination et expliquez la différence (rappel du chapitre 4).

## 3- Connexion TCP

### 1. Isoler la conversation

Remplacez le filtre par (en utilisant l'adresse IP relevée dans la réponse DNS) :

```text
ip.addr == ADRESSE_IP_DU_SERVEUR && tcp.port == 80
```

### 2. Analyser l'établissement de connexion

Repérez les trois premiers segments de la conversation.

Pour chacun, développez l'en-tête TCP puis la section **Flags**. Complétez le tableau :

| Segment | Sens | Port source | Port destination | Bits activés | SEQ (relatif) | ACK (relatif) |
|---------|------|-------------|------------------|--------------|---------------|---------------|
| 1 | | | | | | |
| 2 | | | | | | |
| 3 | | | | | | |

**Faites une capture d'écran** de l'en-tête TCP du premier segment, avec les bits de contrôle développés.

Dans le premier segment, relevez aussi :

- le **numéro d'ordre brut** (*Sequence Number (raw)*) : c'est le numéro initial réel, choisi aléatoirement;
- la valeur du **MSS** annoncée dans les **Options**.

### Questions

1. Le port source du client correspond-il à votre prédiction? Dans quelle plage se trouve-t-il?
2. Dans le segment 2, pourquoi l'ACK vaut-il `1` alors que le segment 1 ne contenait aucune donnée?
3. Comparez le numéro d'ordre brut du client avec celui du serveur. Pourquoi sont-ils différents?

## 4- Échange de données et fermeture

### 1. Vérifier les numéros d'ordre et d'accusé de réception

Repérez le segment qui contient la requête HTTP (`GET / HTTP/1.1`), puis le premier segment de la réponse du serveur.

Complétez :

| Segment | Sens | SEQ | ACK | Longueur des données (*TCP Segment Len*) |
|---------|------|-----|-----|------------------------------------------|
| Requête `GET` | Client → Serveur | | | |
| Réponse du serveur | Serveur → Client | | | |

Vérifiez que :

```text
ACK de la réponse = SEQ de la requête + longueur de la requête
```

### 2. Lire la conversation

Faites un clic droit sur un segment de la conversation, puis **Follow → TCP Stream**.

Wireshark reconstitue les données échangées : la requête du client et la réponse du serveur.

1. Quelle méthode HTTP le client utilise-t-il?
2. Quel code de réponse le serveur renvoie-t-il?

Fermez la fenêtre (le filtre de la conversation reste appliqué).

### 3. Observer la fermeture

Repérez les derniers segments de la conversation.

1. Quels bits de contrôle sont activés?
2. Combien de segments observez-vous pour la fermeture : trois ou quatre? Expliquez.
3. Qui (client ou serveur) a demandé la fermeture en premier?

### 4. Vue d'ensemble

Ouvrez **Statistics → Flow Graph** et cochez **Limit to display filter**.

Wireshark affiche la conversation sous forme de diagramme, semblable à ceux de ce chapitre.

**Faites une capture d'écran** du diagramme et identifiez-y les trois étapes : établissement, échange de données, fermeture.

## Questions de synthèse

1. Comparez vos résultats avec vos prédictions de la partie 1. Lesquelles étaient correctes?
2. Comparez la taille de l'en-tête UDP de la requête DNS avec celle de l'en-tête TCP du premier segment (champ *Header Length*). Pourquoi l'en-tête TCP est-il plus grand?
3. Combien de segments TCP ont été échangés au total pour obtenir une seule page web? Combien de datagrammes UDP pour la requête DNS? Qu'est-ce que cela illustre?
4. Pourquoi la requête DNS utilise-t-elle UDP, alors que la page web utilise TCP?

## Validation

+ [ ] La configuration du poste est relevée.
+ [ ] Les prédictions sont complétées avant la capture.
+ [ ] La requête et la réponse DNS sont identifiées et analysées.
+ [ ] Les trois segments de l'établissement de connexion sont analysés.
+ [ ] La relation entre SEQ, ACK et longueur des données est vérifiée.
+ [ ] La fermeture de connexion est identifiée.
+ [ ] Le *Flow Graph* de la conversation est obtenu.
+ [ ] Les quatre captures d'écran demandées sont faites.