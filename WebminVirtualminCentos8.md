## Install Webmin on centos8
```
nano /etc/selinux/config
    SELINUX=disabled
dnf install perl perl-Net-SSLeay openssl perl-Encode-Detect    
wget https://prdownloads.sourceforge.net/webadmin/webmin-1.960-1.noarch.rpm
rpm -ivh webmin-1.960-1.noarch.rpm
yum -y install net-tools
netstat -ant | grep 10000
ps -ef | grep webmin
``` 
## Install webmin+debian10 on contabo
```
Install webmin+debian10 on contabo
```
## Install Virtualmin
```
wget http://software.virtualmin.com/gpl/scripts/install.sh
sudo /bin/sh install.sh
```
## hostname
```
vi /etc/hostname
    box.wordpreact.com
reboot
-browser
https://box.wordpreact.com:10000

```
## Post-Installation Wizard
```
yum -y install clamav-data.noarch
systemctl enable --now clamd@scan
sudo mysql_secure_installation
  [[password
  check override]
```
## Create a virtual host
```
Create Virtual Server>wordpreact.com
add A record for mail,admin,webmail
Server configuration > SSL Certificate
 vi  /var/named/wordpreact.com.hosts
 uncomment line15?
```
yum install php-pecl-zip
 

