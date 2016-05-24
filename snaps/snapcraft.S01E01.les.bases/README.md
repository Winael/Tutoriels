Bienvenue dans ce premier tutoriel traitant du nouveau paquet d’application snap d’Ubuntu et de l’outils de génération, Snapcraft.

Dans cette série de tutoriel nous étudierons ensemble différents cas pratiques de construction de paquets snaps au travers de leurs fichier de génération, le fichier `snapcraft.yaml`. Afin de bien comprendre ce qui va suivre, nous expliquerons, dans ce premier tutoriel, ce que sont les paquets d’applications snap et comment fonctionne Snapcraft

Episode 1 : Les bases
=====================

I. Qu'est-ce qu'un snap ?
-------------------------

Alors qu'est-ce qu'un paquet snap ?

### A. A l'origine

A l'origine, les paquets snaps sont nés avec le projet Snappy Ubuntu Core. Ils sont en quelques sortes la version 2.0 des paquets clicks développés pour Ubuntuphone.

Les premiers paquets snaps sont donc logiquement apparus avec la première sortie de Snappy Ubuntu Core... sur le nuage de Microsoft, Microsoft Azure, en décembre 2014. Les autres plateformes comme Google Compute ou Amazon Web service suivront Azure quelques jours plus tard.

Début 2015, Snappy Ubuntu Core et ses paquets snaps débarquaient sur l'Internet des Objets. C'est l'apparition de drones, robots, refrigidérateurs, switchs, gateway, qui utilisent Snappy Ubuntu Core et ses snaps.

Un an plus tard, c'est à dire cette année, lors de l'UbuCon Summit qui s'est déroulé à LA, Mark Shuttleworth a annoncé l'arrivée des paquets snaps sur la version traditionnelle du poste de travail pour Ubuntu 16.04 LTS.

### B. Et techniquement ?

Alors techniquement, qu'est-ce qu'un paquet snap ? Contrairement à ce qu'en pensent certains, il ne s'agit en aucun cas d'un paquet d'installation, tes que le serait les paquets MSI chez Microsoft.

Un paquet snap est en réalité un système de fichier encapsulé dans une archive squashfs, montée en lecture seule et protégée via des profiles Seccomp, permettant de filtrer les processus, et des profiles AppArmor, limitant l'accès à cette capsule sur la base de l'adresses MAC virtuelle des paquets. 

### C. Comment ça fonctionne ?

Les paquets snaps sur le poste de travail et le serveur fonctionne de la manière suivante :

- Les paquets snaps sont installés dans le système de fichier de la machine hôte. Ils seront visibles dans le répertoire `/snap/$name/$version/`
- Quand un snap est lancé :
    - Un espace de nom esclave est créé
    - un répertoire `/tmp` privé est créé
    - `ubuntu-core-launcher` monte des liaisons vers les répertoire `/bin`, `/lib`, `/lib64`, `/sbin`, `/usr` du paquet snap `ubuntu-core`
- Le lanceur `ubuntu-core-launcher` applique le confinement AppArmor/Seccomp
- L'application est lancée. Elle peut voir les points de montage `/dev`, `/proc`, `/sys`, `/media` ou autres du système hôte, mais atténués par le profile AppArmor.

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/desktop/get-started/#diving-deeper-into-snaps</em></p>

### D. Mais pourquoi faire ?

#### 1. Des mise à jours transactionnelles

Les mises à jours des paquets snaps sont transactionnelles. Cela veut dire quelles ne peuvent pas se retrouver dans un état entre deux comme cela arrive parfois avec les mise à jour de paquets traditionnels

#### 2. Des paquets isolés les un des autres

Les paquets snaps sont encapsulés avec toutes les librairies dont ils ont besoin. C'est à dire qu'une mise à jour d'un paquet snap ne peut pas entrainer de régression tant au niveau du système d'exploitation qu'au niveau des autres applications

#### 3. Des BLOBs immutables

Les paquets snaps sont des archives squashfs, en lecture seule donc. Aucune modification ne peut être faite au binaire. L'application est garantie fonctionner comme elle a été développée. Personne ne peut l'altérer

#### 4. Des paquets confinés

C’est l’assemblage du système de fichier squashfs, des filtres processus Seccomp et des profile d'accès par adresse MAC AppArmor qui garantie le confinement.

