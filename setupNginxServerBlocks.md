![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/nginx_banner.jpg)  

# How To Set Up Nginx Server Blocks (Virtual Hosts) on Ubuntu 16.04  

## Introduction

When using the Nginx web server, **server blocks** (similar to the virtual hosts in Apache) can be used to encapsulate configuration details and host more than one domain off of a single server.

In this article, we will discuss how to configure server blocks in Nginx on a Ubuntu 16.04 Server.

### Prerequisites
We're going to be using a non-root user with sudo privileges throughout this article. If you do not have a user like this configured, you can create one by following our [Ubuntu 16.04](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/initialSvrSetupUbuntu_16_04.md) initial server setup guide.