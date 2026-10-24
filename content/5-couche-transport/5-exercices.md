+++
pre = '<b>5. </b>'
title = "Exercices"
slug = '5-exercices'
weight = "550"
draft = false
+++
-------------

### 1. Choisir le protocole

Pour chaque situation, indiquez le protocole le plus approprié et justifiez votre choix.

1. Téléchargement d'une mise à jour de système d'exploitation.
2. Capteur de température qui envoie une mesure chaque seconde à un serveur.
3. Appel en visioconférence.
4. Envoi d'un formulaire d'inscription sur un site web.
5. Demande d'adresse IP par un poste qui vient de démarrer.
6. Copie d'une base de données vers un serveur de sauvegarde.

<!-- {{% expand title="Solution" %}}
1. **TCP** : un fichier incomplet ou corrompu est inutilisable.
2. **UDP** : une mesure perdue n'est pas grave, la suivante arrive une seconde plus tard; l'en-tête léger convient aussi aux petits appareils.
3. **UDP** : une image ou un son retransmis arriverait trop tard; mieux vaut une courte distorsion qu'un délai.
4. **TCP** : toutes les données du formulaire doivent arriver intactes.
5. **UDP** : c'est DHCP; le poste n'a pas encore d'adresse IP et l'échange est court.
6. **TCP** : la copie doit être complète et exacte.
{{% /expand %}} -->

### 2. Synthèse

1. Expliquez pourquoi un serveur web peut servir plusieurs clients en même temps sur le seul port `80`.
2. Un client envoie un datagramme UDP à un serveur qui est éteint. Le client en est-il informé par UDP? Comparez avec la même situation en TCP.
3. Calculez le nombre d'octets d'en-tête de transport économisés en envoyant 1000 petits messages avec UDP plutôt qu'avec TCP (en-tête TCP de 20 octets).
4. Une application de clavardage doit garantir que les messages arrivent tous et dans l'ordre. Un développeur propose d'utiliser UDP « parce que c'est plus rapide ». Que lui répondez-vous?

<!-- {{% expand title="Solution" %}}
1. Chaque conversation est identifiée par une paire de sockets. Le socket du serveur (`IP:80`) est le même, mais le socket de chaque client (adresse IP et port éphémère) est différent.
2. Non : UDP n'attend aucune réponse et n'envoie aucun accusé de réception, donc le client ne sait pas si le datagramme est arrivé. En TCP, le client n'obtiendrait jamais de réponse à son SYN : après plusieurs tentatives, la connexion échouerait et l'application serait informée de l'erreur.
3. `(20 - 8) × 1000 = 12 000 octets`.
4. Avec UDP, l'application devrait elle-même gérer les accusés de réception, les retransmissions et la remise en ordre, c'est-à-dire réimplémenter TCP. TCP est le choix approprié.
{{% /expand %}} -->