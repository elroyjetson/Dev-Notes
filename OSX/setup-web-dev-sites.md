# How to create web development sites

*Goal:* Setup development environments that are independent of each other and operate similar to a production environment

## High Level Steps

* Create development site folder
* Add to Apache config
* Update hosts so names resolve in format http://dev_site_name.dev
* Flush system so it picks up the updated hosts file
* Restart Apache

## Assumptions

* For the sake of this how to we will use abcd.dev as the name of the development site we want to create.
* My home directory is /Users/jking, yours will be different
* Text editor is vim

### Step 1: Create development site folder

I like to keep all of my development environments in my home directory sites folder, you can put them anywhere, but this is nice and tidy.

    $ mkdir ~/Sites/abcd.dev

### Step 2:  Add to Apache Config

Add the following to your Apache httpd-vhosts.conf file:

    <Directory "/Users/jking/Sites/abcd.dev">
    Allow From All
    AllowOverride All
    Options FollowSymLinks
    </Directory>
    <VirtualHost *:80>
        ServerName "abcd.dev"
        DocumentRoot "/Users/jking/Sites/abcd.dev"
    </VirtualHost>

    $ sudo vi /etc/apache2/extra/httpd-vhosts.conf
    
### Update hosts so names resolve in format http://dev_site_name.dev

Add the following entry to /etc/hosts:

    127.0.0.1   abcd.dev
    
    $ sudo vi /etc/hosts
    
### Flush system so it picks up the updated hosts file

    $ sudo dscacheutil -flushcache
    
### Restart Apache

    $ sudo apachectl -k restart