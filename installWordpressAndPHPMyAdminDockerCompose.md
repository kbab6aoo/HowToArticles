# How To Install Wordpress and PhpMyAdmin with Docker Compose on Ubuntu 16.04

## Introduction

Docker Compose makes dealing with the orchestration processes of Docker containers (such as starting up, shutting down, and setting up intra-container linking and volumes) really easy.

This article provides a real-world example of using Docker Compose to install an application, in this case WordPress with PHPMyAdmin as an extra. WordPress normally runs on a LAMP stack, which means Linux, Apache, MySQL/MariaDB, and PHP. The official WordPress Docker image includes Apache and PHP for us, so the only part we have to worry about is MariaDB.

### Prerequisites

To follow this article, you will need the following:

-	Ubuntu 16.04 EC2 Instance
-	A non-root user with sudo privileges
-	Docker and Docker Compose installed

#### Step 1:	-	Installing WordPress

We'll be using the official [WordPress](https://hub.docker.com/_/wordpress/]) and [MariaDB](https://hub.docker.com/_/mariadb/) Docker images. If you're curious, there's lots more info about these images and their configuration options on their respective GitHub and Docker Hub pages

Let's start by making a folder where our data will live and creating a minimal `docker-compose.yml` file to run our WordPress container:
```
$	mkdir ~/wordpress && cd $_
```

Then create a `~/wordpress/docker-compose.yml` with your favorite text editor:
```
$	vim ~/wordpress/docker-compose.yml
```
and paste the following:
```
~/wordpress/docker-compose.yml

wordpress:
	image: wordpress
```

This just tells Docker Compose to start a new container called wordpress and download the wordpress image from the Docker Hub.

We can bring the image up like so:
```
$	docker-compose up
```

You'll see Docker download and extract the WordPress image from the Docker Hub, and after some time you will get some error messages

#### Step 2:	-	Installing MariaDB

To add the MariaDB image to the group, re-open `docker-compose.yml` with your text editor:
```
$	vim ~/wordpress/docker-compose.yml
```

Change `docker-compose.yml` to match the below (be careful with the indentation, YAML files are white-space sensitive)

```
docker-compose

wordpress:
  image: wordpress
  links:
    - wordpress_db:mysql
wordpress_db:
  image: mariadb
 ```

What we've done here is define a new container called `wordpress_db` and told it to use the `mariadb` image from the Docker Hub. We also told the our `wordpress` container to link our `wordpress_db` container into the `wordpress` container and call it `mysql` (inside the wordpress container the hostname `mysql` will be forwarded to our `wordpress_db` container).

If you run `docker-compose` up again, you will see it download the MariaDB image, and you'll also see that we're not quite there yet though.

WordPress is still complaining about being unable to find a database, and now we have a new complaint from MariaDB saying that no root password is set.

It appears that just linking the two containers isn't quite enough. Let's go ahead and set the `MYSQL_ROOT_PASSWORD` variable so that we can actually fire this thing up.

Edit the Docker Compose file yet again:

```
$	~/wordpress/docker-compose.yml
```

Add these two lines to the _end_ of the `wordpress_db` section, but **make sure to change** ***examplepass*** **to a more secure password!**

```
wordpress_db:
...
	environment:
		MYSQL_ROOT_PASSWORD: examplepass
...
```

This will set an environment variable inside the `wordpress_db` container called `MYSQL_ROOT_PASSWORD` with your desired password. The MariaDB Docker image is configured to check for this environment variable when it starts up and will take care of setting up the DB with a root account with the password defined as `MYSQL_ROOT_PASSWORD`.

While we're at it, let's also set up a port forward so that we can connect to our WordPress install once it actually loads up. Under the `wordpress` section add these two lines:

```
wordpress:
...
	ports:
		- 8080:80
...
```

The first port number is the port number on the host, and the second port number is the port inside the container. So, this configuration forwards requests on port 8080 of the host to the default web server port 80 inside the container.

>##### Note
> If you would like Wordpress to run on the default web server port 80 on the host, change the previous line to `80:80` so that requests to port 80 on the host are forwarded to port 80 inside the Wordpress container.

Your complete `docker-compose.yml` file should now look like this:

```
docker-compose.yml

wordpress:
  image: wordpress
  links:
    - wordpress_db:mysql
  ports:
    - 8080:80
wordpress_db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: examplepass
```

With this configuration we can actually go ahead and fire up WordPress. This time, let's run it with the `-d` option, which will tell `docker-compose` to run the containers in the background so that you can keep using your terminal:

```
$	docker-compose up -d
```
You will see a whole bunch of text fly by your screen. Once it's calmed down, open up a web browser and browse to the IP of your AWS EC2 Instancce on port 8080 (for example, if the IP address of your server is 123.456.789.123 you should type http://123.456.789.123:8080 into your browser.)

You should see a fresh WordPress installation page and be able to complete the install and blog as usual.

Because these are both official Docker images and are following all of Docker's best practices, each of these images have pre-defined, persistent volumes for you — meaning that if you restart the container, your blog posts will still be there. You can learn more about working with Docker volumes in the [Docker data volumes article.](https://github.com/kbab6aoo/HowToArticles/blob/myHowToArticles/workingWithDockerDataVolumes_16_04.md)

#### Step 3:	-	Adding a PhpMyAdmin Container

So far we have only been using official images, which the Docker team takes great pains to ensure are accurate. You may have noticed that we did not have to give the WordPress container any environment variables to configure it. As soon as we linked it up to a properly configured MariaDB container everything just worked.

This is because there is a script inside the WordPress Docker container that actually grabs the `MYSQL_ROOT_PASSWORD` variable from our `wordpress_db`	 container and uses that to connect to WordPress.

Let's venture out of the official image area a little bit and use a [community contributed PhpMyAdmin image](https://hub.docker.com/r/corbinu/docker-phpmyadmin/). Go ahead and edit docker-compose.yml one more time:

	


























































