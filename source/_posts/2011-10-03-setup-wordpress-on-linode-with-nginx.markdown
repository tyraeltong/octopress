---
layout: post
title: "Setup Wordpress on Linode With Nginx"
date: 2011-10-03 15:22
comments: true
categories: [linode, wordpress, php, nginx]
tags: [linode, wordpress, php, nginx]
weibo: ['@木易小伟', '@SmartHuang']
---
So after a long time being away from blogging anything, I’ve decided to re-start it again, and this time I don’t want to host it on other providers like blogbus, rather, I’d like to setup my own VPS. I’ve heard about Linode on the web for a long time, it’s fast, reliable, and powerful — so I chose to start from it’s 512 plan: 512M memory, 20G harddisk and 200G network transfer per month at $19.95.

Below is my journey setup my wordpress on this Linode VPS.

The initial setup of the VPS is very easy, just follow Linode’s instruction. I chose to host it on Tokyo because the speed to access it from China is very fast. Indeed, ssh from my home to it is just like operating on my local machine!

## OS level setup
**User Account**

{% codeblock lang:sh %}
useradd -d /home/tyrael -m tyrael
passwd tyrael
{% endcodeblock %}

**Add newly created user into sudoers**

{% codeblock lang:sh %}
visudo
{% endcodeblock %}
  
add:

{% codeblock lang:sh %}
newly_created_user ALL=(ALL)ALL
{% endcodeblock %}
  
**OpenSSH config**

***Change the default port***

{% codeblock lang:sh %}
sudo vim /etc/ssh/sshd_config
service sshd restart
{% endcodeblock %}
    
***Upload your ssh public key***

{% codeblock lang:sh %}
mkdir .ssh
scp your_public_key.pub your_user@your_linode_ip:~/.ssh/authorized_keys
{% endcodeblock %}

**Install Nginx/Php/Mysql**

{% codeblock lang:sh %}
sudo apt-get install nginx mysql-server-5.1 php5-cgi php5-mysql phpmyadmin
{% endcodeblock %}

***create a db account***

{% codeblock lang:sh %}
mysql -u root -p
mysql> create user blog@localhost identified by 'password';
mysql> create database blog;
mysql> grant all on blog.* to blog@localhost;
mysql> exit;
{% endcodeblock %}

***create php-fastcgi init script:***

{% codeblock lang:sh %}
sudo vim /etc/init.d/php-fastcgi
{% endcodeblock %}

  paste below lines of code in:

{% codeblock lang:sh %}
#!/bin/bash
BIND=127.0.0.1:9000
USER=www-data
PHP_FCGI_CHILDREN=15
PHP_FCGI_MAX_REQUESTS=1000

PHP_CGI=/usr/bin/php-cgi
PHP_CGI_NAME=`basename $PHP_CGI`
PHP_CGI_ARGS="- USER=$USER PATH=/usr/bin PHP_FCGI_CHILDREN=$PHP_FCGI_CHILDREN PHP_FCGI_MAX_REQUESTS=$PHP_FCGI_MAX_REQUESTS $PHP_CGI -b $BIND"
RETVAL=0

start() {
      echo -n "Starting PHP FastCGI: "
      start-stop-daemon --quiet --start --background --chuid "$USER" --exec /usr/bin/env -- $PHP_CGI_ARGS
      RETVAL=$?
      echo "$PHP_CGI_NAME."
}
stop() {
      echo -n "Stopping PHP FastCGI: "
      killall -q -w -u $USER $PHP_CGI
      RETVAL=$?
      echo "$PHP_CGI_NAME."
}

case "$1" in
    start)
      start
  ;;
    stop)
      stop
  ;;
    restart)
      stop
      start
  ;;
    *)
      echo "Usage: php-fastcgi {start|stop|restart}"
      exit 1
  ;;
esac
exit $RETVAL
{% endcodeblock %}
    

  make the script executable and auto-startup by default:

{% codeblock lang:sh %}
sudo chmod +x /etc/init.d/php-fastcgi
sudo service php-fastcgi start
sudo update-rc.d php-fastcgi defaults
{% endcodeblock %}
    
##Application setup
**Get wordpress**

{% codeblock lang:sh %}
mkdir tmp
cd tmp && wget http://wordpress.org/latest.zip && unzip latest.zip
sudo mv wordpress /var/www/
sudo chown -R your_user:your_user /var/www/wordpress
{% endcodeblock %}

create virtual host rule for wordpress:

{% codeblock lang:sh %}
sudo vim /etc/nginx/sites-available/wordpress
{% endcodeblock %}
  
paste below lines of code in:

{% codeblock lang:sh %}
server{
  listen 80; #or change this to your public IP address eg 1.1.1.1:80
  server_name wordpress; #change this to the domain name, for example www.myblog.com
  access_log /var/log/wordpress.access_log;
  error_log /var/log/wordpress.error_log;

  location / {
    root /home/your-user-name/Sites/wordpress;
    index index.php index.html index.htm;

    # this serves static files that exist without running other rewrite tests
    if (-f $request_filename) {
        expires 30d;
        break;
    }

    # this sends all non-existing file or directory requests to index.php
    if (!-e $request_filename) {
        rewrite ^(.+)$ /index.php?q=$1 last;
    }
  }

  location ~ \.php$ {
      fastcgi_pass    127.0.0.1:9000;
      fastcgi_index   index.php;
      fastcgi_param   SCRIPT_FILENAME /home/your-user-name/Sites/wordpress$fastcgi_script_name;
      include         fastcgi_params;
  }
}
{% endcodeblock %}

enable this vhost:
{% codeblock lang:sh %}
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/wordpress
{% endcodeblock %}

restart nginx:

{% codeblock lang:sh %}
sudo service nginx restart
{% endcodeblock %}

That’s it, my shinning WordPress is up and running on Linode VPS!