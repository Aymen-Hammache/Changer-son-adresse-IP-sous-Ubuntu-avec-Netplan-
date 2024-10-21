
# Changer son adresse IP grâce au terminal sous Ubuntu (avec Netplan)

Dans ce tutoriel, nous allons voir comment changer son adresse IP sous Ubuntu Grâce au terminal, cela peut être très pratique sous Ubuntu server

## Vérification de notre l'adresse IP actuelle

Tout d'abord, nous devons repérer notre adresse IP, pour s'y faire, faites la commande :

```
ip a
```
Vous allez alors voir sortir plusieurs ligne comme celles ci:
```
1: lo: LOWER UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host Io
  valid_lft forever preferred_lft forever
inet6 ::1/128 scope host noprefixroute
  valid_lft forever preferred_lft forever
2: enp@s3: <BROADCAST ,MULTICAST mtu 1500 qdisc fq_codel state UP group default qlen 1000
link/ether 08:00:27:0a:e6:53 brd ff:ff:ff:ff:ff:ff
inet 10.02.15/24 brd 10.0.2.255  scope global dynamic noprefixroute enp@s3
  valid_lft 86316sec preferred_lft 86316sec
inet6 fd00::7baf:6d4b:152d:3d92/92 scope global temporary dynamic
  valid_lft 86316sec preferred_lft 14317sec
inet6 fd00::a00:27ff:fe0a:e653/64 scope global dynamic mngtmpaddr proto kernel_ra
  valid_lft 86317 sec preferred_lft 14317 sec
inet6 fe80::a00:27ff:fe0a:e653/64 scope link proto kernel_ll
valid_lft forever preferred_lft forever
```
en ce qui me concerne, je suis connecté via câble Ethernet, je le sait car enp0s3 est indiqué après le 2.

Voici les préfixes des interfaces réseau:

```
en -- ethernet
sl -- serial line IP (slip)
wl -- wlan
ww -- wwan
```

Le 1 correspond à l'adresse de bouclage, **elle ne peut pas être modifiée**

## Changement de l'adresse IP

Nos fichiers de configuration sont stockés dans le chemin suivant : /etc/netplan

Nous allons donc faire la commande suivante afin de voir nos fichiers dans le dossier:
```
ls /etc/netplan
```

Notre fichier de configuration doit être semblable à : xx-installer-config-yaml

xx correspond à un certain nombre, dans mon cas le nombre est 50

Maintenant, entrez la commande suivante pour changer la configuration réseau:
```
sudo nano /etc/netplan/50-installer-config-yaml
```
Pensez à remplacer le 50 par votre nombre

Vous entrerez alors dans votre fichier .yaml et vous pourrez modifier le fichier ! Le fichier est semblable à ça:

```
network:
    Version: 2
    Renderer: NetworkManager/ networkd
    ethernets:
       [INTERFACE]:
          Dhcp4: yes/no
          Addresses: [ADRESSE_IP/NETMASK]
          Gateway: [PASSERELLE]
          Nameservers:
              Addresses: [SERVEUR_DNS_1, SERVEUR_DNS_2]
```

Pour modifier son adresse IP, il faut modifier ce qui est mis dans la ligne *addresses*. Le masque est noté en CIDR, cela correspond au nombre de 1 qu'il y a dans le masque en binaire !

Pour une adresse IP en classe A, le masque est 255.0.0.0 ce qui nous fait un CIDR de /8

Pour une adresse IP en classe B, le masque est 255.255.0.0 ce qui nous fait un CIDR de /16

Pour une adresse IP en classe C, le masque est 255.255.255.0 ce qui nous fait un CIDR en /24

## Sauvegarder les changements

Lorsque vous avez modifié les changements, faites contrôle+O puis contrôle+X pour sauvegarder le fichier et quitter ! Une fois cela fait, soit vous redemarrez votre machine soit vous faites la commande suivante :

```
sudo netplan apply
```

refaites la commande :

```
ip a
```

Pour vérifier que les changements ont bien été appliqués !
