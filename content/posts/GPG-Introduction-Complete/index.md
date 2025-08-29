+++
date = '2025-04-12T16:42:49+02:00'
title = 'GPG - Introduction Complète'
summary = 'Vous apprendrez peut être une chose ou deux sur GPG, les clés, la signature et le chiffrement.'
tags = [ "linux", "tools" ]
+++

Commençons directement :

## Créer une clé

Pour créer une clé, lancez la commande :

```shell
gpg --gen-key
```

Si cette commande ne vous guide pas dans un processus interactif, alors exécutez la commande :

```shell
gpg --full-generate-key
```

Vous obtiendrez la sortie suivante :

```shellsession
gpg (GnuPG) 2.2.17; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory '/Users/lucashadey/.gnupg' created
gpg: keybox '/Users/lucashadey/.gnupg/pubring.kbx' created
Please select what kind of key you want:
    (1) RSA and RSA (default)
    (2) DSA and Elgamal
    (3) DSA (sign only)
    (4) RSA (sign only)
Your selection?
```

Pour sélectionner l’option RSA par défaut, vous pouvez presser `Enter`.

```shellsession
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
```

Pour la taille de la clé, il est recommandé de choisir 4096 bits.

```shellsession
Please specify how long the key should be valid.
        0 = key does not expire
    <n>  = key expires in n days

    <n>w = key expires in n weeks
    <n>m = key expires in n months
    <n>y = key expires in n years
Key is valid for? (0)
```

La meilleur pratique consiste à définir une date d’expiration pour vos clés. Mais GnuPG dispose également d’une fonctionnalité pour créer des sous-clés pour ne pas avoir a répéter ce processus. Nous aborderons plus détail la création de sous-clés, mais pour l’instant, vous pouvez la laisser sans expiration. Appuyer sur `Enter`.

```shellsession
GnuPG needs to construct a user ID to identify your key.

Real name:
```

Vous pouvez saisir vos informations. Voici un exemple :

```shellsession
Real name: Lucas Hadey
Email address: me@lucashadey.com
Comment: https://lucashadey.com/
You selected this USER-ID:
    "Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
```

Appuyez sur `O`, puis sur `Enter`. (Si vous souhaitez modifier quelque chose, saisissez simplement la lettre entre parenthèses correspondant au champ que vous souhaitez modifier, puis appuyez sur Entrée !)

