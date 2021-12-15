# Linux final challenge

## Flashing

Install raspberry pi imager
Choose os and storage and press write.

## HEADless system | SSH | ip from MAC-address
Add a file "ssh" to the boot partition of the card. 
Connect with ssh using the ip address.

mac-address (ethernet): own mac
To find out the ip we can use the command tcpdump -vvv -i -interface- | grep -mac- 

First start the command
Than plug the ethernet into the Pi

## Static IP

sudo nano /etc/dhcpcd.conf
go to interface eth0
change static ip address to your ip and static routers to the default
gateway


## Hostname

sudo raspi-config
network options -> hostname -> enter the hostname -> enter -> finish
Reboot.

## Creating accounts
```bash
sudo adduser -name-
```
## Creating groups
```bash
sudo addgroup -name-
Add user to group: sudo adduser -username- -groupname
or
sudo usermod -aG -group/groups- -user-
```
## Locking accounts
```bash
sudo passwd -l -name-
usermod --expiredate 1 -name-
```
## SSH using keys

get the public key of your device (not the pi).
On windows: cd ~/.ssh ==> cat id_rsa.pub
copy this.
connect to the pi using ssh ==> create an ssh dir in the home dir
nano ~/.ssh/authorized_keys ==> paste the key
sudo chmod -R 600 ~/.ssh
sudo chmod 700 ~/.ssh

## Setup Shell
```bash
chsh -s /bin/-shell-
```

## Install ZSH
```bash
sudo apt install zsh
```
## Wireless Network
```bash
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=BE

network={
    ssid="YOURSSID"
    psk="YOURPASSWORD"
    scan_ssid=1
}
sudo wpa_cli -i wlan0 reconfigure
```

## UFW Firewall

```bash
sudo apt install ufw
sudo systemctl enable ufw
sudo ufw allow ssh
sudo systemctl start ufw
```

## Apache

```bash
sudo apt install apache2
sudo ufw allow http

cd /var/www/html
```

## Docker

```bash
curl -sSL https://get.docker.com | sh
sudo adduser pi docker
sudo systemctl enable docker
sudo systemctl start docker
```
## Cron POST
```bash

```

## Creating dirs and files  
### Setting up permissions and ownership

create file : touch -name-
create dir : mkdir -name-

ownership file : chown -user- -file-
ownership dir : chown -R -username- -dir-

permissions file : chmod ugo+rwx -file-
permissions dir : chmod -R if you want to change the permissions of everything inside of it as well

## Cloning GIT
```bash
sudo apt install git
git clone (use the http if your devices key has not been added to github.com)
```
## Install with apt
```bash
sudo apt install -package-
```

### Backups

use something simialar to this script

```bash
#!/usr/bin/env bash
now=$(date '+%F_%H'h'-%M'm'-%S's'')
tar -czvf "backup${now}.tar.gz" $HOME/Oef
```

### Cron backups
```bash
crontab -e 0 0 */1 * * tar -czvf "/tmp/home-backup.tar.gz" /home/matias
```