## Install Webmin
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

