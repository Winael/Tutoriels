Episode 1 : Les bases
=====================

I. Qu'est-ce qu'un snap ?
-------------------------

### A. A l'origine

A l'origine, les snaps sont nés avec le projet Snappy Ubuntu Core. Ils sont en quelques sortes la version 2.0 des paquets clicks développés pour Ubuntuphone.

Les premiers snaps sont donc logiquement apparus avec la première sortie de Snappy Ubuntu Core... sur le nuage, en décembre 2014 (d'abord sur Microsoft Azure, puis sur les autres plateformes quelques jours plus tard).

Début 2015, Snappy Ubuntu Core et ses snaps débarquaient sur l'Internet des Objets. C'est l'apparition de drones, robots, refrigidérateurs, switchs, gateway, utilisant Snappy et les snaps.

Un an plus tard, lors de l'UbuCon Summit de cette années qui s'est déroulé à LA, Mark Shuttleworth a annoncé l'arrivée des snaps sur la version traditionnelle du poste de travail pour Ubuntu 16.04 LTS.

### B. Et techniquement ?

Alors qu'est-ce qu'un snaps ? Contrairement à ce qu'en pensent certains, il ne s'agit en aucun cas d'un paquet d'installation, tes que le serait MSI chez Microsoft.

Un snaps est en réalité un système de fichier encapsulé dans une archive squashfs, qui est montée en lecture seule dans un espace de nom dédié du noayu Linux. La capsule est protégée via des profiles Seccomp, permettant de filtrer les processus, et des profile AppArmor, limitant l'accès sur la base de l'adresse MAC de la capsule. 

### C. Comment ça fonctionne ?

Snappy Ubuntu Core sur le poste de travail et le serveur fonctionne de la manière suivante :

- Les snaps sont installés dans le système de fichier de la machine hôte. Il sera visible dans le répertoire `/snap/$name/$version/`
- Quand un snap est lancé :
    - Un espace de nom esclave est créé
    - un répertoire `/tmp` privé est créé
    - `ubuntu-core-launcher` monte à l'aveugle les répertoire `/bin`, `/lib`, `/lib64`, `/sbin`, `/usr` depuis le snap d'`ubuntu-core`

Le lanceur `ubuntu-core-launcher` applique le confinement AppArmor/Seccomp
L'application est lancée. Elle peut voir les points de montage `/dev`, `/proc`, `/sys`, `/media` ou autres du système hôte, mais atténué par le profil AppArmor.

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/desktop/get-started/#diving-deeper-into-snaps</em></p>

### D. Mais pourquoi faire ?

#### 1. Des mise à jours transactionnelles

Les mises à jours des snaps sont transactionnelles. Cela veut dire quelles ne peuvent se retrouver dans un état entre deux comme cela arrive parfois avec les mise à jour de paquets traditionnels

#### 2. Des paquets isolés les un des autres

Les snaps sont encapsulé avec toutes les librairies dont ils ont besoin. C'est à dire qu'une mise à jour d'un snaps ne peut pas entrainer de régression tant au niveau du système que pour les autres applications

#### 3. Des paquets confinés

Les snaps sont confinés au niveau du noyau et sécurisé au travers de profils de filtre processus Seccomp et de profil d'accès par adresse MAC AppArmor.

A tout moment l'utilisateur peut connaitre les interactions entre snaps et les activer/désactiver au besoin.

**Attention :** Ces filtres ne peuvent protéger des accès aux fenêtres entre applications du à l'architecture du serveur graphique X11. L'isolation graphique ne pourra être apporté qu'au travers un serveur graphique sécurisé tel que MIR

#### 4. Des BLOBs immutables

Les paquets snaps sont des archives squashfs, donc en lecture seule. Aucune modification ne peut être fait au binaire. L'application est garantie fonctionner comme elle a été développée. Personne ne peut l'altérer

## II. Qu'est-ce que Snapcraft ? 

Snapcraft est un outils facilitant la construction et l'empaquetement d'applications en snap. Il est donc simple d'ajouter des composants depuis différentes sources et de les construire avec différentes technologies ou solutions. 

### A. Les parties d'Application

L'un des points centraux de Snapcraft réside dans l'utilisation de « parties » d'application. Une partie d'application est une pièce de logiciel ou de données requises par une snap pour fonctionner ou construire d'autres snaps. Chaque « partie » d'application est gérée par des greffons Snapcraft et sont généralement indépendante les unes des autres.

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/snappy/build-apps/#parts</em></p>

### B. Plugins  

Chaque _« partie »_ de logiciel a un plugin qui lui est associé fournissant le mécanisme permettant de la construire. Il existe une variété de plugins déjà inclus pour, permettant de constuire des projets basés sur Python 2 et 3, sur Go, Java, Cmake ou autotools. (Nous y reviendront plus en détail lors de l'études des fichiers `snapcraft.yaml` de différents projets)

### C. Interface

Une _interface_ donne à une snap la possibilité d'utiliser des ressources fournies par une autre snap, incluant bien évidement la snap du système d'exploitation (`ubuntu-core` étant lui même un snap).

### D. Workflow 

Voici une description du workflow qui s’exécute lorsque l’utilisateur exécute la commande snapcraft depuis le répertoire dans lequel se trouve le fichier snapcraft.yaml :

#### 1. Récupération des sources (_« Pull »_)

Première étape. Récupération du contenu depuis un dépôt git ou téléchargement de binaire.

Le contenu de chaque partie sera stocké dans le répertoire `parts/<part-name>/src`

#### 2. Construction des parties (_« Build »_)

Cette étape fait suite à l'étape de _« Récupération des sources »_. 

Chaque partie est construite dans son répertoire `parts/<part-name>/build` et s'installe dans son répertoire `parts/<part-name>/install`

#### 3. Etape de transit (_« Stage »_)

Après l'étape de construction de chaque parties, elles sont combinées en une seule arborescence de répertoires que l'on appelle «zone de transit» et qui se trouve dans le répertoire `./stage` 

C'est à l'intérieur de cette zone que toutes les parties peuvent partager des actifs tels que des librairies.

#### 4. Dépouillement du snap (_« Strip »_)

Lors de cette étape de dépouillement, les données sont déplacées dans le répertoire `./snap` qui ne contiendra que le contenu qui sera mis dans le paquet snap final, contrairement à la zone de transit, dont on a parlé à l'instant, qui peut inclure des fichiers de développement non destinés au paquet.

Les informations de métadonnées Snappy de votre projet vont aussi être placées dans le répertoire `./snap/meta`.

Ce répertoire `./snap` est très utile pour inspecter ce qui se passe dans votre application snap et faire des post-traitements finaux sur la sortie de `snapcraft.`

#### 5 . Construction du paquet snap (_« Snap »_)

La dernière étape construit l'archive squashf (paquet snap) dans le répertoire de travail.

#### 6. Etape par étape

Chaque étape du workflow peut être simulée en la précisant en argument de Snapcraft :

````sh
    $ snapcraft pull
    $ snapcraft build
    $ snapcraft stage
    $ snapcraft strip
    $ snapcraft snap 
````

