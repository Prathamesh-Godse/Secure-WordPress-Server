# S5

# S6

## Firewall
### when ufw enabled by host
```
sudo ufw status verbose
```

```
sudo ufw allow http
```

```
sudo ufw allow https/tcp
```

```
sudo ufw allow https/udp

sudo ufw allow ssh
```

```
sudo ufw status verbose
```

```
sudo ufw reload
```

```
sudo reboot
```

ssh 

```
sudo ufw status verbose
```

### when ufw not enabled by host

```
sudo ufw status verbose
```

```
sudo ufw default deny incoming
```

```
sudo ufw default allow outgoing
```

```
sudo ufw allow http
```

```
sudo ufw allow https/tcp
```

```
sudo ufw allow https/udp

sudo ufw allow ssh
```

```
sudo ufw enable 
```

```
sudo ufw status verbose
```

```
sudo reboot
```

ssh

```
sudo reboot
```

go to vps inbound rules and remove everything and add
1. ICMP
2. ssh my ip
3. https tcp
4. https udp
5. udp

## fail2ban

```
wget -O fail2ban.deb https://github.com/fail2ban/fail2ban/releases/download/1.1.0/fail2ban_1.1.0-1.upstream1_all.deb
```

```
ls
```

```
sudo dpkg -i fail2ban.deb
```

```
sudo apt -f install
```

```
sudo systemctl status fail2ban
```

```
cd /etc/fail2ban
```

```
ls
```

```
sudo cp jail.conf jail.local
```

```
sudo nano jail.local
```

```nano
...
bantime = 7d
...
findtime=3h
...
maxretry=3
...
...
mode = aggressive
port = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
enabled = true
...
```

```
sudo systemctl restart fail2ban
```

```
cd /var/log/
```

```
ls
```


```
sudo cat auth.log
```

```
sudo cat fail2ban.log
```

```
exit
```

now try multiple wrongs attempts for ssh 

now you will see that you got banned 

to unban access the console

```console
cd /var/log
```

```console
ls
```

```console
cat fail2ban.log
```

see your IP address which got banned

```console
fail2ban-client set sshd unbanip <ip>
```

```console
cat fail2ban.log
```

try ssh with correct info and now you will be logged in

```
sudo fail2ban-client status sshd
```

# S7
```
sudo timedatectl
```

```
sudo timedatectl list-timezones
```

```
sudo timedatectl list-timezones | grep Asia
```

```
sudo timedatectl set-timezone <the time zone>
```

```
sudo timedatectl
```

```
date
```


### swap
```
sudo swapon -s
```

```
htop
```

```
ls /
```

```
cd /etc
```

```
ls
```

```
sudo cp fstab fstab.bak
```

```
ls -la
```

```
cd /
```

```
sudo rm swapfile
```

```
sudo swapon -s
```

```
sudo reboot
```

ssh

```
sudo swapon -s
```

```
htop
```

```
sudo dd if=/dev/zero of=/swapfile bs=1024 count=2097152
```

```
ls -la /
```

```
cd /
```

```
sudo chmod 600 swapfile
```

```
sudo mkswap /swapfile
```

```
sudo swapon /swapfile
```

```
sudo swapon -s
```

```
cd /etc
```

```
sudo nano fstab
```

```nano
...add this

/swapfile swap swap defaults 0 0
```

```
sudo reboot
```

ssh

```
sudo swapon -s
```

```
htop
```

```
sudo sysctl -a
```

```
cd /etc
```

```
cd sysctl.d
```

```
sudo nano custom_overrides.conf
```

```nano
# SWAPPINESS AND CACHE PRESSURE
vm.. swappiness = 1
vm.vfs_cache_pressure = 50
```

```
sudo reboot
```

ssh

```
sudo sysctl -a
```

## shared memory

```
cd /etc
```

```
sudo nano fstab
```

```nano
... add this

# HARDEN SHARED MEMORY
none /dev/shm tmpfs defaults,noexec,nosuid,nodev 0 0
```

```
sudo reboot
```

ssh

```
mount | grep shm
```

## disable ipv6

```
ip a | grep inet6

cd /etc/default

sudo nano grub
```


```nano
...
GRUB_CMDLINE_LINUX="ipv6.disable=1"
...
```

```
sudo update-grub

sudo reboot
```

ssh

```
ip a | grep inet6
```

## harden and optimize N/w layer

```
cd /etc/sysctl.d

sudo nano custom_overrides.conf
```

```nano
# SWAPPINESS AND CACHE PRESSURE
vm.swappiness = 1
vm.vfs_cache_pressure = 50
# IP SPOOFING
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = I
# SYN FLOOD
net.ipv4.tcp_syncookies=1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5
# SOURCE PACKET ROUTING
net.ipv4.conf.all.accept_source_route =0
net.ipv6.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0
# Increase number of usable ports:
net.ipv4.ip_local_port_range = 1024 65535
# Increase the size of file handles and inode cache and restrict core dumps: fs.file-max = 2097152
fs.suid_dumpable = 0
# Change the number of incoming connections and incoming connections backlog:
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 262144
# Increase the maximum amount of memory buffers:
net.core.optmem_max = 25165824
# Increase the default and maximum send/receive buffers:
net.core.rmem_default = 31457280
net.core.rmem_max = 67108864
net.core.wmem_default = 31457280
net.core.wmem_max = 67108864
```

```
sudo reboot
```

ssh

## congestion control

```
sudo sysctl net.ipv4.tcp_available_congestion_control

sudo sysctl net.ipv4.tcp_congestion_control
```

## file access times

```
df -h 

sudo cat /proc/mounts

cd /etc

sudo nano fstab
```

```nano
... add this to line where ext4 lies
... add noatime to it
<something uuid>  / ext4 defaults,noatime 0 1
```

```
sudo reboot
```

ssh

```
sudo cat /proc/mounts
```

## open file limits 

```
ulimit -Hn

ulimit -Sn

cd /etc/security/limits.d

sudo nano custom_directives.conf
```

```nano
#<domain>       <type>  <item>  <value>  
*               soft    nofile  120000  
*               hard    nofile  120000  
root            soft    nofile  120000  
root            hard    nofile  120000
```

```
sudo reboot
```

ssh 

```
ulimit -Hn

ulimit -Sn

cd /etc/pam.d

sudo nano common-session
```

```nano
...
# and here are more per-package modules (the "Additional" block)
session required   pam_unix.so
session optional   pam_systemd.so
# end of pam-auth-update config
session required   pam_limits.so
```

```
sudo nano common-session-noninteractive
```

```nano
# and here are more per-package modules (the "Additional" block)
session required    pam_unix.so
# end of pam-auth-update config
session required    pam_limits.so
```

```
sudo reboot
```

ssh

# S8

## domain name

add this to the dns

```
1. type - A, name - prathameshgodse.com, content - <server ip>, proxy status - dns only, ttl - auto
2. type - CNAME, name - www, content - prathameshgodse.com, proxy status - dns only, ttl - auto
```

# S9

## nginx

[[check for manual nginx compilation here]]

```
sudo apt-cache search nginx

sudo apt-cache show njs

sudo apt update

sudo apt upgrade

... if configuring openssh server box appear select "keep the local version currently installed"

sudo add-apt-repository ppa:ondrej/nginx

sudo apt install nginx libnginx-mod-http-cache-purge libnginx-mod-http-headers-more-filter libnginx-mod-http-brotli-filter libnginx-mod-http-brotli-static

sudo systemctl status nginx

sudo systemctl status nginx --no-pager -l

sudo nano /etc/nginx/sites-available/default 
```

```nano
...comment the line under "listen 80 default_server;"
# listen [::] 80 default_server;
```

```
sudo systemctl start nginx

sudo systemctl status nginx --no-pager -l

sudo systemctl is-enabled nginx
```

go to a browser and search the server ip in the url to check the nginx web page

```
curl -I <ip>

curl -i <ip>

cd /var/www/html/

cat index.nginx-debian.html

nginx -v

sudo apt install mariadb-server

sudo systemctl status mariadb

sudo systemctl status mariadb --no-pager -l

sudo systemctl is-enabled mariadb

sudo add-apt-repository ppa:ondrej/php

sudo apt install php8.3-{fpm,gd,mbstring,mysql,xml,xmlrpc,opcache,cli,zip,soap,intl,bcmath,curl,imagick,ssh2}

sudo systemctl status php8.3-fpm

sudo systemctl is-enabled php8.3-fpm
```

