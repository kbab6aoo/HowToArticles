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

To build an rpm file

#### 2.	RPM Build Directories		

rpm-build will 









