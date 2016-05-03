Episode 1 : Les bases
=====================

I. Qu'est-ce qu'un snap ?
-------------------------

A. A l'origine
**************

  - Paquet d'application initialement prévu pour Snappy Ubuntu Core
  - Pour le cloud depuis Décembre 2014
  - Pour l'IoT courant 2015

B. Et techniquement ?
.....................

  - Tarball en squashfs
  - est montée en lecture seule
  - isolée dans un espace de nom du noyau
  - protégée par seccomp et AppArmor

C. Mais pourquoi faire ?
........................

## II. Qu'est-ce que Snapcraft ?  
### A. Parts

[_A central aspect of a snapcraft recipe is a "part". A part is a piece of software or data that the snap package requires to work or to build other parts. Each part is managed by a snapcraft plugin and parts are usually independent of each other._](https://developer.ubuntu.com/en/snappy/build-apps/#parts)

### B. Plugins  
### C. Interface

### D. Workflow  
  - pull
  - build
  - stage
  - strip
  - snap