# S10

mail server

# S11
how to trouble shoot errors in nginx config

```
sudo nano /etc/nginx/sites-available/default
```

do some random change in the file

```
sudo nginx -t

sudo nano /etc/nginx/sites-available/default -l

sudo nginx -t

sudo systemctl reload nginx
```

# S12

```
cd /etc/nginx/

sudo cp nginx.conf nginx.conf.bak

sudo nano nginx.conf
```

```nano
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;
worker_rlimit_nofile 30000;
worker_priority -10;
timer_resolution 100ms;
pcre_jit on;
events {
	worker_connections 4096;
	accept_mutex on;
	accept_mutex_delay 200ms;
	use epoll;
}
...
```

```
sudo mkdir includes

cd includes

sudo touch basic_settings.conf buffers.conf timeouts.conf gzip.conf brotli.conf file_handle_cache.conf

sudo nano basic_settings.conf
```

```nano
##
# BASIC SETTINGS
## 
charset utf-8;
sendfile on;
sendfile_max_chunk 512k;
tcp_nopush on;
tcp_nodelay on;
server_tokens off;
more_clear_headers 'Server';
more_clear_headers 'X-Powered';
server_name_in_redirect off;
server_names_hash_bucket_size 64;
variables_hash_max_size 2048;
types_hash_max_size 2048;
include /etc/nginx/mime.types;
default_type application/octet-stream;
```

```
sudo nano buffers.conf
```

```nano
##
# BUFFERS
## 
client_body_buffer_size 256k;
client_body_in_file_only off;
client_header_buffer_size 64k;
# client max body size - reduce size to 8m after setting up site
# Large value is to allow theme, plugins or asset uploading.
client_max_body_size 100m;
connection_pool_size 512;
directio 4m;
ignore_invalid_headers on;
large_client_header_buffers 8 64k;
output_buffers 8 256k;
postpone_output 1460;
request_pool_size 32k;
```

```
sudo nano timeouts.conf
```

```nano
##
# TIMEOUTS
## 
keepalive_timeout 5;
keepalive_requests 500;
lingering_time 20s;
lingering_timeout 5s;
keepalive_disable msie6;
reset_timedout_connection on;
send_timeout 15s;
client_header_timeout 8s;
client_body_timeout 10s;
```

```
sudo nano gzip.conf
```

```nano
##
# GZIP
## 
gzip on;
gzip_vary on;
gzip_disable "MSIE [1-6]\.";
gzip_static on;
gzip_min_length 1400;
gzip_buffers 32 8k;
gzip_http_version 1.0;
gzip_comp_level 5;
gzip_proxied any;
gzip_types text/plain text/css text/xml application/javascript application/x-javascript application/xml application/xml+rss application/ecmascript application/json image/svg+xml;
```

```
sudo nano brotli.conf
```

```nano
##
# BROTLI
## 
brotli on;
brotli_comp_level 6;
brotli_static on;
brotli_types application/atom+xml application/javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-opentype application/x-font-truetype application/x-font-ttf application/x-javascript application/xhtml+xml application/xml font/eot font/opentype font/otf font/truetype image/svg+xml image/vnd.microsoft.icon image/x-icon image/x-win-bitmap text/css text/javascript text/plain text/xml;
```

```
sudo nano file_handle_cache.conf
```

```nano
##
# FILE HANDLE CACHE
## 
open_file_cache max=50000 inactive=60s;
open_file_cache_valid 120s;
open_file_cache_min_uses 2;
open_file_cache_errors off;
```

```
cd /etc/nginx

sudo nano nginx.conf
```

```nano
http {
	##
	# Basic Settings
	##
	include /etc/nginx/includes/basic_settings.conf;
	##
	# Buffer Settings
	include /etc/nginx/includes/buffers.conf;
	# Timeout Settings
	include /etc/nginx/includes/timeouts.conf;
	# File Handle Cache Settings
	include /etc/nginx/includes/file_handle_cache.conf;
	# Logging Settings
	##
	access_log /var/log/nginx/access.log;
	##
	# Gzip and Brotli Settings
	##
	include /etc/nginx/includes/gzip.conf;
	include /etc/nginx/includes/brotli.conf;
	##
	# Virtual Host Configs
	##
	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```

```
sudo nginx -t

sudo systemctl reload nginx
```

## too many open files

skip this
```
ps aux | grep www-data

cat /proc/<nginx worker process pid>/limits

sudo nano /etc/nginx/nginx.conf
```

```nano
...
worker_rlimit_nofile 45000;
...
```

```
sudo nginx -t

sudo systemctl reload nginx

ps aux | grep www-data

cat /proc/<nginx worker process pid>/limits

sudo nano /etc/nginx/nginx.conf
```

```nano
...
worker_rlimit_nofile 30000;
...
```

```
sudo nginx -t

sudo systemctl reload nginx
```

## bash aliases

```
cd

nano .bash_aliases
```

```nano
alias ngt='sudo nginx -t'
alias ngr='sudo systemctl reload nginx'
alias fpmr='sudo systemctl reload php8.3-fpm'
```


# S13

```
sudo mysql_secure_installation
... switch to unix socket authentication n
... change the root password n
... remove anonymus users y
... disallow root login remotely y
... remove test database and access to it y
... reload previlage table now y

sudo reboot
```

ssh

```
mysql

mysql -u root

mysql -u root -p

sudo mysql
```
---
---

## maria swap

```
cd /etc/mysql/mariadb.conf.d

sudo nano 50-server.cnf
```

```nano
... find and add

#user          = mysql
pid-file       = /run/mysqld/mysqld.pid
basedir        = /usr
#datadir       = /var/lib/mysql
#tmpdir        = /tmp

# Performance Schema
performance_schema=ON
performance-schema-instrument='stage/%=ON'
performance-schema-consumer-events-stages-current=ON
performance-schema-consumer-events-stages-history=ON
performance-schema-consumer-events-stages-history-long=ON
```

```
sudo systemctl restart mariadb

nano ~/.bash_aliases
```

```nano
... add to below
alias mariare='sudo systemctl restart mariadb'
```

```
sudo reboot
```

ssh

```
cd /etc/mysql/mariadb.conf.d/

sudo nano 50-server.cnf
```

```nano
... uncommetnt "skip-name-resolve"
```

```
mariare

sudo nano 50-server.cnf
```

```nano
... find and alter
expire_logs_days     = 3
```

```
sudo mysql
```

```sql
show variables like 'expire_logs_days';

exit
```

```
htop

sudo mysql
```

```sql
show variables like '%innodb_buffer%';

show variables like '%innodb_log%';

exit
```

```
cd /etc/mysql/mariadb.conf.d/

sudo systemctl stop mariadb

sudo nano 50-server.cnf
```

```nano
... searcg for innodb and add lines below "#innodb_buffer_pool_size = 8G"
innodb_buffer_pool_size = 800M
innodb_log_file_size = 200M
```

```
sudo systemctl start mariadb

sudo mysql
```

```sql
show variables like '%innodb_buffer%';

show variables like '%innodb_log%';

exit
```
---
---

```
cd

rm fail2ban.deb

mkdir MySqlTuner

cd MySqlTuner

wget http://mysqltuner.pl/ -O mysqltuner.pl

sudo chmod +x mysqltuner.pl

sudo ./mysqltuner.pl

cd

ps aux | grep mysql

cat /proc/</usr/bin/mariadbd pid>/limits

cd /etc/systemd/system/

sudo mkdir mariadb.service.d

cd mariadb.service.d

sudo nano limits.conf
```

```nano
[Service]
LimitNOFILE=40000
```

```
sudo systemctl daemon-reload

mariare

ps aux | grep mysql

cat /proc/</usr/bin/mariadbd pid>/limits


```
---
---

```
cd /etc/php/8.3/fpm/conf.d 

sudo nano server_override.ini
```

```nano
# HARDEN PHP
allow_url_fopen = Off
cgi.fix_pathinfo = 0
expose_php = Off
```

```
sudo systemctl reload php8.3-fpm

fpmr
```
---
---

```
cd /etc/php/8.3/fpm/conf.d

sudo nano server_override.ini
```

```nano
# HARDEN PHP
allow_url_fopen = Off
cgi.fix_pathinfo = 0
expose_php = 0ff
# OPTIMIZE PHP
upload_max_filesize = 100M
post_max_size = 125M
max_input_vars = 3000
memory_limit = 256M
#max_execution_time = 90
#max_input_time= 60
```

