# How To Install Wordpress and PhpMyAdmin with Docker Compose on Ubuntu 16.04

## Introduction

Docker Compose makes dealing with the orchestration processes of Docker containers (such as starting up, shutting down, and setting up intra-container linking and volumes) really easy.

This article provides a real-world example of using Docker Compose to install an application, in this case WordPress with PHPMyAdmin as an extra. WordPress normally runs on a LAMP stack, which means Linux, Apache, MySQL/MariaDB, and PHP. The official WordPress Docker image includes Apache and PHP for us, so the only part we have to worry about is MariaDB.

Prerequisites

To follow this article, you will need the following:

-	Ubuntu 16.04 EC2 Instance
-	A non-root user with sudo privileges
-	Docker and Docker Compose installed

Step 1:	-	Installing WordPress

We'll be using the official [WordPress](https://hub.docker.com/_/wordpress/]) and [MariaDB](https://hub.docker.com/_/mariadb/)Docker images. If you're curious, there's lots more info about these images and their configuration options on their respective GitHub and Docker Hub pages
