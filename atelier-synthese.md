+++
pre = "<b>7. </b>"
title = "Atelier synthèse"
weight = "470"
draft = false
+++
-------------

## Mise en situation

Une entreprise souhaite concevoir le réseau informatique de son organisation.

L’entreprise dispose du bloc d’adresses privé suivant :

```text
172.16.0.0/16
```
Elle est composée de quatre départements :

| Département                  | Nombre d’hôtes requis |
| ---------------------------- | --------------------: |
| **Direction**                |                   100 |
| **Ressources humaines (RH)** |                    50 |
| **Informatique (IT)**        |                    25 |
| **Finance**                  |                    10 |

L’entreprise dispose de **deux routeurs**.

Chaque routeur dessert deux départements :

+ **Routeur R1**
  + réseau de la Direction
  + réseau des RH
  + liaison WAN vers R2
+ **Routeur R2**
  + réseau de l’IT
  + réseau de la Finance
  + liaison WAN vers R1

La liaison entre les deux routeurs est une liaison **point à point**.

### Topologie logique

![Topologie de l'atelier synthèse](/images/04-atelier-synthese-topologie.png)

```text
                 ┌─────────────────────┐
                 │     Direction       │
                 │     100 hôtes       │
                 └─────────┬───────────┘
                           │
                        G0/0
                           │
                      ┌────┴────┐
                      │   R1    │
                      └────┬────┘
                           │
                        G0/1
                           │
                 ┌─────────┴───────────┐
                 │         RH          │
                 │      50 hôtes       │
                 └─────────────────────┘

                      R1
                       │
                 Liaison WAN
                  point à point
                       │
                      R2

                      R2
                 ┌─────┴─────┐
                 │           │
              G0/0         G0/1
                 │           │
        ┌────────┘           └────────┐
        │                             │
   ┌────┴─────┐                 ┌─────┴─────┐
   │    IT    │                 │  Finance  │
   │ 25 hôtes │                 │ 10 hôtes  │
   └──────────┘                 └───────────┘
```

L’objectif est de concevoir le réseau, puis de l’implémenter et de le tester dans **Cisco Packet Tracer**.


## 1- Plan d’adressage avec VLSM

### 1. Analyse des besoins

À partir du nombre d’hôtes requis pour chaque département, déterminez pour chacun :

1. Le nombre minimal de bits nécessaires pour les hôtes.
2. Le nombre total d'adresses disponibles dans le sous-réseau.
3. Le préfixe CIDR approprié.
4. Le masque de sous-réseau correspondant.

Complétez le tableau :

| Département     | Hôtes requis | Bits hôtes nécessaires | Préfixe | Nombre d’adresses |
| --------------- | -----------: | ---------------------: | ------: | ----------------: |
| **Direction**   |          100 |                        |         |                   |
| **RH**          |           50 |                        |         |                   |
| **IT**          |           25 |                        |         |                   |
| **Finance**     |           10 |                        |         |                   |
| **WAN**         |            2 |                        |         |                   |

{{%notice style="tip" title="Rappel"%}}
Pour un réseau *IPv4* classique, deux adresses sont réservées : l’adresse réseau et l’adresse de diffusion (broadcast).
{{%/notice%}}

### 2. Déterminer l’ordre d’allocation

Avec la méthode **VLSM**, les sous-réseaux doivent être attribués en commençant généralement par les besoins les plus importants.

Classez donc les réseaux du plus grand au plus petit.

### 3. Construire le plan d’adressage

À partir du bloc :
```text
172.16.0.0/16
```
Découpez l’espace d’adressage à l’aide de **VLSM**.

Pour chaque réseau, déterminez :

* l’adresse réseau ;
* le préfixe CIDR ;
* le masque de sous-réseau ;
* la plage d'adresses hôte (1ère -> dernière adresse hôte)
* l’adresse de broadcast ;
* l’adresse à utiliser comme passerelle par défaut.

### Tableau à compléter

| Réseau        | Besoin | Adresse réseau | Préfixe | Masque | Plage d'adresses | Broadcast | Passerelle |
| ------------- | -----: | -------------- | ------- | ------ | ---------------- | --------- | ---------- |
| **Direction** |    100 |                |         |        |                  |           |            |
| **RH**        |     50 |                |         |        |                  |           |            |
| **IT**        |     25 |                |         |        |                  |           |            |
| **Finance**   |     10 |                |         |        |                  |           |            |
| **WAN R1–R2** |      2 |                |         |        |                  |           |            |

### Consigne importante

Pour chacun des quatre réseaux locaux, **la première adresse hôte doit être attribuée à l’interface du routeur** et servir de passerelle par défaut.

Par exemple, si un réseau est :
```text
172.16.0.0/25
```

alors la passerelle devra être :
```text
172.16.0.1
```

## 2- Implémentation Packet Tracer

### 1. Construire la topologie

Dans *Cisco Packet Tracer*, créez une topologie comprenant :

+ **2 routeurs** : R1 et R2 ;
+ **4 commutateurs** ;
+ au moins **1 ordinateur par département** ;
+ une liaison WAN entre R1 et R2.

La topologie doit respecter la structure suivante :

```text
 PC(s)             PC(s)
   │                 │
  SW1               SW2
   │                 │
   └─── R1 ── WAN ── R2 ───┐
                            │
                         SW3   SW4
                          │     │
                         IT   Finance
```

R1 doit donc posséder **trois interfaces** :
* une interface vers la Direction ;
* une interface vers les RH ;
* une interface vers la liaison WAN.

R2 doit également posséder **trois interfaces** :
* une interface vers l’IT ;
* une interface vers Finance ;
* une interface vers la liaison WAN.

### 2. Élaborer le plan d’adressage des équipements

Avant de configurer une seule interface dans *Packet Tracer*, vous devez établir le **plan d’adressage complet de la topologie**.

À partir du plan *VLSM* réalisé dans la partie précédente, attribuez une adresse IPv4 à chaque interface réseau des équipements et construisez un tableau récapitulatif permettant de vérifier l'ensemble du plan d'adressage.

|Équipement   |	Interface / NIC      |	Adresse IPv4|	Préfixe|	Masque|	Passerelle|
|-------------|----------------------|--------------|--------|--------|-----------|
|**R1**       | GigabitEthernet0/0/0 |	            |        |        |     —     |
|**R1**       | GigabitEthernet0/0/1 |	            |        |        |     —     |
|**R1**       | GigabitEthernet0/0/2 |              |        |        |     —     |
|**R2**       | GigabitEthernet0/0/0 |	            |        |        |     —     |
|**R2**       | GigabitEthernet0/0/1 |	            |        |        |     —     |
|**R2**       | GigabitEthernet0/0/2 |              |        |        |     —     |
|**PC-DIR-01**| NIC                  |              |        |        |           |	
|**PC-RH-01** | NIC	                 |              |        |        |           |
|**PC-IT-01** | NIC	                 |              |        |        |           |
|**PC-FIN-01**| NIC	                 |              |        |        |           |
	
	
Vous êtes libre de choisir les adresses des postes à l’intérieur de la plage d’adresses hôtes disponible, à condition de respecter les règles suivantes :

+ L’adresse ne doit pas être l’adresse réseau ;
+ L’adresse ne doit pas être l’adresse de broadcast ;
+ L’adresse ne doit pas être déjà attribuée à une autre interface ;
+ Le poste doit utiliser comme passerelle l’adresse de l’interface du routeur correspondant à son réseau.
+ La passerelle doit correspondre à **la première adresse hôte du sous-réseau**.


{{%notice style="tip" title="Conseils"%}}
Utilisez une convention d’adressage cohérente. Par exemple, vous pouvez attribuer la première adresse hôte au routeur, puis commencer les postes à la deuxième adresse hôte.
{{%/notice%}}

Vérification avant configuration

Avant de poursuivre, vérifiez que :

* [ ] Chaque interface possède une adresse unique.
* [ ] Chaque adresse appartient au bon sous-réseau.
* [ ] Les interfaces des routeurs utilisent la première adresse hôte de leur réseau.
* [ ] Chaque PC utilise une adresse valide de son sous-réseau.
* [ ] Chaque PC utilise la bonne passerelle.
* [ ] Les deux interfaces de la liaison WAN appartiennent au même sous-réseau.
* [ ] Aucune adresse réseau n'est attribuée à un équipement.
* [ ] Aucune adresse de broadcast n'est attribuée à un équipement.
* [ ] Aucun sous-réseau ne chevauche un autre.

{{%notice style="note" title="Important"%}}
Faites valider votre plan d’adressage avant de commencer la configuration des équipements.
{{%/notice%}}

### 2. Configurer les interfaces des routeurs

Configurez les interfaces de R1 et R2 à partir du plan d’adressage réalisé établi à l'étape précédente.

Pour chaque interface :

1. attribuez l’adresse IPv4 appropriée ;
2. configurez le masque ;
3. activez l’interface ;
4. vérifiez son état.

### 3. Configurer les postes clients

Configurez chaque poste avec les informations déterminées dans votre plan d’adressage.

Pour chaque poste, configurez :

* son adresse IPv4 ;
* son masque de sous-réseau ;
* sa passerelle par défaut.

### 4. Configurer le routage

Les quatre réseaux sont situés sur des sous-réseaux différents.

Un poste de la Direction doit donc pouvoir communiquer avec un poste du réseau IT, même si ces deux réseaux sont directement connectés à des routeurs différents.

Configurez le **routage statique** entre R1 et R2 :
  + R1 doit connaître les réseaux situés derrière R2.
  + R2 doit connaître les réseaux situés derrière R1.

1. Sur R1, ajoutez les routes nécessaires vers le réseau **IT** et le réseau **Finance**.
2. Sur R2, ajoutez les routes nécessaires vers le réseau **Direction** et le réseau **RH**.
    Utilisez la syntaxe :

    ```bash
    ip route <réseau_destination> <masque> <prochain_saut>
    ```
3. Sur chacun des routeurs, affichez la table de routage :
    ```text
    show ip route
    ```

    Identifiez :
    * les réseaux directement connectés ;
    * les routes statiques ;
    * l’interface ou le prochain saut utilisé pour chaque réseau distant.

    Vous devez être capable d'expliquer pourquoi R1 sait atteindre le réseau IT et pourquoi R2 sait atteindre le réseau Direction.


### 5. Tester la communication

Effectuez les tests suivants.

###### Test 1 — Communication dans un même réseau

Depuis un poste de la Direction, effectuez un `ping` vers la passerelle :

```text
ping <adresse_de_la_passerelle>
```

Le test doit réussir.

###### Test 2 — Communication entre deux réseaux du même routeur

Depuis un poste de la Direction, testez la communication avec un poste du réseau RH.

```text
ping <adresse_IP_du_poste_RH>
```

Observez le chemin emprunté par les paquets.

###### Test 3 — Communication entre les deux routeurs

Depuis R1, testez l’adresse WAN de R2 :

```text
ping <adresse_WAN_de_R2>
```

Faites également le test inverse depuis R2.

###### Test 4 — Communication entre deux réseaux situés derrière des routeurs différents

Depuis un poste de la Direction, testez :

```text
ping <adresse_IP_du_poste_IT>
```

Puis :

```text
ping <adresse_IP_du_poste_Finance>
```

Ces communications doivent traverser **R1 → liaison WAN → R2**.

### 6. Observer le chemin avec traceroute

Utilisez la commande :

```text
tracert <adresse_IP_destination>
```

depuis un poste.

Observez le nombre de sauts nécessaires pour atteindre un réseau situé derrière l’autre routeur.

###### **Question**

Pourquoi le paquet passe-t-il par la passerelle du réseau local avant d’atteindre le réseau de destination ?

###  7. Validation finale

Avant de terminer l’atelier, vérifiez les éléments suivants :

+ Les quatre sous-réseaux ont été calculés avec VLSM.
+ Aucun sous-réseau ne chevauche un autre.
+ L’espace d’adressage `172.16.0.0/16` est utilisé efficacement.
+ La première adresse hôte de chaque LAN est utilisée comme passerelle.
+ Les interfaces de R1 et R2 sont correctement configurées.
+ La liaison WAN entre R1 et R2 fonctionne.
+ Les tables de routage contiennent les routes nécessaires.
+ Les postes utilisent la bonne passerelle.
+ Les communications entre les quatre départements fonctionnent.
+ `show ip route` permet d'observer les routes configurées.
+ `tracert` permet d'observer le passage par les routeurs.


### Questions de synthèse

Répondez aux questions suivantes en vous basant sur votre configuration.

1. Pourquoi ne peut-on pas attribuer directement `172.16.0.0/16` à tous les postes de l’entreprise?
2. Quel est l’avantage d’utiliser *VLSM* plutôt que de créer quatre sous-réseaux de même taille?
3. Pourquoi la liaison entre **R1** et **R2** utilise-t-elle un sous-réseau beaucoup plus petit que les réseaux des départements?
4. Lorsqu’un poste de la Direction communique avec un poste du réseau **IT**, quelle est la première passerelle utilisée?
5. Pourquoi **R1** a-t-il besoin d’une route vers le réseau **IT** alors que **R1** ne possède aucune interface directement connectée à ce réseau?
6. Quelle différence y a-t-il entre :
    + un réseau directement connecté ;
    + une route statique ;
    + une passerelle par défaut ?
7. À partir de la table de routage de **R1**, expliquez comment le routeur détermine par quelle interface transmettre un paquet destiné au réseau **IT**.
8. Que se passerait-il si le poste de la **Direction** avait une mauvaise passerelle par défaut?
9. Que se passerait-il si la route vers le réseau **IT** était présente sur **R1**, mais que la route de retour vers le réseau Direction était absente sur **R2**?
10. Expliquez le trajet complet d’un paquet envoyé par un poste de la **Direction** vers un poste du réseau **Finance**, en identifiant:
    + le poste source ;
    + la passerelle par défaut ;
    + R1 ;
    + la liaison WAN ;
    + R2 ;
    + le réseau de destination ;
    + le poste destination.