```
fpmr

cd /etc/php/8.3/fpm

ps aux | grep php-fpm

cat /proc/<master process pid>/limits

cat /proc/<pool www pid>/limits

sudo cp php-fpm.conf php-fpm.conf.bak

sudo nano php-fpm.conf
```

```nano
...remove semi colon and alter
rlimit_files = 32768
...
rlimit_core = unlimited
```

```
fpmr

ps aux | grep php-fpm

cat /proc/<master process pid>/limits

cat /proc/<pool www pid>/limits
```

# S15

```
sudo apt update

sudo apt upgrade

sudo apt install tree

cd /var/www

sudo mkdir prathameshgodse.com/public_html

tree
```

# S16

```
cd /etc/nginx/sites-available

ping -c2 prathameshgodse.com

ping -c2 www.prathameshgodse.com

sudo nano prathameshgodse.com.conf
```

```nano
server {
	listen 80;
	server_name prathameshgodse.com www.prathameshgodse.com;

	root /var/www/prathameshgodse.com/public_html;
	index index.php;

	location / {
		try_files $uri $uri/ /index.php$is_args$args;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/run/php/php8.3-fpm.sock;
	}

access_log /var/log/nginx/access.log.prathameshgodse.com.log combined buffer=256k flush=60m;
error_log /var/log/nginx/error.log.prathameshgodse.com.log;
}
```
---
---

```
cd /etc/nginx/includes

sudo nano browser_caching.conf
```

```nano
location ~* \.(webp|3gp|gif|jpg|jpeg|png|ico|wmv|avi|asf|asx|mpg|mpeg|mp4|pls|mp3|mid|wav|swf|flv|exe|zip|tar|rar|gz|tgz|bz2|uha|7z|doc|docx|xls|xlsx|pdf|iso)$ {
    expires 365d;
    add_header Cache-Control "public, no-transform";
    access_log off;
}

location ~* \.(js)$ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
    access_log off;
}

location ~* \.(css)$ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
    access_log off;
}

location ~* \.(eot|svg|ttf|woff|woff2)$ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
    access_log off;
}
```

```
sudo nano fastcgi_optimize.conf
```

```nano
fastcgi_connect_timeout 60;
fastcgi_send_timeout 180;
fastcgi_read_timeout 180;
fastcgi_buffer_size 512k;
fastcgi_buffers 512 16k;
fastcgi_busy_buffers_size 1m;
fastcgi_temp_file_write_size 4m;
fastcgi_max_temp_file_size 4m;
fastcgi_intercept_errors on;
```

```
readlink -f browser_caching.conf

readlink -f fastcgi_optimize.conf

cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf 
```

```nano
server {
	listen 80;
	server_name prathameshgodse.com www.prathameshgodse.com;

	root /var/www/prathameshgodse.com/public_html;
	index index.php;

	location / {
		try_files $uri $uri/ /index.php$is_args$args;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/run/php/php8.3-fpm.sock;
		include /etc/nginx/includes/fastcgi_optimize.conf;
	}

include /etc/nginx/includes/browser_caching.conf;
access_log /var/log/nginx/access.log.prathameshgodse.com.log combined buffer=256k flush=60m;
error_log /var/log/nginx/error.log.prathameshgodse.com.log;
}
```

```
cd /etc/nginx

ls sites-*

ls sites-* -l

sudo ln -s /etc/nginx/sites-available/prathameshgodse.com.conf /etc/nginx/sites-enabled/

ls sites-* -l

ngt

ngr
```

check the server ip on the browser to verifiy its not available

```
cd /var/www/prathameshgodse.com/public_html/

curl -I prathameshgodse.com

cd /etc/nginx/sites-available
```

# S17

```
nslookup prathameshgodse.com

nslookup www.prathameshgodse.com

cd /etc/nginx

ls sites-*

cd sites-available

sudo cat prathameshgodse.com.conf

cat /dev/urandom | tr -dc 'a-za-z0-9' | fold -w 10 | head -n 2
```

```note
prathameshgodse.com

db name              prathameshgodse_com
db_user              <random string generated>
db_pass              <random string generated>
```

```
sudo mysql
```

```sql
create database prathameshgodse_com;

grant all privileges on prathameshgodse_com.* to '<db user>'@'localhost' identified by '<db pass>';

flush privileges;

exit
```

```
cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 30 | head -n 2
```

```note
admin user name    <random string generated>
admin passwd       <random string generated>
```
---
---

```
cd

wget https://wordpress.org/latest.tar.gz

tar xf latest.tar.gz

cd wordpress/

mv wp-config-sample.php wp-config.php

curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

```note
... save the salt data 
```

```
sudo nano wp-config.php
```

```nano
...fill in
......db name
......db user
......db passwd
...
...remove the define salt lines and paste the salts
...
$table_prefix = 'bF6_';
...
... find "/*  Add any custom values between this line and the stop editing ..."
...and add the lenes below
/** Allow Direct Updating Without FTP */
define('FS_METHOD', 'direct');
/** Disable Editing of Themes and Plugins Using the Built In Editor */
define('DISALLOW_FILE_EDIT', 'true');
/** TURN OFF AUTOMATIC UPDATES */
define('WP_AUTO_UPDATE_CORE', false );
define('AUTOMATIC_UPDATER_DISABLED', 'true');
```

```
cd

sudo rsync -artv wordpress/ /var/www/prathameshgodse.com/public_html/

sudo rm -rf latest.tar.gz wordpress

cd /var/www/prathameshgodse.com/

sudo chown -R www-data:www-data public_html/

ls -la
```

enter the domain url

install the wordpress site and login to the admin account

1. settings > permalinks > post name
2. delete all post
3. delete all pages
4. delete all puggins
5. keep only 2024 theme and delete rest 
6. user > profile > nick name and public name
7. pluggins > add a pluggin > maintainence mode > install and activete > configure it
# S18

```
cd /var/log/nginx

ls

sudo cat error.log.prathameshgodse.com.log

sudo cat error.log.prathameshgodse.com.log.1

sudo cat access.log.prathameshgodse.com.log

```

---

```
cd

sudo useradd prathameshgodse

sudo usermod -a -G prathameshgodse www-data

sudo usermod -a -G www-data prathameshgodse

sudo usermod -a -G prathameshgodse ubuntu

id ubuntu

id prathameshgodse

cd /etc/php/8.3/fpm/pool.d/

sudo cp www.conf prathameshgodse.com.conf

sudo nano prathameshgodse.com.conf
```

```nano
... in 4th line "[www]" change it to "[prathameshgodse]"
...
...change the user and group
user = prathameshgodse
group = prathameshgodse
...
... search for "listen = /run/php/php8.3-fpm.sock" 
...and replace it with listen = /run/php/php8.3-fpm-prathameshgodse.com.sock
...
...search for "rlimit"  uncomment and change
rlimit_files = 15000
rlimit_core = 100
... go to the very bottom, uncomment and edit
php_flag[display_errors] = off
php_admin_value[error_log] = /var/log/fpm-php.prathameshgodse.log
php_admin_flag[log_errors] = on
```

```
sudo touch /var/log/fpm-php.prathameshgodse.log

sudo chown prathameshgodse:www-data /var/log/fpm-php.prathameshgodse.log

sudo chmod 660 /var/log/fpm-php.prathameshgodse.log

fpmr

sudo grep "listen = /" prathameshgodse.com.conf

cd /etc/nginx/sites-available/

sudo nano prathameshgodse.com.conf
```

```nano
... search for "fastcgi_pass unix:" 
...and alter it to 
..."fastcgi_pass unix:/run/php/php8.3-fpm-prathameshgodse.com.sock;"
```

```
ngt

ngr

cd /var/www/prathameshgodse.com

```

---
---

```

sudo find /var/www/prathameshgodse.com/public_html/ -type d -exec chmod 770 {} \;

sudo find /var/www/prathameshgodse.com/public_html/ -type f -exec chmod 660 {} \;
```



```
cd /var/www/prathameshgodse.com

sudo chown -R prathameshgodse:prathameshgodse public_html/

sudo find /var/www/prathameshgodse.com/public_html/ -type d -exec chmod 770 {} \;

sudo find /var/www/prathameshgodse.com/public_html/ -type f -exec chmod 660 {} \;

cd public_html/

exit
```

```
cd /var/www/prathameshgodse.com

cd public_html/

