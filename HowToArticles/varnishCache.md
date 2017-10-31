# Getting Started with Varnish Cache

Does your server need to handle lots of traffic? Caching is one of the best ways to maximize the output of your Server. But what is caching, exactly?

The idea behind caching is that your server shouldn’t have to regenerate the same dynamic content from scratch every time it’s accessed. Save your server resources by putting a caching proxy like **Varnish** in front of your web service to accelerate responses to HTTP requests and reduce server workload.  

![alt text](https://github.com/kbab6aoo/HowToArticles/blob/myRedHatPackageManager/HowToArticles/varnish.png)  

Varnish works by handling requests before they make it your backend; whether your backend is Apache, Nginx, or any other webserver.  If it doesn't have a request cached, it will forward the request to your backend and then cache its output.  You can then store these cache requests to your backend and then chace its output.  You an then store these cached requests in memory, so they are retrieved by and delivered to clients much faster than they would be from disk.  

Additionally, Varnish cache can be used as part of a highly available environment, which ensures uptime during high traffic loads to server failures.  

## _Before You Begin - Checklist_  

1.	Set your hostname and timezone
2.	Secure your server and create a standard user account 
3.	Install and configure a web server
4.	Update your system
	
		sudo apt update && sudo apt upgrade
>### Note
>This guide is written for a non-root user.  Commands that required elevated privileges are prefixed with `sudo`.

#### Install and Configure Varnish Cache

1.	Install Varnish with the package manager:

		sudo apt install varnish
2.	To avoid having your configuration overwritten by future updates, make a copy of the default:

		cd /etc/varnish
		sudo cp default.vcl user.vcl
3.	Stop the Varnish Service while making configuration changes:

		sudo systemctl stop varnish

## Configure Varnish Backend with Systemd
Varnish is configured via Varnish Configuration Language (VCL).  Once the configuration file is loaded by the system, Varnish translates and compiles the VCL code into a C program that runs alongside the Varnish process.  

Recent versions of Debian (8 and newer) and Ubuntu (15.04 and newer) require Varnish Configuration through systemd.

1.	Open the `varnish.service` file, set the port, configuration file, and _memory allocation_ on the `ExecStart` line.  In the following example, these values are: `-a :80`, `/etc/varnish/user.vc/` and `malloc,1G`.  
File excerpt: **/lib/systemd/system/varnish.service**

		ExecStart=/usr/sbin/varnishd -j unix,user=vcache -F -a :80 -T localhost:6082 -f /etc/varnish/user.vcl -S /etc/varnish/secret -s malloc,1G

2.	The configuration above allocates a maximum of 1GB of memory to store its cache items.  If you need to adjust this allocation, edit the number in `-s malloc,1G`.  For example, to allocate 2GB of memory:

		-s malloc,1G
3.	Reload systemd:

		sudo systemctl daemon-reload

## Modify Custom Varnish Configuration VCL

Now that we have pointed the Varnish start script to `user.vcl` we need to configure that file to serve the content Varnish gets from the web server.  Edit the `backend default{` section of `/etc/varnish/user.vcl` to tell Varnish where to get the server (backend) content.  The example below uses port `8080`, a web server setting you will configure later:  

File excerpt: **/etc/varnish/user.vcl**  

		backend default {
			.host = "127.0.0.1";
			.port = "8080";
		}

## Configure Cache Time-to-live (TTL)

By default Varnish will cache requests for two minutes.  To adjust this time, open your VCL file and override the `vcl_backend_response` subroutine by updating your backend declaration:

File excerpt: **/etc/varnish/user.vcl**  

		sub vcl_backend_response {
			set beresp.ttl = 5m;
		}

This subroutine is called after request is fetched from the backend.  In this example, we are setting the TTL variable on the object to five minutes (`5m`).  Values can be in seconds (`120s`), minutes (`2m`) or (`2h`).  Your ideal TTL may vary dependending on how often the content of your site is updated, and the amount of traffic you need to handle.

## Take Varnish Live: Configure Web Traffic to Serve Cached Content
![alt text](https://github.com/kbab6aoo/HowToArticles/blob/myRedHatPackageManager/HowToArticles/WebTrafficDiagram.png)































