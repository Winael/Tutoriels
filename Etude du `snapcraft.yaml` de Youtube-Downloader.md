# Etude du `snapcraft.yaml` de Youtube-Downloader

Lien vers l'url du `snapcraft.yaml` de Youtube-Downloader : http://bazaar.launchpad.net/~snappers/snappy-desktop-examples/trunk/view/head:/youtube-dl/snapcraft.yaml

## I. Qu'est-ce qu'un snap ?  
### A. A l'origine

  - Paquet d'application initialement prévu pour Snappy Ubuntu Core
  - Pour le cloud depuis Décembre 2014
  - Pour l'IoT courant 2015

### B. Et techniquement ?

  - Tarball en squashfs
  - est montée en lecture seule
  - isolée dans un espace de nom du noyau
  - protégée par seccomp et AppArmor

## II. Qu'est-ce que Snapcraft ?  
### A. Workflow  
### C. Parts

[_A central aspect of a snapcraft recipe is a "part". A part is a piece of software or data that the snap package requires to work or to build other parts. Each part is managed by a snapcraft plugin and parts are usually independent of each other._](https://developer.ubuntu.com/en/snappy/build-apps/#parts)

### B. Plugins  
### C. Interfaces
## III. Etude du `snapcraft.yaml` de Youtube-Downloader  
### A. Meta-datas  

````yaml
name: youtube-dl
version: 2016.03.27
summary: Youtube Downloader
description: |
  youtube-dl is a small command-line program to download videos from
  YouTube.com and a few more sites.
````

- **name :** Nom du snap
- **version :** Version du snap
- **summary :** Résumé de ce qu'est le snap
- **description :** Description du snap

### B. Apps  

````yaml
apps:
  run:
    command: usr/bin/python3 $SNAP/usr/bin/youtube-dl
    plugs: [home, network, network-bind]
````

[_apps (yaml subsection) A map of keys for applications. These are either daemons or command line accessible binaries.
command (string) Specifies the internal command to expose. If it is a daemon this command is used to start the service._](https://developer.ubuntu.com/en/snappy/build-apps/snapcraft-syntax/)

- **`run` :** Définition de la méthode `youtube-dl.run`
- **`command` :** commande lancée par la méthode `youtube-dl.run`. Ici le script python3 `$SNAP/usr/bin/youtube-dl` lancé via Python3, `$SNAP` ayant pour valeur le point de montage du snap.
- **`plugs` :** Liste des interfaces sur lesquelles pourra se connecter le snap.

[_The snap defines 3 plugs for the home, network and network-bind interfaces. All but the home interface plug automatically connect to the provider's slot with the same name and are thus granted access to the relevant resources. As writing and reading to the /home part of the filesystem is considered a sensitive operation, the plug is intendedly not autoconnected for users upon snap installation. For security reasons, users must explicitly acknowledge that they agree for this app to access the filesystem.

While in the future users will be prompted in a more interactive way, at the time of writing, the way to grant access to the app to the /home filesystem is to manually connect the plug and slot ends of the home interface with this command:

sudo snap connect youtube-dl:home ubuntu-core:home
Note how the connect subcommand puts together the consumer snap's plug (youtube-dl:home) with the provider snap's slot (ubuntu-core:home)_](https://developer.ubuntu.com/en/desktop/examples/#snap-python)

[List of interface](https://github.com/ubuntu-core/snappy/blob/master/docs/interfaces.md#supported-interfaces---basic)

### C. Partie `youtube-dl`  

#### 1. Etude de la documentation officielle de `YouTube-dl`

````yaml
  youtube-dl:
    plugin: python3
    source: https://github.com/rg3/youtube-dl.git
    source-type: git
````

- **plugin :** `youtube-dl` est écrit en Python. Le plugin `python3`
### D. Partie `ffmpeg`  
## IV. Réorganiser les parties  
## V. Mutualiser les parties  
