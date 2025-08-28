---
title: >-
  Nginx: Serveur de Fichier Statiques et Proxy pour Applications
  Node.js
summary: 'Nginx (prononcé "engine-X") est un serveur web et un proxy inverse très perfomant, connu pour sa stabilité, sa riche pallete de fonctionnalités, sa configuration simple et sa faible consommation de ressources.'
tags:
  - Nginx
  - WebServer
  - ReverseProxy
  - NodeJS
  - Linux
  - LinuxServer
date: 2024-06-22 12:12:20
---


## Qu'est-ce que Nginx ?

Nginx (prononcé "engine-X") est un serveur web et un proxy inverse très perfomant, connu pour sa stabilité, sa riche pallete de fonctionnalités, sa configuration simple et sa faible consommation de ressources. Utilisé par de nombreux site web à grande échelle, Nginx est capable de gérer des milliers de connexions simultanées grâce à son architecture événementielle.

![Logo de Nginx](/posts/Nginx/Nginx_logo.png "Logo de Nginx")

## Pourquoi utiliser Nginx ?

Nginx est particulièrement apprécié pour les raisons suivants :

- **Performance et Scalabilité**: Nginx peut gérer un grand nombre de connexions simultanées avec une utilisation minimale de la mémoire.
- **Flexibilité**: Il peut servir des fichiers statiques, agir en tant que reverse proxy pour des applications web, et bien plus encore.
- **Sécurité**: Nginx offre de nombreuses fonctionnalités pour sécuriser votre serveur web.
- **Facilité de configuration**: Sa syntaxe de configuration est simple et intuitive.

## Utilisation de Nginx pour servir des fichiers statiques

### Installation de Nginx

Sur la plupart des distributions Linux, l'installation de Nginx est simple. Sur une distribution basée sur Debian (comme Ubuntu), vous pouvez utilisez les commandes suivantes: 

```bash
sudo apt update
sudo apt install nginx
```

### Configuration de Nginx pour servir des fichiers statiques

Une fois installé, la configuration de Nginx pour servir des fichiers statiques est relativement simple. Voici un exemple de configuration de base:

```nginx
server {
    listen 80;
    server_name exemple.com;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
```

Dans cet exemple:

- `listen 80;` indique à Nginx d'écouter sur le port 80 (HTTP).
- `server_name exemple.com;` spécifie le nom de domaine pour lequel cette configuration est applicable.
- `location / { ... }` définie le répertoire racine pour les fichiers statiques (`/var/www/html`) et les fichiers d'index (`index.html` ou `index.htm`).

Pour appliquer cette configuration, sauvegardez-la dans un fichier dans le répertoire `/etc/nginx/sites-available` (par exemple `exemple.com`), puis créez un lien symbolique vers ce fichier dans le répertoire `/etc/nginx/sites-enabled`:

```bash
sudo ln -s /etc/nginx/sites-available/exemple.com /etc/nginx/sites-enabled/
```

Enfin, testez la configuration et redémarrez Nginx:

```bash
sudo nginx -t
sudo systemctl restart nginx
```

## Utilisation de Nginx en tant que proxy pour une application Node.js

### Configuration de l'application Node.js