sudo nano info123.php
```

```nano
<?php
phpinfo();
?>
```

go to browser and type "prathameshgodse.com/info123.php"



```
sudo nano /etc/php/8.3/fpm/pool.d/prathameshgodse.com.conf
```

```nano
... at the very bottom
; ENABLED FUNCTIONS
; disk_free_space
; DISABLED FUNCTIONS
php_admin_value[disable_functions] = shell_exec, opcache_get_configuration, opcache_get_status, disk_total_space, diskfreespace, dl, exec, passthru, pclose, pcntl_alarm, pcntl_exec, pcntl_fork, pcntl_get_last_error, pcntl_getpriority, pcntl_setpriority, pcntl_signal, pcntl_signal_dispatch, pcntl_sigprocmask, pcntl_sigtimedwait, pcntl_sigwaitinfo, pcntl_strerror, pcntl_waitpid, pcntl_wait, pcntl_wexitstatus, pcntl_wifcontinued, pcntl_wifexited, pcntl_wifsignaled, pcntl_wifstopped, pcntl_wstopsig, pcntl_wtermsig, popen, posix_getpwuid, posix_kill, posix_mkfifo, posix_setpgid, posix_setsid, posix_setuid, posix_uname, proc_close, proc_get_status, proc_nice, proc_open, proc_terminate, show_source, system
```

```
fpmr

sudo rm info123.php
```
---
---

```
cd /var/www/prathameshgodse.com

sudo find /var/www/prathameshgodse.com/public_html/ -type d -exec chmod 770 {} \;

sudo find /var/www/prathameshgodse.com/public_html/ -type f -exec chmod 660 {} \;
```
---
---

```
cd /var/www/prathameshgodse.com

sudo mkdir tmp

sudo chown prathameshgodse:prathameshgodse tmp

sudo chmod 770 tmp

cd /etc/php/8.3/fpm/pool.d

sudo nano prathameshgodse.com.conf
```

```nano
... at the very bottom
php_admin_value[upload_tmp_dir] = /var/www/prathameshgodse.com/tmp/
php_admin_value[sys_temp_dir] = /var/www/prathameshgodse.com/tmp/
php_admin_value[open_basedir] = /var/www/prathameshgodse.com/public_html/:/var/www/prathameshgodse.com/tmp/
```

```
fpmr
```
delete all the themes, plugins, pages etc and upgrade the wp in browser

---
---

```
cd

sudo apt update

sudo apt upgrade

sudo apt install certbot python3-certbot-dns-cloudflare

sudo certbot certonly --webroot -w /var/www/prathameshgodse.com/public_html -d prathameshgodse.com -d www.prathameshgodse.com
```

```note
... copy and paste the output in note
```

```
cd /etc/nginx

sudo mkdir ssl

cd ssl

sudo openssl dhparam -out dhparam.pem 2048
```

---
---


```
ls

sudo ls /etc/letsencrypt/live/prathameshgodse.com

sudo nano /etc/nginx/ssl/ssl_prathameshgodse.com.conf
```

```nano
ssl_certificate /etc/letsencrypt/live/prathameshgodse.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/prathameshgodse.com/privkey.pem; 
ssl_trusted_certificate /etc/letsencrypt/live/prathameshgodse.com/chain.pem;
```

```
readlink -f ssl_prathameshgodse.com.conf
```

```note
... copy the path
```

```
sudo nano /etc/nginx/ssl/ssl_all_sites.conf
```

```nano
# CONFIGURATION RESULTS IN A+ RATING AT SSLLABS.COM
# WILL UPDATE DIRECTIIVES TO MAINTAIN A+ RATING - CHECK DATE
# DATE: MAY 2025

# SSL CACHING AND PROTOCOLS
ssl_session_cache shared:SSL:20m;
ssl_session_timeout 180m;
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
# ssl_ciphers must be on a single line, do not split over multiple lines
ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-CHACHA20-POLY1305;
ssl_dhparam /etc/nginx/ssl/dhparam.pem;
# LETS ENCRYPT REMOVED SUPPORT 7 MAY 2025 FOR SSL STAPLING
# REMOVE OR COMMENT BOTH ssl_stapling directives
#ssl_stapling on;
#ssl_stapling_verify on;

# resolver set to Cloudflare
# timeout can be set up to 30s
resolver 1.1.1.1 1.0.0.1;
resolver_timeout 15s;
ssl_session_tickets off;
# HSTS HEADERS
add_header Strict-Transport-Security "max-age=31536000;" always;
# After settting up ALL of your sub domains - comment the above and uncomment the directive hereunder, then re
# add_header Strict-Transport-Security "max-age=31536000; includeSubDomains;" always;
# Enable QUIC and HTTP/3
ssl_early_data on;
add_header Alt-Svc 'h3=":$server_port"; ma=86400';
add_header x-quic 'H3' ;
quic_retry on;
```
---
---



```
cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... add this to the very top
server {
    listen 80;

    server_name prathameshgodse.com www.prathameshgodse.com;

    # ADD REDIRECT TO HTTPS: 301 PERMANENT 302 TEMPORARY
    return 301 https://prathameshgodse.com$request_uri;
}

...alter the below server block
... from "listen 80" to 
listen 443 ssl;
http2 on;
listen 443 quic reuseport;
http3 on;

...

...below in the location / {} block below try_files add
add_header Cache-Control 'no-cache,no-store';

...

... after closing of flower bracket of "location /" add this below
include /etc/nginx/ssl/ssl_prathameshgodse.com.conf
include /etc/nginx/ssl/ssl_all_sites.conf;

...

... in the block "location ~ \.php$", in the second line add
fastcgi_param HTTP_HOST $host;

```

```
ngt

ngr
```

---
---


```
curl -I http://prathameshgodse.com

curl -I http://www.prathameshgodse.com

curl -I https://www.prathameshgodse.com

curl -I https://prathameshgodse.com
```

go to ssllabs.com to verify the ssl strength

also http3check.net

to check http3 in browser
browser > more tools > developer tools > network > refresh > on the title bar right click and select protocol > refresh 

go to wordpress dashboard > settings > general > wordpress address url and site address url (change it to https) > save changes

```
sudo certbot certificates

sudo crontab -e
```

```nano
m h dom mon dow   command
00 1 14,28 * * certbot renew --force-renewal >/dev/null 2>&1
00 2 14,28 * * systemctl reload nginx >/dev/null 2>&1
```

```
sudo crontab -l
```

---
---

```
curl -I https://prathameshgodse.com

cd /etc/nginx/includes/

sudo nano http_headers.conf
```

```nano
# -------------------------------------------------------
# Add Header Referrer-Policy - Uncomment desired directive
# -------------------------------------------------------
#add_header Referrer-Policy "no-referrer";
add_header Referrer-Policy "strict-origin-when-cross-origin";
#add_header Referrer-Policy "unsafe-url";
# ------------------------------------------------------
add_header X-Content-Type-Options "nosniff";
add_header X-Frame-Options "sameorigin";
add_header X-XSS-Protection "1; mode=block";
add_header Permissions-Policy "geolocation=(),midi=(),sync-xhr=(),microphone=(),camera=(),magnetometer=(),gyroscope=(),fullscreen=self https://www.youtube.com, payment=()";
```

```
cd /etc/nginx/sites-available/

sudo nano prathameshgodse.com.conf
```

```
... add this above the includes of 'location ~ \.php$ {}'
include /etc/nginx/includes/http_headers.conf;
```

```
ngt

ngr

cd /etc/nginx/includes

sudo nano browser_caching.conf
```

```nano
... in the first block of location
... remove add_header and access_log line and add the below
expires 30d;
etag on;
if_modified_since exact;
add_header Pragma "public";
add_header Cache-Control "public, no-transform";
try_files $uri $uri/ /index.php?$args;
include /etc/nginx/includes/http_headers.conf;
access_log off; 

... do the same for all the location blocks
```

```
ngt

ngr

curl -I https://prathameshgodse.com/wp-content/plugins/maintenance/load/css/style.css
```

---
---
```
cd /etc/nginx/includes/

sudo nano nginx_security_directives.conf
```

```nano
# Deny Access To Important WP Files


#location = /xmlrpc.php { deny all; }
location = /wp-config.php { deny all; }
location = /wp-admin/install.php { deny all; }
location ~* /readme\.html$ { deny all; }
location ~* /readme\.txt$ { deny all; }
location ~* /licence\.txt$ { deny all; }
location ~* /license\.txt$ { deny all; }
location ~ ^/wp-admin/includes/ { deny all; }
location ~ ^/wp-includes/[^/]+\.php$ { deny all; }
location ~ ^/wp-includes/js/tinymce/langs/.+\.php$ { deny all; }
location ~ ^/wp-includes/theme-compat/ { deny all; }