A tout moment l'utilisateur peut connaitre les interactions entre les paquets snaps et les activer/désactiver au besoin.

**Attention :** Ces filtres ne peuvent pas protéger des accès aux fenêtres entre applications du à l'architecture du serveur graphique X11. L'isolation graphique ne pourra être apporté qu'au travers un serveur graphique sécurisé tel que MIR ou Wayland

## II. Qu'est-ce que Snapcraft ? 

Snapcraft est un outil facilitant la construction et la création de paquet d'applications snap. Il y est simple d'y ajouter des composants depuis différentes sources et de les construire avec différentes technologies ou solutions. 

### A. Les parties d'Application

L'un des points centraux de Snapcraft réside dans l'utilisation de « parties » d'application. Une partie d'application est une pièce de logiciel ou de données requises par un paquet snap pour fonctionner ou construire d'autres « parties ». Chaque « partie » d'application est gérée par des greffons Snapcraft et sont généralement indépendante les unes des autres.

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/snappy/build-apps/#parts</em></p>

### B. Plugins  

Chaque _« partie »_ de logiciel a un plugin qui lui est associé fournissant le mécanisme permettant de la construire. Il existe une variété de plugins déjà inclus pour, permettant de constuire des projets basés sur Python 2 et 3, sur Go, Java, Cmake ou autotools. (Nous y reviendrons plus en détail lors de l'études des fichiers `snapcraft.yaml` de différents projets)

### C. Interface

Une _interface_ donne à un paquet snap la possibilité d'utiliser des ressources fournies par un autre paquet snap, incluant bien évidement le paquet snap du système d'exploitation (`ubuntu-core` étant lui même un paquet snap).

### D. Workflow 

Voici une description du workflow qui s’exécute lorsque l’utilisateur exécute la commande snapcraft depuis le répertoire dans lequel se trouve le fichier snapcraft.yaml :

#### 1. Récupération des sources (_« Pull »_)

C'est la première étape. Elle consiste à récupérer du contenu depuis un dépôt git, bazaar ou à télécharger un binaire.

Le contenu de chaque partie sera stocké dans le répertoire `parts/<part-name>/src`

#### 2. Construction des parties (_« Build »_)

Cette étape fait suite à l'étape de _« Récupération des sources »_. 

Chaque partie est construite dans son répertoire `parts/<part-name>/build` et s'installe dans son répertoire `parts/<part-name>/install`

#### 3. Etape de transit (_« Stage »_)

Après l'étape de construction de chaque parties, elles sont combinées en une seule arborescence de répertoires que l'on appelle «zone de transit» et qui se trouve dans le répertoire `./stage` 

C'est à l'intérieur de cette zone que toutes les parties peuvent partager des actifs tels que des librairies.

#### 4. Dépouillement du paquet snap (_« Strip »_)

Lors de cette étape de dépouillement, les données sont déplacées dans le répertoire `./snap` qui ne contiendra que le contenu qui sera mis dans le paquet snap final, contrairement à la zone de transit, dont on a parlé à l'instant, qui peut inclure des fichiers de développement non destinés au paquet.

Les informations de métadonnées de votre projet snap vont aussi être placées dans le répertoire `./snap/meta`.

Ce répertoire `./snap` est très utile pour inspecter ce qui se passe dans votre paquet snap et faire des post-traitements finaux sur la sortie de `snapcraft.`

#### 5 . Construction du paquet snap (_« Snap »_)

La dernière étape construit l'archive squashf (paquet snap) dans le répertoire de travail.

#### Etape par étape

Chaque étape du workflow peut être simulée en la précisant en argument de Snapcraft :

````sh
    $ snapcraft pull
    $ snapcraft build
    $ snapcraft stage
    $ snapcraft strip
    $ snapcraft snap 
````

### Conclusion

Voilà, c'est tout pour aujourd'hui. Nous avons vu les bases pour comprendre ce qui va suivre. Dans le prochain épisode nous étudieront le fonctionnement du fichier `snapcraft.yaml` permettant de construire une application de téléchargement de vidéo YouTube, YouTube-downloader, basée sur l'utilitaire `youtube-dl`.

Merci d'avoir suivi cette vidéo, n'hésitez pas à faire part de vos critiques dans les commentaires. Pouce vert si vous avez aimé, pouce rouge si vous n'avez pas aimé, mercid e votre retour.

A bientôt

