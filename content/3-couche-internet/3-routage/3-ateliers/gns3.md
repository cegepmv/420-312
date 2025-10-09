+++
title = "GNS3"
weight = "332"
draft = false
+++
-------------------

Les logiciels de simulation réseau jouent un rôle essentiel dans la conception et l’analyse des infrastructures réseau. Ils permettent aux concepteurs, ingénieurs et étudiants de tester virtuellement des topologies, d’expérimenter différents protocoles et configurations, et d’observer le comportement du réseau sans avoir à déployer du matériel coûteux. Grâce à ces outils, il est possible d’identifier les points faibles, d’optimiser les performances et d’anticiper les problèmes potentiels avant d'investir dans du matériel couteux.

Cet atelier a pour but de vous introduire à la simulation réseau sur le logiciel GNS3. Vous apprendrez à modéliser un réseau, configurer un routeur *Mikrotik* pour faire communiquer deux LANs composés d’un PC et d’un commutateur chacun. Voici la topologie du réseau en question :  

![Topologie réseau GNS3](../../../images/34-25.png)

Ce réseau a les spécifications suivantes : 

+ Sous-réseau 1 : `192.168.10.0/24` avec 
    + Un ordinateur virtuel (Virtual PC ou VPC) avec une adresse IP 192.168.10.1
    + L’interface Ether1 du routeur avec l’adresse IP 192.168.10.254 (la dernière adresse hôte disponible)
    + Un commutateur Ethernet (S2)

+ Sous-réseau 2 : `192.168.20.0/24` avec : 
    + Un ordinateur virtuel avec une adresse IP 192.168.20.1
    + l’interface Ether2 du routeur avec l’adresse IP 192.168.20.254 (la dernière adresse hôte disponible)
    + Un commutateur Ethernet (`S1`)


