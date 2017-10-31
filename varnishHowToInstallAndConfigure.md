# Getting Started with Varnish Cache

Does your server need to handle lots of traffic? Caching is one of the best ways to maximize the output of your Server. But what is caching, exactly?

The idea behind caching is that your server shouldn’t have to regenerate the same dynamic content from scratch every time it’s accessed. Save your server resources by putting a caching proxy like **Varnish** in front of your web service to accelerate responses to HTTP requests and reduce server workload.  

![alt text](https://github.com/kbab6aoo/HowToArticles/blob/myRedHatPackageManager/images/varnish.png)   

Varnish works by handling requests before they make it your backend; whether your backend is Apache, Nginx, or any other webserver.  If it doesn't have a request cached, it will forward the request to your backend and then cache its output.  You can then store these cache requests to your backend and then cache its output.  You an then store these cached requests in memory, so they are retrieved by and delivered to clients much faster than they would be from disk.  

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

This subroutine is called after request is fetched from the backend.  In this example, we are setting the TTL variable on the object to five minutes (`5m`).  Values can be in seconds (`120s`), minutes (`2m`) or (`2h`).  Your ideal TTL may vary depending on how often the content of your site is updated, and the amount of traffic you need to handle.

## Take Varnish Live: Configure Web Traffic to Serve Cached Content

Now that we have configured Varnish, use this section to make it your web server by swapping the ports your web server and Varnish listen on.  As illustrated in the graphic below, all web traffic will be served from Varnish cache and refreshed every two minutes or the interval configured above:  

![alt text](![alt text](https://github.com/kbab6aoo/HowToArticles/blob/myRedHatPackageManager/images/WebTrafficDiagram.png)  

To allow Varnish to communicate with our web server, we will need to modify a few settings in the virtual host file for your site.

1.	If using Nginx, skip thi step.  If using Apache, change the port Apache listens on.  Edit `/etc/apache2/ports.conf` and any virtual hosts.  Open `ports.conf` and change the `80` in `Listen 80` to another port.  Port `8080` will be used in for our example.

		Listen 8080

2.	Modify your virtual host file or server block to listen on port 8080:

**Apache:**

File excerpt: /etc/apache2/sites-available/example.com.conf
	
	<VirtualHost *:8080>

**Nginx:**

File excerpt: /etc/nginx/sites-available/example.com

	listen 8080;
	listen [::]:8080;

3.	Check your `/etc/varnish/user.vcl` file and make sure the `backend default` is set to use port 8080:  

		backend default	{
			.host = "127.0.0.1";
			.port = "8080";
		}

4. Reload the configuration for your web server

		sudo systemctl reload apache2
		sudo systemctl restart nginx

5.	Start Varnish

		sudo systemctl start varnish

Once it has been started, Varnish will be live to site visitors and content will be served from the cache whenever possible, according to your configuration.

## Advanced Varnish Configuration
The VCL allows extended control over how requests are cached, and you may need to make some modifications.  This section will go over a few common VCL configurations.  

These modifications should be made in your `user.vcl` file.

## Exclude Content from Varnish Cache
You may want to exclude specific parts of your website from Varnish caching, particularly if there is a non-public or administration portion.  To do this, we need to access Varnish's request object for information about the request, and conditionally tell Varnish to **pass** the request through to the backend with no caching.  

We need to override the `vcl_recv` subroutine in our VCL file, which is run each time Varnish receives a request, then add a conditional:

File excerpt: **/etc/varnish/user.vcl**  

		sub vcl_recv
		{
			if (req.http == "example.com" &&
				req.url ~"^/admin")
			{
				return (pass);
			}
		}

This example checks for two conditions you don't want to cache. The first is any requests for `example.com`, the second is for any URI requests that begin with `/admin`. if both of these are true, Varnish will not cache the request.

## Unset Cookies
As mentioned earlier, if Varnish detects your website is setting cookies, it assumes your site needs to interact with those cookies and shows dynamic content accordingly, and as a result, Varnish will not cache those pages.  You can override this behavior by unsetting the `Cookie` variable on Varnish's `req.http` object.  

Add this line to the bottom of the `vcl_recv` section:

File excerpt: **/etc/varnish/user.vcl**

		unset req.http.Cookie;

You may find that a particular cookie is important for displaying content or determines if your user is logged in or not.  In this case, you probably don't want to show cached content and instead, just want to send the user straight to the backend.  

For this case, we will check `req.http.cookie` for a cookie `logged_in`, and if it's found, the request will be passed on to the backend with no caching.  Here's our entire `vcl_recv` subroutine thus far:

File excerpt: **/etc/varnish/user.vcl**  

		sub vcl_recv
		{
			if ((req.http == "example.com" &&
				req.url ~"^/admin") ||
				req.http.Cookie == "logged_in")
			{
				return (pass);
			}

			unset req.http.Cookie;
		}





















