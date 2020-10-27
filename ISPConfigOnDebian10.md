## The Perfect Server - Debian 10 (Buster) with Apache, BIND, Dovecot, PureFTPD and ISPConfig 3.1
```
=Netinstall
-web server/standard system utilities->virtualbox
-hostname:server1.example.com
-IP:192.168.1.100
-gateway:192.168.1.254
nano /etc/network/interfaces
[# The primary network interface
auto enp0s3
iface enp0s3 inet static
  address 192.168.1.100
  netmask 255.255.255.0
  gateway 192.168.1.254
  dns-nameservers 192.168.1.254 8.8.8.8]


su -
apt-get update
#apt install bzip2 p7zip-full xz-utils lzip rar unrar-free goaccess dovecot-lmtpd
apt-get install ssh openssh-server nano vim-nox
nano /etc/hosts
[127.0.0.1       localhost.localdomain   localhost
192.168.1.100   server1.example.com     server1]
nano /etc/hostname
[server1]
systemctl reboot
hostname -f
[server1.example.com]

nano /etc/apt/sources.list
-add contrib non-free
apt-get update
apt-get upgrade
dpkg-reconfigure dash
-NO
apt-get -y install ntp
apt-get -y install postfix postfix-mysql postfix-doc mariadb-client mariadb-server openssl getmail4 rkhunter binutils dovecot-imapd dovecot-pop3d dovecot-mysql dovecot-sieve dovecot-lmtpd sudo




```
