# Using Varnish to Serve WordPress on Debian 8 SSL & HTTP
_**Varnish**_ is a powerful and flexible caching HTTP reverse proxy.  It can be installed in front of any web server to cache it's content, which will improve speed and reduce server load.  When client requests a webpage, Varnish first tries to send it from cache.  If the page is not cached, Varnish forwards the request to the backend server, fetches the response, stores it in the cache, and delivers it to the client.
![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/VarnishDebian.png)  

When cached resources is requested through Varnish, the request doesn't reach the web server or involve PHP or MySQL execution.  Instead, Varnish reads it from memory, delivering the cached page in a matter of microseconds.  

One Varnish drawback is that it doesn't support SSL-encrypted traffic.  You can circumvent this issue by using **Nginx** for both SSL decryption and as a backend web server.  Using Nginx for both tasks reduces the complexity of the setup, leading to fewer potential points of failure, lower resources consumption, and fewer components to maintain.  

Both Varnish and Nginx are versatile tools with a variety of uses.  This guide uses Varnish 4.0, which comes included in Debian 8 repositories, and presents a basic setup that you can refine to meet your specific needs.

## How Varnish and Nginx Work Together

In this guide, we will configure Nginx and Varnish for two WordPress sites:
-	www.example-over-http.com will be an unencrypted, HTTP-only site.
-	www.example-over-https.com will be a separate, HTTPS-encrypted site.

For HTTP traffic, Varnish will listen on port `80`. If content is found in the cache, Varnish will serve it. If not, it will pass the request to Nginx on port `8080`. In the second case, Nginx will send the requested content back to Varnish on the same port, then Varnish will store the fetched content in the cache and deliver it to the client on port `80`.

For HTTPS traffic, Nginx will listen on port `443` and send decrypted traffic to Varnish on port `80`. If content is found in the cache, Varnish will send the unencrypted content from the cache back to Nginx, which will encrypt it and send it to the client. If content is not found in the cache, Varnish will request it from Nginx on port 8080, store it in the cache, and then send it unencrypted to frontend Nginx, which will encrypt it and send it to the client’s browser.

Our setup is illustrated below. Please note that frontend Nginx and backend Nginx are one and the same server:  

![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/VarnishDebian.png)  

