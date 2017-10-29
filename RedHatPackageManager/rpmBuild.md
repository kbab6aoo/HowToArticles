# Steps to Build a RPM from Source on CentOs / RedHat
Sometimes you might have access to an open source application source code but might not have the RPM file to install it on your system.

In that situation, you can either compile the source code and install the application from source code, or build a RPM file from source code yourself, and use the RPM file to install the application.

There might also be a situation where you want to build a custom RPM Package for the application that you developed.

## This Article explains how to build a RPM package from the source code.

In order to build RPMs, you will need source code, which usually means a compressed `tar` file that also includes the `SPEC` file.

The `SPEC` file typically contains instructions on how to build an `RPM`, what files are part of the package and where it should be installed.

The `RPM` performs the following tasks during the build process

1.	Executes the commands and macros mentioned in the prep section of the `spec` file
2.	Checks the content of the first line
3.	Executes the commands and macros in the build section of the `spec` file
4.	Creates the binary package file
5.	Create the source package file

Once the `RPM` execute the above steps, it creates the binary package file and source package file.

The binary package file consists of all source files along with any additional information to install or uninstall the package.

It is usually enabled with all the options for installing the package that are platform specific.  Binary package files contain complete applications of libraries of functions compiled for a particular architecture.  The source package usually consists of the original compressed `tar` file, `spec` file and the patches which are required to create the binary package file.

Let us see how to create a simple source BIN RPM package using a `tar` file.

If you are new to rpm packaging, you might want to first understand hot to use rpm command to install, upgrade and remove packages on CentOS or REDHat.

#### 1.	Install rpm-build 

To build an rpm file based on the `spec` file that we just created, we need to use `rpm-build` command.

`rpm-build` command is part pf the `rpm-build` package.  Install it as shown below

```
# yum install rpm-build
```

`rpm-build` is dependent on the following package.  If you don't have these installed already, `yum` will automatically install these dependencies for you.

```
elfutils-libelf
rpm
rpm-libs
rpm-python
```
#### 2.	RPM Build Directories		

rpm-build will automatically create the following directory structures that will be used during the RPM build

```
# ls -lF /root/rpmbuild/
drwx-xr-x	2	root	root	BUILD/
drwx-xr-x	2	root	root	BUILDROOT/
drwx-xr-x	2	root	root	RPMS/
drwx-xr-x	2	root	root	SOURCES/
drwx-xr-x	2	root	root	SPECS/
drwx-xr-x	2	root	root	SRPMS/
```
_Note:_ The above directory structure is for both CentOS and RedHat when using `rpm-build` package.  You can also use `_usr/src/redhat_` directory, but you need to change the `'topdir'` parameter accordingly during the rpm build.  If you are doing this SUSE Enterprise Linux, use _/usr/src/packages_ directory.

If you want to use your own directory structure instead of the _/root/rpmbuild,_ you can use one of the following option:
-	Use-buildroot option and specify the custom directory during the rpmbuild
-	Specify the topdir parameter in the rpmrc file or rpmmacros file

#### 3.	Download Source Tar File

Next, download the source `tar` file for the package that you want to build and save it under SOURCES directory.

For this example, we are going to use the source code of _icecase_ open source application, which is a server software for streaming multi-media.  But, the steps are exactly the same for building an RPM for any other application.  You just have to download the corresponding source code for the RPM that you are trying to build.

```
#	cd /root/rpmbuild/SOURCES/

#	wget http://downloads.xiph.org/releases/icecast/icecast-2.3.3.tar.gz

#	ls -l

#	-rw-r--r--	1	root	root	icecast-2.3.3.tar.gz
```
#### 4.	Create the SPEC File
-	-	-	
In this step, we direct RPM in the build process by creating a `spec` file.  The `spec` file usually consists of the following eight different sections:

1.	_Preamble -_ The preamble section contains information about the package being built and define any dependencies to the package.  In general, the preamble consists of entries, one per line, that start with a tag followed by a colon, and then some information.

2.	_%prep -_ In this section, we prepare the software for building process.  Any previous builds are removed during the process and the source file _(.tar)_ file is expanded, etc.

3.	One more key thing is to understand there are pre-defined macros available to perform various shortcut options to build an rpm.  You may be using this macro when you try to build any complex packages.  In the below example, in the example below we are going to use %setup which removes any previous builds, untar the source files and changes the ownership of the files.  You can also use _sh_ scripts under %prep section to perform this action but %setup macro simplifies the process by using predefined _sh_ scripts.

4.	_%description -_ the description section usually contains description about the package.

5.	_%build -_ This is the section that is responsible for performing the build.
Usually the %build section is an _sh_ script.

6.	_%install -_ the % install section is also 


















































































