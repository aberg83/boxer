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

Interactively configure packages:
```
sudo dpkg-reconfigure unattended-upgrades
sudo dpkg-reconfigure tzdata
```

Install and configure Tailscale:
```
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --accept-dns=false
```

Import ZFS pools:
```
sudo zpool import -d /dev/disk/by-id apps
sudo zpool import -d /dev/disk/by-id storage
```

Install docker and bring up compose stack
```
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo usermod -aG docker $USER
sudo rm get-docker.sh
cat << EOF > /home/aberg/.bash_aliases
alias dcup='docker compose -f /mnt/storage/scripts/docker-compose.yml up -d'
alias dcdown='docker compose -f /mnt/storage/scripts/docker-compose.yml stop'
alias dcpull='docker compose -f /mnt/storage/scripts/docker-compose.yml pull'
alias dclogs='docker compose -f /mnt/storage/scripts/docker-compose.yml logs -tf --tail="50" '
alias dtail='docker logs -tf --tail="50" "$@"'
alias dcupdate='dcpull && dcup'
alias dcedit='nano /mnt/storage/scripts/docker-compose.yml'
EOF
source ~/.bashrc
dcup
```

Install autorestic and use to restore /etc and /home:
```
sudo wget -qO - https://raw.githubusercontent.com/cupcakearmy/autorestic/master/install.sh | sudo bash
```

Create samba users interactively:
```
sudo smbpasswd -a aberg
sudo adduser jberg
sudo smbpasswd -a jberg
sudo systemctl restart smbd
```

Restart/enable services:
```
sudo netplan apply
sudo systemctl restart smartmontools
sudo systemctl restart nut-monitor
sudo systemctl enable fan_control
sudo systemctl start fan_control
sudo systemctl restart zfs-zed
```

Configure Postfix SMTP password:
```
sudo postmap hash:/etc/postfix/sasl_passwd
sudo postfix reload
```
