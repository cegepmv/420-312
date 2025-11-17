+++
title = "Ateliers"
weight = "531"
+++
# Introduction à nginx
-------------------

**Nginx** est un serveur web léger, rapide et très utilisé.
Il peut servir :

+ des pages statiques (HTML, images…),
+ des applications web via des reverse proxies,
+ du HTTPS (certificats),
+ de la répartition de charge (load balancing).

## Installation et vérification
```bash
sudo dnf install nginx
sudo systemctl status nginx
```

### Fichiers utiles
+ **Fichier de configuration (défaut) -** `/etc/nginx/nginx.conf`
+ /etc/nginx/sites-available/
+ **Contenu Web par défaut -** `/var/www/html/`

### Configuration minimale

Exemple simple de serveur web :
```nginx
server {
    listen 80;
    server_name exemple.com;

    root /var/www/html;
    index index.html;
}
```

## Laboratoires

### 1- Introduction à nginx

1. Installer et configurer `nginx`
2. Créer une page `index.html` personnalisée dans le répertoire `/var/www/html`
3. Tester en tapant l'IP du serveur dans un navgateur
4. Observez les logs :
    + `/var/log/nginx/access.log` 
    + `/var/log/nginx/error.log`

{{%notice style="tip" title="Astuce"%}}
Pour voir l'évolution des logs en temps réel, vous pouvez utiliser la commande `tail -f`.
{{%/notice%}}

### 2- Servir une application React
1. Clonez une application de votre choix (peut-être votre projet final du cours Applications Web?)

2. À l'intérieur du répertoire du projet :
```bash
npm install
npm run build
```
La deuxième commande va créer une version exportable de votre projet avec un `index.html` dans un répertoire `build`. 

3. Copiez le contenu de `build` dans le répertoire `/var/www/html`. 