# Disable PHP in Uploads, Plugins and Theme Directories
location ~* ^/wp\-content/uploads/.*\.(?:php[1-7]?|pht|phtml?|phps)$ { deny all; }
location ~* ^/wp\-content/plugins/.*\.(?:php[1-7]?|pht|phtml?|phps)$ { deny all; }
location ~* ^/wp\-content/themes/.*\.(?:php[1-7]?|pht|phtml?|phps)$ { deny all; }

# Disable access to the site php override file
location = /.user.ini { deny all; }

# Filter Request Methods
if ( $request_method ~* ^(TRACE|DELETE|TRACK)$ ) { return 403; }

# Filter Suspicious Query Strings in the URL
set $susquery 0;
if ( $args ~* "\.\./" ) { set $susquery 1; }
if ( $args ~* "\.(bash|git|hg|log|svn|swp|cvs)" ) { set $susquery 1; }
if ( $args ~* "etc/passwd" ) { set $susquery 1; }
if ( $args ~* "boot\.ini" ) { set $susquery 1; }
if ( $args ~* "ftp:" ) { set $susquery 1; }
if ( $args ~* "(<|%3C)script(>|%3E)" ) { set $susquery 1; }
if ( $args ~* "mosConfig_[a-zA-Z_]{1,21}(=|%3D)" ) { set $susquery 1; }
if ( $args ~* "base64_decode\(" ) { set $susquery 1; }
if ( $args ~* "%24&x" ) { set $susquery 1; }
if ( $args ~* "127\.0" ) { set $susquery 1; }
if ( $args ~* "(globals|encode|request|localhost|loopback|request|insert|concat|union|declare)" ) { set $susquery 1; }
if ( $args ~* "%[01][0-9A-F]" ) { set $susquery 1; }
if ( $args ~ "^loggedout=true" ) { set $susquery 0; }
if ( $args ~ "^action=jetpack-sso" ) { set $susquery 0; }
if ( $args ~ "^action=rp" ) { set $susquery 0; }
if ( $http_cookie ~ "wordpress_logged_in_" ) { set $susquery 0; }
if ( $http_referer ~* "^https?://maps\.googleapis\.com/" ) { set $susquery 0; }
if ( $susquery = 1 ) { return 403; }

# BLOCK COMMON SQL INJECTIONS
set $block_sql_injections 0;
if ($query_string ~ "union.*select.*\(") { set $block_sql_injections 1; }
if ($query_string ~ "union.*all.*select.*") { set $block_sql_injections 1; }
if ($query_string ~ "concat.*\(") { set $block_sql_injections 1; }
if ($block_sql_injections = 1) { return 403; }

# BLOCK FILE INJECTIONS
set $block_file_injections 0;
if ($query_string ~ "[a-zA-Z0-9_]=http://") { set $block_file_injections 1; }
if ($query_string ~ "[a-zA-Z0-9_]=(\.\.//?)+") { set $block_file_injections 1; }
if ($query_string ~ "[a-zA-Z0-9_]=/([a-z0-9_.]//?)+") { set $block_file_injections 1; }
if ($block_file_injections = 1) { return 403; }

# BLOCK COMMON EXPLOITS
set $block_common_exploits 0;
if ($query_string ~ "(<|%3C).*script.*(>|%3E)") { set $block_common_exploits 1; }
if ($query_string ~ "GLOBALS(=|\[|\%[0-9A-Z]{0,2})") { set $block_common_exploits 1; }
if ($query_string ~ "_REQUEST(=|\[|\%[0-9A-Z]{0,2})") { set $block_common_exploits 1; }
if ($query_string ~ "proc/self/environ") { set $block_common_exploits 1; }
if ($query_string ~ "mosConfig_[a-zA-Z_]{1,21}(=|\%3D)") { set $block_common_exploits 1; }
if ($query_string ~ "base64_(en|de)code\(.*\)") { set $block_common_exploits 1; }
if ($block_common_exploits = 1) { return 403; }

# BLOCK SPAM
set $block_spam 0;
if ($query_string ~ "\b(ultram|unicauca|valium|viagra|vicodin|xanax|ypxaieo)\b") { set $block_spam 1; }
if ($query_string ~ "\b(erections|hoodia|huronriveracres|impotence|levitra|libido)\b") { set $block_spam 1; }
if ($query_string ~ "\b(ambien|blue\spill|cialis|cocaine|ejaculation|erectile)\b") { set $block_spam 1; }
if ($query_string ~ "\b(lipitor|phentermin|pro[sz]ac|sandyauer|tramadol|troyhamby)\b") { set $block_spam 1; }
if ($block_spam = 1) { return 403; }

# BLOCK USER AGENTS
set $block_user_agents 0;
if ($http_user_agent ~ "Indy Library") { set $block_user_agents 1; }
if ($http_user_agent ~ "libwww-perl") { set $block_user_agents 1; }
if ($http_user_agent ~ "GetRight") { set $block_user_agents 1; }
if ($http_user_agent ~ "GetWeb!") { set $block_user_agents 1; }
if ($http_user_agent ~ "Go!Zilla") { set $block_user_agents 1; }
if ($http_user_agent ~ "Download Demon") { set $block_user_agents 1; }
if ($http_user_agent ~ "Go-Ahead-Got-It") { set $block_user_agents 1; }
if ($http_user_agent ~ "TurnitinBot") { set $block_user_agents 1; }
if ($http_user_agent ~ "GrabNet") { set $block_user_agents 1; }
if ($http_user_agent ~ "dirbuster") { set $block_user_agents 1; }
if ($http_user_agent ~ "nikto") { set $block_user_agents 1; }
if ($http_user_agent ~ "SF") { set $block_user_agents 1; }
if ($http_user_agent ~ "sqlmap") { set $block_user_agents 1; }
if ($http_user_agent ~ "fimap") { set $block_user_agents 1; }
if ($http_user_agent ~ "nessus") { set $block_user_agents 1; }
if ($http_user_agent ~ "whatweb") { set $block_user_agents 1; }
if ($http_user_agent ~ "Openvas") { set $block_user_agents 1; }
if ($http_user_agent ~ "jbrofuzz") { set $block_user_agents 1; }
if ($http_user_agent ~ "libwhisker") { set $block_user_agents 1; }
if ($http_user_agent ~ "webshag") { set $block_user_agents 1; }
if ($http_user_agent ~ "Acunetix-Product") { set $block_user_agents 1; }
if ($http_user_agent ~ "Acunetix") { set $block_user_agents 1; }
if ($block_user_agents = 1) { return 403; }
```

```
readlink -f nginx_security_directives.conf
```

```note
copy the output
```

```
cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... paste the copied path above location ~ \.php$ {}
```

```
ngt

ngr
```

now reload the prathameshgodse.com/readme.html to confirm not found error

skip the below

```
cd /etc/nginx/includes

cd /var/www/prathameshgodse.com/public_html/wp-content/plugins

sudo nano test556.php
```

```nano
Contents:
<?php
phpinfo();
?>
```

check that pluggins are not availabe in the browser

https://prathameshgodse.com/wp-content/plugins/test556.php


```
readlink -f test556.php
```

```copy
copy the output
```

```
sudo nano /etc/nginx/sites-available/prathameshgodse.com.conf
```

```nano
... below location ~ \.php$ {} block 
location = <copied path <remove the location already existing in root>> {
	allow all;
    include snippets/fastcgi-php.conf;
	fastcgi_param HTTP_HOST $host;
    fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    include /etc/nginx/includes/fastcgi_optimize.conf;
}
```

```
ngt

ngr
```

refresh the page and you will get the info

undo all the changes 

---
---

```
cd /etc/nginx/

sudo nano nginx.conf
```

```nano
... below gzip block
##
# Rate Limiting
limit_req_zone $binary_remote_addr zone=wp:10m rate=30r/m;
```

```
cd /etc/nginx/includes/

sudo nano rate_limiting_prathameshgodse.com.conf
```

```nano
location = /wp-login.php {
limit_req zone=wp burst=20 nodelay;
limit_req_status 444;
include snippets/fastcgi-php.conf;
fastcgi_param HTTP_HOST $host;
fastcgi_pass unix:/run/php/php8.3-fpm-prathameshgodse.sock;
include /etc/nginx/includes/fastcgi_optimize.conf;
}

