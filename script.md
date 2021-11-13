This shell script can be used to deploy react app in both producction and stage.

Name this file as deploy.sh 
--- This file can only be used after you deployed and want to push it again.

```
#!/bin/sh  
echo server alias Name:
read server_alias
yarn build 
echo “Building React Project …”
cd build
zip -r ../file.zip *
cd ..
echo “Copying file …” 
rsync -av -e ssh file.zip $server_alias:/home/ubuntu/
echo “Removing file from local directory …” 
rm -r build file.zip
ssh server_alias 'unzip file.zip -d student-portal/'
```

if you want to have fresh deploy then you can do following>

``` 
echo server alias Name:
read server_alias
ssh $server_alias 'sudo apt install nginx && ufw app list && ufw allow 'Nginx HTTP' && ufw allow 'Nginx HTTPS' && ufw allow 'Nginx Full' && systemctl enable nginx && systemctl start nginx'
ssh $server_alias:/home/ubuntu/ && 'mkdir frontend conf && touch frontend_nginx.conf'
ssh $server_alias:/home/ubuntu/ && ''

```
