# konstructor
Konstructor is a operating system image builder designed for VM or containers. 

# Goals

Operating system images are everywhere : virtual machines, baremetal, containers, chroot ... 

I'm important to keep the images :
- easy to understand
- signed 
- hashed
- up-to-date
- controlled by the administrator




# Implementation

A base image is created from BOOTSTRAP (debootstrap), other images depends on a base image or another image. 
The description is templated with variables from the construction command line (filesystem, cloud provider, arch)

## base image : 108 MB

```
BOOTSTRAP jessie
ARCH amd64
DESCRIPTION Debian 8 - Jessie 

PACKAGES -ppp -isc-dhcp-client -isc-dhcp-common -libicu52 -libpsl0 -wget -iptables  -e2fsprogs -rsyslog -manpages -man-db
```


## image: server

```
FROM jessie
PACKAGES isc-dhcp-client isc-dhcp-common libicu52 libpsl0 wget iptables  e2fsprogs rsyslog manpages man-db
#if $filesystem == "ZFS"
PACKAGES zfsonlinux
#endif 
#if $cloudprovider == "AWS"
PACKAGES ...
#endif

```


## image: PHP 5.6 app server
```
FROM server:jessie
PACKAGES apache2 libapache2-mod-php5 mysql-server
```


# Building

Image creation is made from a central host from the description files. 

Build a PHP 5.6 container image with squashfs on "planet-work" cloud provider

```# konstructor build-image php56 -f squashfs -c planet-work```

Allowed filesystems :
- squashfs for containers
- tgz for containers
- ext4 / btrfs / zfs

Images are signed with GnuPG, and copied in a repository. The image loader will need to check the signature against a knowned key. 