Supposons que vous avez une application Node.js fonctionnant sur le port 3000. Voici un exemple de fichier `server.js`:

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.listen(3000, () => {
    console.log('Server is running on http://localhost:3000');
});
```

### Configuration de Nginx en tant que proxy inverse

Pour configurer Nginx en tant que proxy inverse pour cette application, vous pouvez utiliser la configuration suivante:

```nginx
server {
    listen 80;
    server_name exemple.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Dans cette configuration:

- `proxy_pass http://localhost:3000;` indique à Nginx de rediriger toutes les requêtes vers le port 3000 où votre application Node.js est en cours d'exécution.
- Les directives `proxy_set_header` sont utilisées pour transmettre les en-têtes HTTP originaux au serveur Node.js.

Pour appliquer cette configuration, sauvegardez-la dans un fichier dans le répertoire `/etc/nginx/sites-available/` (par exemple, `exemple.com`), créez un lien symbolique, testez la configuration, et redémarrez Nginx comme précédement.

### Pourquoi utiliser Nginx en tant que proxy pour une application Node.js ?

Utiliser Nginx comme proxy inverse pour une application Node.js présente plusieurs avantages:

- **Amélioration des Performances**: Nginx est capable de gérer de nombreuses connexions simultanées avec une faible empreinte mémoire, ce qui permet de décharger votre application Node.js des tâche de gestion des connexions et de se concentrer sur le traitement des requêtes.
- **Sécurité**: Nginx peut filtrer et gérer les requêtes malveillantes avant qu'elles n'atteignent votre application, ajoutant ainsi une couche supplémentaire de sécurité.
- **Gestion des Erreurs et Redondance**: En de défaillance de votre application, Nginx peut servir des pages d'erreur personnalisées, rediriger les trafic vers des serveurs de secours, ou gérer des scénarios de reprise.
- **Mise en Cache**: Nginx peut mettre en cache les réponses de votre application, ce qui améliore les temps de réponse pour les requêtes répétitives.

### Test et Dépannage

Pour vérifier que votre configuration fonctionne correctement, ouvrez un navigateur web et accédez à `http://exemple.com`. Vous devriez voir le message "Hello World!" de votre application Node.js.

Si vous rencontrez des problèmes, consultez les logs d'erreurs de Nginx (généralement situés dans `/var/log/nginx/error.log`) et de votre application Node.js.

### Schéma de Proxy Inverse

Voici un schéma illustrant comment Nginx agit en tant que proxy inverse pour une application Node.js:

![reverse proxy](/posts/Nginx/reverse_proxy.png "Schéma de proxy inverse")

## Configuration de plusieurs serveurs Nginx

Nginx permet de configurer plusieurs serveurs simultanément, que ce soit pour servir des fichiers statiques ou pour agir en tant que proxy inverse. Voici un exemple de configuration où Nginx sert à la fois des fichiers statiques et agit en tant que proxy pour une application Node.js.

### Exemple de configuration

```nginx
# Serveur pour les fichiers statiques
server {
    listen 80;
    server_name static.exemple.com;

    location / {
        root /var/www/static;
        index index.html index.htm;
    }
}

# Serveur proxy pour l'application Node.js
server {
    listen 80;
    server_name app.exemple.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Structure de Répertoire pour Plusieurs Serveurs

Voici un exemple de structure de répertoires pour les deux serveurs:

```
/var/www/static/
├── index.html
└── images/
    └── logo.png

/var/www/app/
└── (application Node.js)
```

## Configuration Supplémentaire

Nginx offre de nombreuses autres options de configuration pour affiner les performances et la sécurité de vos serveurs:

### Compression gzip

Vous pouvez activer la compression gzip pour réduire la taille des réponse HTTP:

```nginx
http {
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
}
```

### Mise en cache des fichiers statiques

Pour améliorer les performances, vous pouvez configurer la mise en cache des fichiers statiques:

```nginx
location / {
    root /var/www/static;
    index index.html index.htm;
    expires 30d;
    add_header Cache-Control "public, no-transform";
}
```

### Limitation du débit

Pour limiter le débit des connexions afin de prévenir les abus:

```nginx
http {
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=1r/s;

    server {
        location / {
            limit_req zone=mylimit burst=5;
        }
    }
}
```

### Redirection HTTP vers HTTPS

Pour forcer les utilisateurs à utiliser une connexion sécurisée:

```nginx
server {
    listen 80;
    server_name exemple.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name exemple.com;

    ssl_certificate /etc/nginx/ssl/exemple.com.crt;
    ssl_certificate_key /etc/nginx/ssl/exemple.com.key;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}
```

### Sécurisation des en-têtes HTTP

Pour ajouter des en-têtes de sécurité HTTP:

```nginx
server {
    listen 80;
    server_name exemple.com;

    location / {
        root /var/www/html;
        index index.html index.htm;
    }

    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
}
```

## Conclusion

Nginx est un outil puissant et flexible, essentiel. Que ce soit pour servir des fichiers statiques ou agir en tant que proxy inverse pour des applications web, Nginx offre une solution efficace et robuste. En maîtrisant Nginx, vous pouvez améliorer considérablement les performances et la sécurité de vos services web.

J'espère que cet article vous a aidé à mieux comprendre comment utiliser Nginx dans différents scénarios.
