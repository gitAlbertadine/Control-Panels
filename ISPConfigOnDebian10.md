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
-add
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

apt-get install mailman
-en>ok
newlist mailman
-listadmin@example.com
-pass
-enter

nano /etc/aliases
-add
[## mailman mailing list
mailman:              "|/var/lib/mailman/mail/mailman post mailman"
mailman-admin:        "|/var/lib/mailman/mail/mailman admin mailman"
mailman-bounces:      "|/var/lib/mailman/mail/mailman bounces mailman"
mailman-confirm:      "|/var/lib/mailman/mail/mailman confirm mailman"
mailman-join:         "|/var/lib/mailman/mail/mailman join mailman"
mailman-leave:        "|/var/lib/mailman/mail/mailman leave mailman"
mailman-owner:        "|/var/lib/mailman/mail/mailman owner mailman"
mailman-request:      "|/var/lib/mailman/mail/mailman request mailman"
mailman-subscribe:    "|/var/lib/mailman/mail/mailman subscribe mailman"
mailman-unsubscribe:  "|/var/lib/mailman/mail/mailman unsubscribe mailman"]

newaliases
systemctl restart postfix
ln -s /etc/mailman/apache.conf /etc/apache2/conf-enabled/mailman.conf
systemctl restart apache2
systemctl restart mailman

-access the Mailman admin interface for a list at
http://server1.example.com/cgi-bin/mailman/admin/
http://server1.example.com/cgi-bin/mailman/listinfo/
http://server1.example.com/pipermail

apt-get install pure-ftpd-common pure-ftpd-mysql quota quotatool
openssl dhparam -out /etc/ssl/private/pure-ftpd-dhparams.pem 2048
nano /etc/default/pure-ftpd-common
[[...]
STANDALONE_OR_INETD=standalone
[...]
VIRTUALCHROOT=true
[...]]

echo 1 > /etc/pure-ftpd/conf/TLS
mkdir -p /etc/ssl/private/
openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem
chmod 600 /etc/ssl/private/pure-ftpd.pem
systemctl restart pure-ftpd-mysql
-change
[UUID=45576b38-39e8-4994-b8c1-ea4870e2e614 / ext4 errors=remount-ro,usrjquota=quota.user,grpjquota=quota.group,jqfmt=vfsv0 0 1]
mount -o remount /
quotacheck -avugm
quotaon -avug

apt-get install bind9 dnsutils
apt-get install haveged

apt-get install webalizer awstats geoip-database libclass-dbi-mysql-perl libtimedate-perl
nano /etc/cron.d/awstats
-comment out *

apt-get install build-essential autoconf automake libtool flex bison debhelper binutils

cd /tmp
wget http://olivier.sessink.nl/jailkit/jailkit-2.20.tar.gz
tar xvfz jailkit-2.20.tar.gz
cd jailkit-2.20
echo 5 > debian/compat
./debian/rules binary

cd ..
dpkg -i jailkit_2.20-1_*.deb
rm -rf jailkit-2.20*

apt-get install fail2ban
nano /etc/fail2ban/jail.local
-add
[[pure-ftpd]
enabled = true
port = ftp
filter = pure-ftpd
logpath = /var/log/syslog
maxretry = 3

[dovecot]
enabled = true
filter = dovecot
logpath = /var/log/mail.log
maxretry = 5

[postfix-sasl]
enabled = true
port = smtp
filter = postfix[mode=auth]
logpath = /var/log/mail.log
maxretry = 3]

systemctl restart fail2ban
apt-get install ufw

mkdir /usr/share/phpmyadmin
mkdir /etc/phpmyadmin
mkdir -p /var/lib/phpmyadmin/tmp
chown -R www-data:www-data /var/lib/phpmyadmin
touch /etc/phpmyadmin/htpasswd.setup

