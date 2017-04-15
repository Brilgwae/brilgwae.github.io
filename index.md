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
donc maintenant on peut se connecter en SSH. Toujours sur mac on peut alors taper dans un terminal
```
ssh pi@IP_ADDRESS
```
sachant que le mot de passe est raspberry

bon je laisse de cote la securite, hein !!! c'est mal je sais mais je suis en train de jouer. on verra plus tard. si vous voulez essayer utilisez la commande passwd pour changer le mot de passe par defaut.

pour installer jeedom, on a plus qu'a faire
```
sudo apt-get update
sudo apt-get upgrade
wget https://raw.githubusercontent.com/jeedom/core/stable/install/install.sh
chmod +x install.sh
./install.sh
```
et hop, ca prend une plombe mais a la fin, vous devriez pouvoir lancer un navigateur sur l'adresse IP de votre raspberry.
