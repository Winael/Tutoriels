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


### C. Partie `youtube-dl`  
### D. Partie `ffmpeg`  
## IV. Réorganiser les parties  
## V. Mutualiser les parties  
