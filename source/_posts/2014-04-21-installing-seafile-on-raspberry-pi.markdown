---
layout: post
title: "Installing Seafile on Raspberry Pi"
date: 2014-04-21 00:23
comments: true
categories: [Raspberry Pi, cloud, seafile]
---
With all the security issues in the past relating to privacy I've been wanting to install a private cloud service similar to [Dropbox](http://www.dropbox.com) for some time now. So, here's a post on how to install Seafile on the Raspberry Pi.

I choose [Seafile](http://seafile.com/en/home/) over [Owncloud](http://owncloud.org/) because I have read multiple posts that (1) Owncloud is not very responsive an a Raspberry Pi and (2) Seafile has a better security model (see [1](http://blog.kovah.de/private-cloud-owncloud-alternativen-teil-2), [2](http://stevenhickson.blogspot.de/2013/04/cloud-storage-on-raspberry-pi.html), [3](http://stevenhickson.blogspot.de/2013/04/cloud-storage-on-raspberry-pi.html)).

Using a [Raspberry Pi](http://www.raspberrypi.org) for the server seems like a good choice, because it has very low power consumption, so you can have it running 24/7. Furthermore, the Rasperry Pi can be setup with Debian GNU/Linux (f. ex. [Raspbian](http://www.raspbian.org)) running in server mode. As Debian is a widely used Linux distribution, most problems can be easily solved by searching the web.

Installing Seafile should be straightforward from following the instructions at the official [Seafile Wiki](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server). If you can read German, you can also follow the excellent instructions on Jan Karres's blog [Raspberry Pi: Owncloud-Alternative Seafile Server installieren](http://jankarres.de/2013/06/raspberry-pi-owncloud-alternative-seafile-server-installieren/).

As there are some pitfalls along the way, I'll describe how I installed Seafile with SSL support.

## Installation

Note: This is mainly a merge of [Jan Karres's blog post (in German)](http://jankarres.de/2013/06/raspberry-pi-owncloud-alternative-seafile-server-installieren/) and the [official wiki documentation from Seafile](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server).

### Demo values

```
domain: no-ip.org
sub-domain: mycloud
DDNS-domain: mycloud.no-ip.org
internal server name: mycloud
internal IP address of Raspberry Pi: 192.168.1.42
```

### Prerequisites

I'll assume you have a working Raspian installation on a Raspberry Pi.

If you want to reach your Seafile server from the internet and your ISP only provides you with a dynamic IP you will have to register with a [DDNS](http://en.wikipedia.org/wiki/Dynamic_DNS) provider such as [Dyn](http://dyn.com/dns/) or [no-ip.com](http://www.noip.com/).

### Step 0 Update

Update Raspbian:

`$ sudo aptitude update`


### Step 1 Install dependencies

Install dependencies required by Seafile:

`$ sudo aptitude -y install python2.7 python-setuptools python-simplejson python-imaging sqlite3`


### Step 2 Create seafile user

For security reasons we'll create a separate user for running Seafile. The user will be called `seafile` and will not require a password, since we will never be accessing this user directly through SSH.

`$ sudo adduser seafile --disabled-password`

Switch to being `seafile` user:

`$ sudo su seafile`

Change to `seafile`'s home directory:

`$ cd`


### Step 3 Download and unpack

First we'll create a new folder `mycloud` in the seafile user's home directory:

`$ mkdir mycloud && cd mycloud`

Download and unpack Seafile Server for Raspberry Pi from [http://www.seafile.com/en/download/](http://www.seafile.com/en/download/).

``` sh
wget https://bitbucket.org/haiwen/seafile/downloads/seafile-server_2.1.5_pi.tar.gz
tar -xvzf seafile-server_*
mkdir installed
mv seafile-server_* installed
```

You should have the following directory structure:

``` sh
tree /home/seafile/mycloud/ -L 2
/home/seafile/mycloud/
├── installed
│   └── seafile-server_2.1.5_pi.tar.gz
└── seafile-server-2.1.5
    ├── reset-admin.sh
    ├── runtime
    ├── seaf-fuse.sh
    ├── seafile
    ├── seafile.sh
    ├── seahub
    ├── seahub.sh
    ├── setup-seafile-mysql.py
    ├── setup-seafile-mysql.sh
    ├── setup-seafile.sh
    └── upgrade
```

All config files are in the folder `mycloud` (currently there are no config files present yet).
New versions can be installed side by side without having to change the config files. The install process will create a soft link `seafile-server-latest` pointing the the most current installation.


### Step 4 Installation

`$ cd seafile-server-2.1.5`

Before you start the install process you can have a look at the options being configured during installation [here](https://github.com/haiwen/seafile/wiki/Download-and-setup-seafile-server#SSetup).

For this example we'll assume your DDNS domain is `mycloud.no-ip.org` and that we'll use the default location for storing our data.
Furthermore, we'll use `mycloud` as our server name.

During the installation of Seahub (the web frontend for the Seafile server) you must enter an admin email address and provide a password (this password is your Seafile admin password and should not be the same as your email account password).

All other question can be answered by using the default values (press ENTER).

`./setup-seafile.sh`

Your directory tree should now look something like this:

``` sh
tree . -L 2
.
├── ccnet # <--------------------------- configuration files
│   ├── ccnet.conf
│   ├── ccnet.conf.lan
│   ├── ccnet.conf.wan
│   ├── ccnet.db
│   ├── GroupMgr
│   ├── misc
│   ├── mykey.peer
│   ├── OrgMgr
│   ├── PeerMgr
│   └── seafile.ini
├── conf
│   └── seafdav.conf
├── installed
│   └── seafile-server_2.1.5_pi.tar.gz
├── logs
│   ├── ccnet.log
│   ├── controller.log
│   ├── http.log
│   ├── seafile.log
│   ├── seahub_django_request.log
│   └── seahub.log
├── seafile-server-2.1.5
│   ├── reset-admin.sh
│   ├── runtime
│   ├── seaf-fuse.sh
│   ├── seafile
│   ├── seafile.sh
│   ├── seahub
│   ├── seahub.sh
│   ├── setup-seafile-mysql.py
│   ├── setup-seafile-mysql.sh
│   ├── setup-seafile.sh
│   └── upgrade
├── seafile-server-latest -> seafile-server-2.1.5
├── seahub-data
│   └── avatars
├── seahub.db
├── seahub_settings.py
└── seahub_settings.pyc
```

### Step 5 Update URLs for Seahub

`nano /home/seafile/mycloud/ccnet/ccnet.conf`

``` sh /home/seafile/mycloud/ccnet/ccnet.conf
SERVICE_URL = https://mycloud.no-ip.org:8001
```

Don't forget replacing `http` with `https`...

Also add a line to `seahub_settings.py`.

`nano /home/seafile/mycloud/seahub_settings.py`

``` sh /home/seafile/mycloud/seahub_settings.py
SECRET_KEY ...
HTTP_SERVER_ROOT = 'https://mycloud.no-ip.org:8001/seafhttp'
```


### Step 6 (Re)start Seahub in FastCGI mode

`/home/seafile/mycloud/seafile-server-latest/seahub.sh stop`

`/home/seafile/mycloud/seafile-server-latest/seahub.sh start-fastcgi`


### Step 7 Install nginx (as admin)

IMPORTANT: Do not run steps 7 to 11 as user `seafile`. Use the default `pi` user for admin stuff.

`sudo aptitude install nginx`

Patching nginx for Raspberry Pi:
``` sh
sudo sed -i "s/worker_processes 4;/worker_processes 1;/g" /etc/nginx/nginx.conf
sudo sed -i "s/worker_connections 768;/worker_connections 128;/g" /etc/nginx/nginx.conf
sudo /etc/init.d/nginx start
```

### Step 8 Create a self certified SSL certificate (as admin)

The following commands create a self certified SSL certificate. The second to last command is interactive and will ask a few questions. Provide *Country Name* (enter your two letter country code, i.e. DE for Germany, UK for United Kingdom) and *Common Name*. The later should be your DDNS name (`mycloud.no-ip.org` in this example).

``` sh
sudo mkdir /etc/nginx/ssl
cd /etc/nginx/ssl
sudo openssl genrsa -out seahub.key 2048
sudo openssl req -new -key seahub.key -out seahub.csr
sudo openssl x509 -req -days 3650 -in seahub.csr -signkey seahub.key -out seahub.crt
```

### Step 9 Create nginx Seahub site (as admin)

`sudo nano /etc/nginx/sites-available/seahub`

``` sh /etc/nginx/sites-available/seahub
server {
    listen 8001; # <--------------------------------------- NGINX PORT
    ssl on; # <-------------------------------------------- SSL
    ssl_certificate /etc/nginx/ssl/seahub.crt; # <--------- SSL
    ssl_certificate_key /etc/nginx/ssl/seahub.key; # <----- SSL
    server_name mycloud.no-ip.org.tld; # <----------------- CHANGE THIS
    error_page 497  https://$host:$server_port$request_uri;
    
    location / {
        fastcgi_pass    127.0.0.1:8000;
        fastcgi_param   SCRIPT_FILENAME     $document_root$fastcgi_script_name;
        fastcgi_param   PATH_INFO           $fastcgi_script_name;
 
        fastcgi_param   SERVER_PROTOCOL $server_protocol;
        fastcgi_param   QUERY_STRING        $query_string;
        fastcgi_param   REQUEST_METHOD      $request_method;
        fastcgi_param   CONTENT_TYPE        $content_type;
        fastcgi_param   CONTENT_LENGTH      $content_length;
        fastcgi_param   SERVER_ADDR         $server_addr;
        fastcgi_param   SERVER_PORT         $server_port;
        fastcgi_param   SERVER_NAME         $server_name;
        fastcgi_param   HTTPS   on;
        fastcgi_param HTTP_SCHEME https;
     
        access_log      /var/log/nginx/seahub.access.log;
        error_log       /var/log/nginx/seahub.error.log;
    }       
    location /seafhttp {
        rewrite ^/seafhttp(.*)$ $1 break;
        proxy_pass http://127.0.0.1:8082;
        client_max_body_size 0;
    }
    
    location /media {
        root /home/seafile/mycloud/seafile-serverlatest/seahub; # <-- change: 2014-07-11
        # include /etc/nginx/mime.types; # <--- UNCOMMENT THIS IF CSS FILES AREN'T LOADED
    }
}
```


### Step 10 Activate nginx Seahub site (as admin)

`sudo ln -s /etc/nginx/sites-available/seahub /etc/nginx/sites-enabled/seahub`


### Step 11 Restart nginx (as admin)

`sudo /etc/init.d/nginx restart`

### Step 12 Network: Setup port forwarding

Depending on your router, the naming might differ. Some routers call it "port mapping", some call it "port forwarding". For what we are doing, it's all the same.

My ISP provided me with a combined dsl-modem/router called "Easybox 904 xDSL", so YMMV:

{% img center /images/posts/seafile_rpi/router_portmapping.png %}


### Step 13 Test if everything works

#### LAN test using IP address

Test if Seafile/Seahub is reachable from within your LAN using the *IP address* of your Raspberry Pi. Assuming the Raspberry Pi has the internal IP address 192.168.1.42, entering `https://192.168.1.42:8001` in your browser should render the Seahub page.

If this fails, go back and confirm that you don't have any typos in your config files.

#### Internet test

Test if Seafile/Seahub is reachable from the *internet*. You must use a device which is not connected to your LAN. If you have a smartphone, you can deactivate your WiFi, and try connecting to your newly setup server at `https://mycloud.no-ip.org:8001`. The Seahub page should be rendered correctly in your 'internet' browser.

If this fails, go back and confirm that you don't have any typos in your config files.

#### LAN test using DDNS

Try connecting to Seafile/Seahub from within the LAN using the DDNS name: `https://mycloud.no-ip.org:8001`.

If this works out of the box, you can skip the rest of this article.

### Step 14

I should probably mention that I am not a trained network admin. So everything below falls under the category "works for my setup, sorry I can't provide support".

Here's an image of what has to be accomplished:

{% img center /images/posts/seafile_rpi/nat-loopback.svg %}


### Step 14a NAT loopback

If trying to reach the IP of your DDNS from within your LAN fails, you should try to setup your NAT loopback (see your router documentation for details).



### Step 14b Workaround, in case NAT loopback doesn't work

This is the tricky part. (Marko, thanks!)

*BEWARE: This works with my setup, but it might ruin your setup.*

1. Rename internal LAN DNS zone to `no-ip.org` (replace with your domain)

    {% img center /images/posts/seafile_rpi/router_rename_wan_domain.png 'Rename internal LAN DNS zone' %}

2. Rename internal LAN name (static DHCP lease) to `mycould` (replace with your sub domain)

    {% img center /images/posts/seafile_rpi/router_dhcp_static_leases.png 'Rename internal LAN name' %}
