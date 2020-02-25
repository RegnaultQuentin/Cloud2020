# Cloud2020
# Installation

## I. Installation physique


* Installer l'os raspian sur une carte sd pour la raspberry. 

* Avoir deux disques durs et les brancher (vous allez potentiellement avoir besoin d'un cable spécial pour alimenter les disques durs la raspbérry n'étant pas assez puissante).
    

* Brancher la rasberry à votre box.


## II. Mettre en place l'accés à distance


### 1. Configuration


* Pour configurer l'accès à distance (en dehors de votre réseau local) de votre raspberry il faut vous connecter à l'interface web de votre box.

* Vous allez configurer une adresse ip fixe pour votre raspberry, puis faire une redirection de port (voir image si dessous)
<img src="https://cdn.discordapp.com/attachments/582825013690892290/681945539377758222/unknown.png">
Grace à ça vous pouvez vous connecter à votre machine en utilisant vrote ip public.

### 2. Connection SSH

* Avec l'outil Putty vous pouvez vous connecter en ssh via votre ip public et sur le 22

* Depuis le terminal vous aller pouvoir travaillé sur votre machine et envoyé des données quand on aura fini l'installation.

## III. Serveur FTP

### Installation

* Tout d'abord il faut installer le serveur FTP pour cela la commande est :
```
sudo apt install vsftpd
```
### Configuration
* Ouvrez dans un éditeur de texte (ici vim) le fichier de configuration :
```
sudo vim /etc/vsftp.conf
```
* Dans ce fichier il y a quelques lignes à changer
```
local_enable = YES
local_unmask=022
Write_enabled=YES
Ascii_upload_enabled=YES
Ascii_download_enabled=YES
```

* Votre serveur FTP est prêt, vous pouvez vous y connecter via un client ftp comme FileZila

