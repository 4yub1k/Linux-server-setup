
**1- Update:**
```
sudo apt update
```
**2- Install NginX**
```
sudo apt install nginx
```
**3- Configure Firewall**
```
sudo ufw app list | grep Nginx
```
![image](https://user-images.githubusercontent.com/45902447/157601868-4733b351-1f56-4eb0-a1bf-1d5d59f3797a.png)

**Nginx Full**: both port **80**, port **443**\
**Nginx HTTP**: only port **80** (unencrypted web traffic)\
**Nginx HTTPS**: only port **443** (TLS/SSL encrypted traffic)\

For active rules/profiles:\
![image](https://user-images.githubusercontent.com/45902447/157602088-84a64a3c-f4c2-4487-bda9-f7dc273c2381.png)

**4- Allow port 80:**

Note: you can later allow HTTPS incase for using SSL/TLS.
```
sudo ufw allow 'Nginx HTTP'
```
![image](https://user-images.githubusercontent.com/45902447/157602700-67fc521f-58ec-4f97-8de9-e23c4894efa0.png)

Now, check if server is up
```
systemctl status nginx
```
![image](https://user-images.githubusercontent.com/45902447/157603740-0c678d43-5e08-4919-b7f8-7bc9654d3712.png)

use :
```
ip a | grep inet
```
Or
```
ip addr show ens33 | grep inet      #ens33 your interface name
```
![image](https://user-images.githubusercontent.com/45902447/157603251-5176f0e1-90ae-4e2d-a505-bb0b0bc6078d.png)

Goto your brower and enter:
```
http://192.168.242.131
```
![image](https://user-images.githubusercontent.com/45902447/157603601-9bed01c7-cc88-4880-83de-c2e8f23a5a0d.png)

After making any config changes to server always do:
```
sudo systemctl reload nginx
```
**5- Setup Directory**

_**server block** or **NginX virtual host** which is by default configured to server documents at /var/www/html_

it is best practice to create directory with domain name, in order to make it easy to manage directories for multiple domains on same server.

```
/var/www/domain_name/html
```
```
1- sudo mkdir -p /var/www/domain_test/html
2- sudo chown -R salah:salah -p /var/www/domain_test/html  or use sudo chown -R $USER:$USER -p /var/www/domain_test/html
3- sudo chmod -R 755  /var/www/domain_test/html
```
Create index.html:
```
nano /var/www/domain_test/html/index.html
```
```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Nginx Server</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
        <div>   <h1>Welcome to Home</h1>
                <p> Hi, i'm Salah ud din, </p>
        </div>
  </body>
</html>
```
Save it ctrl + s,then ctrl + x.\
**6- create Server Block**
_configure the webroot in order to server files of our directory_
```
/etc/nginx/sites-available/domain_test
```

let's configure it

```
sudo nano /etc/nginx/sites-available/domain_test
```
Add:
```
server {
        listen 80;
        listen [::]:80;

        root /var/www/domain_test/html;
        index index.html;

        server_name domain_test www.domain_test;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
Save and exit.\
Note: Modify it accoring to requirements.\
**For details https://www.nginx.com/resources/wiki/start/topics/examples/server_blocks/**

**7- Create link NginX startup read files**
```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
**Nginx Config:**
_for any configuration modify this file, before this refer to Nginx documentaions_

```
sudo nano /etc/nginx/nginx.conf
```
**8- Check Nginx sytax errors:**
```
sudo nginx -t
```
![image](https://user-images.githubusercontent.com/45902447/157609459-fe78b122-d635-4ad8-afce-65cae13d7406.png)

now, restart the server
```
sudo systemctl restart nginx
```
![image](https://user-images.githubusercontent.com/45902447/157610349-d96a473b-6a70-49e1-8ab1-e0bac6735eb4.png)

### And Make sure the DNS is configured for site

Note : you are now serving two directory one default and the other domain_test\
**domain_test**: Will respond to requests for your_domain and www.domain_test.\
**default**: Will respond to any requests on port 80 that do not match the other two blocks.

you can remove default (rm) index.nginx-debian.html, or remove /var/www/html, or edit index.nginx-debian.html to show custom message\
OR if you want to change deafult server,
```
sudo nano /etc/nginx/sites-enabled/default
```
Remove the default_server
```
        listen 80 default_server;
        listen [::]:80 default_server;
```
save and exit.

Now open you domain_test 
```
sudo nano /etc/nginx/sites-enabled/domain_test
```
Add:
```
        listen 80 default_server;
        listen [::]:80 default_server;
```
save and exit

### DONE
