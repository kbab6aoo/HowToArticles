## Steps to Build a RPM from Source on CentOs / RedHat
Sometimes you might have access to an open source application source code but might not have the RPM file to install it on your system.

In that situation, you can either compile the source code and install the application from source code, or build a RPM file from source code yourself, and use the RPM file to install the application.

Ther might also be a situation where you want to build a cusotm RPM Package for the application that you developed.

### This Article explains how to build a RPM package from the source code.

In order to build RPMs, you will need source code, which usually means a compressed `tar` file that also includes the `SPEC` file