### Pré-requis
+ Avoir téléchargé et ouvert la VM GNS3 sur VMWare ([lien de téléchargement](https://gns3.com/software/download-vm))
+ Télécharger les émulations des produits *Mikrotik* sur le [marketplace de GNS3](https://gns3.com/marketplace/appliances), onglet *“Appliances”* : 
+ Téléchargez Mikrotik CHR :

![Mikrotik CHR](../../../images/34-26.png)


+ Sur GNS3, allez dans *File -> Import Appliance* et ouvrez le fichier `mikrotik-chr.gns3a` nouvellement téléchargé. Sélectionnez l’installation sur *GNS3 VM*

![Installation Mikrotik CHR](../../../images/34-27.png)

+ Sélectionnez l’installation *MikroTik Cloud Hosted Router* (version la plus récente)

Vous aurez ensuite accès au routeur Mikrotik dans l’onglet Routeurs.

### Implémentation du réseau 
Pour ce faire, procédez comme suit : 

1. Créez un nouveau projet et appelez-le `lab01`.
2. Faites glisser deux « VPCS » (Virtual PCs) sur le diagramme de réseau à partir du panneau de gauche. Si vous êtes invité à choisir un serveur, sélectionnez *"GNS3 VM"*.
3. Faites glisser le routeur `Mikrotik CHR` sur le diagramme de réseau à partir du panneau de gauche.
4. Faites glisser deux *"Switch Ethernet"* sur le diagramme de réseau à partir du panneau de gauche.
5. En utilisant le bouton *"Add a link"* sur le panneau de gauche, faites les branchements nécessaires  à l’aide des câbles Ethernet virtuels. Faites en sorte que votre réseau ressemble au réseau décrit ci-haut.

Remarques :
+ les PC n'ont qu'une seule interface, vous ne pouvez donc pas connecter le câble au mauvais port.
+ Le routeur possède 8 interfaces. Les ports sur lesquels vous branchez vos câbles réseau doivent correspondre à la manière dont vous configurez votre routeur dans le logiciel. Pour l'instant, contentez-vous de suivre scrupuleusement le schéma. 
+ Vous vous demandez pourquoi votre diagramme n'affiche pas les étiquettes des ports ? Appuyez sur le bouton *Show/Hide Interface Labels*.

6. Appuyez sur le bouton Démarrer pour lancer vos deux PC virtuels, le routeur et le commutateur. Tous les liens doivent passer du ROUGE au VERT.
7. Appuyez sur le bouton *Console Connect to All Nodes* (Connexion de la console à tous les nœuds) : Le bouton *Console Connect* permet d'ouvrir un terminal vers les deux PC et les périphériques du routeur. (Vous pouvez également cliquer avec le bouton droit de la souris sur chacun d'eux et choisir Console, mais nous devons configurer les trois). Le simple commutateur fourni par *GNS3* n'a pas de console.

### Configuration du routage

+ Dans la console *MikroTik*

Note : Nous configurons d'abord le routeur, car nous ne pouvons pas configurer complètement le réseau PC tant que la passerelle par défaut (le routeur) n'existe pas.

1. Saisissez le login par défaut de MikroTik (admin) et un mot de passe vierge.
2. Sélectionnez “n” lorsque l'on vous demande d'afficher le fichier de licence.
3. Basculez vers le mode sans échec (“Safe Mode”) CTRL-X (une bonne habitude lors de l'expérimentation de la configuration).
4. Configurez deux interfaces (correspondant aux deux fils branchés)
    ```bash
    ip address add address=192.168.10.254/24 interface=ether1
    ip address add address=192.168.20.254/24 interface=ether2
    ```
5. Imprimez la configuration pour confirmer : ip address print
6. Donnez un nom d'hôte (hostname) à votre routeur pour l'identifier dans les grands réseaux :
    + Vérifier le nom actuel du routeur : system identity print
    + Définissez un nouveau nom pour le routeur : 
    ```bash
    system identity set name=<LE NOM QUE VOUS VOULEZ>
    ```
    + Vérifier le nom actuel du routeur : 
    ```bash
    system identity print
    ```

+ Dans la console de PC1 : 
    + Affichez le menu d'aide pour la commande disponible (rappelez-vous qu'il s'agit d'un PC simulé rudimentaire) : help
    + Configurez une adresse IP : 
    ```bash
    ip 192.168.10.1/24 192.168.10.254
    ```
    + Cette commande attribue au PC l'adresse IP `192.168.10.1` dans le réseau `192.168.10.0/24`, avec la passerelle par défaut `192.168.10.254` (qui est l’interface du routeur).
    + Affichez la configuration : 
    ```
    show ip
    ```
    + Sauvegarder la configuration pour qu'elle persiste après un redémarrage de l'alimentation : 
    ```
    save
    ```

+ Dans la console de PC2
    + Faites les mêmes étapes, avec comme adresse IP `192.168.20.1/24` et passerelle `192.168.20.254`

Enfin, démontrez que le réseau est fonctionnel :
+ Accédez à la console PC1
    + Faites un `ping` de l'interface `ether1` du routeur, à laquelle `PC1` est directement connecté : 
    + Effectuez un `ping` de l'interface `ether2` du routeur, qui se trouve de « l'autre côté » (pour ainsi dire...) du routeur :
    + Effectuez un ping vers le PC2 à travers le routeur : ping 192.168.20.1. Cette opération devrait réussir.
    + De retour au routeur, quittez le mode sans échec via CTRL-X lorsque vous êtes satisfait de la configuration du routeur. La configuration sera sauvegardée.
+ Accédez à la console PC2
    + Faites un ping de l'interface ether2 du routeur, à laquelle PC1 est directement connecté : ping 192.168.20.254. Appuyez sur CTRL-C pour arrêter le ping.
    + Effectuez un ping de l'interface ether1 du routeur, qui se trouve de « l'autre côté » (pour ainsi dire...) du routeur : ping 192.168.10.254. Cette opération devrait réussir.
    + Effectuez un ping vers le PC2 à travers le routeur : ping 192.168.10.1. Cette opération devrait réussir.
    + De retour au routeur, quittez le mode sans échec via CTRL-X lorsque vous êtes satisfait de la configuration du routeur. La configuration sera sauvegardée.