location = /xmlrpc.php {
limit_req zone=wp burst=20 nodelay;
limit_req_status 444;
include snippets/fastcgi-php.conf;
fastcgi_param HTTP_HOST $host;
fastcgi_pass unix:/run/php/php8.3-fpm-prathameshgodse.sock;
include /etc/nginx/includes/fastcgi_optimize.conf;
}
```

```
readlink -f rate_limiting_prathameshgodse.com.conf

... copy the output

cd ../sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... paste the address copied below the location ~ \.php$ {} block 
```

---
---
search for ninja firewall in wp plugin
activate it

ninja firewall > dashboard > full waf mode 
select http server : nginx + cgi/fsat cgi or php-fpm

finish installation


skip the below

```
cd /var/www/prathameshgodse.com/public_html

sudo nano wp-config.php
```

```nano
... find "/** TURN OFF AUTO..." and below this block add
/** DISALLOW FILE MODS **/
define('DISALLOW_FILE_MODS', 'true');
```

```
fpmr
```

go to pluggins in wp 

---
---

```
cd /var/www/prathameshgodse.com/public_html

grep DB_ wp-config.php

sudo mysql
```

```sql
REVOKE ALL PRIVILEGES ON prathameshgodse_com.* FROM '<db_user>'@'localhost';

GRANT SELECT, INSERT, UPDATE, DELETE ON prathameshgodse_com.* TO '<db_user>'@'localhost';

show grants for '<db_user>'@'localhost';

FLUSH PRIVILEGES;
```

add disable rest pluggin in wp
activate > settings 

---
# S19

check pagespeed.web.net for some statistics

---
---

```
cd /var/www/prathameshgodse.com/public_html/

sudo nano wp-config.php
```

```nano
... below "define('AUTOMETIC_UPDATER_DISABLE'....)"
/** DISABLE POST RIVISIONS **/
define('WP_POST_REVISIONS', 'false');
```

---
---

```
cd /etc/php/8.3/fpm/pool.d

sudo nano prathameshgodse.com.conf
```

```nano
... find "php_admin_value[memory_limit]" and alter
php_admin_value[memory_limit] = 256M
```

```
cd /var/www/prathameshgodse.com/public_html

sudo nano wp-config.php
```

```nano
... find "/** DISABLE POST RIVISION ..." and add the line below
/** MEMORY LIMIT */
define('WP_MEMORY_LIMIT', '256M');
```

```
fpmr

```

---
---

```

sudo nano wp-config.php
```

```nano
... find "/** MEMORY LIMIT */" and add below the block
/** DISABLE WORDPRESS CRONS */
define('DISABLE_WP_CRON', true);
```

```
crontab -e
```

```nano
# m h dom mon dow command
*/15 * * * * wget -q -O - https://prathameshgodse.com/wp-cron.php?doing_wp_cron >/dev/null 2>&1
```

```
crontab -l

```

---
---

```

cd /etc/php/8.3/fpm/pool.d/

sudo nano prathameshgodse.com.conf
```

```nano
... at the very end
; OPCACHE CONFIGURATION - DEVELOPMENT SERVER
php_admin_flag[opcache.enabled] = 1
php_admin_value[opcache.memory_consumption] = 256
php_admin_value[opcache.interned_strings_buffer] = 32
php_admin_value[opcache.max_accelerated_files] = 20000
php_admin_flag[opcache.validate_timestamps] = 1
php_admin_value[opcache.revalidate_freq] = 2
php_admin_flag[opcache.validate_permission] = 1

; OPCACHE CONFIGURATION - PRODUCTION SERVER
;php_admin_flag[opcache.enabled] = 1
;php_admin_value[opcache.memory_consumption] = 256
;php_admin_value[opcache.interned_strings_buffer] = 32
;php_admin_value[opcache.max_accelerated_files] = 20000
;php_admin_flag[opcache.validate_timestamps] = 0
;php_admin_flag[opcache.validate_permission] = 1
```

```
fpmr

cd /var/www/

sudo find . -type f -print | grep php | wc -l
```

check the link for more https://www.php.net/manual/en/opcache.configuration.php#ini.opcache.max-accelerated-files

```
cd /etc/php/8.3/fpm/pool.d

sudo cat prathameshgodse.com.conf

cd /var/www/

sudo find . -type f -print | grep php | wc -l

```

---
---

```

cd /etc/nginx

sudo nano nginx.conf
```

```nano
... paste it below the # rate limiting block
### FASTCGI CACHING
# fastcgi_cache_path directive - PATH & NAME must be unique for each site
# Add a new fastcgi_cache_path for each site and give a new keys_zone name
fastcgi_cache_path /var/run/prathameshgodse levels=1:2 keys_zone=prathameshgodse:100m inactive=60m;
# applied to all sites
fastcgi_cache_key "$scheme$request_method$host$request_uri";
fastcgi_cache_use_stale error timeout invalid_header http_500;
fastcgi_ignore_headers Cache-Control Expires Set-Cookie;
```

```
ngt

cd sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... within location ~ \.php$ {} after all the lines in it
# FASTCGI CACHING DIRECTIVES
fastcgi_cache_bypass $skip_cache;
fastcgi_no_cache $skip_cache;
fastcgi_cache NAME;
fastcgi_cache_valid 60m;
```

```
ngt

cd ../includes/

sudo nano fastcgi_cache_excludes.conf
```

```nano
# NGINX SKIP CACHE INCLUDE FILE
set $skip_cache 0;
# POST requests and urls with a query string should always go to PHP
if ($request_method = POST) {
    set $skip_cache 1;
}
if ($query_string != "") {
    set $skip_cache 1;
}   
# Don't cache uris containing the following segments
if ($request_uri ~* "/wp-admin/|/xmlrpc.php|wp-.*.php|/feed/|index.php|sitemap(_index)?.xml") {
    set $skip_cache 1;
}  
# Don't use the cache for logged in users or recent commenters
if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_no_cache|wordpress_logged_in") {
    set $skip_cache 1;
}
```

```
readlink -f fastcgi_cache_exclude.conf

... copy the path

cd ../sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... below the "location / {}" block
include /etc/nginx/includes/fastcgi_cache_excludes.conf;
add_header X-FastCGI-Cache $upstream_cache_status;
```

```
ngt

ngr

curl -I https://prathameshgodse.com

```

---
---

```

sudo mkdir nginx_bash_scripts

cd /etc/nginx/sites-available 

sudo nano prathameshgodse.com.conf
```

```nano
... remove the fast cgi lines starting from above of location ~ \.php$
... the above 2 lines of location ~ \.php$
... and the fastcgi block in location ~ \.php$
```

```
cd /etc/nginx/includes

sudo rm fastcgi_cache_excludes.conf

cd ..

sudo nano nginx.conf
```

```nano
... remove the fast cgi directives from # rate limit to # virtual host configs 
```

```
ngt

ngr

```

---
---

```

cd /etc/nginx/includes

sudo nano wp_super_cache_excludes.conf
```

```nano
# WP Super Cache NGINX Cache Exclusions Rules.
set $cache_uri $request_uri;
 
# POST requests and urls with a query string should always go to PHP
if ($request_method = POST) {
	set $cache_uri 'null cache';
}

if ($query_string != "") {
        set $cache_uri 'null cache';
}

# Don't cache uris containing the following segments
if ($request_uri ~* "(/wp-admin/|/xmlrpc.php|/wp-(app|cron|login|register|mail).php|wp-.*.php|/feed/|index.php|wp-comments-popup.php|wp-links-opml.php|wp-locations.php|sitemap(_index)?.xml|[a-z0-9_-]+-sitemap([0-9]+)?.xml)") {
        set $cache_uri 'null cache';
}

# Don't use the cache for logged in users or recent commenters
if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_logged_in") {
        set $cache_uri 'null cache';
}

# Use cached or actual file if they exists, otherwise pass request to WordPress

location / {
        try_files /wp-content/cache/supercache/$http_host/$cache_uri/index-https.html $uri $uri/ /index.php?$args ;
}
```

```
readlink -f wp_super_cache_excludes.conf

cd ..

sudo cat includes/wp_super_cache_excludes.conf | grep "location /"

sudo cat sites-available/prathameshgodse.com.conf | grep "location /"

sudo nano prathameshgodse.com.conf 
```

```nano
... comment the entire directive of "location / {}" 
...
... before location ~ \.php$
include /etc/nginx/includes/wp_super_cache_exclusions.conf;
```

```
ngt

