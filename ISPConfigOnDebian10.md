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
  
sudo systemctl restart networking


su -
apt-get update
apt-get install bzip2 p7zip-full xz-utils lzip rar unrar-free goaccess 
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
-Internet Site
-server1.example.com

mysql_secure_installation
enter+5y

nano /etc/postfix/master.cf
[submission inet n - - - - smtpd
 -o syslog_name=postfix/submission
 -o smtpd_tls_security_level=encrypt
 -o smtpd_sasl_auth_enable=yes
 -o smtpd_client_restrictions=permit_sasl_authenticated,reject
 
 smtps inet n - - - - smtpd
 -o syslog_name=postfix/smtps
 -o smtpd_tls_wrappermode=yes
 -o smtpd_sasl_auth_enable=yes
 -o smtpd_client_restrictions=permit_sasl_authenticated,reject]
 
 systemctl restart postfix
 
 nano /etc/mysql/mariadb.conf.d/50-server.cnf
 [#bind-address           = 127.0.0.1]
 
 echo "update mysql.user set plugin = 'mysql_native_password' where user='root';" | mysql -u root
 
 nano /etc/mysql/debian.cnf
 [password ...
 password ...]
 
 nano /etc/security/limits.conf
 -add at the end
mysql soft nofile 65535
mysql hard nofile 65535

mkdir -p /etc/systemd/system/mysql.service.d/
nano /etc/systemd/system/mysql.service.d/limits.conf
-add
[[Service]
LimitNOFILE=infinity]

systemctl daemon-reload
systemctl restart mariadb

netstat -tap | grep mysql
-[tcp6       0      0 [::]:mysql              [::]:*                  LISTEN      17084/mysqld]

apt-get install amavisd-new spamassassin clamav clamav-daemon unzip bzip2 arj nomarch lzop cabextract p7zip p7zip-full unrar lrzip apt-listchanges libnet-ldap-perl libauthen-sasl-perl clamav-docs daemon libio-string-perl libio-socket-ssl-perl libnet-ident-perl zip libnet-dns-perl libdbd-mysql-perl postgrey

systemctl stop spamassassin
systemctl disable spamassassin

apt-get -y install apache2 apache2-doc apache2-utils libapache2-mod-php php7.3 php7.3-common php7.3-gd php7.3-mysql php7.3-imap php7.3-cli php7.3-cgi libapache2-mod-fcgid apache2-suexec-pristine php-pear mcrypt  imagemagick libruby libapache2-mod-python php7.3-curl php7.3-intl php7.3-pspell php7.3-recode php7.3-sqlite3 php7.3-tidy php7.3-xmlrpc php7.3-xsl memcached php-memcache php-imagick php-gettext php7.3-zip php7.3-mbstring memcached libapache2-mod-passenger php7.3-soap php7.3-fpm php7.3-opcache php-apcu libapache2-reload-perl

a2enmod suexec rewrite ssl actions include dav_fs dav auth_digest cgi headers actions proxy_fcgi alias

nano /etc/apache2/conf-available/httpoxy.conf
-paste
[<IfModule mod_headers.c>
    RequestHeader unset Proxy early
</IfModule>]

a2enconf httpoxy
systemctl restart apache2

cd /usr/local/bin
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
./certbot-auto --install-only







 




```
