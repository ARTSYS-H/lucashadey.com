---
title: 'Wormhole: Transférer des Secrets'
summary: 'Né de la nécessité de partager des informations sensibles de manière sécurisée, WormHole offre une approche unique et cryptographiquement solide.'
tags:
  - tools
date: 2024-05-20 13:56:55
---


Né de la nécessité de partager des informations sensibles de manière sécurisée, **WormHole** offre une approche unique et cryptographiquement solide. En tant qu'administarteur système, comprendre et maîtriser **WormHole** n'est pas seulement un atout, mais une nécessité dans notre quête incessante de sécurité et d'efficacité.

## Installation du Client

Après avoir compris l'importance cruciale de **WormHole** dans la sécurisation des transferts de secrets, il est temps de passer à la pratique :
installer et configurer **WormHole** sur votre système.

```
pip install magic-wormhole --user

ou

pipx install magic-wormhole
```

Cette commande installe la dernière version de **Wormhole** disponible dans les dépôts officiels.

## Transfert de Fichiers Sécurisé avec Wormhole

Après avoir installé **WormHole**, nous sommes prêts à explorer son utilisation la plus courante : le transfert sécurisé de fichiers. Cette fonctionnalité est particulièrement utile dans les opérations quotidiennes, où le partage rapide et sécurisé de fichiers est une nécessité.

### Envoyer un Fichier

Pour envoyer un fichier, la commande est simple et directe. Voici comment procéder :

```
wormhole send /tmp/airflow-1.png

Sending 87.0 kB file named 'airflow-1.png'
Wormhole code is: 78-insurgent-stagnate
On the other computer, please run:

wormhole receive 78-insurgent-stagnate
```

Lorsque vous exécutez cette commande, **WormHole** génère un code secret, également appelé "Wormhole code". Ce code est à communiquer au destinataire pour qu'il puisse recevoir le fichier.

**Recevoir un Fichier** : De l'autre côté, le destinataire utilisera ce code pour recevoir le fichier. La commande pour le destinataire est tout aussi simpple :

```
wormhole receive 78-insurgent-stagnate
```

Il est important de souligner que la sécurité offerte par **WormHole** est robuste. Le code secret est une mesure de sécurité supplémentaire, s'assurant que seul le destinataire prévu peut accéder au fichier envoyé. De plus, tout le processus est chiffré de bout en bout, garantissant que vos données restent privées et sécurisées pendant le transfert.

### Envoi de texte

La procédure pour envoyer du texte est aussi simple que celle pour les fichiers. Supposons que vous vouliez envoyer une commande sécurisée ou un snippet de code. Voici comment procéder :

```
wormhole send --text "Votre texte ici"
```

### Partage de clé ssh

Dans le monde du **DevOps**, le partage sécurisé de clés SSH est une tâche essentielle. **Magic WormHole** propose des commandes spécifiques pour simplifier ce processus : `wormhole ssh invite` et `wormhole ssh accept`. Ces commandes offrent une méthode sécurisée et efficace pour échanger des clés publiques SSH.

Lorsque vous souhaitez autorisez un nouvelle utilisateur sur votre serveur, la commande `wormhole ssh invite` est un moyen rapide et sûr de le faire. Voici comment l'utiliser :

```
wormhole ssh invite

Now tell the other user to run:

wormhole ssh accept 3-tambourine-repay
```

Cette commande ajoute la clé publique SSH de l'utilisateur distant à votre fichier `~/.ssh/authorized_keys`, facilitant ainsi son accès autorisé à votre serveur. Il existe une option permettant d'indiquer à quel utilisateur appartient la clé.

En réponse à une invitation, utilisez `wormhole ssh accept` pour envoyer votre clé publique SSH de manière sécurisée. La commande est la suivante :

```
Multiple public-keys found:
  0: id_ed25519.pub
  1: id_ed25519.pub
Send which one?:
```

Cette action anvoie votre clé publique SSH à l'expéditeur de l'invitation, lui permettant de vous ajouter facilement à son fichier `authorized_keys`.

## Conclusion

Tout au long de cet article, nous avons exploré les diverses facettes de **WormHole**, un outil inestimable dans l'arsenal de tout administrateur système. De l'installation initiale à l'envoi sécurisé de clés SSH, **WormHole** se révèle être une solution robuste et fiable pour le transfert sécurisé de secrets.

Je vous encourage à intégrer **WormHole** dans votre pratique et à expérimenter par vous-même la facilité et la sécurité qu'il apporte. N'oubliez pas de consulter les ressources ci-dessous pour rester à jour et maximiser l'utilisation de cet outil exceptionnel.

**WormHole** n'est pas seulement un outil, c'est un compagnon de confiance dans votre quête constante pour un environnement sécurisé et efficace.

## Plus d'infos

- **Projet sur GitHub**: [GitHub - Magic Wormhole](https://github.com/magic-wormhole/magic-wormhole)
- **Documentation**: [Wormhole Docs](https://magic-wormhole.readthedocs.io/en/latest/)