cd /tmp
wget https://files.phpmyadmin.net/phpMyAdmin/4.9.0.1/phpMyAdmin-4.9.0.1-all-languages.tar.gz
tar xfz phpMyAdmin-4.9.0.1-all-languages.tar.gz
mv phpMyAdmin-4.9.0.1-all-languages/* /usr/share/phpmyadmin/
rm phpMyAdmin-4.9.0.1-all-languages.tar.gz
rm -rf phpMyAdmin-4.9.0.1-all-languages

cp /usr/share/phpmyadmin/config.sample.inc.php  /usr/share/phpmyadmin/config.inc.php
nano /usr/share/phpmyadmin/config.inc.php
[$cfg['blowfish_secret'] = 'bD3e6wva9fnd93jVsb7SDgeiBCd452Dh';]
-add
$cfg['TempDir'] = '/var/lib/phpmyadmin/tmp';

nano /etc/apache2/conf-available/phpmyadmin.conf
-add
[# phpMyAdmin default Apache configuration

Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
 Options FollowSymLinks
 DirectoryIndex index.php

 <IfModule mod_php7.c>
 AddType application/x-httpd-php .php

 php_flag magic_quotes_gpc Off
 php_flag track_vars On
 php_flag register_globals Off
 php_value include_path .
 </IfModule>

</Directory>

# Authorize for setup
<Directory /usr/share/phpmyadmin/setup>
 <IfModule mod_authn_file.c>
 AuthType Basic
 AuthName "phpMyAdmin Setup"
 AuthUserFile /etc/phpmyadmin/htpasswd.setup
 </IfModule>
 Require valid-user
</Directory>

# Disallow web access to directories that don't need it
<Directory /usr/share/phpmyadmin/libraries>
 Order Deny,Allow
 Deny from All
</Directory>
<Directory /usr/share/phpmyadmin/setup/lib>
 Order Deny,Allow
 Deny from All
</Directory> ]

a2enconf phpmyadmin
systemctl restart apache2
mysql -u root -p
CREATE DATABASE phpmyadmin;
CREATE USER 'pma'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON phpmyadmin.* TO 'pma'@'localhost' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EXIT;

mysql -u root -p phpmyadmin < /usr/share/phpmyadmin/sql/create_tables.sql
nano /usr/share/phpmyadmin/config.inc.php
-edit+password
[/* User used to manipulate with storage */
$cfg['Servers'][$i]['controlhost'] = 'localhost';
$cfg['Servers'][$i]['controlport'] = '';
$cfg['Servers'][$i]['controluser'] = 'pma';
$cfg['Servers'][$i]['controlpass'] = 'mypassword';

/* Storage database and tables */
$cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
$cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
$cfg['Servers'][$i]['relation'] = 'pma__relation';
$cfg['Servers'][$i]['table_info'] = 'pma__table_info';
$cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
$cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
$cfg['Servers'][$i]['column_info'] = 'pma__column_info';
$cfg['Servers'][$i]['history'] = 'pma__history';
$cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
$cfg['Servers'][$i]['tracking'] = 'pma__tracking';
$cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
$cfg['Servers'][$i]['recent'] = 'pma__recent';
$cfg['Servers'][$i]['favorite'] = 'pma__favorite';
$cfg['Servers'][$i]['users'] = 'pma__users';
$cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
$cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
$cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
$cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
$cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
$cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';]

echo "CREATE DATABASE roundcube;" | mysql --defaults-file=/etc/mysql/debian.cnf
apt-get install roundcube roundcube-core roundcube-mysql roundcube-plugins
yes>pass>ok
nano /etc/roundcube/config.inc.php
-edit
[$config['default_host'] = 'localhost';
$config['smtp_server'] = 'localhost';]
nano /etc/apache2/conf-enabled/roundcube.conf
-add at the beginning
[Alias /roundcube /var/lib/roundcube
Alias /webmail /var/lib/roundcube]
systemctl reload apache2

cd /tmp
wget http://www.ispconfig.org/downloads/ISPConfig-3-stable.tar.gz
tar xfz ISPConfig-3-stable.tar.gz
cd ispconfig3_install/install/
php -q install.php
-voir
https://www.howtoforge.com/perfect-server-debian-10-buster-apache-bind-dovecot-ispconfig-3-1/









 




```
