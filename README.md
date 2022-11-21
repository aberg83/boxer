# boxer

Provision a fresh ubuntu server install setup in the installer with eno1 static ip and eno2 dhcp and with ssh keys imported from github user 'aberg83'.

Update system:
```
sudo apt update
sudo apt dist-upgrade -y
```

Install packages:
```
sudo apt install zfsutils-linux ipmitool libjson-perl lm-sensors smartmontools sanoid samba samba-vfs-modules nut-client postfix mailutils unattended-upgrades bzip2 mc glances -y
sudo apt autoremove
```
