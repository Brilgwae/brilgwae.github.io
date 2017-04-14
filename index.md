# Jeedom, Jarvis et moi

Il y a quelques mois un collegue a gagné plusieurs amazon echo dot. Tres gentiment il m'en a donné un. 
N'etant pas convaincu par le cote "Amazon ecoute mes conversations". je me suis mis dans la tete de m'en faire
un equivalent qui soit plus privé.

Apres avoir essayé divers truc je suis arrive sur une combinaison [Jeedom](www.jeedom.com) + [Jarvis](http://domotiquefacile.fr/jarvis/) sur un Raspberry Pi 3.

Dans le principe ca repond a mes attentes, mais je n'ai jamais utilise un raspberry, et mon experience Linux est assez limité.

## Installation du Raspberry

### Raspbian
Plutot simple. on flashe [Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/) (avec PIXEL, pas Lite). Sur mac, j'utilise [Etcher](https://etcher.io/). Simple et efficace.

Il faut creer un fichier vide nommé ssh a la racine. Cela permet d'activer l'acces via SSH pour la suite. 

Sur mac dans un terminal on tape
```
cd /Volumes/boot
touch ssh
```

