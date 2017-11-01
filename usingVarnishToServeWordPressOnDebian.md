# Using Varnish to Serve WordPress on Debian 8 SSL & HTTP
_**Varnish**_ is a powerful and flexible caching HTTP reverse proxy.  It can be installed in front of any web server to cache it's content, which will improve speed and reduce server load.  When client requests a webpage, Varnish first tries to send it from cache.  If the page is not cached, Varnish forwards the request to the backend server, fetches the reponse, stores it in the cache, and delivers it to the client.
![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/VarnishDebian.png)
