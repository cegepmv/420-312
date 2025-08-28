+++
pre = '<b>2. </b>'
title = 'Exercices'
draft = false
weight = "142"
+++

### Exercice 1
*Alors que votre VM est éteinte, mettez le type de son adapteur réseau à NAT. Ensuite démarrez-la.*

1. Remplissez le tableau ci-dessous avec les paramètres de votre machine :

| Adresse IP | Masque de sous-réseau | Passerelle par défaut | DNS1 | DNS2 |
| ---------- | --------------------- | --------------------- | ---- | ---- |
|            |                       |                       |      |      |

2. Est-ce que votre machine a une adresse IP statique ou dynamique (obtenue automatiquement)?
3. En utilisant la commande `nmcli`, donnez une adresse IP statique à votre machine en utilisant les paramètres que vous avez inscrit dans le tableau. Testez que tout fonctionne et que vous avez Internet après avoir redémarré le réseau.
4. Revenez en arrière de façon que votre ordinateur obtienne son adresse IP automatiquement.
5. Testez que tout fonctionne.

### Exercice 2
1. Ajoutez deux adresses IP à votre interface à l’aide de la commande `ip`, vous aurez donc 3 adresses IP différentes.

{{% notice style="warning" title="Attention" %}}
Les deux adresses doivent être dans le même réseau.
{{% /notice %}}

2. Configurez le fichier `hosts` pour que chacune des 3 adresses répondent aux `ping` avec un nom différent.
3. Quand vous redémarrez le service `Network`, est-ce que l’interface est toujours là?
4. Comment les rendre persistante? (Essayez de trouver la réponse au moins en théorie, testez le si vous le souhaitez).