GPG devrait maintenant vous demander un mot de passe. Saisissez-le, puis appuyez sur Entrée. Vous devrez le faire deux fois. Cela constituera une protection contre toute personne qui s’introduireait dans votre ordinateur et prendrait la clé. Cette personne ne pourra pas utiliser la clé sans le mot de passe. (Cela ne signifie PAS, cependant, que vous ne devez pas révoquer votre clé. Si vous remarquez que votre clé a été compromise, [révoquez-la immédiatement](#révoquer-une-clé).) 

GPG devrait alors lancer le processus de génération de votre paire de clés publique/privée. Cet avertissement devrait s’afficher :

```shellsession
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
```

Le processus peut prendre un certains temps.

```shellsession
gpg: /Users/Lucashadey/.gnupg/trustdb.gpg: trustdb created
gpg: key 56F399E7A57D6E5D marked as ultimately trusted
gpg: directory '/Users/lucashadey/.gnupg/openpgp-revocs.d' created

gpg: revocation certificate stored as '/Users/lucashadey/.gnupg/openpgp-revocs.d/475C562EBC0520048AE79ADD56F399E7A57D6E5D.rev'
public and secret key created and signed.

pub   rsa4096 2019-12-20 [SC]
    475C562EBC0520048AE79ADD56F399E7A57D6E5D
uid                      Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>
sub   rsa4096 2019-12-20 [E]
```

Bravo ! Vous avez créé votre première clé GPG.

## Liste des clés disponibles

Pour voire les clés disponibles :

```shell
gpg --list-keys
```

Vous devriez obtenir :

```shellsession
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/Users/lucashadey/.gnupg/pubring.kbx
------------------------------------
pub   rsa4096/56F399E7A57D6E5D 2019-12-20 [SC]
    475C562EBC0520048AE79ADD56F399E7A57D6E5D
uid                 [ultimate] Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>
sub   rsa4096/2698CAAB8C3C9C8C 2019-12-20 [E]
```

Notez que certaines distributions et certains systèmes d’exploitation peuvent ne pas afficher l’ID de clé dans son intégralité. Pour voir le résultat correct :

```shell
gpg --list-keys --keyid-format long
```

Vous pouvez également utiliser `short`, si vous avez un petit nombre de clés et que vous ne souhaitez pas saisir l’UUID en entier.

## Publier votre clé

Pour donner votre clé à d’autres personnes, vous devez d’abord la publier.

Tout d’abord, vous devez trouver l’ID de votre clé publique. Pour ce faire, [exécutez la commande depuis « Liste des clés disponibles »](#liste-des-clés-disponibles). Dans la sortie, recherchez cette ligne :

```shellsession
pub   rsa4096/56F399E7A57D6E5D 2019-12-20 [SC]
```

Tout ce qui suit `rsa4096/` devrait être différent pour vous. `56F399E7A57D6E5D`, est votre identifiant de clé publique.

Envoyez-la ensuite au serveur de clés !

```shell
gpg --send-keys YOUR_PUBLIC_KEY_ID_HERE
```

Soit :

```shell
gpg --send-keys 56F399E7A57D6E5D
```

## Réception des clés publiées

Si vous avez suivi les étapes ci-dessus pour publier vos clés publiques, il devrait être simple pour d’autres de récupérer vos clés et de commencer à les utiliser pour chiffrer des données ou vérifier que quelque chose est réellement fait par vous.

```shell
gpg --recv-keys PUBLIC_KEY_ID_HERE
```

Donc, pour que les gens obtiennent mon identifiant de clé (notez que ce n’est PAS ma clé réelle, c’est juste pour ce tutoriel.), ils peuvent simplement exécuter :

```shell
gpg --recv-keys 56F399E7A57D6E5D
```

Si GPG génère une erreur, il se peut que la clé ne soit pas disponible. Pour recherchez une clé :

```shell
gpg --search-keys PUBLIC_KEY_ID_HERE
```

## Utilisation de serveurs de clés personnalisés

Si vous exécutez simplement `gpg --send-keys` ou `gpg --recv-keys` sans aucun autre paramètre, GnuPG utilise généralement le serveur de clés par défaut, qui se trouve à `hkps://hkps.pool.sks-keyservers.net`. Vous pouvez voir que cette URL est un peu unique : au lieu de `https`, elle lit maintenant `hkps`. `hkp` et `hkps` sont ce que GnuPG utilise pour les emplacements des serveurs de clés.

Par exemple, lorsque j’ai dû vérifier mon fichier `.iso` de Manjaro, j’ai dû télécharger les fichiers de clés de Philip Müller, le chef de projet, à partir d’un autre serveur de clés. Leur wiki donne l’adresse suivante : `hkp://pool.sks-keyservers.net`

Donc pour utiliser un serveur de clés personnalisé, spécifiez-le simplement comme paramètre :

```shell
gpg --keyserver hkps://keyserver.url.here <parameters you want to run>
```

Donc, pour récupérer la clé de Philip Müller, j’ai dû exécuter :

```shell
gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 11C7F07E
```

Notez que le paramètre `--keyserver` doit être tapé avant les autres paramètres.

## Créer un certificat de révocation

Cette étape est nécessaire lorsque vous exposez accidentellement votre clé privée. Peut-être avez-vous accidentellement envoyé le mauvais fichier de clé lors de l’envoi de votre clé publique (c’est pourquoi il est préférable des serveurs de clés, comme indiqué ci-dessus)

Exécutez la commande suivante :

```shell
gpg --gen-revoke PUBLIC_KEY_ID_HERE > PUBLIC_KEY_ID_HERE_revoke.asc
```

Pour moi, cela donnera :

```shell
gpg --gen-revoke 56F399E7A57D6E5D > 56F399E7A57D6E5D_revoke.asc
```

Vous obtiendrez :

```shellsession
sec  rsa4096/56F399E7A57D6E5D 2019-12-20 Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>

Create a revocation certificate for this key? (y/N)
```

Appuyer sur `y` et `Enter` pour confirmer.

```shellsession
Please select the reason for the revocation:
    0 = No reason specified
    1 = Key has been compromised
    2 = Key is superseded
    3 = Key is no longer used
    Q = Cancel
(Probably you want to select 1 here)
Your decision?
```

Vous pouvez sélectionner la raison de la révocation.

```shellsession
Enter an optional description; end it with an empty line:
>
```

Cette partie est facultative. Continuez en appuyant sur `Enter`.

```shellsession
Reason for revocation: Key has been compromised
Is this okay? (y/N)
```

Vous pouvez confirmer.

GPG vous demandera ensuite votre mot de passe pour signer ce certificat de révocation avec votre clé privée. Saisissez votre mot de passe, puis appuyez sur `Enter`.

```shellsession
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
```

Fait amusant : Mallory est un alias que la communauté GPG a donné aux « mauvais acteurs ». Plus vous en saurez ! De plus, si vous ne comprenez pas le terme « sortie blindée ASCII », consultez cette section expliquant les différences entre les fichiers GPG classiques et les fichiers GPG blindés ASCII.

Enfin, enregistrez le certificat de révocation dans un endroit sûr. Cela peut être utilisé pour effacer complètement votre paire de clés et la rendre inutilisable, comme le message l’indique ci-dessus. Certaines personnes paranoïaques impriment même la sortie blindée ASCII et lorsqu’il est temps de les utiliser, elles utilisent un logiciel OCR pour le lire ! Mais c’est probablement exagéré.

## Révoquer une clé

Récupérez le certificat que vous avez généré dans la section ci-dessus. Commençons par révoquer localement votre clé :

```shell
gpg --import PUBLIC_KEY_ID_HERE_revoke.asc
```

Suivez ensuite à nouveau les étapes de [« Publier votre clé »](#publier-votre-clé) pour mettre à jour la clé publique dans le serveur de clés et informer les autres que votre clé a été compromise. Notez qu’une fois les modifications publiées, <mark>vous ne pourrez pas arrêter la révocation localement.</mark>

## Annuler la révocation de la clé

Vous ne pouvez annuler la révocation de votre clé **que si vous n’avez pas envoyé** la clé publique révoquée à un serveur de clés.

Pour annuler la révocation d’une clé :

```shell
gpg --expert --delete-key PUBLIC_KEY_ID_HERE
```

Le paramètre `--expert` vous permet de supprimer uniquement la clé publique.

```shellsession
gpg (GnuPG) 2.2.17; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.



pub  rsa4096/56F399E7A57D6E5D 2019-12-20 Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>

Delete this key from the keyring? (y/N)
```

Vous pouvez confirmer.

Nous devons maintenant télécharger la clé publique propre à partir du serveur de clés, qui n’a pas été marquée comme révoquée. Suivez les étapes de [« Réception des clés publiées »](#réception-des-clés-publiées) pour ce processus.

Vous pouvez également [importer une copie locale de la clé publique](#importation-de-clés), si vous disposez d’une sauvegarde avec la clé non marquée comme révoquée.

## Importation de clés

L’importation de clés est également cruciale. Vous l’utiliserez tout le temps pour obtenir des clés à partir d’autres sources (au cas où vous ne feriez pas confiance aux serveurs de clés).

Heureusement, ce processus est assez simple. Vous avez juste besoin d’un fichier GPG ou d’un fichier GPG [ASCII-armor](#quest-ce-que-lascii-armor-). Ils ont généralement les extensions suivantes : `.gpg`, `.asc` ou `.key`.

Exécutez la commande suivante :

```shell
gpg --import KEY_FILE_NAME.asc
```

## Qu’est-ce que l’ASCII-armor ?

ASCII-armor signifie essentiellement qu’il s’agit d’un fichier au format texte et non binaire. Cela est utile, par exemple, lorsque vous envoyez le fichier par courrier électronique.

En général, vous pouvez forcer ASCII-armored en ajoutant le paramètre `--armor` à votre commande, cela garantira que votre fichier est modifiable via un éditeur de texte (même si vous ne voudriez jamais modifier quelque chose généré par GPG).

## Signature des clés

Cela fait également partie du processus de gestion des clés. Ne vous inquiétez pas, nous arrivons à la partie amusante du chiffrement, du déchiffrement et de la certification des données, mais cette partie peut en fait être plus amusante pour vous.

Je dois d’abord expliquer le [« réseau de confiance ».](https://en.wikipedia.org/wiki/Web_of_trust) Qui s’oppose au modèle [« d’Autorité de certification »](https://en.wikipedia.org/wiki/Certificate_authority) utilisé par SSL entre autre.

Pour chiffré son site, le propriétaire doit obtenir un certificat SSL auprès d’une autorité racine, telle que Let’s Encrypt, Comodo, DigiCert, etc. Parmi ces fournisseurs, Let’s Encrypt est le seul à le faire gratuitement, mais la raison pour laquelle ils peuvent le faire gratuitement est qu’ils sont sponsorisés par de nombreuses organisations caritatives, et parce que la fenêtre de signature est courte (c’est-à-dire que vous ne pouvez le faire signer que pendant environ 3 mois). De plus, Let’s Encrypt s’appuie sur un moyen automatisé de vérification des sites Web, ce qui permet de réduire les coûts.

Alors pourquoi les autres fournisseurs demandent-ils de l’argent pour les certificats SSL ? Eh bien, chaque fois qu’ils distribuent des certificats SSL, les fournisseurs doivent s’assurer qu’ils les donnent au véritable propriétaire du site Web. Des problèmes peuvent se produire s’ils distribuent accidentellement des certificats SSL aux mauvais propriétaires/serveurs, ce qui s’est certainement produit dans le passé. Lorsque cela se produit fréquemment, les développeurs de navigateurs suppriment généralement de la liste l’autorité racine spécifique qui cause des problèmes, de sorte que les fournisseurs ont tout intérêt à vérifier correctement les propriétaires.

En opposition, GPG utilise un autre modèle : le « réseau de confiance ». Vous comprendrez pourquoi on l’appelle un réseau dans un instant. Disons que j’ai un ami, A. Je sais qui est A car je l’ai rencontré en personne, donc je signe la clé de A. Plus tard, A rencontre B, un ami proche de A mais un parfait inconnu pour moi. B demande à A si A peut signer sa clé. Parce que A fait confiance à B, A fait de même et signe la clé de B. Maintenant, A me suggère d’organiser une fête avec B. Comme je ne connais pas B, je dois partager mes coordonnées avec B. Je vais sur le site Web de B et je trouve la clé publique de B. Maintenant, je sais que je peux faire confiance à B, car B a la confiance de A, et je fais confiance à A.

Ce modèle fonctionne à merveille, car plus vous signez de clés, plus il en signe, plus le niveau de confiance que les clés appartiennent à la bonne personne est élevé. C’est pourquoi on l’appelle un réseau de confiance. Une fois assemblé, l’ensemble du système ressemble à un réseau :

![Web of Trust](/posts/gpg-introduction-complete/Web_of_Trust.png)

[Les gens organisent même des fêtes dans la vraie vie pour signer des clés !](https://en.wikipedia.org/wiki/Key_signing_party) Bien sûr, cela ne signifie pas que vous devez signer la clé de n’importe quel inconnu… Vous devez toujours vérifier son identité !

![responsible behavior](/posts/gpg-introduction-complete/responsible_behavior.png)

Le processus est simple : vous notez votre identifiant de clé publique sur papier (vous n’apportez généralement pas votre ordinateur contenant votre paire de clés, car cela peut augmenter les risques de fuite de votre clé privée) et vous participez à une soirée de signature de clés. Au cours de l’événement, vous notez autant d’identifiants de clés publiques que possible, à condition d’avoir d’abord vérifié l’identité de chaque individu. Ensuite, vous rentrez chez vous, [vous recevez les clés](#réception-des-clés-publiées), puis vous les signez :

```shell
gpg --sign-key PUBLIC_KEY_ID_OF_STRANGER
```

Et puis vous [les publiez à nouveau](#publier-votre-clé) pour montrer que vous faites confiance à la clé. (Notez que vous devez publier leur clé publique, PAS la vôtre.) Attention : si leur clé n’a pas été publiée en premier lieu, la publier peut être impoli, vous devez donc toujours demander l’autorisation du propriétaire de la clé.

Par exemple, si je voulais signer la clé du développeur Manjaro :

```shell
gpg --sign-key 11C7F07E
```

Ce qui donnera :

```shellsession
pub  rsa2048/CAA6A59611C7F07E
    created: 2012-05-05  expires: never       usage: SC
    trust: unknown       validity: unknown
sub  rsa2048/320011450576724A
    created: 2012-05-05  expires: never       usage: E
[ unknown] (1). Philip Müller (Called Little) <philm@manjaro.org>



pub  rsa2048/CAA6A59611C7F07E
    created: 2012-05-05  expires: never       usage: SC
    trust: unknown       validity: unknown
Primary key fingerprint: E4CD FE50 A2DA 85D5 8C8A  8C70 CAA6 A596 11C7 F07E

    Philip Müller (Called Little) <philm@manjaro.org>

Are you sure that you want to sign this key with your
key "Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>" (56F399E7A57D6E5D)

Really sign? (y/N)
```

Tapez `y`, puis `Enter`. GPG vous demandera votre mot de passe. Tapez-le, puis appuyez sur `Enter` pour signer la clé.

N’oubliez pas que vous devrez peut-être également mettre à jour leur [« niveau de confiance »](#quest-ce-que-le-niveau-confiance-).

## Guide de signature de clés

- N’apportez pas votre ordinateur. Cela augmente les risques d’infection de votre ordinateur par un logiciel malveillant de vol de clés ou d’égarement accidentel de votre clé privée. Publiez simplement votre clé publique, notez votre identifiant de clé publique quelque part, puis apportez-le à l’événement.

- Pour les amis, signez simplement leurs clés. Pour les inconnus (par exemple si vous êtes à un événement international ou autre), vérifiez leur identité avec un document comme leur passeport et assurez-vous que leurs papiers d’identité ne sont pas falsifiés.

- Vous pouvez toujours signer des clés par lots après l’événement de signature de clé. Bizarre, non ? Oui, la signature de clé proprement dite a lieu après l’événement.

## Qu’est-ce que le niveau confiance ?

La confiance, dans GnuPG, est cette note mentale que vous inscrivez dans votre propre trousseau de clés, notant à quel point une personne est « digne de confiance ».

Imaginons que j’ai un ami qui ne signera de manière responsable qu’en vérifiant toutes les identités avant de signer. Je vais donner à cet ami une valeur de confiance plus élevée parce que je crois qu’il prendra de bonnes décisions.

Comparez cela à un événement de signature de clé, où je signe les clés de parfaits inconnus. Je ne sais même pas s’ils sont responsables ou non - je viens juste de les rencontrer ! Par conséquent, lorsque je signe leur clé, je leur attribue une valeur de confiance de inconnu, ou 1.

Notez que si vous signez la clé de quelqu’un, sa valeur de confiance par défaut sera 1 ou inconnu. C’est généralement ce que vous voulez, à moins que vous ne fassiez vraiment confiance à l’individu pour prendre de bonnes décisions.

Pour modifier la valeur de confiance de quelqu’un, utilisez cette commande :

```shell
gpg --edit-key SOMEONES_PUBLIC_KEY_ID
```

Tapez ensuite `trust`, puis `Enter` pour modifier la valeur de confiance. Appuyez sur `Enter` pour enregistrer la valeur de confiance, puis tapez `quit` pour quitter l’application.

## Sauvegarder vos clés

Si vous voulez être paresseux, sauvegardez simplement l’intégralité du répertoire `.gnupg`. Et voilà, gardez-le en sécurité. Mais si vous voulez une approche plus approfondie :

```shell
gpg --export-secret-keys --armor KEY_ID > KEY_ID_secret.asc
```

Cela devrait enregistrer votre clé secrète dans le fichier `KEY_ID_secret.asc`. Sauvegardez-la dans un endroit sûr.

Pour plus de commodité, sauvegardez également votre clé publique. Ce n’est pas strictement obligatoire :

```shell
gpg --export --armor KEY_ID > KEY_ID_public.asc
```

Notez que si jamais vous réimportez vos clés, [vous devrez peut-être redéfinir leur niveau de confiance sur 5](#quest-ce-que-le-niveau-confiance-) pour indiquer à GPG que les clés sont les vôtres.

## Sous-clés !

Les sous-clés sont en fait des clés créées à partir de votre de clé principale. Vous pouvez jouer un peu avec elles, car même si elles sont exposées, vous pouvez facilement les révoquer avec votre clé principale.

Exécutez la commande suivante pour modifier votre clé principale :

```shell
gpg --edit-key YOUR_KEY_ID
```

Cela donnera le résultat suivant :

```shellsession
gpg (GnuPG) 2.2.17; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/56F399E7A57D6E5D
    created: 2019-12-20  expires: never       usage: SC
    trust: ultimate      validity: ultimate
ssb  rsa4096/2698CAAB8C3C9C8C
    created: 2019-12-20  expires: never       usage: E
[ultimate] (1). Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>

gpg>
```

Ajoutez une clé avec `addkey` :

```shellsession
Please select what kind of key you want:
    (3) DSA (sign only)
    (4) RSA (sign only)
    (5) Elgamal (encrypt only)
    (6) RSA (encrypt only)
Your selection?
```

Vous aurez probablement besoin de deux sous-clés : une pour la signature et une pour le chiffrement. Il existe déjà une sous-clé pour le chiffrement, donc pour l’instant, je vais vous montrer comment créer une sous-clé de signature. Tapez `4`, puis appuyez sur `Enter` :

```shellsession
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
```

Encore une fois, utilisez une taille de 4096 bits.

```shellsession
Requested keysize is 4096 bits
Please specify how long the key should be valid.
        0 = key does not expire
    <n>  = key expires in n days
    <n>w = key expires in n weeks
    <n>m = key expires in n months
    <n>y = key expires in n years
Key is valid for? (0)
```

A nouveau, la clé n’expire pas (là encore, vous pouvez simplement la révoquer si vous la perdez). Si vous avez besoin d’une clé temporaire qui doit expirer, utilisez la syntaxe ci-dessus pour définir une limite de temps pour celle-ci. Par exemple, si vous souhaitez qu’elle expire après 3 mois, saisissez 3m. Mais pour l’instant, j’accepterai les valeurs par défaut :

```shellsession
Key does not expire at all

Is this correct? (y/N)
```

Acceptez.

```shellsession
Really create? (y/N)
```

Acceptez à nouveau. Il devrait vous demander votre mot de passe, puis vous avertir de l’entropie, puis créer cette sous-clé :

```shellsession
sec  rsa4096/56F399E7A57D6E5D
    created: 2019-12-20  expires: never       usage: SC
    trust: ultimate      validity: ultimate
ssb  rsa4096/2698CAAB8C3C9C8C
    created: 2019-12-20  expires: never       usage: E
ssb  rsa4096/CFE50DE6144F5CA0
    created: 2019-12-20  expires: never       usage: S
[ultimate] (1). Lucas Hadey (https://lucashadey.com/) <me@lucashadey.com>

gpg>
```

Vous pouvez voir la sous-clé, avec l’ID CFE50DE6144F5CA0. Nous devons maintenant enregistrer les modifications. Tapez save, puis Enter.

Pour exporter la clé et l’utiliser ailleurs, tapez :

```shell
gpg --export-secret-subkeys --armor CFE50DE6144F5CA0! > CFE50DE6144F5CA0_subkey.asc
```

Le point d’exclamation est **extrêmement important**. Sans lui, vous exporterez TOUTES les sous-clés, ce qui n’est peut-être pas ce que vous souhaitez (par exemple, vous ne voudriez pas exporter votre sous-clé de déchiffrement vers un ordinateur sensible !)

Ensuite, sur l’ordinateur cible, tapez :

```shell
gpg --import CFE50DE6144F5CA0_subkey.asc
```

Pour importer la sous-clé ! Si vous souhaitez la révoquer à tout moment, modifiez simplement à nouveau votre clé principale, puis saisissez `revkey`. Enfin, publiez vos modifications sur le serveur de clés.

Pour supprimer votre trousseau principal, exécutez :

```shell
gpg --delete-secret-key KEY_ID
```

Notez que cela supprime également votre trousseau de clés (soyez prudent !).

## Maintenir les clés à jour

Les clés changent constamment. Les gens peuvent signer des clés, les révoquer ou même en générer de nouvelles ! Pour garder votre trousseau de clés à jour, il est donc judicieux d’exécuter cette opération régulièrement :

```shell
gpg --refresh-keys
```

Cela met également à jour votre clé publique, si vous l’avez publiée sur un serveur de clés.

## Chiffrement de fichiers

Pour cela, vous avez besoin de la clé publique de l’autre personne. Tapez la commande suivante :

```shell
gpg --encrypt file.tar.gz
```

GPG vous demandera de saisir l’identifiant de l’utilisateur. Saisissez son nom si celui-ci est unique dans votre trousseau, ou son identifiant de clé publique.

Vous pouvez également le spécifier avec les paramètres :

```shell
gpg --encrypt file.tar.gz -r "Lucas Hadey"
```

N’oubliez pas, vous pouvez protéger votre fichier GPG avec [ASCII-armor](#quest-ce-que-lascii-armor-) !

## Déchiffrer des fichiers

Celle-ci est facile, car votre fichier de clé privée est déjà là. Exécutez la commande suivante :

```shell
gpg --decrypt file.tar.gz.gpg
```

Ou `.asc`, si le fichier est [ASCII-armored](#quest-ce-que-lascii-armor-).

## Signature de fichiers

Selon la manière dont vous souhaitez signer un fichier, plusieurs options s’offrent à vous. Si vous souhaitez créer un fichier compressé et signé, exécutez la commande suivante :

```shell
gpg --sign file.tar.gz
```

Cette commande créera soit `file.tar.gz.gpg` soit `file.tar.gz.asc`, selon que vous ayez fourni ou non l’indicateur `--armor`.

Lorsque vous envoyez le fichier à votre destinataire, il vous suffit de lui envoyer le fichier `file.tar.gz.asc`. Il peut le vérifier à l’aide de la commande ci-dessous et le déchiffrer (décompresser) à l’aide de la commande ci-dessus, sans avoir besoin de votre clé privée. (Cela est dû au fait que le fichier est simplement compressé et signé, il n’est pas chiffré avec votre clé publique.)

Cette méthode présente toutefois des problèmes. Que faire si vous envoyez un e-mail et que vous souhaitez que votre destinataire puisse le lire immédiatement, sans déchiffrer le fichier ?

Eh bien, vous pouvez envoyer à votre destinataire un fichier signé en clair avec votre clé :

```shell
gpg --clearsign file.txt
```

Ceci est utilisé lorsque vous envoyez des documents en texte brut, comme des e-mails. Le message en texte brut est d’abord affiché, puis la signature suit peu de temps après.

C’est très bien, mais qu’en est-il des gros fichiers ? Vous ne voulez pas nécessairement créer un autre fichier pour la signature qui occupera autant de place que le fichier d’origine ! C’est là qu’interviennent les fichiers de signature « détachés ». La plupart des distributions Linux et d’autres gros projets de bibliothèque/framework utilisent cette méthode pour la distribution. Vous créez une signature détachée à l’aide de cette commande :

```shell
gpg --detach-sig file.iso
```

« Attendez ». « Quelle est la différence entre cela et la première commande ? »

La première commande compresse l’intégralité du fichier, le signe, puis génère un fichier GPG basé sur celui-ci. Cela signifie que le transport devient plus facile, car la signature et le fichier deviennent un seul fichier. Cependant, la méthode nécessite que vous compressiez le fichier et que vous dépensiez des ressources pour créer un nouveau fichier, et la méthode nécessite que le destinataire utilise autant de ressources pour décompresser le fichier de signature afin d’obtenir le fichier d’origine.

Cependant, cette commande `detach` crée un fichier de signature qui ne fait que quelques kilo-octets. Celui-ci est à son tour utilisé pour vérifier le fichier d’origine. C’est ce que vous voyez lorsque vous voyez des fichiers sur les sites Web de distribution Linux, « Ubuntu.iso » et « Ubuntu.iso.sig » ou « Ubuntu.iso.asc ». Les deux derniers vérifient le premier fichier, et ils ne font que quelques kilo-octets.

En comparaison, si vous avez utilisé la première commande pour générer la signature, GPG vous avertira que la signature n’est PAS une signature détachée et que GPG n’a donc aucun moyen de vérifier le fichier d’origine. Vous devrez décompresser le fichier signé, puis comparer les sommes de contrôle entre les deux fichiers pour vous assurer qu’ils ne sont pas falsifiés. Mais à ce stade, vous n’avez pas besoin de télécharger le fichier d’origine. C’est pourquoi la première commande doit être réservée aux fichiers de petite taille.

> **TL;DR** - `Clearsign` pour les e-mails et les fichiers en texte brut, `detach` pour les gros fichiers (comme .isos), et enfin compressez et signez pour les fichiers normaux (quelques mégaoctets).

## Vérification des fichiers

```shell
gpg --verify file.tar.gz.gpg
```

## Conclusion

Merci d’avoir suivi ce long article ! J’espère que vous avez appris une chose ou deux sur GPG, les clés, la signature et le chiffrement.

---

Article traduit de l’anglais, [GPG - The Complete Crash Course](https://ericswpark.com/blog/2020/2020-02-25-gpg-the-complete-crash-course/) par [Eric Park](https://ericswpark.com/).
