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
### B. Plugins  
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
### C. Partie `youtube-dl`  
### D. Partie `ffmpeg`  
## IV. Réorganiser les parties  
## V. Mutualiser les parties  
