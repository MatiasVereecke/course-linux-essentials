# Linux final challenge

## Flashing

```bash
Install Rasperry Pi imager
Download PI lite (0,5GB)

Press CTRL + Shift + X
setup what is Preferred
```

## ## HEADless system | SSH | ip from MAC-address

```bash
Add a file "ssh" to the boot partition

Mac eth: e4:5f:01:1d:75:af
Mac wif: e4:5f:01:1d:75:b1

use the command tcpdump -vvv -i -interface- | grep -mac- 
to find IP address 
Do this before plugging the ethernet cable into the pi 
```

## Static IP
```bash
- sudo nano /etc/dhcpcd.conf
```
1. go to interface eth0
2. change static ip address to your ip and static routers ip.
3. default gateway
4. domain-name


## Hostname
```bash
sudo raspi-config
```
1. Network
2. hostname
3. enter hostname
4. finish
5. Reboot

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
```bash 
get the public key of your device.
On windows: cd ~/.ssh ==> cat id_rsa.pub
copy this.
connect to the pi using ssh ==> create an ssh dir in the home dir
nano ~/.ssh/authorized_keys ==> paste the key
sudo chmod -R 600 ~/.ssh
sudo chmod 700 ~/.ssh
```
## Setup Shell
```bash
chsh -s /bin/-(type shell)-
```

## Install ZSH
```bash
sudo apt install zsh
chsh -s /bin/zsh
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
### Commands
```bash
sudo docker run hello-world
sudo docker stop hello-world
sudo docker images
sudo docker ps
docker run --detach --publish 4000:1880 nodered/node-red
```
docker run --detach --publish 8080:80 _/nginx
docker run  -d -p 8080:80 s_/nginx
docker run --my-nginx some-nginx -d -p 8080:80 some-content-nginx

## Cron POST
```bash
crontab -e
curl -X POST -D "test Message" url
```

## Creating dirs and files  
### Setting up permissions and ownership
```bash 
create file : touch -name-
create dir : mkdir -name-

ownership file : chown -user- -file-
ownership dir : chown -R -username- -dir-

permissions file : chmod ugo+rwx -file-
permissions dir : chmod -R
```

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
```bash
#!/usr/bin/env bash
now=$(date '+%F_%H'h'-%M'm'-%S's'')
tar -czvf "backup${now}.tar.gz" $HOME/Oef
```

### Cron backups
```bash
crontab -e 0 0 */1 * * tar -czvf "/tmp/home-backup.tar.gz" /home/matias
```

### MQTT
```bash
sudo apt install mosquitto-clients
mosquitto_pub --topic linux/lab/raspberry -h mqtt.devbit.be -m "Matias Vereecke"
```