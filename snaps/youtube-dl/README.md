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

How does it all work?

Snappy Ubuntu Core sur le poste de travail et le serveur fonctionne de la manière suivante :

- Les snaps sont installés dans le système de fichier de la machine hôte. Il sera visible dans le répertoire `/snap/$name/$version/`
- Quand un snap est lancé :
    - Un espace de nom esclave est créé
    - un répertoire `/tmp` privé est créé
    - `ubuntu-core-launcher` monte à l'aveugle les répertoire `/bin`, `/lib`, `/lib64`, `/sbin`, `/usr` depuis le snap d'`ubuntu-core`

Le lanceur `ubuntu-core-launcher` applique le confinement AppArmor/Seccomp
L'application est lancée. Elle peut voir les points de montage `/dev`, `/proc`, `/sys`, `/media` ou autres du système hôte, mais atténué par le profil AppArmor.

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/desktop/get-started/#diving-deeper-into-snaps</em></p>

### C. Mais pourquoi faire ?



## II. Qu'est-ce que Snapcraft ?  
### A. Les parties

L'un des points centraux de Snapcraft réside dans l'utilisation de « parties » de logiciel.

[_A central aspect of a snapcraft recipe is a "part". A part is a piece of software or data that the snap package requires to work or to build other parts. Each part is managed by a snapcraft plugin and parts are usually independent of each other._]()

<p><em><strong>Source :</strong> https://developer.ubuntu.com/en/snappy/build-apps/#parts</em></p>

### B. Plugins  
### C. Interface

### D. Workflow  
  - pull
  - build
  - stage
  - strip
  - snap

