# Using Varnish to Serve WordPress on Debian 8 SSL & HTTP
_**Varnish**_ is a powerful and flexible caching HTTP reverse proxy.  It can be installed in front of any web server to cache it's content, which will improve speed and reduce server load.  When client requests a webpage, Varnish first tries to send it from cache.  If the page is not cached, Varnish forwards the request to the backend server, fetches the response, stores it in the cache, and delivers it to the client.
![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/VarnishDebian.png)  

When cached resources is requested through Varnish, the request doesn't reach the web server or involve PHP or MySQL execution.  Instead, Varnish reads it from memory, delivering the cached page in a matter of microseconds.  

One Varnish drawback is that it doesn't support SSL-encrypted traffic.  You can circumvent this issue by using **Nginx** for both SSL decryption and as a backend web server.  Using Nginx for both tasks reduces the complexity of the setup, leading to fewer potential points of failure, lower resources consumption, and fewer components to maintain.  

Both Varnish and Nginx are versatile tools with a variety of uses.  This guide uses Varnish 4.0, which comes included in Debian 8 repositories, and presents a basic setup that you can refine to meet your specific needs.

