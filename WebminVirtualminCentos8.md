## Install Webmin
```
nano /etc/selinux/config
    SELINUX=disabled
dnf install perl perl-Net-SSLeay openssl perl-Encode-Detect    
wget https://prdownloads.sourceforge.net/webadmin/webmin-1.960-1.noarch.rpm
rpm -ivh webmin-1.960-1.noarch.rpm
```  
