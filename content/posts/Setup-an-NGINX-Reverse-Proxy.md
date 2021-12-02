---

title: Setup an NGINX Reverse Proxy
date: 2021-12-01T22:59:00Z
author: "ahmza"
draft: false

tags: ["HomeLab", "Networking", "NGINX", "ReverseProxy"]
description: "Learn to setup a quick and easy reverse proxy using NGINX"
canonicalURL: "https://blog.ahmza.com/Page%28/posts/Setup-an-NGINX-Reverse-Proxy.md%29"

cover:
    image: "/img/Reverse-Proxy/feature-anim.gif" # image path/url
    alt: "abstracted reverse proxy diagram" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    searchHidden: false

---


# **Introduction**
In this post we will go over setting up an *NGINX* reverse proxy to **allow different
web services to reach the outside world.** We will also cover setting up **auto
renewing SSL certificates.**

Why not just port forward the services, it does the same thing Right? Reverse
proxies are a great way to expose web services to the outside world in a more
secure and customisable manner. **Some of the features that a reverse proxy provides
are as follows:**

* **Host multiple services using the same HTTP/HTTPS ports**
* **Hides the origin server**
* **HTTPS TLS encryption**
* **Caching**
* **Compression**

# **Creating a Reverse Proxy**
## **Prerequisites**
* **a server to act as the reverse proxy**
* **a web server**
* **a registered domain name**
* **Have A records setup for the Domain name**

## **Install** ***NGINX***
I'll be using the apt package manager as I'm on Ubuntu server,
this may differ for you

Update packages, then **install** ***NGINX***
``` bash
sudo apt update
sudo apt install nginx
```
Now we want to **enable** ***NGINX*** so the program will start when the server boots
``` bash
sudo systemctl enable --now nginx
```

# **Firewall**
> **Quick Note:** now is a good time to port forward ports 80 and 443 from the router
> to the reverse proxy

Ubuntu server comes with a firewall (ufw), so be sure to allow access to *NGINX* and
enable it.

The reverse proxy handles both HTTP and HTTPS so **allow "Nginx Full"**
``` bash
sudo ufw allow 'Nginx full'
```
**If you are using SSH allow it through**
``` bash
sudo ufw allow 'OpenSSH'
```

# **Configure** ***NGINX***
**Unlink the default ***NGINX*** config**
``` bash
sudo unlink /etc/nginx/sites-enabled/default
```
## **Create ***NGINX*** Config**
Create a config file in **/etc/nginx/site-available**
> **Quick Note:** use whichever text editor you want, I'm using vim
``` bash
sudo vim /etc/nginx/sites-available/blog.conf
```
``` nginx
server {
    listen 80;
    server_name blog.example.com;
    location / {
        proxy_pass http://ip-of-server:port;
    }
}
```
* **server_name** is the domain name you want use this can include sub-domains
* **proxy_pass is the IP address of the web server**

## **Enable ***NGINX*** Config**
To activate a config file we need to link the file from the **sites-available**
folder to **sites-enabled**.
> **Pro Tip:** make sure to use full paths when creating symlinks
``` bash
sudo ln -s /etc/nginx/sites-available/blog.conf /etc/nginx/sites-enabled/blog.conf
```
**Check the config**, this is an important step as it warns you of any errors before
reloading *NGINX*
``` bash
sudo nginx -t
```
If the output looks simmiler to below then you're good to go !!
``` bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
Now **restart the ***NGINX*** service**
``` bash
sudo systemctl restart nginx
```
you should now have a working reverse proxy !!

# **Setting up SSL**
## **Prerequisites**
* Make sure HTTP and HTTPS are port forwarded to the reverse proxy
* Make sure **server_name** is set in the config file you created

## **Installing** ***Certbot***
*Certbot* is a tool that requests an SSL certificate and makes sure it is renewed
when needed.
**Install** ***Certbot*** as well as the **python ***NGINX*** plug-in**
``` bash
sudo apt install certbot python3-certbot-nginx
```
> **Quick Note:** double check firewall rules

## **Request SSL Certificate**
Now all we need to do is **request the certificate** !!
``` bash
sudo certbot --nginx -d blog.example.com
```
* **--nginx** specifies we are using NGINX, this will update the config file we created
to allow the use of SSL
* **-d** is the domain name you want to use

If this is the first time running *Certbot* you will be asked to enter an email as well
as agree to terms and services.

If the certificate request is successful you will be prompted with

``` bash
Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
```
**If you want all traffic to be SSL encrypted choose option 2**
That's it we have SSL certificates installed. You will need a new certificate for each
different domain name or subdomain.

## **Verify Auto Renewal**
All we need to do now is make sure that Certbot can automatically renew certificates.
``` bash
sudo certbot renew --dry-run
```
If you see no errors then you are ready to go.

# **Conclusion**
By following this you have hopefully managed to setup an *NGINX* Reverse proxy to
handle incoming HTTP/HTTPS requests as well as configured automatically renewing
SSL certificates.

This posts barely scratches the surface of what *NGINX* can do, I would highly
recommend reading more into this great piece of software !!

My reverse proxy handling non existent traffic
![neo awakening](/img/gifs/i-know-kung-fu.gif)

Give it a few months and this blog will ...
![matrix flop](/img/gifs/matrix-jump-fail.gif)
