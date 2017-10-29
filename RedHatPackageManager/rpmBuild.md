# Steps to Build a RPM from Source on CentOs / RedHat
Sometimes you might have access to an open source application source code but might not have the RPM file to install it on your system.

In that situation, you can either compile the source code and install the application from source code, or build a RPM file from source code yourself, and use the RPM file to install the application.

Ther might also be a situation where you want to build a cusotm RPM Package for the application that you developed.

## This Article explains how to build a RPM package from the source code.

In order to build RPMs, you will need source code, which usually means a compressed `tar` file that also includes the `SPEC` file

The `SPEC` file typically contains instructions on how to build an `RPM`, what files are part of the package and where it should be installed.

The `RPM` performs the following tasks during the build process

1.	Executes the commands and macros mentioned in the prep section of the `spec` file
2.	Checks the content of the first line