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

* Avec l'outil Putty vous pouvez vous connecter en ssh via votre ip public et sur le port 22

* Depuis le terminal vous allez pouvoir travailler sur votre machine et envoyer des données quand on aura fini l'installation.

## III. Serveur FTP et montage des disques durs

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

* Votre serveur FTP est prêt, vous pouvez vous y connecter via un client ftp comme FileZila. C'est avec ce serveur que vos données vont être envoyer ou récupérer sur votre machine.


## Borgbackup/Borgmatic

### Installation 

* Tout d'abord il vous faut installer les modules <br>
Pour borgbackup 
```
sudo apt install borgbackup
```

Pour borgmatic c'est assez similaire

```
sudo apt install borgmatic
```

### Initialisation 
* Pour effectuer des sauvegardes il vous faut avant tout un dépo, il sera à l'endroit ou les archives seront stockées. La commande :
```
borg init -e repokey nom_du_repo.borg
```
Il va vous demandé une passphrase, voila votre dépo initialisé. 
* Dans mon projet j'ai deux dépos, de ce fait toutes les commandes vont être faite en double sur les deux disques différents.

### Set-up de borgmatic

* Cette commande va générer un fichier de conf borgmatic qui sera utilisé lors des sauvegardes. 

```
generate-borgmatic-config -d nom_du_fichier.yaml
```


* Ensuite vous devrez ouvrir ce fichier avec un éditeur de texte (toujours vim) :
```
vim nom_du_fichier.yaml
```
* Exemple d'un fichier de mon projet 

```
# Where to look for files to backup, and where to store those backups. See
# https://borgbackup.readthedocs.io/en/stable/quickstart.html and
# https://borgbackup.readthedocs.io/en/stable/usage.html#borg-create for details.
location:
    # List of source directories to backup (required). Globs and tildes are expanded.
    source_directories:
        - /datab

    # Paths to local or remote repositories (required). Tildes are expanded. Multiple
    # repositories are backed up to in sequence. See ssh_command for SSH options like
    # identity file or port.
    repositories:
        - /savea
    
    #one_file_system: true

    # backing up special devices such as /dev/zero.
    #read_special: false

    # Record bsdflags (e.g. NODUMP, IMMUTABLE) in archive. Defaults to true.
    #bsd_flags: true
    
    #files_cache: ctime,size,inode

    #local_path: borg1
    
    #remote_path: borg1

    #    - R /
    #    - '- /home/*/.cache'
    #    - + /home/susan
    #    - '- /home/*'
    
    #patterns_from:
    #    - /etc/borgmatic/patterns

    
    #exclude_patterns:
    #    - '*.pyc'
    #    - ~/*/.cache
    #    - /etc/ssl

    #exclude_from:
    #    - /etc/borgmatic/excludes

    #exclude_caches: true

    # Exclude directories that contain a file with the given filename.
    #exclude_if_present: .nobackup

storage:
    
    #encryption_passcommand: secret-tool lookup borg-repository repo-name

    encryption_passphrase: IciLaPassPhrase  
   
    #checkpoint_interval: 1800

#Cette section sert à supprimer les archive au bout d'un certain temps
retention:
    # Keep all archives within this time interval.
    #keep_within: 3H

    # Number of secondly archives to keep.
    #keep_secondly: 60

    # Number of minutely archives to keep.
    #keep_minutely: 60

    # Number of hourly archives to keep.
    #keep_hourly: 24

    # Number of daily archives to keep.
    #keep_daily: 7

    # Number of weekly archives to keep.
    keep_weekly: 2

    # Number of monthly archives to keep.
    #keep_monthly: 6

    # Number of yearly archives to keep.
    #keep_yearly: 1

    #prefix: sourcehostname

# Shell commands or scripts to execute before and after a backup or if an error has occurred.
# IMPORTANT: All provided commands and scripts are executed with user permissions of borgmatic.
# Do not forget to set secure permissions on this file as well as on any script listed (chmod 0700) to
# prevent potential shell injection or privilege escalation.
#hooks:
    # List of one or more shell commands or scripts to execute before creating a backup.
    #before_backup:
    #    - echo "`date` - Starting a backup job."

    # List of one or more shell commands or scripts to execute after creating a backup.
    #after_backup:
    #    - echo "`date` - Backup created."

    # List of one or more shell commands or scripts to execute in case an exception has occurred.
    #on_error:
    #    - echo "`date` - Error while creating a backup."

```
(J'ai enlever pas mal de comentaires pour la lisibilité)


* Pour effectuer une sauvegarde la commande est : 
```
borgmatic --config nom_du_fichier.yaml --verbosity 1
```
(Cela peut prendre un peu de temps si suivant la taille des données à sauvegarder)
* Il faut mettre cette commande dans une crontab pour avoir une sauvegarde automatique avec :
```
crontab -e
```

* Pour lister ces sauvegardes :
```
bormatic -c nom_du_fichier.yaml --list
```

* Pour restaurer les archives : 
```
borg extract /chemin/du/depot::nomdel'archive 
```
Les arguments comment -p ou --progress permet de voir la progression de la rétauration


# Voila vous savez comment faire un Cloud maison et avoir des sauvegardes à dispositions !
