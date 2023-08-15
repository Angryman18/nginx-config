# How to Deploy on Nginx Server

- ### On an empty ubuntu machine install `nginx, git, node, vim`

- ### Clone your repo on `/var/www/abcd.com` here is `abcd.com` is the folder name and make sure you have files in it. if your are cloning from github then `git clone https://github.com/xyz.git abcd.com` this way will create a folder named `abcd.com` and you will have your files in it.

- ### Now you have your files you can do `yarn install` to install all the dependencies.

- ### Now you install `pm2` which helps to run your server if you exit your terminal. so do `npm i -g pm2` since we need to install it globally.

- ### Here after installing pm2 we can run our server using `pm2 start server.js`. Assume the `server.js` file is the entry point of your application.

- ### Now lets install `nginx` by running `sudo apt-get install nginx`

- ### after installing `nginx` we need to configure it properly.

- ### Very First lets create a configuration file for our app which we are going to deploy. in order to do that go to `/etc/nginx/sites-available` and create a file called `abcd.com` and open it using vim. like this `sudo vim /etc/nginx/sites-available/abcd.com`. On the vim editor lets paste some code that we need.

```
server {
 server_name 165.232.177.116; // if you donot have any domain then keep your local id otherwise you gotaa put your domain name here and www version also you can put

     location / {
       proxy_pass http://localhost:3000; // Here its your Node app port
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
   }
}
```

- ### It should look something like this below

```
server {
 server_name abcd.com www.abcd.com;
     location / {
       proxy_pass http://localhost:3000;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection 'upgrade';
       proxy_set_header Host $host;
       proxy_cache_bypass $http_upgrade;
   }
}
```

- ### After than save and exit out of the vim editor by pressing `Esc` and then type `:wq` command. you can write on your terminal `vim -h` in order to know more about the commands on vim editor.

- ### You can check your if you have correct syntex or not by running `nginx -t`

- ### After doing that we need to create a link to a directory called `/etc/nginx/sites-enabled` and to do it we run this below command

```
sudo ln -s /etc/nginx/sites-available/abcd.com /etc/nginx/sites-enabled/abcd.com
```

- ### _Replace `abcd.com` to your desired domain name i have put here as an example. when you replace you should replace all the places in `/var/www/abcd.com` this one also as you may have multiple app running on the same server so its good to diferentiate them using the specefic domain names._

- ### After doing that we need to make sure we delete the `default` folder from the `/etc/nginx/sites-available` directory and also from `/etc/nginx/sites-enabled` directory.

- ### Restart NGINX by running `sudo nginx -s reload`

- ### after that you can run `pm2 start server.js` to start your server and congrats you have successfully deployed your app.


# Firewell Setup & SSL
- ### Run this to enable firewell.
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

- ### these are required or you might also want to add these also

```
sudo ufw allow 'OpenSSH'
sudo ufw allow 'Nginx Full'
```

### This above one is optional.


### Lets add SSL With Lets Encrypt

```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```