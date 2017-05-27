# Rush Administration System Epitech

Ce document donne les étapes majeures pour réaliser ce qui est demandé pour le rush d'administration système Epitech.

**/!\ Il est conseillé de faire toutes les commandes suivantes sur un shell su.**

## Conseils pour les adresses IP 

Pour plus de facilité, il est conseillé de configurer les adresses IP suivantes pour les VM:

VM | IP
--- | ---
vm-gateway (local) | 192.168.0.254
vm-dns | 192.168.0.253
vm-dhcp | 192.168.0.1

Il suffit de mettre les lignes suivantes dans le fichier de configuration:

```sh
$ nano /etc/network/interfaces

iface eth0 inet static
address $(adresse correspondante au tableau)
netmask 255.255.255.0
gateway 192.168.0.254

$ ifup eth0
```

## Gateway - Mettre les deux cartes réseaux en pont

Pour créer une VM Gateway avec deux cartes réseaux, une en NAT et une en réseau local. Mettons que eth0 représente la carte réseau NAT, et eth1 la carte réseau locale. Vous devez ajouter les lignes suivantes au fichier **/etc/network/interfaces**:

```sh
$ nano /etc/network/interfaces

iface eth1 inet static
address 192.168.0.254
netmask 255.255.255.0

$ ifup eth1
```

Si tout s'est bien passé, la carte réseau devrait désormais avoir l'IP que vous aviez mis dans le fichier de configuration, en faisant un ifconfig par exemple. Vous pouvez désormais mettre en pont les deux cartes réseaux avec les commandes suivantes.

```sh
$ echo 1 > /proc/sys/net/ipv4/ip_forward
$ iptables -A FORWARD -i eth0 -o eth1 -j ACCEPT
$ iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
$ iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

## Serveur SSH

À l'installation de la VM Debian, cochez l'option "Serveur SSH" lors de l'installation. Alternativement, vous pouvez installer le paquet **openssh-server** pour le serveur et **openssh-client** pour le client.

Pour installer et lancer le serveur:
```sh
$ apt-get install openssh-server
$ systemctl start sshd
```

Pour se connecter depuis le client:
```sh
$ ssh user@ip
Password : motdepasse
```
Avec l'IP de la machine à laquelle vous tentez de vous connecter et un user qui existe sur cette machine.

## Serveur Web

### Base

Installez le paquet **apache2**, **php5** et **mysql-server** :

```sh
$ apt-get install apache2 php5 mysql-server
```

Il vous sera demandé un mot de passe root pour MySQL. Mettez celui indiqué dans le sujet si demandé, ou mettez celui que vous voulez.
Lorsque vous ouvrez la page http://localhost sur un navigateur, vous devriez avoir une page montrant que tout a bien fonctionné.

### PhpMyAdmin

Installez le paquet **phpmyadmin** et créez un lien symbolique pour activer PhpMyAdmin:

```sh
$ apt-get install phpmyadmin
$ ln -s /usr/share/phpmyadmin /var/www/phpmyadmin
```

Si tout s'est bien passé, vous pouvez accéder à PhpMyAdmin depuis un navigateur à l'adresse http://localhost/phpmyadmin/

### UserDir

Activez le module **UserDir**:
```sh
$ a2enmod userdir
$ service apache2 restart
```

Si il est demandé que le dossier *public_html* soit crée automatiquement lorsque l'on crée un utilisateur:
```sh
$ mkdir /etc/skel/public_html
$ mkdir /etc/skel/logs
$ echo "<h1>Public_html webpage</h1>" > /etc/skel/public_html/index.html
```

### Site Web

Pour créer un site internet, nous allons prendre pour exemple le nom de domaine **intra.asr.lan**.

```sh
$ nano /etc/hosts
127.0.0.1 	localhost
192.168.0.2	intra.asr.lan
$ nano /etc/apache2/sites-available/intra.asr.conf
<VirtualHost 192.168.0.2:80>
	DocumentRoot	/var/www/virtual1.com
	ServerName	virtual1.com
</VirtualHost>
$ sudo mkdir /var/www/intra.asr.lan
$ cd /etc/apache2/sites-available
```

modifier le fichier de conf 
voir https://doc.ubuntu-fr.org/tutoriel/virtualhosts_avec_apache2




## DNS

W.I.P

## DHCP

W.I.P
