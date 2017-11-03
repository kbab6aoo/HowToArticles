<!---
![my image is missing..!](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/images/ubuntu16_04img.png)  
-->

# Initial Server Setup with Ubuntu 16.04

## Introduction

When you first create a new Ubuntu 16.04 server, there are a few configuration steps that you should take early on as part of the basic setup. This will increase the security and usability of your server and will give you a solid foundation for subsequent actions.

-	Step One — Root Login
To log into your server, you will need to know your server's public IP address. You will also need the password or, if you installed an SSH key for authentication, the private key for the "root" user's account. If you have not already logged into your server, you may want to follow the first tutorial in this series, How to Connect to Your Instance with SSH, which covers this process in detail.

If you are not already connected to your server, go ahead and log in as the root user using the following command (substitute the highlighted word with your server's public IP address):