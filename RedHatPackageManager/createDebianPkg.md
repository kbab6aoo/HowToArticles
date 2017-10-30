# How to create debian package from source

In any GNU/Linux distribution, which have a package manager on board, is recommended to install software by using "packages".

At some point it may happen that create a package will be easier than build it from source again and again. For example: some programs required for several colleagues and not all of them is able to understand why building from source code failed.

## Building from Source

#### Step 1:	GPG Key

The first thing you need is generate a gpg key. Package will be signed by this key. Run this commands in your terminal:
	
	gpg --gen-key
	gpg -a --output ~/.gnupg/YOUR_NAME.gpg --export 'YOUR NAME'
	gpg --import ~/.gnupg/YOUR_NAME.gpg
It's important to remember that in next stage it will be necessary to use the same name and email that were used for creating the key.
#### Step 2:	Prepare the Environment
You need install these packages for further building:

	sudo apt-get install build-essentials autoconf automake \
	autotools-dev dh-make debhelper devscripts fakeroot \
	xutils lintian pbuilder

#### Step 3:	Obtain & prepare source code  
Download tarball with source code. Unpack it and rename directory like this: "name-version". Important: the directory name must be in lowercase.

On the same level with that directory you need to place the archive with the source code. You can simply archive a directory that you just created.

	mkdir -p ~/build/memcached/1.4.17
	cd ~/build/memcached/1.4.17
	wget -c http://www.memcached.org/files/memcached-1.4.17.tar.gz
	tar -xzf memcached-1.4.17
	ls

	... memcached-1.4.17/ memcached-1.4.17.tar.gz

#### Step 4:	Debianization  
Now we will prepare the package structure

	cd ~ / build/memcached/1.4.17/memcached-1.4.17
	dh_make -e youremail@address -f .. / memcached-1.4.17.tar.gz
Do not forget that you must use the same email, that was used to generate the key . After you run this command in the terminal you'll see the following prompt:  
	
	Type of package: single binary, indep binary, multiple binary,
    library, kernel module, kernel patch?
	[s /i/m/l/k/n/b]
Choose the easiest option - s  
In our directory appeared a new subdirectory called "debian" which contains the files necessary for further assembly. Now we must edit the information about our package.

##### "control" file

First of all we must find out dependencies for our package:

	dpkg-depcheck -d ./configure

In terminal we will see something like that:

	Packages needed:
    mime-support
    libsigsegv2:amd64
    gawk
    libevent-dev

After that we must edit debian/control file like this:

	Source: memcached
	Section: web
	Priority: optional
	Maintainer: YOUR NAME <youremail.org>
	Build-Depends: debhelper (> = 8.0.0), autotools-dev, mime-support, libsigsegv2, gawk, libevent-dev
	Standards-Version: 3.9.4
	Homepage: http://memcached.org/

	Package: memcached
	Architecture: any
	Depends: $ {shlibs: Depends}, $ {misc: Depends}
	Description: High-performance, distributed memory object caching system Memcached is an in-memory key-value store for small chunks of arbitrary data (strings, objects)

##### "copyright" file

In the same "debian" folder, you will also find a "copyright" file which you need to edit. The important things to add to this file are the place you got the package from and the actual copyright notice and license.

##### "changelog" file

Make sure that email used in this file is correct (it must be same as used for gpg-key generation)

#### Step 5:	Biuld a Package  
Ok. It's time to build our package. Let's do it:

	dpkg-buildpackage -rfakeroot
If there had been no error in the previous stages the assembly process will receive a password prompt from the gpg-key. After that our new package will appear in directory one level up. You can install it by following command:

	sudo dpkg -i memcached_1.4.17-1_amd64.deb

As you can see the assembly was carried out on the platform x86_64. Do you need another platform? No problem.

Assembly for a another platform

To build, for example, for the i386 platform we can use pbuilder - an automatic Debian Package Building system for personal development workstation environments.

	cd ~/build/memcached/1.4.17
	sudo pbuilder --create --architecture i386
	sudo pbuilder --update
	# memcached_1.4.17-1.dsc was generated when we had built a package
	sudo pbuilder --build memcached_1.4.17-1.dsc

Package for i386 platform will be generated in the /var/cache/pbuilder/result directory