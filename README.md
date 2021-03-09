## Internal Server

Pl3xMap ships with a very basic internal web server to help host your map files. It has all the bells and whistles you'll need to get your map online and sharable.

```yaml
  # the internal web server settings
  internal-webserver:

    # set to true to use the internal web server.
    # set to false if you want to run your own
    # external web server
    enabled: true

    # the ip the internal web server will bind to
    # (leave this alone if you don't know what it does)
    bind: 0.0.0.0

    # the port the internal web server will bind to
    port: 8080
```

By default, the internal web server is enabled for your convenience. If you need a more advanced setup (like ssl certs) you will want to use an external server instead.

The default address to bind to is `0.0.0.0` which simply means "any address the machine gets." Most people will want to leave this setting alone. It is only used if you have multiple network cards installed on the machine and need to bind to a specific one. If you dont understand what that means, then this option isn't for you and you should leave it alone.

The default port is 8080. You will want to ensure this port is open to outside internet connections so others can access the map. If you have access to port 80 and no other services are using it, you can set it to port 80 which will allow users to use the map without specifying a port in the address bar.

## External Server

There are 2 main options people use these days for web servers. Apache2, and Nginx.

### Apache

_todo_

# Nginx


This tutorial assumes that you have

- root/sudo access on the machine,
- you run a distro with apt-get.
- #### you have Ngix installed. No additional changes required, the default config is great. 

#### Here is a great tutorial: [Click here](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04). Follow till Step 4, and come back here.

### Serve the map with Nginx without a Reverse Proxy (recommented)(+sll).


#### 1 - First we have to set up a Server Block for Pl3xMap. This will be the directory that Nginx will serve the map from.
```
sudo mkdir -p /var/www/your_domain/html
```
Replace `you_domain` with the domain you wish to use. This can be the name of your subdomain too.

#### 2 - Next, we will have to assing the correct permission to the server block, to allow your user to read/whrite. 
```
sudo chown -R $USER:$USER /var/www/your_domain/html
sudo chmod -R 755 /var/www/your_domain
```
The fist command will assign ownership to your user.
The second command will allow the owner to read, write, and execute, while only allow read and execute perms to groups and others

#### 3 - In the next step we will move the files from the defult pl3xmap web dir (`~/plugins/Pl3xMap/web`) to Ngnixes web root. (`/var/www/your_domain/html`)
```
sudo cp /path/to/your/server/plugins/Pl3xMap/web /var/www/your_domain/html 
```
As usual, replace `your_domain` with your domain, and `/path/to/your/server/` to the path where you run the server.

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

#### 5 - In this step wil will activate the server block by symlinking the now created block with an other dirs file.
```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
This command will symlink our just created block file with one in `/etc/ngnix/sites-enabled/`. This is the direcotry where Nginx will read for live site blocks, and enable them.

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

#### 6 - Test our configuration.
```
sudo nginx -t
```
Testing is very important. This command will check for syntax errors, and missconfigured options.

#### 7 - Restart Nginx
```
sudo systemctl restart nginx
```
If there where no errors, restart Nginx with the command. At this point you should have pl3xmap working.
Navigate to `http://yourip/`, and you should see the map. 
## Congrats!
