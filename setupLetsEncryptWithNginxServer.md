# How To Set Up Let's Encrypt with Nginx Server Blocks on Ubuntu 16.04

Introduction

Let's Encrypt is a Certificate Authority (CA) that provides an easy way to obtain and install free TLS/SSL certificates, thereby enabling encrypted HTTPS on web servers. It simplifies the process by providing a software client, Certbot, that attempts to automate most (if not all) of the required steps. Currently, the entire process of obtaining and installing a certificate is fully automated on both Apache and Nginx.

In this article, you will use Certbot to obtain a free SSL certificate for Nginx on Ubuntu 16.04 and set up your certificate to renew automatically.

This article will use a separate Nginx server block file instead of the default file. Best practice recommends creating new Nginx server block files for each domain because it helps to avoid some common mistakes and maintains the default files as a fallback configuration as intended.

## Prerequisites
To follow this article, you will need:

-	One Ubuntu 16.04 server set up by following this initial server setup for Ubuntu 16.04 tutorial, including a sudo non-root user and a firewall.
-	A fully registered domain name. This tutorial will use example.com throughout. You can purchase a domain name on Namecheap, get one for free on Freenom, or use the domain registrar of your choice.
-	Both of the following DNS records set up for your server. You can follow this hostname tutorial for details on how to add them.

	-	An A record with `example.com` pointing to your server's public IP address.
	-	An A record with `www.example.com` pointing to your server's public IP address.

-	Nginx installed by following How To Install Nginx on Ubuntu 16.04.
-	A separate Nginx server block file for your domain, set up by following this Nginx server blocks tutorial for Ubuntu 16.04. This tutorial will use `/etc/nginx/sites-available/example.com`

###	Step 1 -	Installing Certbot

The first step to using Let's Encrypt to obtain an SSL certificate is to install the Certbot software on your server.

Certbot is in very active development, so the Certbot packages provided by Ubuntu tend to be outdated. However, the Certbot developers maintain a Ubuntu software repository with up-to-date versions, so we'll use that repository instead.

First, add the repository.

```
$ 	sudo add-apt-repository ppa:certbot/certbot
```

You'll need to press `ENTER` to accept. Then, update the package list to pick up the new repository's package information.

```
$	sudo apt-get update
```

And finally, install Certbot's Nginx package with `apt-get`.

```
$ 	sudo apt-get install python-certbot-nginx
```

Certbot is now ready to use, but in order for it to configure SSL for Nginx, we need to verify some of Nginx's configuration.

###	Step 2 — Confirming Nginx's Configuration

Certbot needs to be able to find the correct `server` block in your Nginx configuration for it to be able to automatically configure SSL. Specifically, it does this by looking for a server_name directive that matches the domain you request a certificate for.

If you followed the prerequisite tutorial on Nginx server blocks, you should have a server block for your domain at `/etc/nginx/sites-available/example.com` with the `server_name` directive already set appropriately.

To check, open the server block file for your domain using `nano` or your favorite text editor.

```
$	sudo nano /etc/nginx/sites-available/example.com
```

Find the existing `server_name` line. It should look like this:

file excerptFile excerpt: **/etc/nginx/sites-available/example.com**

```
. . .
server_name example.com www.example.com;
. . .
```

If it does, you can exit your editor and move on to the next step.

If it doesn't, update it to match. Then save the file, quit your editor, and verify the syntax of your configuration edits.

```
$	sudo nginx -t
```

If you get an error, reopen the server block file and check for any typos or missing characters. Once your configuration file's syntax is correct, reload Nginx to load the new configuration.

```
$	sudo systemctl reload nginx
```

Certbot can now find the correct `server` block and update it.

Next, we'll update our firewall to allow HTTPS traffic.

###	Step 3 — Allowing HTTPS Through the Firewall

If you have the ufw firewall enabled, as recommended by the prerequisite guides, you'll need to adjust the settings to allow for HTTPS traffic. Luckily, Nginx registers a few profiles with `ufw` upon installation.

You can see the current setting by typing:

```
$	sudo ufw status
```

It will probably look like this, meaning that only HTTP traffic is allowed to the web server:

![my image is missing: ufwStatusoutputimg..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/ufwStatusoutputimg.png)

To additionally let in HTTPS traffic, we can allow the Nginx Full profile and then delete the redundant Nginx HTTP profile allowance:

```
$	sudo ufw allow 'Nginx Full'
$	sudo ufw delete allow 'Nginx HTTP'
```

Your status should look like this now:

```
$	sudo ufw status
```

![my image is missing: ufwStatusHTTPimg..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/ufwStatusHTTPimg.png)





























