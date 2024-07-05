---
title: "Django Deployment"
categories: "Deployment"
tags:
  - "Python"
  - "Django"
  - "Deploy"
---


# Install Postgres on Server

[How To Install and Use PostgreSQL on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04)
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
```

```console
sudo systemctl start postgresql.service
```

# Install Git Nginx and other Tools
```console
sudo apt-get install -y nginx git make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils python-setuptools         
```
# Setting Up folders and Remote Repo
```console        
mkdir repo.git app conf logs media static
cd repo.git
```

```console
# create a bare repo
git init --bare
git --bare update-server-info
git config core.bare false
git config receive.denycurrentbranch ignore
git config core.worktree /home/ubuntu/app/ #[update here]
```

```console
cat > hooks/post-receive <<EOF
#!/bin/sh
git checkout -f
EOF
```

```console
chmod +x hooks/post-receive
```

# Setting Up Virtual Env and Install Server Dependency
```console
sudo apt-get install python3-venv libcairo2
sudo apt-get install build-essential libssl-dev libffi-dev
sudo apt-get install python3-pip python3-setuptools python3-wheel
sudo apt-get install libpango-1.0-0 libpangocairo-1.0-0 libgdk-pixbuf2.0-0 shared-mime-info
sudo apt-get install python3-dev python3-cffi
python3 -m venv env
```

# Activate Virtual Env
```console
source env/bin/activate
```

# Install Circus and Chaussette
```console
pip install circus
pip install chaussette
cd conf
nano circus.ini
```

# add this to circus.ini
```console
               
[watcher:webapp]
cmd = chaussette --fd $(circus.sockets.webapp) config.wsgi.application # django setting location
uid=ubuntu
endpoint_owner=ubuntu
numprocesses = 3
use_sockets = True
copy_env = True
copy_path = True
virtualenv = /home/ubuntu/env/
stdout_stream.class = FileStream
stdout_stream.filename = /home/ubuntu/logs/app.log
stderr_stream.class = FileStream
stderr_stream.filename = /home/ubuntu/logs/app_err.log

stdout_stream.max_bytes = 1073741824
stdout_stream.backup_count = 3
stderr_stream.max_bytes = 1073741824
stderr_stream.backup_count = 3

[socket:webapp]
host = 127.0.0.1
port = 8085
```
# Run and demonize circus.ini
```console
circusd --daemon circus.ini
circusctl reloadconfig
circusctl reload
```

# Add Nginx Config
```console
nano nginx.conf  # current location is ~/conf/
```
```console           
upstream django {
    server 127.0.0.1:8085; # for a web port socket see circus.ini [socket:webapp]
}

# configuration of the server
server {

    # the port your site will be served on
    listen 80;

    # the domain name it will serve for
    # server_name demo.example.com;
    charset     utf-8;

    client_max_body_size 75M;

    location /static/ {
            alias /home/ubuntu/static/;
    }

    location /media/ {
        alias /home/ubuntu/media/;
    }

    location / {
        proxy_pass http://django;
        proxy_pass_header Server;
        proxy_set_header Host $host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_connect_timeout 600;
        proxy_send_timeout 600;
        proxy_read_timeout 600;
        # proxy_set_header X-Forwarded-Proto https;
    }
}
```

# Add nginx config to /etc/nginx/nginx.conf
```console
nano /etc/nginx/nginx.conf
# include your nginx conf
# include /home/ubuntu/conf/nginx.conf;
```
# Test config and restart the server

```console
sudo nginx -t    # test should be successful
sudo systemctl restart nginx  # restart the server
```

# Extras

## For HTTPS Part
```console
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx
```
## Update nginx Conf
```console
# include proxy_set_header X-Forwarded-Proto https so your new configuration will look like
    location / {
        ........
        ........
        proxy_set_header X-Forwarded-Proto https;
    }
```
## HTTPS Settings For Django
```console 
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
```
