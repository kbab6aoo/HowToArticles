# Getting Started with Varnish Cache

Does your server need to handle lots of traffic? Caching is one of the best ways to maximize the output of your Server. But what is caching, exactly?

The idea behind caching is that your server shouldn’t have to regenerate the same dynamic content from scratch every time it’s accessed. Save your server resources by putting a caching proxy like **Varnish** in front of your web service to accelerate responses to HTTP requests and reduce server workload.  
![alt text](https://github.com/kbab6aoo/HowToArticles/blob/myRedHatPackageManager/HowToArticles/varnish.png)  

Varnish works by handling requests before they make it your backend; whether your backend is Apache, Nginx, or any other webserver.  If it doesn't have a request cached, it will forward the request to your backend and then cache its output.  You can then store these cache requests to your backend and then chace its output.  You an then store these cached requests in memory, so they are retrieved by and delivered to clients much faster than they would be from disk.  

Additionally, Varnish cache can be used as part of a highly available environment, which ensures uptime during high traffic loads to server failures.  

_Before You Begin - Checklist_  

1.	Set your hostname and timezone
2.	Secure your server and create a standard user account 
3.	Install and configure a web server
4.	Update your system
	
	sudo apt update && sudo apt upgrade
>## Note
>This guide is written for a non-root user.  Commands that required elevated privileges are prefixed with `sudo`.

Install and Configure Varnish Cache