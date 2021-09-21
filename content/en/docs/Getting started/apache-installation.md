---
title: "Apache Installation"
linkTitle: "Apache Installation"
weight: 1
description: >
  How to install OpenDataBio
---


> These instructions are for an [apache](https://httpd.apache.org)-based installation, but can be easily tuned to work with [nginx](https://www.nginx.com).

## Server requirements

1. The minimum supported PHP version is 7.4
1. The web server may be [apache](https://httpd.apache.org) or [nginx](https://www.nginx.com). For nginx, check configuration in the docker files to tune these instructions, which are for apache.
1. It requires a SQL database, [MySQL](https://www.mysql.com/) and [MariaDB](https://mariadb.org/) have been tested, but may also work with Postgres. Tested with MYSQL.v8 and MariaDB.v15.1.
1. PHP extensions required 'openssl', 'pdo', 'pdo_mysql', 'mbstring', 'tokenizer', 'xlm', 'dom', 'gd', 'exif', 'bcmath', 'zip'
1. [Pandoc](https://pandoc.org/) is used to translate LaTeX code used in the bibliographic references. It is not necessary for the installation, but it is suggested for a better user experience.
1. Requires [Supervisor](http://supervisord.org/), which is needed [background jobs](/docs/concepts/data-access/#user-job)


## Create Dedicated User

The recommended way to install OpenDataBio for production is using a **dedicated system user**. In this instructions this user is  **odbserver**.


## Download OpenDataBio

Login as your `Dedicated User` and download or clone this software to where you want to install it.
Here we assume this is `/home/odbserver/opendatabio` so that the installation files will reside in this directory. If this is not your path, change below whenever it applies.

{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" >}}

## Prep the Server

First, install the prerequisite software: Apache, MySQL, PHP, Pandoc and Supervisor. On a Debian system, you need to install some PHP extensions as well and enable them:

```bash
sudo apt-get install apache2 mysql-server php7.4 libapache2-mod-php7.4 php7.4-mysql \
		php7.4-cli pandoc php7.4-mbstring php7.4-xml php7.4-gd php7.4-bcmath php7.4-zip\
		supervisor
     \
a2enmod php7.4
phpenmod mbstring
phpenmod xml
phpenmod dom
phpenmod gd
#To check if they are installed:
php -m | grep -E 'mbstring|cli|xml|gd|mysql|pandoc|supervisord|bcmath|pcntl|zip'
```

Add the following to your Apache configuration.

* Change `/home/odbserver/opendatabio` to your path (the files must be accessible by apache)
* You may create a new file in the sites-available folder: `/etc/apache2/sites-available/opendatabio.conf` and place the following code in it.


```bash
touch /etc/apache2/sites-available/opendatabio.conf
echo '<IfModule alias_module>
        Alias /opendatabio      /home/odbserver/opendatabio/public/
        Alias /fonts /home/odbserver/opendatabio/public/fonts
        Alias /images /home/odbserver/opendatabio/public/images
        <Directory "/home/odbserver/opendatabio/public">
                Require all granted
                AllowOverride All
        </Directory>
</IfModule>' > /etc/apache2/sites-available/opendatabio.conf
```

This will cause Apache to redirect all requests for `/` to the correct folder, and also allow the provided `.htaccess` file to handle the rewrite rules, so that the URLs will be pretty. If you would like to access the file when pointing the browser to the server root, add the following directive as well:

```bash
RedirectMatch ^/$ /
```

Configure your **php.ini** file. The installer may complain about missing PHP extensions, so remember to activate them in both the **cli** (`/etc/php/7.4/cli/php.ini`) and the web **ini** (`/etc/php/7.4/apache2/php.ini`) files for PHP!

Update the values for the following variables:

```bash
Find files:
php -i | grep 'Configuration File'

Change in them:
	memory_limit should be at least 512M
	post_max_size should be at least 30M
	upload_max_filesize should be at least 30M

```
Something like:

```bash
[PHP]
allow_url_fopen=1
memory_limit = 512M

post_max_size = 100M
upload_max_filesize = 100M

```

Enable the Apache modules 'mod_rewrite' and 'mod_alias' and restart your Server:

```bash
sudo a2enmod rewrite
sudo a2ensite
sudo systemctl restart apache2.service
```

## Mysql Charset and Collation

1. You should add the following to your configuration file (mariadb.cnf or my.cnf), i.e. the Charset and Collation you choose for your installation must match that in the 'config/database.php'

```bash
[mysqld]
character-set-client-handshake = FALSE  #without this, there is no effect of the init_connect
collation-server      = utf8mb4_unicode_ci
init-connect          = "SET NAMES utf8mb4 COLLATE utf8mb4_unicode_ci"
character-set-server  = utf8mb4
log-bin-trust-function-creators = 1
sort_buffer_size = 4294967295  #this is needed for geometry (bug in mysql:8)


[mariadb] ou [mysql]
max_allowed_packet=100M
innodb_log_file_size=300M  #no use for mysql

```

2. If using MariaDB and you still have problems of type **#1267 Illegal mix of collations**, then [check here](https://github.com/phpmyadmin/phpmyadmin/issues/15463) on how to fix that,


## Configure supervisord

Configure Supervisor, which is required for jobs. Create a file name **opendatabio-worker.conf** in the Supervisor configuration folder `/etc/supervisor/conf.d/opendatabio-worker.conf` with the following content:

```bash
touch /etc/supervisor/conf.d/opendatabio-worker.conf
echo ";--------------
[program:opendatabio-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/odbserver/opendatabio/artisan queue:work --sleep=3 --tries=1 --timeout=0
autostart=true
autorestart=true
user=odbserver
numprocs=8
redirect_stderr=true
stdout_logfile=/home/odbserver/opendatabio/storage/logs/supervisor.log
;--------------" > /etc/supervisor/conf.d/opendatabio-worker.conf
```


## Folder permissions

* Folders `storage` and `bootstrap/cache` must be writable by the Server user (usually www-data). Set `0775` permission to these directories.
* [This link](https://linuxhint.com/how-to-set-up-file-permissions-for-laravel) has different ways to set up permissions for files and folders of a Laravel application. Below the preferred method:

```bash
cd /home/odbserver

#give write permissions to odbserver user and the apache user
sudo chown -R odbserver:www-data opendatabio
sudo find ./opendatabio -type f -exec chmod 644 {} \;
sudo find ./opendatabio -type d -exec chmod 775 {} \;  

#in these folders the server stores data and files.
#Make sure their permission is correct
cd /home/odbserver/opendatabio
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache

```


## Install OpenDataBio

1. Many Linux distributions (most notably Ubuntu and Debian) have **different php.ini** files for the command line interface and the Apache plugin. It is recommended to use the configuration file for Apache when running the install script, so it will be able to correctly point out missing extensions or configurations. To do so, find the correct path to the _.ini_ file, and export it before using the `php install` command.

  For example,
  ```bash
  export PHPRC=/etc/php/7.4/apache2/php.ini
  ```
2. The installation script will download the [Composer](https://getcomposer.org/) dependency manager and all required PHP libraries listed in the `composer.json` file. However, if your server is behind a proxy, you should install and configure Composer independently. We have implemented PROXY configuration, but we are not using it anymore and have not tested properly (if you require adjustments, place an issue on Github).

3. The script will prompt you configurations options, which are stored in the environment `.env` file in the application root folder.

  You may, optionally, configure this file before running the installer:

  - Create a `.env` file with the contents of the provided `cp .env.example .env`
  - Read the comments in this file and adjust accordingly.

4. **Run the installer**:

```bash
cd /home/odbserver/opendatabio
php install
```
5. **Seed data** - the script above will ask if you want to install seed data for Locations and Taxons - seed data is version specific. Check the [seed data repository version notes](https://github.com/opendatabio/data).

{{< alert color="success" title="Ready to go">}}
If the install script finishes with success, you're good to go! Point your browser to http://localhost/opendatabio. The database migrations include an administrator account, with login `admin@example.org` and password `password1`. Change the password after installing.
{{< /alert >}}

## Installation issues

There are countless possible ways to install the application, but they may involve more steps and configurations.

* if you browser return **500|SERVER ERROR** you should look to the last **error** in `storage/logs/laravel.log`. If you have **ERROR: No application encryption key has been specified** run:  
```bash
php artisan key:generate
php artisan config:cache
```
*  If you receive the error "failed to open stream: Connection timed out" while running the installer, this indicates a misconfiguration of your IPv6 routing. The easiest fix is to disable IPv6 routing on the server.
* If you receive errors during the random seeding of the database, you may attempt to remove
the database entirely and rebuild it. Of course, do not run this on a production installation.
```bash
php artisan migrate:fresh
```
* You may also replace the Locations and Taxons  tables with [seed data](https://github.com/opendatabio/data) after a fresh migration using:
```bash
php seedodb
```

## Post-install configs
* If your import/export jobs are not being processed, make sure Supervisor is running `systemctl start supervisord && systemctl enable supervisord`, and check the log files at `storage/logs/supervisor.log`.
* You can change several configuration variables for the application. The most important of those are probably set
by the installer, and include database configuration and proxy settings, but many more exist in the `.env` and
`config/app.php` files. In particular, you may want to change the language, timezone and e-mail settings. Run `php artisan config:cache` after updating the config files.
* In order to stop search engine crawlers from indexing your database, add the following to your "robots.txt" in your server root folder (in Debian, /var/www/html):
```bash
User-agent: *
Disallow: /
```

## Storage & Backups

You may change storage configurations in `config/filesystem.php`, where you may define cloud based storage, which may be needed if have many users submitting media files, requiring lots of drive space.

1. **Data downloads** are queue as jobs and a file is written in a temporary folder, and the file is deleted when the job is deleted by the user. This folder is defined as the `download disk` in filesystem.php config file, which point to `storage/app/public/downloads`. UserJobs web interface difficult navigation will force users to delete old jobs, but a cron cleaning job may be advisable to implement in your installation;
2. **Media files** are by default stored in the `media disk`, which place files in folder `storage/app/public/media`;
3. For regular configuration **create** both directories `storage/app/public/downloads` and `storage/app/public/media` with writable permissions by the Server user, see below topic;
4. Remember to include media folder in a backup plan;
