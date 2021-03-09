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

```

