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
sudo wget https://raw.githubusercontent.com/jeedom/core/stable/install/install.sh
sudo chmod +x install.sh
sudo ./install.sh
sudo reboot
```
et hop, ca prend une plombe mais a la fin, vous devriez pouvoir lancer un navigateur sur l'adresse IP de votre raspberry.

c'est bien aussi de desactiver l'autoswitch hdmi en ajoutant a /boot/config.txt
```
# deactivate HDMI autoswitch
hdmi_ignore_cec_init=1
```

### HA-Bridge
Used to control the lights using Alexa
```
cd
mkdir habridge
cd habridge
wget https://github.com/bwssytems/ha-bridge/releases/download/v4.5.6/ha-bridge-4.5.6.jar
```
then we need to start the service at boot
```
cd /etc/systemd/system
sudo nano habridge.service
```
then copy the below into nano
```
[Unit]
Description=HA Bridge
Wants=network.target
After=network.target

[Service]
Type=simple
WorkingDirectory=/home/pi/habridge
ExecStart=/usr/bin/java -jar -Dconfig.file=/home/pi/habridge/data/habridge.config -Dserver.port=8080 /home/pi/habridge/ha-bridge-4.5.6.jar

[Install]
WantedBy=multi-user.target
```


### Control HDMI
```
#!/bin/bash
# hdmi-tv.sh : control a tv through hdmi-cec
# requires cec-utils package

if [[ $# -eq 1  && $1 == "ON"  ]]; then
        echo 'on 0' | cec-client -s -d 1 RPI
        echo "ON"
elif [[ $# -eq 1  && $1 == "OFF" ]]; then
        echo 'standby 0' | cec-client -s -d 1 RPI
        echo "OFF"
elif [[ $# -eq 1  && $1 == "HDMI1" ]]; then
        hdmi_command(){
                echo 'on 0'
                echo 'tx 4F:82:10:00'
                echo 'q'
                }
        hdmi_command | cec-client -d 1 RPI
        echo "HDMI 1"
elif [[ $# -eq 1  && $1 == "HDMI2" ]]; then
        hdmi_command(){
                echo 'on 0'
                echo 'tx 4F:82:20:00'
                echo 'q'
                }
        hdmi_command | cec-client -d 1 RPI
        echo "HDMI 2"
elif [[ $# -eq 1  && $1 == "HDMI3" ]]; then
        hdmi_command(){
                echo 'on 0'
                echo 'tx 4F:82:30:00'
                echo 'q'
                }
        hdmi_command | cec-client -d 1 RPI
        echo "HDMI 3"
elif [[ $# -eq 1  && $1 == "HDMI4" ]]; then
        hdmi_command(){
                echo 'on 0'
                echo 'tx 4F:82:40:00'
                echo 'q'
                }
        hdmi_command | cec-client -d 1 RPI
        echo "HDMI 4"
else
        echo
        echo -e "Usage"
        echo -e "`basename $0` <ON>|<OFF>"
        echo -e "`basename $0` <HDMI1>|<HDMI2>|<HDMI3>|<HDMI4>"
        echo
        exit 0
fi

echo
```

### Yamaha control
```
#!/bin/bash
# yamaha.sh : control a yamaha amplifier

IP="192.168.0.22"
HDR="Content-Type: text/xml"
URL="YamahaRemoteControl/ctrl"
XML="<?xml version=\"1.0\" encoding=\"UTF-8\" ?>\r\n"

CMD_ON="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Power_Control><Power>On</Power></Power_Control></Main_Zone></YAMAHA_AV>"
CMD_OFF="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Power_Control><Power>Standby</Power></Power_Control></Main_Zone></YAMAHA_AV>"

CMD_MUTE="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Volume><Mute>On</Mute></Volume></Main_Zone></YAMAHA_AV>"
CMD_UNMUTE="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Volume><Mute>Off</Mute></Volume></Main_Zone></YAMAHA_AV>"

CMD_VOL1="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Volume><Lvl><Val>-"
CMD_VOL2="0</Val><Exp>1</Exp><Unit>dB</Unit></Lvl></Volume></Main_Zone></YAMAHA_AV>"

CMD_INP1="<YAMAHA_AV cmd=\"PUT\"><Main_Zone><Input><Input_Sel>"
CMD_INP2="</Input_Sel></Input></Main_Zone></YAMAHA_AV>"

if [[ $# -eq 1  && $1 == "ON"  ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_ON}" http://${IP}/${URL}
        echo "ON"
elif [[ $# -eq 1  && $1 == "OFF" ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_OFF}" http://${IP}/${URL}
        echo "OFF"
elif [[ $# -eq 1  && $1 == "MUTE" ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_MUTE}" http://${IP}/${URL}
        echo "MUTE"
elif [[ $# -eq 1  && $1 == "UNMUTE" ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_UNMUTE}" http://${IP}/${URL}
        echo "UNMUTE"
elif [[ $# -eq 2  && $1 == "VOLUME" ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_VOL1}${2}${CMD_VOL2}" http://${IP}/${URL}
        echo "VOLUME `echo $2`"
elif [[ $# -eq 2  && $1 == "INPUT" ]]; then
        curl -H "${HDR}" -X POST -d "${XML}${CMD_ON}" http://${IP}/${URL}
        curl -H "${HDR}" -X POST -d "${XML}${CMD_INP1}${2}${CMD_INP2}" http://${IP}/${URL}
        echo "INPUT `echo $2`"
else
        echo
        echo -e "Usage"
        echo -e "`basename $0` <ON>|<OFF>"
        echo -e "`basename $0` <MUTE>|<UNMUTE>"
        echo -e "`basename $0` VOLUME 0-80 (higher is quieter)"
        echo -e "`basename $0` INPUT <INPUTNAME>"
        echo
        exit 0
fi

echo
```

### ps4 waking
install ps4-waker and then use
```
#!/bin/bash
# ps4.sh : Start/stop a playtation 4
# requires nodejs and ps4-waker
# require pi as part of sudoers
# also needs initial standalone configuration

if [[ $# -eq 1  && $1 == "ON"  ]]; then
        sudo ps4-waker
        echo "PS4 ON"
elif [[ $# -eq 1  && $1 == "OFF" ]]; then
        sudo ps4-waker standby
        echo "PS4 OFF"
else
        echo
        echo -e "Usage"
        echo -e "`basename $0` <ON>|<OFF>"
        echo
        exit 0
fi

echo
```

