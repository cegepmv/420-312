+++
title = "Limitation d’IPv4"
weight = "321"
+++
-------------------

+ **Manque d’adresses IP :** l’IPv4 a un nombre limité d’adresses IP publiques disponibles. Bien qu’il existe environ 4 milliards d’adresses IPv4, le nombre croissant de périphériques IP, les connexions permanentes et la croissance potentielle des pays en voie de développement entraînent une hausse du nombre d’adresses devant être disponibles.

<!-- + **Croissance de la table de routage Internet :** Une table de routage est utilisée par les routeurs pour déterminer les meilleurs chemins disponibles. À mesure que le nombre de serveurs (nœuds) connectés à Internet augmente, il en va de même pour le nombre de routes réseau. Ces routes IPv4 consomment beaucoup de mémoire et de ressources processeur sur les routeurs Internet. -->

<!-- + Absence de connectivité de bout en bout : La technologie de traduction d’adresses réseau (NAT) est généralement implémentée dans les réseaux IPv4. Cette technologie permet à plusieurs périphériques de partager une adresse IP publique unique. Cependant, étant donné que l’adresse IP publique est partagée, l’adresse IP d’un hôte interne du réseau est masquée. Cela peut être problématique pour les technologies nécessitant une connectivité de bout en bout. -->

### Solution : IPv6
+ **Espace d’adressage plus important :** Les adresses IPv6 sont basées sur un adressage hiérarchique de 128 bits (32 bits pour l’IPv4), ce qui augmente considérablement le nombre d’adresses IP disponibles.

+ **Traitement des paquets plus efficace :** L’en-tête IPv6 a été simplifié et comporte moins de champs. Cela améliore le traitement des paquets par les routeurs intermédiaires et permet également la prise en charge d’extensions et d’options pour plus d’évolutivité et de longévité.

+ **Traduction d’adresses réseau non nécessaire :** Grâce au grand nombre d’adresses publiques IPv6, la technologie NAT n’est plus nécessaire. Les sites clients, des plus grandes entreprises aux sites de particuliers, peuvent obtenir une adresse réseau publique IPv6. Cela évite certains des problèmes d’application causés par la technologie NAT, qui sont rencontrés par des applications nécessitant une connectivité de bout en bout.

+ **Sécurité intégrée :** l’IPv6 prend nativement en charge les fonctions d’authentification et de confidentialité. Avec l’IPv4, d’autres fonctions devaient être mises en œuvre pour bénéficier de ces fonctionnalités.

+ 4 milliards d’adresses IPv4 (2 ^ 32 = 4000000000) [4294967296]

+ 340 undécillions d’adresses IPv6 (2^128 = 340x10^36)340000000000000000000000000000000000000 [340282366920938463463374607431768211456]