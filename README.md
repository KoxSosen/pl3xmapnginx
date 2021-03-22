# Nginx tutorial for [Pl3xMap](https://github.com/pl3xgaming/Pl3xMap/)


This tutorial assumes that you have

- root/sudo access on the machine,
- you run a linux distro with apt-get.
- you have port `80`, and port `443` open at TCP.
- you allready ran pl3xmap once with the internal webserver enabled.  
- #### you have ***the latest stable relase of*** Ngix installed. No additional changes required, the default config is great. 

#### Here is a great tutorial: [Click here](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04). Follow till Step 4, and come back here.

### Serve the map with Nginx without a Reverse Proxy (recommented)(+sll).


#### 1 - First we have to set up a Server Block for Pl3xMap. This will be the directory that Nginx will serve the map from.
```
sudo mkdir -p /var/www/your_domain/html
```
Replace `your_domain` with the domain you wish to use. This can be the name of your subdomain too.
***
#### 2 - Next, we will have to assing the correct permission to the server block, to allow your user to read/whrite. 
```
sudo chown -R $USER:$USER /var/www/your_domain/html
sudo chmod -R 755 /var/www/your_domain
```
The fist command will assign ownership to your user.
The second command will allow the owner to read, write, and execute, while only allow read and execute perms to groups and others
***
### 2.1 - Addotional step: Instead of giving pl3xmap whrite & read access, you can add your user to the www-data group.
```
sudo usermod -a -G www-data youruser
```
### 2.2 - An other additional step. In the next step we will copy the files from pl3xmaps webdir to Nginxes webroot. It's fine if you want to do  that, however it can be sloved with a simple symlink too. 
```
sudo ln -s ~/plugins/Pl3xMap/web /var/www/your_domain/html
```
Please note that after executing this step, your Nginx server block configuration will require addtional options since it's a symlink. If you wouldn't like to deal with this you can skip this step, and move to the 3rd one.
***
#### 3 - In the next step we will move the files from the defult pl3xmap web dir (`~/plugins/Pl3xMap/web`) to Ngixes web root. (`/var/www/your_domain/html`)
```
sudo cp /path/to/your/server/plugins/Pl3xMap/web /var/www/your_domain/html 
```
As usual, replace `your_domain` with your domain, and `/path/to/your/server/` to the path where you run the server.

**Important**: Now that you moved the web direcotry to `/var/www/your_domain/html` you need to change the path in pl3xmaps directory is well.
The option can be found here:
```yml
web: /var/www/your_domain/html
```
***
#### 4 - The next part is very important. Here we will set up the server block with the correct directives.
```
sudo nano /etc/nginx/sites-available/your_domain
```
This will create a new file under `/etc/nginx/sites-avalivable`. Add this inside:
```
server {
        listen 80; // listens on port 80
        listen [::]:80; // listen on all adresses port 80

        root /var/www/your_domain/html; // replace with your path
        index index.html; // index file name

        server_name your_domain; // replace with your domain

        location / { // location of your files
                try_files $uri $uri/ =404; // if location == null return 404
        }
}

```
This will create a new block for pl3xmap. **Pay close attention to the comments.** (For more experienced users: we will talk about ssl support later in this tutorial, so you don't have to add anything regarding it.)
Hit `ctrl+x`, and then `y`.
***
#### 5 - In this step wil will activate the server block by symlinking the now created block with an other dirs file.
```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
This command will symlink our just created block file with one in `/etc/ngnix/sites-enabled/`. This is the direcotry where Nginx will read for live site blocks, and enable them.
***
#### 5.1 - Additional step: To avoid a possible hash bucket memory problem that can arise from adding additional server names, it is necessary to adjust a single value in the `/etc/nginx/nginx.conf` file.
```
...
http {
    ...
    server_names_hash_bucket_size 64; // uncomment this option
    ...
}
...
```
This option controls server names. You can learn more [Here]().
***
#### 6 - Test our configuration.
```
sudo nginx -t
```
Testing is very important. This command will check for syntax errors, and missconfigured options.
***
#### 7 - Restart Nginx
```
sudo systemctl restart nginx
```
If there where no errors, restart Nginx with the command. At this point you should have pl3xmap working.
Navigate to `http://yourip/`, and you should see the map. 
***
#### 8 - Add a DNS record to your domain.

Navigate to your registar, and add an `A` record pointing at your ipv4 adress. I recommend using [Cloudflare]().
***
#### 8.1 This step is additional if you would not like to use nginxes certbot plugin.

In step 9, we will use certbots built in nginx plugin, to install and gain an ssl certificate for our website. If you would like to to do that, skip this step, and go to step 9. If you would like to do it manually, please refer to [this guide](https://github.com/mikroskeem/nginx-config/blob/master/doc/certbot.md).
***
#### 9 - Add ssl for an encrypted connection & security.
```
sudo apt-get update
sudo apt-get install certbot
sudo cerbot --nginx
```
The folowing steps are very straight foward. Choose your server block, and your prefererd option. Wait for it to finish.
***
### Now navigate to https://map.yourdomain.test . Congrats!!

