
Installing Java on Fedora (17+)
-------------------------------
#### Author: Jack Kasbeer
#### Created: 03.03.19 : 08:19


### Intro

Most Java appss run on one of the following:
* OpenJDK - an open-source (o-p) implementation of the Java Platform, Std. Ed.
* Oracle Java SE - a free JDK from Oracle


Installing OpenJDK (OJDK)
=========================
1. ```$ dnf search openjdk```
2. Copy the version of the OJDK you want to install
3. In the directory of the the recently downloaded [.rpm] file, run the command: ```$ dnf install <openjdk-package-name>```
4. Check if you have an alternative version of Java (**in case of failure at this point**): ```$ alternatives --config java```


Installing Oracle Java SE (OJSE)
================================
1. Navigate to Oracle Java SE downloads page
2. Choose version of Java you wish to use. Typically, go to version 8 page.
3. Download appropriate `rpm` file for your Fedora's 64-bit architecture: e.g., ```jdk-8u201-linux-x64.rpm```
4. Install OJSE: ```$ dnf instal jdk-8u201-linux-x64.rpm```


Java-related Acronymns
======================
* JRE - Java Runtime Environment
* JVM - Java Virtual Machine
* JDK - Java Development Kit
* SDK - Software Development Kit

* JavaWS [Java Web Start] 
  - Framework to start applications from the internet
* JavaFX 
  - Platform to create & deliver desktop & rich internet apps
* OpenJFX
  - JavaFX o-p implementation
* IceadTea
  - Dev project supporting OJDK
* IcedTea-Web
  - Java Web Start package; install to run JNPL files
* applets
  - obsolete technology; no longer implemented in recent packages
* JSE, J2SE, JEE,...
  - obsolete acronyms for Java Standard & Enterprise Edition. JavaSE = JRE



**JDK has many tools to be taken advantage of.**








