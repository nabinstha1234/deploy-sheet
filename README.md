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