ngr
```

this will be reverted after use

``` 
... 
install and activate "WP Super Cache"
activate > settings 
easy > caching on > update status
verify the alert of "cron system disabled"

cache tester > test cache > result should be "the time stamp on both pages match" 

advanced > cache delivery method > expert
			> missaslanious > dont cache pages with GET
			 				> compress pages 
			 				> 304
			 > advancede > clear all cache files
			 
update status

Preload > Preload mode and Preload Tab > save settings
```

```
cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... remove the include file "include /etc/nginx/includes/wp_super_cache_exclusions.conf;
"
...
... uncomment 
#    location / {
#        try_files $uri $uri/ /index.php$is_args$args;
#    }
```

```
ngt 

ngr
```

---
---

even this will be reverted

```
cd /var/www/prathameshgodse.com/public_html

sudo touch nginx.conf

sudo chown prathameshgodse:prathameshgodse nginx.conf

sudo chmod 660 nginx.conf

cd /etc/nginx/includes

sudo nano nginx_security_directives.conf
```

```nano
... below "#location = /xmlrpc.php ..." paste the below
location = /nginx.conf { deny all; }
```

```
ngt

sudo nano w3tc_cache_excludes.conf
```

```nano
# ---------------------
# W3 TOTAL CACHE EXCLUDES FILE 
# ---------------------
set $cache_uri $request_uri;

# POST requests and urls with a query string should always go to PHP
if ($request_method = POST) {
        set $cache_uri 'null cache';
}   
if ($query_string != "") {
        set $cache_uri 'null cache';
}   
# Don't cache uris containing the following segments
if ($request_uri ~* "(/wp-admin/|/xmlrpc.php|/wp-(app|cron|login|register|mail).php|wp-.*.php|/feed/|index.php|wp-comments-popup.php|wp-links-opml.php|wp-locations.php|sitemap(_index)?.xml|[a-z0-9_-]+-sitemap([0-9]+)?.xml)") {
        set $cache_uri 'null cache';
}   
# Don't use the cache for logged in users or recent commenters
if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_logged_in") {
        set $cache_uri 'null cache';
}
# Use cached or actual file if file exists, otherwise pass request to WordPress
location / {
        try_files /wp-content/w3tc/pgcache/$cache_uri/_index.html $uri $uri/ /index.php?$args;
}
```

```
readlink -f w3tc_cache_excludes.conf

cd /etc/nginx/sites-available/

sudo nano prathameshgodse.com.conf
```

```nano
... find and comment the below
#location / {
#    try_files $uri $uri/ /index.php$is_args$args;
#}

... just before "location ~ \.php$ {}"
include /etc/nginx/includes/w3tc_cache_excludes.conf;
include /var/www/prathameshgodse.com/public_html/nginx.conf;
```

```
ngt

ngr

sudo apt update

sudo apt upgrade

sudo apt install php8.3-tidy
```

install a pluggin in wp named "W3 Total Cache"

and make sure that installation is failed and the folder already exist

```
cd /var/www/prathameshgodse.com/public_html/wp-content/plugins

sudo rm -rf w3-total-cache

cd /var/www/prathameshgodse.com/public_html
```

refresh the wp plugin page and retry to install the plugin

activate

```
ngt

ngr
```

ignore the w3tc warning 

go to settings of w3tc > accept the policy > next > disk enhance > next .. > enable web p > enable lazy loading > dash board 

```
ngt 

ngr
```

wp w3tc general settings > performance > minify > enable minify > save settings 

```
ngt

ngr
```

wp w3tc page cashe > cache preload > enable cache preload > empty all caches

browser cache > enable http strict transport security policy > enable x-xss protection > enable x content type option > save settings > empty caches

```
ngt 

ngr

curl -I https://prathameshgodse.com
```

```to-un-install-w3ct
... purge all caches

... performance > general settings > disable everything > save settings > deactivate > delete

cd /var/www/prathameshgodse.com/public_html/wp-content

sudo rm -rf cache w3tc-config

cd plugins

cd /var/www/prathameshgodse.com/public_html

sudo rm nginx.conf

cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf
... uncomment the default location files and remove the 2 includded files
```

---
---

```
sudo apt update

sudo apt upgrade

sudo apt install redis-server php8.3-redis

sudo systemctl status redis-server

cd /var/log/

sudo cat redis/redis-server.log

cd /etc/sysctl.d/

sudo nano 11-redis.conf
```

```nano
vm.overcommit_memory = 1
```

```
sudo reboot
```

ssh

```
sudo systemctl status redis-server

sudo cat /var/log/redis/redis-server.log

cd /etc/

sudo ls redis/

sudo cp redis/redis.conf redis/redis.conf.bak

sudo ls redis/

sudo nano redis/redis.conf
```

```nano
... search and edit the below
maxmemory 256mb
...
maxmemory-policy allkeys-lru
```

```
sudo systemctl restart redis-server

cd /var/www/example	.com/public_html/

sudo nano wp-config.php
```

```nano
... below of salt block
define( 'WP_CACHE_KEY_SALT', 'prathameshgodse.com' );
```

```
fpmr
```

---
---

### Wp super cache and redis

```
cd /etc/nginx/sites-available

sudo nano prathameshgodse.com.conf
```

```nano
... comment the below line
# location / {
# try_files $uri $uri/ /index.php$is_args$args;
#}
...
... just before "location ~ \.php$ {}" paste the below line
include /etc/nginx/includes/wp_super_cache_excludes.conf;
```

```
ngr
```

go to wp-admin dashboard > plugin > new plugin > install and activate wp-super cache 

wp super cache settings > caching : enable caching > test cache > advance cache > cache delivery method : expert > enable dont cache pages with get parameters > enable compress pages so they are served more quickly > enable 304 browser caching > enable clear all cache files when a post or page is published or updated > update status

wp super cache settings > payload > enable payload more > enable payload tags, categories and other taxonomies > save setting 

wp super cache settings > advance > reject url strings > paste the below in the box
```
wp-.*\.php
index\.php
/cart/
/my-account/
/checkout/
``` 
rejected cookies > paste the below in the box
```
woocommerce_cart_hash
woocommerce_items_in_cart
wp_woocommerce_session_
woocommerce_recently_viewed
store_notice[notice id]
```

```
cd /var/www/example.com/public_html/

sudo nano wp-config.php
```

```nano
...below the previous salts
define( 'WP_CACHE_KEY_SALT', 'example.com' );
...
/** PREVENT REDIS CACHING WOO SESSION DATA */
define('WP_REDIS_IGNORED_GROUPS', 'wc_session');
```

search and install  and activate the plugin "Redis Object Cache". > enable object cache > 

---
---

Display POOL Usernames:

grep -E '^\s*user\s*=' /etc/php/8.3/fpm/pool.d/*.conf | awk -F= '{print $2}' | xargs | tr ' ' '\n' | sort -u

Calculate Average Memory, replacing POOL_USER with actual POOL Username

ps -C php-fpm --user POOL_USER -o rss= | awk '{ sum += $1; count++ } END { if (count > 0) printf ("%d%s\n", sum/NR/1024,"M") }'

ONDEMAND:

cd /etc/php/8.1/fpm/pool.d/
ls
sudo nano example.com.conf

pm = ondemand
pm.max_children = as calculated
pm.process_idle_timeout = 10s;
pm.max_requests = 500

sudo systemctl reload php8.3-fpm

ls /var/log/
sudo grep max_children /var/log/php8.3-fpm.log

Grep will display the following line if it occurs in your log file

WARNING: [pool www] server reached max_children setting (25), consider raising it

---
---

search for "https://www.cloudflare.com/ips-v4"  "https://www.cloudflare.com/ips-v6" and copy the addresses

```
cd /etc/nginx/includes

sudo nano cloudflare_ip_list.conf
```

```nano
# Last updated OCT 2022
set_real_ip_from 173.245.48.0/20;
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 104.16.0.0/13;
set_real_ip_from 104.24.0.0/14;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 131.0.72.0/22;
set_real_ip_from 2400:cb00::/32;
set_real_ip_from 2606:4700::/32;
set_real_ip_from 2803:f800::/32;
set_real_ip_from 2405:b500::/32;
set_real_ip_from 2405:8100::/32;
set_real_ip_from 2a06:98c0::/29;
set_real_ip_from 2c0f:f248::/32;
real_ip_header CF-Connecting-IP;
```

```
cd /etc/nginx/sites-available/

