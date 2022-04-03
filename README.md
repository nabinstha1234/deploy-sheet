After creating new instances we have to do following things

step1. install nginx

``` 
  sudo apt install nginx
  
```

step2. install certbot

```
sudo apt install certbot

```
step3. install certbot python
```
sudo apt install python3-certbot-nginx

```

step4. includes conf in nginx

```
sudo nano ~/etc/nginx/nginx.conf
```

```

user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        
                ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
        include /home/ubuntu/conf/*.conf;
        include /home/ubuntu/backend/app/conf/dvtms_backend_nginx.conf;
        include /home/ubuntu/backend/chat/conf/dvtms_backend_nginx.conf;
}

```


Deploy Frontend Manually using Zip file:

1. First create a build using command:

```	
ng build --prod 

```

2. Now, you can see there's a folder name dist/ in the root directory of the project.

	```
  cd dist/project-name/
  
  ```

3. Inside the project, we can get all the compressed files of FE project. We need to copy them and zip it in a new file.
	
	``` 
  zip -r ../newfile.zip * 
  
  ```

4. Copy the newfile.zip from the directory where it is located.
	scp newfile.zip servername:/home/ubuntu/
	
	``` 
  Examples:
	scp newfile.zip yatrustage:/home/ubuntu/
	scp newfile.zip ubuntu@3.0.156.155:/home/ubuntu/
  ```

5. Go to the server by opening the terminal
	``` 
  ssh servername
  ```
	
6. We can locate the folders using ls (listing) command inside which we want to unzip the file.
	``` 
  unzip newfile.zip -d server-folder/
  
  ```
	
	Example:
	``` 
  unzip newfile.zip -d frontend/
  
  ```

7. And press A for All if asked while unzipping files	


```

1. npm run build && cd build
2. zip -r ../newfile.zip *
3. cd .. && scp newfile.zip levelup:/home/ubuntu/bootcamp/
4. ssh levelup
5. cd bootcamp 
6. unzip newfile.zip -d frontend/
7. press A

```


/server configuration
```
                                                                                             
server {
        client_max_body_size 64M;

        # set the correct host(s) for your site
        server_name domain.com;

        keepalive_timeout 5;

        # path for static files
        root /home/ubuntu/frontend;
        index index.html;

        #rewrite ^/$ https://iwbootcamp.com/login redirect;
        location / {
                       add_header Last-Modified $date_gmt;
        add_header Cache-Control 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
        if_modified_since off;
        expires off;
        etag off;
                try_files $uri $uri/ /index.html;
        }

      listen 80;
}

```

```
sudo nginx -t //it check ngnix config is right or not
sudo systemctl restart nginx //it restart nginx
sudo systemctl status nginx //to print nginx status
sudo certbot --nginx / redirect for https
```
in nano edito 

ctrl +6 to select multiple line
ctrl +k to delete selected line


8. Deploy Backend App

### Install Postgres on Server
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04

``` 
sudo apt update
sudo apt install postgresql postgresql-contrib
```
### Add Remote 

On Your Machine
https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories

```
git remote add server dgfg:/home/ubuntu/repo.git/
git push server --all
```
### Install Git Nginx and other Tools

``` 
sudo apt-get install -y nginx git make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils python-setuptools

```
### Setting Up folders and Remote Repo

```             
mkdir repo.git app conf logs media static
cd repo.git

```

```

# create a bare repo
git init --bare
git --bare update-server-info
git config core.bare false
git config receive.denycurrentbranch ignore
git config core.worktree /home/ubuntu/app/

```

```
cat > hooks/post-receive <<EOF
#!/bin/sh
git checkout -f
EOF

```
``` 
chmod +x hooks/post-receive

```

### Setting Up Virtual Env and Install Server Dependency

``` 
sudo apt-get install python3-venv libcairo2
sudo apt-get install build-essential libssl-dev libffi-dev python-dev
sudo apt-get install python3-pip python3-setuptools python3-wheel
sudo apt-get install libpango-1.0-0 libpangocairo-1.0-0 libgdk-pixbuf2.0-0 shared-mime-info
sudo apt-get install python3-dev python3-cffi
python3 -m venv env

```
### Activate Virtual Env

```
source env/bin/activate

```

### Install Circus and Chaussette

``` 
pip install circus
pip install chaussette
cd conf
nano circus.ini

```

### add this to circus.ini

``` 
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

[env:webapp]
PYTHONPATH=/home/ubuntu/app/

```

### Run and demonize circus.ini

```
                
circusd --daemon circus.ini
circusctl reloadconfig
circusctl reload

```

### Add Nginx Config

```
nano nginx.conf  # current location is ~/conf/

```
```
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

### Add nginx config to /etc/nginx/nginx.conf

``` 
                  
nano /etc/nginx/nginx.conf
# include your nginx conf
# include /home/ubuntu/conf/nginx.conf;
                  
```

### Test config and restart the server

```
sudo nginx -t    # test should be successful
sudo systemctl restart nginx  # restart the server

```

### Extras
### For HTTPS Part

```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx              

```

### Update nginx Conf

```
                  
# include proxy_set_header X-Forwarded-Proto https so your new configuration will look like
    location / {
        ........
        ........
        proxy_set_header X-Forwarded-Proto https;
    }

```

### HTTPS Settings For Django

``` 
USE_X_FORWARDED_HOST = True
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

```
