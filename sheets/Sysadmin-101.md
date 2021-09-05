# Sysadmin 101 · Neo4j on Centos/RedHat

## Contrôle du système et des services

Démarrer/arrêter/redémarrer le service Neo4j:

```shell
$ sudo systemctl {start|stop|restart} neo4j
```

Consulter l'état du service:

```shell
$ sudo systemctl status neo4j
```

Consulter les journaux du service:

```shell
$ sudo journalctl -ef -u neo4j
```

Modifier la configuration du service:

```shell
$ sudo systemctl edit neo4j
```

Voir la configuration du service:

```shell
$ sudo systemctl cat neo4j
```

Démarrer Neo4j en mode exécution immédiate (pour déboguer):

```shell
$ sudo -uneo4j /usr/bin/neo4j console
```

Redémarrer la machine:

```shell
$ sudo reboot
```

## Transférer sources du PTE vers VM

### Archive des sources

```PowerShell
PS> git archive --format=tar.gz -o ../‹project›-YYYYMMDD.tar.gz --prefix=‹project›/ master
```

### Transfert du PTE vers VM

```PowerShell
PS> sftp ‹youruser›@‹hostname›
sftp> put ‹project›-YYYYMMDD.tar.gz
Uploading ‹project›-YYYYMMDD.tar.gz to /home/‹youruser›/‹project›-YYYYMMDD.tar.gz
‹project›-YYYYMMDD.tar.gz 
```

### Extraire sources sur VM

```bash
$ tar zxvf ‹project›-YYYYMMDD.tar.gz
$ cd ‹project›
```

### Installer les dépendances Python

```bash
‹project›$ pip3 install --user --proxy=‹proxy-hostname›:3128 -r requirements.txt
```

## Description du système

#### Adresse du système

```bash
$ hostname
‹hostname.fqdn›
```

#### Version du système

```bash
$ uname -a
Linux ‹hostname› 4.18.0-80.11.2.el8_0.x86_64 #1 SMP Sun Sep 15 11:24:21 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux

$ hostnamectl
   Static hostname: ‹hostname›
         Icon name: computer-vm
           Chassis: vm
        Machine ID: ABCDEFGHIJKLMNOPQRSTUVWXYZ012345
           Boot ID: 012345ZYXWVUTSRQPONMLKJIHGFEDCBA
    Virtualization: kvm
  Operating System: Red Hat Enterprise Linux 8.2 (Ootpa)
       CPE OS Name: cpe:/o:redhat:enterprise_linux:8.2:GA
            Kernel: Linux 4.18.0-80.11.2.el8_0.x86_64
      Architecture: x86-64

$ cat /etc/system-release-cpe
cpe:/o:redhat:enterprise_linux:8.2:ga

$  lsb_release -d
Description:    Red Hat Enterprise Linux release 8.2 (Ootpa)

$ rpm --query redhat-release
redhat-release-8.2-1.0.el8.x86_64
```

#### Volumes et points de montage

```bash
$ df -h
Filesystem                           Size  Used Avail Use% Mounted on
…
/dev/mapper/vg_system-root_vol       5.0G  2.3G  2.8G  46% /
/dev/sda1                            507M  329M  178M  65% /boot
/dev/mapper/vg_system-tmp_vol        2.0G   35M  2.0G   2% /tmp
/dev/mapper/vg01-lv_data             5.0G   68M  5.0G   2% /data
/dev/mapper/vg_system-home_vol      1014M   61M  954M   7% /home
/dev/mapper/vg_system-var_vol        4.0G  2.0G  2.1G  49% /var
/dev/mapper/vg_system-opt_vol        5.0G  861M  4.2G  17% /opt
/dev/mapper/vg_system-vartmp_vol     2.0G   35M  2.0G   2% /var/tmp
…
```

ou encore:

```bash
$ sudo vgs 
  VG        #PV #LV #SN Attr   VSize   VFree
  vg01        1   1   0 wz--n-  <6.00g 1020.00m
  vg_system   1  11   0 wz--n- <99.50g  <61.53g
```

## Configuration disques et volumes

#### Etendre un volume dynamique

```bash
$ sudo lvextend -L +10G -n /dev/vg_system/home_vol -r
```

#### Diminuer un volume dynamique

Attention: risque de corruption, veiller à ne pas réduire au-delà du volume utilisé. Ne fonctionne pas sur tous les systèmes de fichiers.

```bash
$ sudo lvreduce -L 5G -n /dev/vg_system/varopt_vol -r
```

## Configuration réseau

#### Liste des services qui écoutent sur un port réseau

```bash
$ sudo nmap -sT -O localhost
…
PORT     STATE SERVICE
22/tcp   open  ssh
111/tcp  open  rpcbind
8089/tcp open  unknown
…
```

#### Configuration du firewall

Vérifier l'état:

```bash
$ sudo firewall-cmd --list-all
FirewallD is not running
```

Stopper le firewall (jusqu'au prochain reboot):

```bash
$ sudo systemctl stop firewalld.service  
```

Désactiver/réactiver le firewall de façon permanente:

```bash
$ sudo systemctl disable/enable firewalld.service
```

```bash
$ sudo firewall-cmd --zone=public --permanent --add-port=7473/tcp
$ sudo firewall-cmd --zone=public --permanent --add-port=7474/tcp
$ sudo firewall-cmd --zone=public --permanent --add-port=7687/tcp
$ sudo systemctl restart firewalld.service
]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens3
  sources: 10.166.60.0/22
  services: cockpit dhcpv6-client ssh
  ports: 5666/tcp 9026/tcp 7474/tcp 7473/tcp 7687/tcp
  …
```

#### Démarrer un petit serveur HTTP

Pour vérifier qu'on peut accéder à la VMdepuis un poste de travail sur http://‹hostname›/:

```bash
$ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

## Packages applicatifs

#### Liste des packages installés

```bash
$ sudo yum history
Updating Subscription Management repositories.
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
    17 | install java-11-openjdk  | 2020-11-10 13:41 | Install        |   49
    16 | install yum-utils        | 2020-11-05 09:27 | Install        |    1
    15 | install virtualenv       | 2020-11-02 16:26 | Install        |    8
    14 | install python38         | 2020-11-02 10:13 | Install        |    6
…
```

#### Liste des packages qui pourraient être mis à jour

```bash
$ sudo yum updateinfo
```

#### Mise à jour des packages installés

```bash
$ sudo yum update
```

#### Rechercher/installer un package à partir d'un repository spécifique

```bash
$ sudo yum --enablerepo="*" search R-core
$ sudo yum --enablerepo="*" install R-core
```

#### Ajouter de façon permanente le repository EPEL8

```bash
$ sudo subscription-manager repos --enable="epel8-x86_64_epel8-x86_64"
```

## Utilisateurs

Ajouter un nouvel utilisateur:

```bash
$ sudo useradd -c "‹Your Name›" -g "users" ‹username›
$ sudo passwd ‹username›
```

Ajouter un utilisateur aux [sudoers](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sect-deployment_guide-gaining_privileges-the_sudo_command):

```bash
$ sudo echo "‹username›  ALL=(ALL)  NOPASSWD: ALL" > /etc/sudoers.d/‹username›
$ sudo chmod 400 /etc/sudoers.d/‹username›
```

Ajouter un utilisateur à un groupe:

```bash
$ sudo usermod -a -g ‹primaryGroupName› -G ‹otherGroupNames› ‹username›
```

## Articles de référence

* [Yum command Cheat Sheet for Red Hat Enterprise Linux](https://access.redhat.com/sites/default/files/attachments/rh_yum_cheatsheet_1214_jcs_print-1.pdf)