sudo nano prathameshgodse.com.conf
```

```nano
... add this above "location ~ \.php$ {}"
include /etc/nginx/includes/cloudflare_ip_list.conf;
```

```
ngt

ngr
```

in CF

1. select domain name > DNS > enable Proxy 
2. ssl/tls > enable full (strict) 
3. ssl/tls > edge certificates > enable always use HTTPS > Minimum tls set it to 1.2 
4. speed > optimization > content optimization > enable cloudflare fonts > enable early hints 
5. caching > configuration > browser cache set to 1 month > enable always online 
6. caching > teared cache > enable smart tiered caching topology 
7. network > enable ipv6 compatibility
8. scrape shield > enable both options 

# S20

server updates

```
cd

sudo apt update && sudo apt upgrade && sudo apt autoremove 

nano .bash_aliases
```

```nano
alias server_updates='sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y'
```

```
cd 

mkdir server_bash_scripts

cd server_bash_scripts

nano server_updates.sh
```

```nano
#!/bin/bash

# Check if script is run as root
if [ "$EUID" -ne 0 ]; then
  echo "Please run this script with sudo or as root."
  exit
fi

# Update package lists
echo "Updating package lists..."
sudo apt update

# Upgrade installed packages
echo "Upgrading installed packages..."
sudo apt upgrade -y

# Remove unnecessary packages
echo "Removing unnecessary packages..."
sudo apt autoremove -y

echo "Update, upgrade, and autoremove completed."
```

```
chmod +x server_updates.sh

sudo ./server_updates.sh
```

---
---

WP UPDATES

DEFAULT PHP POOL USER and PERMISSIONS

```
cd /var/www/example.com/public_html

ls -la

sudo chown -R PHP_POOL_USER:PHP_POOL_USER public_html/

sudo find /var/www/example.com/public_html/ -type d -exec chmod 770 {} \;

sudo find /var/www/example.com/public_html/ -type f -exec chmod 660 {} \;
```

Open the DASHBOARD and all updates will run without issue

Restart php-fpm

```
sudo systemctl reload php8.3-fpm
```

DEFAULT PHP POOL USER and HARDENED PERMISSIONS

```
cd /var/www/example.com/

sudo ls -l public_html/

sudo chown -R PHP_POOL_USER:PHP_POOL_USER public_html/

sudo find /var/www/example.com/public_html/ -type d -exec chmod 550 {} \;

sudo find /var/www/example.com/public_html/ -type f -exec chmod 440 {} \;

sudo find /var/www/example.com/public_html/wp-content/ -type d -exec chmod 770 {} \;

sudo find /var/www/example.com/public_html/wp-content/ -type f -exec chmod 660 {} \;
```

---

```
cd 

mkdir wp_bash_scripts

cd wp_bash_scripts

nano 1.pre_update_example.com.sh
```

```nano
#!/bin/bash

sudo chown -R PHP_POOL_USER:PHP_POOL_USER public_html/
sudo find /var/www/example.com/public_html/ -type d -exec chmod 770 {} \;
sudo find /var/www/example.com/public_html/ -type f -exec chmod 660 {} \;
sudo systemctl reload php8.3-fpm
```

```
chmod +x 1.pre_update_example.com.sh

sudo ./1.pre_update_example.com.sh

cp 1.pre_update_example.com.sh 2.post_update_example.com.sh

nano 2.post_update_example.com.sh
```

```nano
#!/bin/bash

sudo chown -R PHP_POOL_USER:PHP_POOL_USER public_html/
sudo find /var/www/example.com/public_html/ -type d -exec chmod 550 {} \;
sudo find /var/www/example.com/public_html/ -type f -exec chmod 440 {} \;
sudo find /var/www/example.com/public_html/wp-content/ -type d -exec chmod 770 {} \;
sudo find /var/www/example.com/public_html/wp-content/ -type f -exec chmod 660 {} \;
sudo systemctl reload php8.3-fpm
```

```
sudo ./2.post_update_example.com.sh
```

---
---
backup and restore practices

---
---

virus and malware scanning

important note
these scanners can pose excessive load on the server

```
sudo apt update

server_updates

sudo apt install clamav
The clam av definition database will be updated automatically after installation.  y
```

DISABLE CLAMAV-FRESHCLAM

```
sudo systemctl stop clamav-freshclam

sudo systemctl disable clamav-freshclam
```

To manually update, run Freshclam

```
sudo freshclam

... before running the below command open htop in another terminal to watch the resource utilization

sudo clamscan -r /path/2/scan
```

---

RKHUNTER

```
sudo apt install rkhunter

sudo rkhunter --propupd

sudo rkhunter --checkall --sk

sudo cat /var/log/rkhunter.log

sudo less /var/log/rkhunter.log

cd /etc/cron.daily/

sudo rm rkhunter

cd /etc/cron.weekly/

sudo rm rkhunter
```

---
---

Database tuning

run this script only once in 60 to 90 days

important remainder
innodb log file size directive. stop the mariadb service. modify 50-server.cnf. start the mariadb service. do not modify 50-server.cnf file and restart mariadb you may corrupt the innodb tables

```
sudo mysql
```

```sql
... View InnoDB Buffer Pool ACTUAL Memory Usage

SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_bytes_data';

exit
```

```
cd /etc/mysql/mariadb.conf.d

sudo nano 50-server.cnf
```

```nano
... scroll down to where you have innodb directives and add this below
... or find "innodb_log_file_size = 200M"

innodb_log_buffer_size = 32M
```

```
sudo systemctl restart mariadb

```

---

```

sudo mysql
```

```sql
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_bytes_data';

... View Database Size

SELECT table_schema AS "Database", ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) AS "Size (MB)" FROM information_schema.tables GROUP BY table_schema;

SELECT table_schema AS "Database", SUM(data_length + index_length) / 1024 / 1024 AS "Size (MB)" FROM information_schema.tables WHERE engine = 'InnoDB' GROUP BY table_schema;

... View the Buffer Pool being used by each database:

SELECT table_schema AS "Database", SUM(data_length + index_length) / 1024 / 1024 AS "Size (MB)" FROM information_schema.tables WHERE engine = 'InnoDB' GROUP BY table_schema;

... View the database Table Usage I/O statistics

SELECT object_schema AS 'Database', object_name AS 'Table', COUNT_READ AS 'Reads', COUNT_WRITE AS 'Writes', SUM_TIMER_READ / 1000000000 AS 'Read Time (ms)', SUM_TIMER_WRITE / 1000000000 AS 'Write Time (ms)' FROM performance_schema.table_io_waits_summary_by_table ORDER BY SUM_TIMER_READ + SUM_TIMER_WRITE DESC;

exit
```

```
sudo mysqlcheck --optimize --all-databases

sudo mysqlcheck --auto-repair --check --all-databases
```

---

MYSQLTUNER

```
cd

cd MySQLTuner/

ls -l

... To run mysqltuner, you need to use sudo as mysqltuner needs to login to mariadb

sudo ./mysqltuner
```

---
---

server and site monitoring

uptime robot

htop

glances
is not recomended due to heavy large amount of dependencies

external server monitoring
consume additional resource

netdata.cloud

---
---

administrating disk space

Administering disk space on an Ubuntu server is an important task, as you server can crash is you run out of space.

The process involves several tasks, including monitoring disk usage and then cleaning up unnecessary files

CHECK DISK SPACE

```
df -h

sudo apt autoremove && sudo apt clean

df -h

sudo journalctl --vacuum-time=1days

df -h

cd /

du -ah --max-depth=1 | sort -h

cd /var

du -ah --max-depth=1 | sort -h

cd /var/www

du -ah --max-depth=1 | sort -h

cd /lib

du -ah --max-depth=1 | sort -h

... dont delele anything in the lib directory
```

---
---

nginx backlog

```
cd /etc/nginx/sites-available

sudo nano example.com.conf
```

```nano
... in the http server block alter the "listen 80;" line to 
listen 80 backlog=2048;

... in the https server block alter the "listen 443 ssl;" line to 
listen 443 ssl backlog=2048;
```

backlog option is only added to one of the site listen socket. when another site is added it will be automatically created by nginx.

```
ngt

ngr
```

---
---

summary

server updates 3x per week
wordpress updates 3x per week
site backups daily
virus and malware scanning weekly
dbms tuning every 60 days
php-fpm tuning every 3days
administrating disk space weekly
opcache administration weekly

---
---

# S21

